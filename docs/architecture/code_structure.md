# OpenManus Code Structure

This document provides an overview of the OpenManus codebase structure, explaining the purpose of each directory and key files.

## Directory Structure

```
OpenManus/
├── app/                    # Core application code
│   ├── agent/              # Agent implementations
│   ├── flow/               # Flow implementations
│   ├── prompt/             # Prompt templates
│   ├── tool/               # Tool implementations
│   ├── __init__.py         # Package initialization
│   ├── config.py           # Configuration management
│   ├── exceptions.py       # Custom exceptions
│   ├── llm.py              # LLM integration
│   ├── logger.py           # Logging configuration
│   └── schema.py           # Data structures
├── assets/                 # Static assets
├── config/                 # Configuration files
│   └── config.example.toml # Example configuration
├── docs/                   # Documentation
├── examples/               # Example use cases
│   ├── japan-travel-plan/  # Japan travel planning example
│   └── pictures/           # Example images
├── .git/                   # Git repository
├── .github/                # GitHub configuration
├── .gitignore              # Git ignore file
├── .gitattributes          # Git attributes file
├── .pre-commit-config.yaml # Pre-commit hooks configuration
├── LICENSE                 # License file
├── README.md               # English README
├── README_zh.md            # Chinese README
├── main.py                 # Main entry point
├── run_flow.py             # Alternative entry point using flows
├── requirements.txt        # Python dependencies
└── setup.py                # Package setup script
```

## Key Files

### Entry Points

- `main.py`: The main entry point for the application, which creates a Manus agent and runs it in interactive mode.
- `run_flow.py`: An alternative entry point that uses the PlanningFlow to orchestrate execution.

### Configuration

- `config/config.example.toml`: Example configuration file that shows how to configure the LLM and other components.
- `app/config.py`: Configuration management code that loads and processes configuration from TOML files.

### Core Components

#### Agents

- `app/agent/base.py`: Base agent implementation that provides foundational functionality.
- `app/agent/react.py`: ReAct agent implementation that adds reasoning and acting capabilities.
- `app/agent/toolcall.py`: ToolCall agent implementation that adds tool/function calling capabilities.
- `app/agent/manus.py`: Manus agent implementation that combines all capabilities.
- `app/agent/planning.py`: Planning agent implementation that adds planning capabilities.
- `app/agent/swe.py`: Software engineering agent implementation.

#### Flows

- `app/flow/base.py`: Base flow implementation that provides foundational functionality.
- `app/flow/planning.py`: Planning flow implementation that manages planning and execution.
- `app/flow/flow_factory.py`: Factory for creating flows.

#### Tools

- `app/tool/base.py`: Base tool implementation that provides foundational functionality.
- `app/tool/tool_collection.py`: Tool collection implementation that manages multiple tools.
- `app/tool/bash.py`: Bash tool implementation for executing shell commands.
- `app/tool/browser_use_tool.py`: Browser tool implementation for web browsing.
- `app/tool/create_chat_completion.py`: Tool for creating chat completions.
- `app/tool/file_saver.py`: Tool for saving files.
- `app/tool/google_search.py`: Tool for searching the web using Google.
- `app/tool/planning.py`: Tool for planning.
- `app/tool/python_execute.py`: Tool for executing Python code.
- `app/tool/run.py`: Tool for running commands.
- `app/tool/str_replace_editor.py`: Tool for editing text.
- `app/tool/terminate.py`: Tool for terminating execution.

#### Prompts

- `app/prompt/manus.py`: Prompt templates for the Manus agent.
- `app/prompt/planning.py`: Prompt templates for planning.
- `app/prompt/swe.py`: Prompt templates for software engineering.
- `app/prompt/toolcall.py`: Prompt templates for tool calls.

#### Other

- `app/llm.py`: LLM integration code that provides a unified interface for different LLM providers.
- `app/schema.py`: Data structures used throughout the application.
- `app/logger.py`: Logging configuration.
- `app/exceptions.py`: Custom exceptions.

## Package Structure

The OpenManus package is structured as follows:

- `app`: The main package that contains all the code.
  - `agent`: Subpackage for agent implementations.
  - `flow`: Subpackage for flow implementations.
  - `prompt`: Subpackage for prompt templates.
  - `tool`: Subpackage for tool implementations.

This structure allows for easy extension and customization of the different components. 