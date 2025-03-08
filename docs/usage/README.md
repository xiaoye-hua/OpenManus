# OpenManus Usage Guide

This document provides detailed information about how to use OpenManus, including installation, configuration, and examples.

## Installation

### Prerequisites

- Python 3.12 or higher
- pip or conda for package management

### Steps

1. Create a new conda environment:

```bash
conda create -n open_manus python=3.12
conda activate open_manus
```

2. Clone the repository:

```bash
git clone https://github.com/mannaandpoem/OpenManus.git
cd OpenManus
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

## Configuration

OpenManus requires configuration for the LLM APIs it uses. Follow these steps to set up your configuration:

1. Create a `config.toml` file in the `config` directory (you can copy from the example):

```bash
cp config/config.example.toml config/config.toml
```

2. Edit `config/config.toml` to add your API keys and customize settings:

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

## Running OpenManus

### Basic Usage

To run OpenManus with the default configuration:

```bash
python main.py
```

This will start the OpenManus agent in interactive mode, where you can enter prompts and receive responses.

### Alternative Flow

For an alternative execution flow that uses the planning system:

```bash
python run_flow.py
```

## Example Use Cases

OpenManus can handle a wide range of tasks. Here are some examples:

### Web Research

```
Enter your prompt: Research the latest developments in quantum computing and summarize the findings
```

OpenManus will:
1. Use Google Search to find relevant information
2. Browse the web pages to gather details
3. Summarize the findings

### Code Generation

```
Enter your prompt: Create a simple Flask web application that displays a form and stores the submitted data in a SQLite database
```

OpenManus will:
1. Plan the application structure
2. Generate the necessary code files
3. Set up the database
4. Provide instructions for running the application

### Data Analysis

```
Enter your prompt: Analyze the data in my CSV file data.csv and create visualizations to show the trends
```

OpenManus will:
1. Read the CSV file
2. Analyze the data using Python
3. Generate visualizations
4. Save the results

### Travel Planning

```
Enter your prompt: Plan a 5-day trip to Japan, including accommodations, activities, and transportation
```

OpenManus will:
1. Research destinations in Japan
2. Find accommodations
3. Suggest activities
4. Plan transportation
5. Create a detailed itinerary

## Advanced Usage

### Adding Custom Tools

You can extend OpenManus with custom tools to add new capabilities. See the [Tools documentation](../tools/README.md) for details.

### Customizing Agent Behavior

You can customize the behavior of the Manus agent by modifying the system prompt and other parameters. For example:

```python
from app.agent.manus import Manus

# Create a custom agent
agent = Manus()
agent.system_prompt = "You are a specialized agent focused on scientific research..."
agent.max_steps = 20  # Increase the maximum number of steps

# Run the agent
await agent.run("Research the latest developments in CRISPR technology")
```

### Using Different LLM Providers

OpenManus supports different LLM providers through the configuration system. To use a different provider, update the configuration:

```toml
[llm]
model = "claude-3-opus-20240229"
base_url = "https://api.anthropic.com/v1"
api_key = "sk-ant-..."
api_type = "anthropic"
```

## Troubleshooting

### Common Issues

- **API Key Issues**: Ensure that your API key is correctly set in the configuration file
- **Dependency Issues**: Make sure all dependencies are installed correctly
- **Tool Execution Errors**: Check the error messages for details on what went wrong

### Logging

OpenManus uses the loguru library for logging. You can adjust the log level in `app/logger.py` to get more detailed information:

```python
logger.remove()
logger.add(sys.stderr, level="DEBUG")  # Change to DEBUG for more detailed logs
``` 