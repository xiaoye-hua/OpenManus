# Extending OpenManus

This document provides guidance on how to extend OpenManus with custom components, such as agents, tools, and flows.

## Overview

OpenManus is designed to be extensible, allowing you to add custom components to enhance its capabilities. You can extend OpenManus in several ways:

1. **Custom Agents**: Create specialized agents for specific tasks
2. **Custom Tools**: Add new tools to enable new capabilities
3. **Custom Flows**: Create new flows to orchestrate agents in different ways
4. **Custom Prompts**: Modify the prompts used by agents to change their behavior

## Creating Custom Agents

Custom agents allow you to create specialized agents for specific tasks. To create a custom agent, you need to:

1. Create a new class that inherits from one of the existing agent classes
2. Define the agent's name, description, and other attributes
3. Optionally override methods to customize behavior

Here's an example of a custom agent:

```python
from app.agent.toolcall import ToolCallAgent
from app.prompt.manus import NEXT_STEP_PROMPT, SYSTEM_PROMPT
from app.tool import ToolCollection
from app.tool.python_execute import PythonExecute
from app.tool.file_saver import FileSaver
from app.tool.terminate import Terminate

class DataAnalysisAgent(ToolCallAgent):
    """
    A specialized agent for data analysis tasks.
    """

    name: str = "DataAnalysisAgent"
    description: str = "A specialized agent for data analysis tasks"

    system_prompt: str = """
    You are a specialized agent for data analysis tasks. Your goal is to help users analyze data,
    create visualizations, and extract insights. You have access to Python and various data analysis
    libraries like pandas, numpy, matplotlib, and seaborn.
    """
    next_step_prompt: str = NEXT_STEP_PROMPT

    # Add specialized tools for data analysis
    available_tools: ToolCollection = ToolCollection(
        PythonExecute(), FileSaver(), Terminate()
    )
```

To use the custom agent:

```python
from your_module import DataAnalysisAgent

# Create the agent
agent = DataAnalysisAgent()

# Run the agent
await agent.run("Analyze the data in data.csv and create visualizations")
```

## Creating Custom Tools

Custom tools allow you to add new capabilities to OpenManus. To create a custom tool, you need to:

1. Create a new class that inherits from `BaseTool`
2. Define the tool's name, description, and parameters
3. Implement the `execute()` method

Here's an example of a custom tool:

```python
from app.tool.base import BaseTool, ToolResult

class WeatherTool(BaseTool):
    """
    A tool for getting weather information.
    """

    name = "weather"
    description = "Get weather information for a location"
    parameters = {
        "type": "object",
        "properties": {
            "location": {
                "type": "string",
                "description": "The location to get weather for (e.g., 'New York', 'London')"
            }
        },
        "required": ["location"]
    }

    async def execute(self, location: str) -> ToolResult:
        """
        Get weather information for a location.
        """
        try:
            # In a real implementation, you would call a weather API here
            # For this example, we'll just return a mock response
            weather_info = f"Weather for {location}: Sunny, 25°C"
            return ToolResult(output=weather_info)
        except Exception as e:
            return ToolResult(error=f"Error getting weather: {str(e)}")
```

To use the custom tool:

```python
from app.agent.manus import Manus
from your_module import WeatherTool

# Create the agent
agent = Manus()

# Add the custom tool
agent.available_tools.add_tool(WeatherTool())

# Run the agent
await agent.run("What's the weather in New York?")
```

## Creating Custom Flows

Custom flows allow you to orchestrate agents in different ways. To create a custom flow, you need to:

1. Create a new class that inherits from `BaseFlow`
2. Define the flow's attributes
3. Implement the `execute()` method

Here's an example of a custom flow:

```python
from typing import Dict, List, Union

from app.agent.base import BaseAgent
from app.flow.base import BaseFlow
from app.schema import Message

class SequentialFlow(BaseFlow):
    """
    A flow that executes agents sequentially.
    """

    agent_order: List[str] = []

    def __init__(
        self, agents: Union[BaseAgent, List[BaseAgent], Dict[str, BaseAgent]], **data
    ):
        super().__init__(agents, **data)
        
        # Set agent_order to all agent keys if not specified
        if not self.agent_order:
            self.agent_order = list(self.agents.keys())

    async def execute(self, prompt: str) -> str:
        """
        Execute the flow with the given prompt.
        """
        result = ""
        
        # Execute agents in order
        for agent_key in self.agent_order:
            agent = self.agents[agent_key]
            agent_result = await agent.run(prompt + "\n\nPrevious results:\n" + result)
            result += f"\n\n{agent_key} result:\n{agent_result}"
        
        return result
```

To use the custom flow:

