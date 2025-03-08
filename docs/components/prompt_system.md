# OpenManus Prompt System

This document provides detailed information about the prompt system in OpenManus, which is a key component that enables agents to understand and execute tasks effectively.

## Overview

The prompt system in OpenManus consists of carefully crafted prompts that guide the behavior of different agents. Each agent type has specific prompts tailored to its purpose and capabilities. The prompt system is organized into two main categories:

1. **System Prompts**: Define the agent's identity, capabilities, and overall behavior
2. **Next Step Prompts**: Guide the agent's decision-making process for the next action

These prompts are stored in the `app/prompt` directory and imported by the respective agent implementations.

## Prompt Architecture

### Directory Structure

The prompt system is organized in the following directory structure:

```
app/
└── prompt/
    ├── __init__.py
    ├── manus.py       # Prompts for the Manus agent
    ├── planning.py    # Prompts for the Planning agent
    ├── swe.py         # Prompts for the Software Engineering agent
    └── toolcall.py    # Prompts for the ToolCall agent
```

### Prompt Types

#### System Prompts

System prompts define the agent's identity, capabilities, and overall behavior. They are typically provided as the first message in a conversation with the language model and set the context for all subsequent interactions.

Example from `manus.py`:
```python
SYSTEM_PROMPT = "You are OpenManus, an all-capable AI assistant, aimed at solving any task presented by the user. You have various tools at your disposal that you can call upon to efficiently complete complex requests. Whether it's programming, information retrieval, file processing, or web browsing, you can handle it all."
```

#### Next Step Prompts

Next step prompts guide the agent's decision-making process for the next action. They are typically provided after the agent has processed the user's request and needs to decide what to do next.

Example from `planning.py`:
```python
NEXT_STEP_PROMPT = """
Based on the current state, what's your next step?
Consider:
1. Do you need to create or refine a plan?
2. Are you ready to execute a specific step?
3. Have you completed the task?

Provide reasoning, then select the appropriate tool or action.
"""
```

## Integration with Agents

The prompts are integrated into the agent implementations through imports and class attributes. Each agent class has `system_prompt` and `next_step_prompt` attributes that are set to the appropriate prompts from the prompt modules.

Example from `manus.py`:
```python
from app.prompt.manus import NEXT_STEP_PROMPT, SYSTEM_PROMPT

class Manus(ToolCallAgent):
    # ...
    system_prompt: str = SYSTEM_PROMPT
    next_step_prompt: str = NEXT_STEP_PROMPT
    # ...
```

## Dynamic Prompts

In addition to the static prompts defined in the prompt modules, OpenManus also supports dynamic prompts that are generated at runtime based on the current state of the agent and the task.

For example, in the `PlanningFlow` class, a dynamic prompt is generated for each step execution:

```python
step_prompt = f"""
CURRENT PLAN STATUS:
{plan_status}

YOUR CURRENT TASK:
You are now working on step {self.current_step_index}: "{step_text}"

Please execute this step using the appropriate tools. When you're done, provide a summary of what you accomplished.
"""
```

This allows the agent to have context-aware prompts that incorporate the current state of the task.

## Prompt Usage in the LLM Interface

The prompts are used in the LLM interface to guide the language model's responses. The `LLM` class in `app/llm.py` provides methods for sending prompts to the language model and receiving responses.

For example, the `ask` method sends a prompt to the LLM and returns the response:

```python
async def ask(
    self,
    messages: List[Message],
    system_msgs: Optional[List[Message]] = None,
    **kwargs,
) -> str:
    # ...
```

And the `ask_tool` method sends a prompt with tool options:

```python
async def ask_tool(
    self,
    messages: List[Message],
    system_msgs: Optional[List[Message]] = None,
    tools: Optional[List[Dict]] = None,
    tool_choice: Literal["none", "auto", "required"] = "auto",
    **kwargs,
) -> Message:
    # ...
```

## Prompt Customization

OpenManus allows for prompt customization by modifying the prompt modules or by setting the prompt attributes directly on the agent instances.

For example, to customize the system prompt for a Manus agent:

```python
from app.agent.manus import Manus

agent = Manus()
agent.system_prompt = "You are a specialized agent focused on data analysis..."
```

This flexibility allows for tailoring the agent's behavior to specific use cases without modifying the core code.

## Best Practices

When working with the prompt system in OpenManus, follow these best practices:

1. **Keep prompts clear and concise**: Prompts should be clear, concise, and focused on the agent's purpose
2. **Provide context**: Include relevant context in dynamic prompts to help the agent make informed decisions
3. **Be specific about tools**: Clearly specify which tools the agent should use and how to use them
4. **Test prompts thoroughly**: Test prompts with different inputs to ensure they produce the desired behavior
5. **Iterate and refine**: Continuously iterate and refine prompts based on the agent's performance

## Summary

The prompt system in OpenManus is a key component that enables agents to understand and execute tasks effectively. It consists of system prompts that define the agent's identity and capabilities, and next step prompts that guide the agent's decision-making process. The prompts are organized in modules and integrated into the agent implementations through imports and class attributes. OpenManus also supports dynamic prompts that incorporate the current state of the task, and allows for prompt customization to tailor the agent's behavior to specific use cases. 