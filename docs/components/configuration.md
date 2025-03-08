# OpenManus Configuration System

This document provides detailed information about the configuration system in OpenManus, which allows users to customize the behavior of the agent and its components.

## Overview

OpenManus uses a configuration system based on TOML files to manage settings for various components, including:

- LLM settings (model, API key, etc.)
- Agent settings (max steps, etc.)
- Tool settings (timeouts, etc.)

The configuration system is designed to be flexible and extensible, allowing users to customize the behavior of OpenManus to suit their needs.

## Configuration File

The main configuration file is `config/config.toml`, which is created by copying the example configuration file `config/config.example.toml`:

```bash
cp config/config.example.toml config/config.toml
```

The configuration file uses the TOML format, which is a simple and readable configuration format. Here's an example of the configuration file:

```toml
# Global LLM configuration
[llm]
model = "gpt-4o"
base_url = "https://api.openai.com/v1"
api_key = "sk-..."  # Replace with your actual API key
max_tokens = 4096
temperature = 0.0

# Optional configuration for specific LLM models
[llm.vision]
model = "gpt-4o"
base_url = "https://api.openai.com/v1"
api_key = "sk-..."  # Replace with your actual API key
```

## Configuration Structure

The configuration file is structured into sections, each corresponding to a specific component or feature:

### LLM Configuration

The `[llm]` section contains settings for the language model:

- `model`: The name of the model to use (e.g., "gpt-4o")
- `base_url`: The base URL for the API (e.g., "https://api.openai.com/v1")
- `api_key`: The API key for authentication
- `max_tokens`: The maximum number of tokens to generate
- `temperature`: The temperature for generation (0.0 to 1.0)
- `api_type`: The type of API to use (e.g., "openai", "azure")
- `api_version`: The API version to use (for Azure)

You can also define specific configurations for different LLM instances by using subsections:

```toml
[llm.vision]
model = "gpt-4o"
base_url = "https://api.openai.com/v1"
api_key = "sk-..."
```

### Agent Configuration

The `[agent]` section contains settings for the agent:

- `max_steps`: The maximum number of steps the agent can take
- `system_prompt`: The system prompt for the agent
- `next_step_prompt`: The prompt for determining the next step

### Tool Configuration

The `[tool]` section contains settings for the tools:

- `timeout`: The timeout for tool execution
- `max_output_size`: The maximum size of tool output

You can also define specific configurations for different tools by using subsections:

```toml
[tool.browser]
headless = true
timeout = 30000
```

## Configuration Loading

The configuration is loaded by the `Config` class in `app/config.py`. This class provides a unified interface for accessing configuration values:

```python
from app.config import config

# Access LLM configuration
model = config.llm["default"].model
api_key = config.llm["default"].api_key

# Access agent configuration
max_steps = config.agent.max_steps

# Access tool configuration
timeout = config.tool.timeout
```

The `Config` class also supports environment variables as fallbacks for configuration values. For example, if the `OPENAI_API_KEY` environment variable is set, it will be used if the `api_key` is not specified in the configuration file.

## Configuration Precedence

The configuration system follows this precedence order:

1. Configuration file values
2. Environment variables
3. Default values

This means that values specified in the configuration file take precedence over environment variables, which in turn take precedence over default values.

## Environment Variables

The following environment variables are supported:

- `OPENAI_API_KEY`: The OpenAI API key
- `OPENAI_BASE_URL`: The base URL for the OpenAI API
- `OPENAI_MODEL`: The OpenAI model to use
- `AZURE_API_KEY`: The Azure API key
- `AZURE_BASE_URL`: The base URL for the Azure API
- `AZURE_API_VERSION`: The Azure API version

## Customizing Configuration

You can customize the configuration by editing the `config/config.toml` file or by setting environment variables. Here are some examples:

### Using a Different LLM Provider

To use a different LLM provider, update the configuration:

```toml
[llm]
model = "claude-3-opus-20240229"
base_url = "https://api.anthropic.com/v1"
api_key = "sk-ant-..."
api_type = "anthropic"
```

### Customizing Agent Behavior

To customize the agent behavior, update the configuration:

```toml
[agent]
max_steps = 20
system_prompt = "You are a specialized agent focused on scientific research..."
```

### Customizing Tool Behavior

To customize the tool behavior, update the configuration:

```toml
[tool.browser]
headless = false
timeout = 60000
```

## Programmatic Configuration

You can also configure OpenManus programmatically by modifying the configuration at runtime:

```python
from app.config import config

# Update LLM configuration
config.llm["default"].model = "gpt-4o"
config.llm["default"].api_key = "sk-..."

# Update agent configuration
config.agent.max_steps = 20

# Update tool configuration
config.tool.timeout = 60
```

This is useful for dynamically adjusting the configuration based on runtime conditions or user preferences.

## Configuration Validation

The configuration system validates the configuration values to ensure they are valid and consistent. If a required value is missing or invalid, an error will be raised.

For example, if the `api_key` is not specified and the corresponding environment variable is not set, an error will be raised when attempting to use the LLM.

## Best Practices

When working with the configuration system, follow these best practices:

- **Keep sensitive information secure**: Do not commit API keys or other sensitive information to version control. Use environment variables or a secure configuration file that is not tracked by version control.
- **Use specific configurations**: Use specific configurations for different components to avoid conflicts and make the configuration more maintainable.
- **Document custom configurations**: Document any custom configurations you create to make it easier for others to understand and use them.
- **Validate configuration values**: Validate configuration values to ensure they are valid and consistent, especially when accepting user input. 