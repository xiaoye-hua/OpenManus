# OpenManus Components

This document provides an overview of the core components of OpenManus.

## Components Overview

OpenManus consists of several key components:

- **Agents**: The core entities that process user requests and execute actions
- **Flows**: Orchestration mechanisms that coordinate the execution of agents
- **Tools**: Specialized modules that agents can use to perform specific tasks
- **LLM Integration**: Integration with language models for natural language understanding and generation
- **Configuration**: System for managing settings and customization
- **Prompt System**: System for guiding agent behavior through carefully crafted prompts

## Documentation

### Planning System

The [Planning System](./planning.md) document provides detailed information about the planning system in OpenManus, which is a key feature that enables the agent to break down complex tasks into manageable steps.

### PlanningFlow Usage

The [PlanningFlow Usage](./planningflow_usage.md) document provides detailed information about where and how the PlanningFlow component is used in the OpenManus project, including practical usage examples.

### Prompt System

The [Prompt System](./prompt_system.md) document provides detailed information about the prompt system in OpenManus, which is a key component that enables agents to understand and execute tasks effectively.

### Prompt Examples and Evolution

The [Prompt Examples and Evolution](./prompt_examples.md) document provides detailed examples of prompts used in different scenarios in OpenManus and discusses how these prompts evolve throughout the execution of tasks.

### Configuration System

The [Configuration System](./configuration.md) document provides detailed information about the configuration system in OpenManus, which allows users to customize the behavior of the agent and its components.

## Key Components

### Agents

Agents are the core entities in OpenManus that process user requests and execute actions. The agent architecture is based on a hierarchical inheritance structure:

- `BaseAgent`: The abstract base class that provides foundational functionality
- `ReActAgent`: Extends BaseAgent with reasoning and acting capabilities
- `ToolCallAgent`: Extends ReActAgent with tool/function calling capabilities
- `Manus`: The main agent implementation that combines all capabilities

### Flows

Flows orchestrate the execution of agents, coordinating their actions to achieve complex tasks. The main flow implementation is:

- `PlanningFlow`: Manages planning and execution of tasks using agents

### Tools

Tools are specialized modules that agents can use to perform specific tasks. Each tool has a defined interface and can be easily integrated into the agent system. See the [Tools](../tools/README.md) documentation for more details.

### LLM Integration

OpenManus integrates with language models (LLMs) to provide natural language understanding and generation capabilities. The LLM integration is abstracted through the `LLM` class, which provides a unified interface for interacting with different language model providers.

### Configuration

OpenManus uses a configuration system to manage settings for LLMs and other components. See the [Configuration System](./configuration.md) documentation for more details.

### Prompt System

OpenManus uses a prompt system to guide agent behavior through carefully crafted prompts. The prompt system consists of system prompts that define the agent's identity and capabilities, and next step prompts that guide the agent's decision-making process. See the [Prompt System](./prompt_system.md) and [Prompt Examples and Evolution](./prompt_examples.md) documentation for more details. 