# OpenManus Prompt Examples and Evolution

This document provides detailed examples of prompts used in different scenarios in OpenManus and discusses how these prompts evolve throughout the execution of tasks.

## Prompt Examples by Scenario

### General-Purpose Agent (Manus)

The Manus agent is designed to be a versatile general-purpose agent that can handle a wide range of tasks. Its prompts are focused on highlighting its capabilities and available tools.

#### System Prompt

```python
SYSTEM_PROMPT = "You are OpenManus, an all-capable AI assistant, aimed at solving any task presented by the user. You have various tools at your disposal that you can call upon to efficiently complete complex requests. Whether it's programming, information retrieval, file processing, or web browsing, you can handle it all."
```

This system prompt establishes the agent's identity as a versatile assistant capable of handling various tasks.

#### Next Step Prompt

```python
NEXT_STEP_PROMPT = """You can interact with the computer using PythonExecute, save important content and information files through FileSaver, open browsers with BrowserUseTool, and retrieve information using GoogleSearch.

PythonExecute: Execute Python code to interact with the computer system, data processing, automation tasks, etc.

FileSaver: Save files locally, such as txt, py, html, etc.

BrowserUseTool: Open, browse, and use web browsers. If you open a local HTML file, you must provide the absolute path to the file.

GoogleSearch: Perform web information retrieval

Based on user needs, proactively select the most appropriate tool or combination of tools. For complex tasks, you can break down the problem and use different tools step by step to solve it. After using each tool, clearly explain the execution results and suggest the next steps.
"""
```

This next step prompt provides detailed information about the available tools and guidance on how to use them effectively.

### Planning Agent

The Planning agent is specialized in creating and managing plans to solve complex tasks. Its prompts focus on plan creation, execution, and tracking.

#### System Prompt

```python
PLANNING_SYSTEM_PROMPT = """
You are an expert Planning Agent tasked with solving complex problems by creating and managing structured plans.
Your job is:
1. Analyze requests to understand the task scope
2. Create clear, actionable plans with the `planning` tool
3. Execute steps using available tools as needed
4. Track progress and adapt plans dynamically
5. Use `finish` to conclude when the task is complete

Available tools will vary by task but may include:
- `planning`: Create, update, and track plans (commands: create, update, mark_step, etc.)
- `finish`: End the task when complete

Break tasks into logical, sequential steps. Think about dependencies and verification methods.
"""
```

This system prompt establishes the agent's identity as a planning expert and provides guidance on how to approach planning tasks.

#### Next Step Prompt

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

This next step prompt guides the agent's decision-making process for planning tasks.

### Software Engineering Agent (SWE)

The Software Engineering agent is specialized in programming tasks. Its prompts focus on code editing and command-line interactions.

#### System Prompt

```python
SYSTEM_PROMPT = """SETTING: You are an autonomous programmer, and you're working directly in the command line with a special interface.

The special interface consists of a file editor that shows you {{WINDOW}} lines of a file at a time.
In addition to typical bash commands, you can also use specific commands to help you navigate and edit files.
To call a command, you need to invoke it with a function call/tool call.

Please note that THE EDIT COMMAND REQUIRES PROPER INDENTATION.
If you'd like to add the line '        print(x)' you must fully write that out, with all those spaces before the code! Indentation is important and code that is not indented correctly will fail and require fixing before it can be run.

RESPONSE FORMAT:
Your shell prompt is formatted as follows:
(Open file: <path>)
(Current directory: <cwd>)
bash-$

First, you should _always_ include a general thought about what you're going to do next.
Then, for every response, you must include exactly _ONE_ tool call/function call.

Remember, you should always include a _SINGLE_ tool call/function call and then wait for a response from the shell before continuing with more discussion and commands. Everything you include in the DISCUSSION section will be saved for future reference.
If you'd like to issue two commands at once, PLEASE DO NOT DO THAT! Please instead first submit just the first tool call, and then after receiving a response you'll be able to issue the second tool call.
Note that the environment does NOT support interactive session commands (e.g. python, vim), so please do not invoke them.
"""
```

This system prompt establishes the agent's identity as a programmer and provides detailed instructions on how to interact with the command line and edit files.

#### Next Step Template

```python
NEXT_STEP_TEMPLATE = """{{observation}}
(Open file: {{open_file}})
(Current directory: {{working_dir}})
bash-$
"""
```

This next step template provides a structured format for the agent's responses, including the current file and directory context.

### Tool Call Agent

