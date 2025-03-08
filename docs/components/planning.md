# OpenManus Planning System

This document provides detailed information about the planning system in OpenManus, which is a key feature that enables the agent to break down complex tasks into manageable steps.

## Overview

The planning system in OpenManus consists of several components:

1. **PlanningFlow**: Orchestrates the planning and execution process
2. **PlanningTool**: Provides planning capabilities to agents
3. **Planning Agent**: Specialized agent for planning tasks

Together, these components enable OpenManus to:

1. Create an initial plan based on the user request
2. Break down the plan into manageable steps
3. Execute each step using appropriate agents
4. Track progress and update the plan as needed

## Planning Flow

The `PlanningFlow` class is responsible for orchestrating the planning and execution process. It:

1. Creates an initial plan using the planning tool
2. Breaks down the plan into steps
3. Executes each step using appropriate agents
4. Tracks progress and updates the plan as needed

Key methods:
- `execute()`: Main entry point for flow execution
- `_create_initial_plan()`: Creates the initial plan
- `_execute_step()`: Executes a specific step
- `_finalize_plan()`: Finalizes the plan and returns results

## Planning Tool

The `PlanningTool` class provides planning capabilities to agents. It:

1. Creates plans based on user requests
2. Breaks down plans into steps
3. Provides step-by-step guidance

Key methods:
- `create_plan()`: Creates a plan based on a user request
- `get_next_step()`: Gets the next step in the plan
- `update_plan()`: Updates the plan based on execution results

## Plan Structure

A plan in OpenManus consists of:

1. **Goal**: The overall goal of the plan
2. **Steps**: The individual steps to achieve the goal
3. **Dependencies**: The dependencies between steps
4. **Status**: The status of each step (pending, in-progress, completed, failed)

Example plan structure:

```json
{
  "plan_id": "plan_1234567890",
  "goal": "Create a simple web application",
  "steps": [
    {
      "step_id": "step_1",
      "description": "Set up the project structure",
      "status": "completed",
      "dependencies": []
    },
    {
      "step_id": "step_2",
      "description": "Create the backend API",
      "status": "in-progress",
      "dependencies": ["step_1"]
    },
    {
      "step_id": "step_3",
      "description": "Create the frontend UI",
      "status": "pending",
      "dependencies": ["step_1"]
    },
    {
      "step_id": "step_4",
      "description": "Connect the frontend to the backend",
      "status": "pending",
      "dependencies": ["step_2", "step_3"]
    }
  ]
}
```

## Planning Process

The planning process in OpenManus follows these steps:

1. **Plan Creation**: The user request is analyzed to create an initial plan
2. **Step Execution**: Each step in the plan is executed in order, respecting dependencies
3. **Plan Update**: The plan is updated based on the results of step execution
4. **Plan Finalization**: The plan is finalized and results are returned to the user

### Plan Creation

The plan creation process involves:

1. Analyzing the user request to understand the goal
2. Breaking down the goal into manageable steps
3. Identifying dependencies between steps
4. Creating an initial plan structure

### Step Execution

The step execution process involves:

1. Selecting the next step to execute based on dependencies
2. Selecting an appropriate agent to execute the step
3. Executing the step using the selected agent
4. Updating the plan based on the results

### Plan Update

The plan update process involves:

1. Updating the status of the executed step
2. Adding new steps if needed
3. Modifying existing steps if needed
4. Updating dependencies if needed

### Plan Finalization

The plan finalization process involves:

1. Ensuring all steps are completed
2. Collecting results from all steps
3. Creating a final summary
4. Returning the results to the user

## Example Usage

Here's an example of how the planning system is used in OpenManus:

```python
from app.flow.flow_factory import FlowFactory
from app.flow.base import FlowType
from app.agent.manus import Manus

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
result = await flow.execute("Create a simple web application with Flask and React")

# Print the result
print(result)
```

This will:
1. Create a plan for building a web application
2. Break down the plan into steps (setup, backend, frontend, etc.)
3. Execute each step using the Manus agent
4. Return the results to the user

## Customizing the Planning System

You can customize the planning system by:

1. Modifying the planning prompts in `app/prompt/planning.py`
2. Extending the `PlanningTool` class to add new planning capabilities
3. Extending the `PlanningFlow` class to modify the planning process

For example, to modify the planning prompts:

```python
from app.prompt.planning import PLANNING_PROMPT

# Modify the planning prompt
PLANNING_PROMPT = """
You are a planning agent. Your task is to create a detailed plan for the following request:

{request}

The plan should include:
1. A clear goal
2. Detailed steps to achieve the goal
3. Dependencies between steps
4. Estimated time for each step

Please provide the plan in JSON format.
"""
``` 