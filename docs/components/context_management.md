# Context Management in OpenManus

This document provides detailed information about the context management system in OpenManus, which is responsible for managing the context window for language model interactions and browser contexts for web interactions.

## Overview

Context management in OpenManus encompasses two main areas:

1. **LLM Context Window Management**: Managing the context window for language model interactions, including token limits and context optimization
2. **Browser Context Management**: Managing browser contexts for web interactions, including page navigation, DOM manipulation, and state management

Both aspects of context management are critical for the effective operation of OpenManus agents, enabling them to interact with language models and web browsers efficiently.

## LLM Context Window Management

### Token Limits and Configuration

OpenManus manages the context window for language model interactions through token limits configured in the `LLMSettings` class in `app/config.py`:

```python
class LLMSettings(BaseModel):
    model: str = Field(..., description="Model name")
    base_url: str = Field(..., description="API base URL")
    api_key: str = Field(..., description="API key")
    max_tokens: int = Field(4096, description="Maximum number of tokens per request")
    temperature: float = Field(1.0, description="Sampling temperature")
    api_type: str = Field(..., description="AzureOpenai or Openai")
    api_version: str = Field(..., description="Azure Openai version if AzureOpenai")
```

The `max_tokens` parameter (default: 4096) defines the maximum number of tokens that can be generated in a single response. This parameter is used in the `LLM` class when making requests to the language model:

```python
async def ask(
    self,
    messages: List[Message],
    system_msgs: Optional[List[Message]] = None,
    **kwargs,
) -> str:
    # ...
    response = await self.client.chat.completions.create(
        model=self.model,
        messages=formatted_messages,
        max_tokens=self.max_tokens,
        temperature=self.temperature,
        **kwargs,
    )
    # ...
```

### Context Window Optimization

OpenManus implements several strategies to optimize the use of the context window:

1. **Memory Management**: The Memory class implements a maximum message limit to prevent context overflow:
   ```python
   if len(self.messages) > self.max_messages:
       self.messages = self.messages[-self.max_messages :]
   ```

2. **Selective Context Inclusion**: When making requests to the language model, only relevant messages are included in the context:
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

3. **Truncation Warnings**: Some tools, like the string replacement editor, include warnings about truncated content to save on context:
   ```python
   TRUNCATED_MESSAGE: str = "<response clipped><NOTE>To save on context only part of this file has been shown to you. You should retry this tool after you have searched inside the file with `grep -n` in order to find the line numbers of what you are looking for."
   ```

### Dynamic Context Generation

OpenManus generates dynamic context for specific scenarios to provide relevant information to the agent:

1. **Step Execution Context**: In the PlanningFlow, a dynamic context is generated for each step execution:
   ```python
   step_prompt = f"""
   CURRENT PLAN STATUS:
   {plan_status}

   YOUR CURRENT TASK:
   You are now working on step {self.current_step_index}: "{step_text}"

   Please execute this step using the appropriate tools. When you're done, provide a summary of what you accomplished.
   """
   ```

2. **State Transition Context**: The BaseAgent includes a context manager for safe state transitions:
   ```python
   @asynccontextmanager
   async def state_context(self, new_state: AgentState):
       """Context manager for safe agent state transitions."""
       previous_state = self.state
       self.state = new_state
       try:
           yield
       finally:
           self.state = previous_state  # Revert to previous state
   ```

## Browser Context Management

OpenManus includes a sophisticated browser context management system through the `BrowserUseTool` class, which leverages the `browser-use` library to interact with web browsers.

### Browser Context Initialization

The `BrowserUseTool` class initializes a browser context when needed:

```python
async def _ensure_browser_initialized(self) -> BrowserContext:
    """Ensure browser and context are initialized."""
    if self.browser is None:
        self.browser = await Browser.create()
    if self.context is None:
        self.context = await self.browser.new_context()
        self.dom_service = DomService(await self.context.get_current_page())
    return self.context
```

This lazy initialization approach ensures that browser resources are only allocated when needed.

### Browser Context Operations

The `BrowserUseTool` class provides a wide range of operations for interacting with the browser context:

1. **Navigation**:
   ```python
   async def execute(self, action: str, url: str = None, **kwargs) -> ToolResult:
       # ...
       if action == "navigate":
           context = await self._ensure_browser_initialized()
           await context.navigate_to(url)
           # ...
   ```