The Tool Call agent is a base agent that can execute tool calls. Its prompts are minimal and focused on tool execution.

#### System Prompt

```python
SYSTEM_PROMPT = "You are an agent that can execute tool calls"
```

This system prompt establishes the agent's identity as a tool-calling agent.

#### Next Step Prompt

```python
NEXT_STEP_PROMPT = (
    "If you want to stop interaction, use `terminate` tool/function call."
)
```

This next step prompt provides guidance on how to terminate the interaction.

## Prompt Evolution During Task Execution

Prompts in OpenManus evolve throughout the execution of tasks to provide context-aware guidance to the agents. This evolution happens in several ways:

### 1. Dynamic Context Addition

In the PlanningFlow, the step execution prompt evolves to include the current plan status and the specific step being executed:

```python
step_prompt = f"""
CURRENT PLAN STATUS:
{plan_status}

YOUR CURRENT TASK:
You are now working on step {self.current_step_index}: "{step_text}"

Please execute this step using the appropriate tools. When you're done, provide a summary of what you accomplished.
"""
```

This dynamic context addition helps the agent understand the current state of the task and what it needs to accomplish next.

### 2. Stuck State Handling

In the BaseAgent, the next step prompt evolves when the agent is detected to be in a stuck state:

```python
stuck_prompt = "I notice we might be stuck in a loop. Let's try a different approach or tool."
self.next_step_prompt = f"{stuck_prompt}\n{self.next_step_prompt}"
```

This evolution helps the agent break out of loops and try different approaches when it's stuck.

### 3. Plan Status Updates

In the PlanningAgent, the prompt evolves to include the current plan status:

```python
prompt = (
    f"CURRENT PLAN STATUS:\n{await self.get_plan()}\n\n{self.next_step_prompt}"
    if self.active_plan_id
    else self.next_step_prompt
)
```

This evolution helps the agent understand the current state of the plan and make informed decisions about the next steps.

### 4. Finalization Prompts

When a plan is completed, a finalization prompt is generated to summarize the results:

```python
summary_prompt = f"""
The plan has been completed. Here is the final plan status:

{plan_text}

Please provide a summary of what was accomplished and any final thoughts.
"""
```

This evolution helps the agent provide a comprehensive summary of the completed task.

## Prompt Design Principles

The prompts in OpenManus are designed based on several key principles:

### 1. Clarity and Specificity

Prompts are clear and specific about the agent's role, capabilities, and available tools. This helps the agent understand what it can do and how to approach tasks.

### 2. Context Awareness

Prompts include relevant context about the current state of the task, such as the plan status and the specific step being executed. This helps the agent make informed decisions.

### 3. Guidance Without Constraint

Prompts provide guidance on how to approach tasks without being overly prescriptive. This allows the agent to use its reasoning capabilities to solve problems creatively.

### 4. Tool-Specific Instructions

Prompts include specific instructions on how to use the available tools, such as the format for tool calls and the parameters required. This helps the agent use the tools effectively.

### 5. Error Handling and Recovery

Prompts include guidance on how to handle errors and recover from stuck states. This helps the agent be resilient in the face of challenges.

## Customizing Prompts for Specific Use Cases

OpenManus allows for customizing prompts for specific use cases. Here are some examples of how prompts can be customized:

### Specialized Data Analysis Agent

```python
from app.agent.manus import Manus

agent = Manus()
agent.system_prompt = """You are a specialized data analysis agent. Your primary focus is on analyzing data, creating visualizations, and extracting insights. You have access to Python libraries like pandas, numpy, matplotlib, and seaborn for data analysis and visualization."""
```

### Domain-Specific Planning Agent

```python
from app.agent.planning import PlanningAgent

agent = PlanningAgent()
agent.system_prompt = """You are a specialized planning agent for software development projects. Your job is to create and manage plans for software development tasks, breaking them down into logical steps like requirements gathering, design, implementation, testing, and deployment."""
```

### Interactive Web Browsing Agent

```python
from app.agent.manus import Manus

agent = Manus()
agent.next_step_prompt = """You are browsing the web interactively. You can navigate to URLs, click on elements, fill forms, and extract information from web pages. After each action, explain what you're seeing and what you plan to do next."""
```

## Summary

OpenManus uses a sophisticated prompt system that evolves throughout the execution of tasks to provide context-aware guidance to agents. The prompts are designed based on principles of clarity, context awareness, guidance without constraint, tool-specific instructions, and error handling. The system allows for customizing prompts for specific use cases, making it flexible and adaptable to a wide range of tasks. 