```python
from app.agent.manus import Manus
from your_module import SequentialFlow

# Create agents
agents = {
    "research": Manus(),
    "analysis": Manus(),
    "summary": Manus(),
}

# Create a sequential flow
flow = SequentialFlow(
    agents=agents,
    agent_order=["research", "analysis", "summary"]
)

# Execute the flow
result = await flow.execute("Research the latest developments in quantum computing")
```

## Customizing Prompts

Customizing prompts allows you to change the behavior of agents without modifying their code. To customize prompts, you can:

1. Create new prompt templates
2. Modify existing prompt templates
3. Set custom prompts when creating agents

Here's an example of customizing prompts:

```python
from app.agent.manus import Manus

# Create a custom system prompt
CUSTOM_SYSTEM_PROMPT = """
You are a specialized agent for scientific research. Your goal is to help users research
scientific topics, find relevant papers, and summarize findings. You should focus on
providing accurate and up-to-date information from reliable sources.
"""

# Create a custom next step prompt
CUSTOM_NEXT_STEP_PROMPT = """
Based on the current state of the research, what should be the next step?
Consider:
1. What information is still missing?
2. What sources should be consulted next?
3. How can the current findings be validated?
"""

# Create the agent with custom prompts
agent = Manus()
agent.system_prompt = CUSTOM_SYSTEM_PROMPT
agent.next_step_prompt = CUSTOM_NEXT_STEP_PROMPT

# Run the agent
await agent.run("Research the latest developments in CRISPR technology")
```

## Best Practices for Extending OpenManus

When extending OpenManus, follow these best practices:

### General Best Practices

- **Follow the existing patterns**: Follow the patterns and conventions used in the existing codebase
- **Keep it simple**: Keep your extensions simple and focused on a specific task
- **Document your code**: Document your code with docstrings and comments
- **Write tests**: Write tests for your extensions to ensure they work correctly

### Agent Best Practices

- **Specialize for a purpose**: Create agents that are specialized for a specific purpose
- **Provide clear instructions**: Provide clear instructions in the system prompt
- **Use appropriate tools**: Include only the tools that are relevant to the agent's purpose

### Tool Best Practices

- **Single responsibility**: Each tool should have a single responsibility
- **Clear parameters**: Define clear parameters with descriptive names and documentation
- **Error handling**: Handle errors gracefully and provide informative error messages
- **Resource management**: Manage resources carefully, especially for tools that use external services

### Flow Best Practices

- **Clear orchestration**: Define clear orchestration logic that is easy to understand
- **Flexible agent selection**: Allow for flexible agent selection based on the task
- **Progress tracking**: Track progress and provide feedback to the user
- **Error recovery**: Implement error recovery mechanisms to handle failures

## Example: Creating a Specialized Agent for Scientific Research

Here's a complete example of creating a specialized agent for scientific research:

```python
from pydantic import Field

from app.agent.toolcall import ToolCallAgent
from app.tool import Terminate, ToolCollection
from app.tool.browser_use_tool import BrowserUseTool
from app.tool.file_saver import FileSaver
from app.tool.google_search import GoogleSearch
from app.tool.python_execute import PythonExecute

class ScientificResearchAgent(ToolCallAgent):
    """
    A specialized agent for scientific research tasks.
    """

    name: str = "ScientificResearchAgent"
    description: str = "A specialized agent for scientific research tasks"

    system_prompt: str = """
    You are a specialized agent for scientific research tasks. Your goal is to help users research
    scientific topics, find relevant papers, and summarize findings. You should focus on
    providing accurate and up-to-date information from reliable sources.
    
    When conducting research:
    1. Start by searching for recent review papers or meta-analyses
    2. Look for papers from reputable journals and institutions
    3. Consider the methodology and sample size of studies
    4. Look for consensus across multiple sources
    5. Acknowledge limitations and areas of uncertainty
    
    Provide balanced and nuanced summaries that reflect the current state of knowledge.
    """
    
    next_step_prompt: str = """
    Based on the current state of the research, what should be the next step?
    Consider:
    1. What information is still missing?
    2. What sources should be consulted next?
    3. How can the current findings be validated?
    """

    # Add tools for scientific research
    available_tools: ToolCollection = Field(
        default_factory=lambda: ToolCollection(
            GoogleSearch(), BrowserUseTool(), PythonExecute(), FileSaver(), Terminate()
        )
    )
    
    # Increase the maximum number of steps for thorough research
    max_steps: int = 20
```

To use this specialized agent:

```python
from your_module import ScientificResearchAgent

# Create the agent
agent = ScientificResearchAgent()

# Run the agent
await agent.run("Research the latest developments in CRISPR technology and summarize the key findings")
```

This specialized agent will conduct thorough scientific research, focusing on finding reliable sources and providing balanced summaries. 