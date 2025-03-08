# OpenManus Tools

This document provides detailed information about the tools available in OpenManus and how to create new ones.

## Tool Architecture

OpenManus tools are built on a modular architecture that allows for easy extension and integration. The tool architecture consists of:

- `BaseTool`: The abstract base class for all tools
- `ToolResult`: Represents the result of a tool execution
- `ToolCollection`: Manages multiple tools

### BaseTool

`BaseTool` is the abstract base class that all tools must inherit from. It defines:

- `name`: The name of the tool
- `description`: A description of what the tool does
- `parameters`: The parameters that the tool accepts

Key methods:
- `execute()`: Abstract method that must be implemented by subclasses
- `to_param()`: Converts the tool to a format that can be used by the LLM

### ToolResult

`ToolResult` represents the result of a tool execution. It contains:

- `output`: The output of the tool execution
- `error`: Any error that occurred during execution
- `system`: System-level messages

### ToolCollection

`ToolCollection` manages multiple tools and provides a unified interface for tool registration and execution. Key methods:

- `execute()`: Executes a specific tool
- `execute_all()`: Executes all tools in the collection
- `add_tool()`: Adds a tool to the collection
- `to_params()`: Converts all tools to a format that can be used by the LLM

## Available Tools

OpenManus comes with a variety of built-in tools:

### BrowserUseTool

Enables web browsing capabilities, allowing the agent to:
- Navigate to URLs
- Click on elements
- Fill forms
- Extract information from web pages

### PythonExecute

Allows the agent to execute Python code. This is useful for:
- Data processing
- Calculations
- Custom logic

### GoogleSearch

Enables the agent to search the web using Google. This is useful for:
- Information retrieval
- Research
- Fact-checking

### FileSaver

Allows the agent to save files to the local filesystem. This is useful for:
- Saving results
- Creating reports
- Storing data

### PlanningTool

Enables planning capabilities, allowing the agent to:
- Break down complex tasks into manageable steps
- Create and execute plans
- Track progress

### StrReplaceEditor

Provides text editing capabilities, allowing the agent to:
- Create and edit files
- Apply patches
- Modify code

### Bash

Allows the agent to execute shell commands. This is useful for:
- System operations
- File management
- Running external tools

### Terminate

Allows the agent to terminate execution. This is useful for:
- Graceful termination
- Error handling

## Creating Custom Tools

Creating a custom tool involves the following steps:

1. Create a new class that inherits from `BaseTool`
2. Define the tool's name, description, and parameters
3. Implement the `execute()` method

Here's an example of a simple custom tool:

```python
from app.tool.base import BaseTool, ToolResult

class MyCustomTool(BaseTool):
    name = "my_custom_tool"
    description = "A custom tool that does something useful"
    parameters = {
        "type": "object",
        "properties": {
            "input": {
                "type": "string",
                "description": "The input to process"
            }
        },
        "required": ["input"]
    }

    async def execute(self, input: str) -> ToolResult:
        # Process the input
        result = f"Processed: {input}"
        return ToolResult(output=result)
```

To use the custom tool, add it to the agent's available tools:

```python
from app.agent.manus import Manus
from app.tool import ToolCollection

# Create the agent
agent = Manus()

# Add the custom tool
agent.available_tools.add_tool(MyCustomTool())

# Run the agent
await agent.run("Use my custom tool to process 'hello world'")
``` 