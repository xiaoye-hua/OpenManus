# Memory Management in OpenManus

This document provides detailed information about the memory management system in OpenManus, which is responsible for storing and retrieving conversation history and agent state.

## Overview

Memory management in OpenManus is a critical component that enables agents to maintain conversation history, track their state, and make informed decisions based on past interactions. The memory system is designed to be simple yet effective, providing a way to store and retrieve messages exchanged between the user, the agent, and tools.

## Memory Architecture

### Memory Class

The core of the memory management system is the `Memory` class defined in `app/schema.py`. This class is responsible for storing and managing messages exchanged during agent interactions.

```python
class Memory(BaseModel):
    messages: List[Message] = Field(default_factory=list)
    max_messages: int = Field(default=100)

    def add_message(self, message: Message) -> None:
        """Add a message to memory"""
        self.messages.append(message)
        # Optional: Implement message limit
        if len(self.messages) > self.max_messages:
            self.messages = self.messages[-self.max_messages :]

    def add_messages(self, messages: List[Message]) -> None:
        """Add multiple messages to memory"""
        self.messages.extend(messages)

    def clear(self) -> None:
        """Clear all messages"""
        self.messages.clear()

    def get_recent_messages(self, n: int) -> List[Message]:
        """Get n most recent messages"""
        return self.messages[-n:] if n < len(self.messages) else self.messages
```

Key features of the Memory class:
- Stores a list of messages with roles (user, system, assistant, tool)
- Implements a maximum message limit to prevent memory overflow
- Provides methods for adding, retrieving, and clearing messages

### Message Class

The `Message` class, also defined in `app/schema.py`, represents individual messages in the conversation. Each message has a role, content, and optional tool-related information.

```python
class Message(BaseModel):
    """Represents a chat message in the conversation"""
    role: Literal["system", "user", "assistant", "tool"]
    content: Optional[str] = None
    tool_calls: Optional[List[ToolCall]] = None
    name: Optional[str] = None
    tool_call_id: Optional[str] = None
```

The Message class provides factory methods for creating different types of messages:
- `user_message`: Creates a message from the user
- `system_message`: Creates a system message (instructions)
- `assistant_message`: Creates a message from the assistant
- `tool_message`: Creates a message from a tool

## Integration with Agents

The memory system is integrated into the agent architecture through the `BaseAgent` class, which includes a `memory` attribute and methods for updating and retrieving memory.

```python
class BaseAgent(BaseModel, ABC):
    # ...
    memory: Memory = Field(default_factory=Memory, description="Agent's memory store")
    # ...

    def update_memory(
        self,
        role: Literal["user", "system", "assistant", "tool"],
        content: str,
        **kwargs,
    ) -> None:
        """Add a message to the agent's memory."""
        # ...
        self.memory.add_message(msg)

    @property
    def messages(self) -> List[Message]:
        """Retrieve a list of messages from the agent's memory."""
        return self.memory.messages

    @messages.setter
    def messages(self, value: List[Message]) -> None:
        """Set the list of messages in the agent's memory."""
        self.memory.messages = value
```

This integration allows agents to:
- Store user requests, system instructions, and their own responses
- Track tool calls and their results
- Access conversation history to make informed decisions

## Memory Usage in Agent Execution

The memory system plays a crucial role in the agent execution flow:

1. **Initialization**: When an agent is created, its memory is initialized as an empty list of messages.

2. **Request Processing**: When a user request is received, it's added to the agent's memory:
   ```python
   async def run(self, request: Optional[str] = None) -> str:
       # ...
       if request:
           self.update_memory("user", request)
       # ...
   ```

3. **Response Generation**: The agent generates responses based on the messages in its memory:
   ```python
   async def think(self) -> bool:
       # ...
       response = await self.llm.ask_tool(
           messages=self.messages,
           system_msgs=[Message.system_message(self.system_prompt)]
           if self.system_prompt
           else None,
           # ...
       )
       # ...
       self.memory.add_message(Message.assistant_message(response.content))
       # ...
   ```

4. **Tool Execution**: When tools are executed, their results are added to the agent's memory:
   ```python
   async def execute_tool(self, tool_call: ToolCall) -> ToolResult:
       # ...
       tool_msg = Message.tool_message(
           str(result), tool_call_id=tool_call.id, name=tool_call.function.name
       )
       self.memory.add_message(tool_msg)
       # ...
   ```

5. **Loop Detection**: The memory is used to detect loops and prevent the agent from getting stuck:
   ```python
   def _detect_loop(self) -> bool:
       """Detect if the agent is stuck in a loop."""
       if len(self.memory.messages) < 2:
           return False

       last_message = self.memory.messages[-1]
       # ...
       for msg in reversed(self.memory.messages[:-1]):
           # ...
   ```

## Memory Limitations and Management

The memory system in OpenManus includes mechanisms to manage memory usage and prevent issues:

1. **Maximum Message Limit**: The Memory class implements a maximum message limit (default: 100) to prevent memory overflow:
   ```python
   if len(self.messages) > self.max_messages:
       self.messages = self.messages[-self.max_messages :]
   ```

2. **Selective Memory Updates**: Agents can selectively update their memory, focusing on relevant information.

3. **Memory Clearing**: The Memory class provides a `clear()` method to reset the agent's memory when needed.

## Best Practices

When working with the memory system in OpenManus, follow these best practices:

1. **Manage Memory Size**: Be mindful of the memory size, especially for long-running agents. Consider adjusting the `max_messages` parameter for your use case.

2. **Use Appropriate Message Types**: Use the appropriate message types (user, system, assistant, tool) to maintain a clear conversation structure.

3. **Include Relevant Context**: When adding messages to memory, include relevant context to help the agent make informed decisions.

4. **Clear Memory When Appropriate**: Clear the agent's memory when starting a new conversation or when the context changes significantly.

5. **Monitor Memory Usage**: Monitor the agent's memory usage, especially for long-running agents, to prevent performance issues.

## Customizing Memory Management

OpenManus allows for customizing memory management by:

1. **Adjusting Maximum Message Limit**: You can adjust the maximum message limit to suit your needs:
   ```python
   from app.agent.manus import Manus
   from app.schema import Memory

   agent = Manus()
   agent.memory = Memory(max_messages=200)  # Increase memory capacity
   ```

2. **Implementing Custom Memory Logic**: You can extend the Memory class to implement custom memory management logic:
   ```python
   from app.schema import Memory, Message

   class PrioritizedMemory(Memory):
       def add_message(self, message: Message) -> None:
           # Custom logic to prioritize important messages
           # ...
           super().add_message(message)
   ```

3. **Selective Memory Updates**: You can selectively update the agent's memory based on your specific requirements:
   ```python
   # Only add important messages to memory
   if is_important_message(message):
       agent.update_memory(role, content)
   ```

## Summary

The memory management system in OpenManus is a critical component that enables agents to maintain conversation history, track their state, and make informed decisions based on past interactions. It provides a simple yet effective way to store and retrieve messages exchanged during agent interactions, with mechanisms to prevent memory overflow and detect loops. The system is integrated into the agent architecture, allowing agents to access and update their memory as needed. 