2. **Element Interaction**:
   ```python
   elif action == "click":
       # ...
       element = await context.get_dom_element_by_index(index)
       download_path = await context._click_element_node(element)
       # ...
   ```

3. **Screenshot Capture**:
   ```python
   elif action == "screenshot":
       # ...
       screenshot = await context.take_screenshot(full_page=True)
       # ...
   ```

4. **JavaScript Execution**:
   ```python
   elif action == "execute_javascript":
       # ...
       result = await context.execute_javascript(script)
       # ...
   ```

5. **Tab Management**:
   ```python
   elif action == "switch_tab":
       # ...
       await context.switch_to_tab(tab_id)
       # ...
   ```

### Browser Context Cleanup

The `BrowserUseTool` class includes methods for cleaning up browser contexts to free resources:

```python
async def close(self) -> ToolResult:
    """Close the browser and free resources."""
    if self.context is not None:
        await self.context.close()
        self.context = None
    # ...
```

This cleanup is important to prevent resource leaks, especially in long-running applications.

## Context Management Best Practices

When working with context management in OpenManus, follow these best practices:

### LLM Context Window Management

1. **Monitor Token Usage**: Be mindful of token usage, especially for complex tasks that require large context windows.

2. **Optimize Message Content**: Keep messages concise and focused to maximize the effective use of the context window.

3. **Use System Messages Effectively**: Use system messages to provide important context that should be preserved throughout the conversation.

4. **Clear Irrelevant Context**: Clear irrelevant context when it's no longer needed to free up space in the context window.

5. **Adjust Token Limits**: Adjust the `max_tokens` parameter based on your specific use case and the capabilities of the language model you're using.

### Browser Context Management

1. **Initialize Contexts Lazily**: Initialize browser contexts only when needed to conserve resources.

2. **Close Contexts When Done**: Close browser contexts when they're no longer needed to free up resources.

3. **Handle Multiple Tabs Carefully**: When working with multiple tabs, keep track of the active tab to avoid confusion.

4. **Manage DOM State**: Be aware of the DOM state when interacting with web pages, especially for dynamic content.

5. **Handle Errors Gracefully**: Implement error handling for browser context operations to prevent crashes and ensure a smooth user experience.

## Customizing Context Management

OpenManus allows for customizing context management to suit your specific needs:

### Customizing LLM Context Window Management

1. **Adjusting Token Limits**: You can adjust the token limits in the configuration file:
   ```toml
   [llm]
   model = "gpt-4o"
   base_url = "https://api.openai.com/v1"
   api_key = "sk-..."
   max_tokens = 8192  # Increase token limit
   temperature = 0.0
   ```

2. **Implementing Custom Context Selection**: You can implement custom logic to select which messages to include in the context:
   ```python
   # Select only the most relevant messages
   relevant_messages = select_relevant_messages(agent.memory.messages)
   response = await agent.llm.ask(messages=relevant_messages)
   ```

3. **Using Different Models for Different Tasks**: You can use different language models with different context window sizes for different tasks:
   ```python
   # Use a model with a larger context window for complex tasks
   complex_llm = LLM(config_name="complex")  # Configured with a larger max_tokens
   response = await complex_llm.ask(messages=messages)
   ```

### Customizing Browser Context Management

1. **Configuring Browser Options**: You can configure browser options when creating the browser context:
   ```python
   browser = await Browser.create(headless=False)  # Show the browser window
   context = await browser.new_context(viewport={"width": 1920, "height": 1080})
   ```

2. **Implementing Custom Browser Interactions**: You can implement custom browser interactions by extending the `BrowserUseTool` class:
   ```python
   class CustomBrowserTool(BrowserUseTool):
       async def execute(self, action: str, **kwargs) -> ToolResult:
           if action == "custom_action":
               # Implement custom browser interaction
               # ...
           else:
               return await super().execute(action, **kwargs)
   ```

3. **Managing Multiple Browser Contexts**: You can manage multiple browser contexts for different tasks:
   ```python
   # Create a separate browser context for each task
   task1_context = await browser.new_context()
   task2_context = await browser.new_context()
   ```

## Summary

Context management in OpenManus encompasses both LLM context window management and browser context management. The system provides mechanisms for optimizing context usage, generating dynamic context, and managing browser interactions. By following best practices and customizing context management to suit your specific needs, you can ensure efficient and effective operation of OpenManus agents. 