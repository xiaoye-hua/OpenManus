# PlanningFlow Usage in OpenManus

This document provides detailed information about where and how the PlanningFlow component is used in the OpenManus project.

## Overview

PlanningFlow is a key component in OpenManus that orchestrates the planning and execution of tasks using agents. It's designed to break down complex tasks into manageable steps and execute them using appropriate agents.

## Where PlanningFlow is Used

### 1. Main Usage in run_flow.py

The primary usage of PlanningFlow is in `run_flow.py`, which serves as an alternative entry point to the application. This file:

1. Creates a Manus agent
2. Takes user input through the command line
3. Creates a PlanningFlow using the FlowFactory
4. Executes the flow with the user's prompt
5. Displays the result

Here's the key code from `run_flow.py`:

```python
async def run_flow():
    agents = {
        "manus": Manus(),
    }

    while True:
        try:
            prompt = input("Enter your prompt (or 'exit' to quit): ")
            if prompt.lower() == "exit":
                logger.info("Goodbye!")
                break

            flow = FlowFactory.create_flow(
                flow_type=FlowType.PLANNING,
                agents=agents,
            )
            if prompt.strip().isspace():
                logger.warning("Skipping empty prompt.")
                continue
            logger.warning("Processing your request...")
            result = await flow.execute(prompt)
            logger.info(result)
        except KeyboardInterrupt:
            logger.warning("Goodbye!")
            break
```

To use this entry point, users can run:

```bash
python run_flow.py
```

This will start the interactive prompt where users can enter their requests, which will be processed by the PlanningFlow.

### 2. Flow Factory Creation

PlanningFlow is created through the `FlowFactory` class in `app/flow/flow_factory.py`. This factory pattern allows for easy creation of different flow types:

```python
class FlowFactory:
    @staticmethod
    def create_flow(
        flow_type: FlowType,
        agents: Union[BaseAgent, List[BaseAgent], Dict[str, BaseAgent]],
        **kwargs,
    ) -> BaseFlow:
        flows = {
            FlowType.PLANNING: PlanningFlow,
        }

        flow_class = flows.get(flow_type)
        if not flow_class:
            raise ValueError(f"Unknown flow type: {flow_type}")

        return flow_class(agents, **kwargs)
```

Currently, `FlowType.PLANNING` is the only flow type defined in the `FlowType` enum in `app/flow/base.py`.

## How PlanningFlow Works

### 1. Execution Process

When the PlanningFlow is executed with a user prompt, it follows this process:

1. **Plan Creation**: The flow creates an initial plan based on the user's request using the PlanningTool
2. **Step Execution**: It executes each step in the plan using appropriate agents
3. **Progress Tracking**: It tracks the progress of the plan and updates step statuses
4. **Plan Finalization**: Once all steps are completed, it finalizes the plan and returns a summary

The execution process is implemented in the `execute()` method of PlanningFlow:

```python
async def execute(self, input_text: str) -> str:
    try:
        if not self.primary_agent:
            raise ValueError("No primary agent available")

        # Create initial plan if input provided
        if input_text:
            await self._create_initial_plan(input_text)

        result = ""
        while True:
            # Get current step to execute
            self.current_step_index, step_info = await self._get_current_step_info()

            # Exit if no more steps or plan completed
            if self.current_step_index is None:
                result += await self._finalize_plan()
                break

            # Execute current step with appropriate agent
            step_type = step_info.get("type") if step_info else None
            executor = self.get_executor(step_type)
            step_result = await self._execute_step(executor, step_info)
            result += step_result + "\n"

            # Check if agent wants to terminate
            if hasattr(executor, "state") and executor.state == AgentState.FINISHED:
                break

        return result
    except Exception as e:
        logger.error(f"Error in PlanningFlow: {str(e)}")
        return f"Execution failed: {str(e)}"
```

### 2. Agent Selection

PlanningFlow can work with multiple agents and selects the appropriate agent for each step based on the step type:

```python
def get_executor(self, step_type: Optional[str] = None) -> BaseAgent:
    """
    Get an appropriate executor agent for the current step.
    Can be extended to select agents based on step type/requirements.
    """
    # If step type is provided and matches an agent key, use that agent
    if step_type and step_type in self.agents:
        return self.agents[step_type]

    # Otherwise use the first available executor or fall back to primary agent
    for key in self.executor_keys:
        if key in self.agents:
            return self.agents[key]

    # Fallback to primary agent
    return self.primary_agent
```

This allows for specialized agents to handle specific types of steps. For example, if a step is tagged with `[SEARCH]`, it could be routed to a search-specialized agent.

### 3. Integration with PlanningTool

PlanningFlow works closely with the PlanningTool, which provides the actual planning capabilities:

```python
llm: LLM = Field(default_factory=lambda: LLM())
planning_tool: PlanningTool = Field(default_factory=PlanningTool)
```

The PlanningTool is used to:
- Create initial plans
- Get step information
- Update step statuses
- Finalize plans

## Practical Usage Examples

### Basic Usage

The most common way to use PlanningFlow is through the `run_flow.py` script:

```bash
python run_flow.py
```

Then enter a prompt like:

```
Create a simple web application with Flask that allows users to create, read, update, and delete notes
```

The PlanningFlow will:
1. Create a plan with steps like "Set up project structure", "Create database models", "Implement CRUD operations", etc.
2. Execute each step using the Manus agent
3. Return the results, which might include code, instructions, and explanations

### Programmatic Usage

You can also use PlanningFlow programmatically in your own code:

```python
import asyncio
from app.agent.manus import Manus
from app.flow.flow_factory import FlowFactory
from app.flow.base import FlowType

async def main():
    # Create agents
    agents = {
        "manus": Manus(),
    }
    
    # Create a planning flow
    flow = FlowFactory.create_flow(
        flow_type=FlowType.PLANNING,
        agents=agents,
    )
    
    # Execute the flow with a user request
    result = await flow.execute("Create a data visualization of COVID-19 cases by country")
    
    # Print the result
    print(result)

if __name__ == "__main__":
    asyncio.run(main())
```

### Using Multiple Agents

You can use multiple specialized agents with PlanningFlow:

```python
import asyncio
from app.agent.manus import Manus
from app.flow.flow_factory import FlowFactory
from app.flow.base import FlowType

# Create a custom agent for data analysis
from app.agent.toolcall import ToolCallAgent
from app.tool import ToolCollection
from app.tool.python_execute import PythonExecute
from app.tool.file_saver import FileSaver

class DataAnalysisAgent(ToolCallAgent):
    name: str = "data_analysis"
    description: str = "Specialized agent for data analysis"
    available_tools: ToolCollection = ToolCollection(PythonExecute(), FileSaver())

async def main():
    # Create multiple agents
    agents = {
        "manus": Manus(),
        "data_analysis": DataAnalysisAgent(),
    }
    
    # Create a planning flow with multiple agents
    flow = FlowFactory.create_flow(
        flow_type=FlowType.PLANNING,
        agents=agents,
        executor_keys=["manus", "data_analysis"],
    )
    
    # Execute the flow
    result = await flow.execute("Analyze the data in data.csv and create visualizations")
    
    # Print the result
    print(result)

if __name__ == "__main__":
    asyncio.run(main())
```

In this example, steps that require data analysis will be routed to the DataAnalysisAgent.

## Summary

PlanningFlow is a central component in OpenManus that provides planning and orchestration capabilities. It's primarily used in the alternative execution flow (`run_flow.py`) to break down complex tasks into manageable steps and execute them using appropriate agents. The flow works closely with the PlanningTool to create and manage plans, and can work with multiple agents to execute different types of steps.

This architecture allows OpenManus to handle complex tasks by breaking them down into simpler steps, making it more effective at solving a wide range of problems. 