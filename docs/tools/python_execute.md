# Python Execute Tool in OpenManus

This document provides detailed information about the Python execution tool in OpenManus, which enables agents to execute Python code.

## Overview

The Python execution tool in OpenManus is implemented as `PythonExecute` and allows agents to execute arbitrary Python code. This is a powerful capability that enables agents to:

- Perform data processing and analysis
- Create and manipulate files
- Make API calls
- Generate visualizations
- And much more

This tool is particularly useful for tasks that require data processing, calculations, or custom logic that can be expressed in Python.

## Implementation

The Python execution tool is implemented in `app/tool/python_execute.py` and provides a safe and controlled environment for executing Python code.

The tool is integrated into the Manus agent by default, making it available for use in any task that requires Python code execution.

## Capabilities

The Python execution tool provides the following capabilities:

- **Execute Python code**: Execute arbitrary Python code and return the result
- **Access to standard libraries**: Access to Python's standard libraries
- **Access to installed packages**: Access to packages installed in the environment
- **Output capture**: Capture and return the output of the executed code
- **Error handling**: Handle and report errors in the executed code

## Usage

The Python execution tool can be used by the agent to perform a wide range of tasks. Here's an example of how it might be used:

```
Enter your prompt: Analyze the data in data.csv and create a bar chart showing the distribution of values
```

The agent will:
1. Use the Python execution tool to read the CSV file
2. Process the data using pandas
3. Create a bar chart using matplotlib
4. Save the chart to a file
5. Return the results

## Example Code

Here's an example of how the Python execution tool is used in the code:

```python
from app.tool.python_execute import PythonExecute

# Create the Python execution tool
python_tool = PythonExecute()

# Execute a simple calculation
result = await python_tool.execute(
    code="2 + 2"
)
# result.output will be "4"

# Execute more complex code
result = await python_tool.execute(
    code="""
import pandas as pd
import matplotlib.pyplot as plt

# Read the data
data = pd.read_csv('data.csv')

# Create a bar chart
plt.figure(figsize=(10, 6))
data['column'].value_counts().plot(kind='bar')
plt.title('Distribution of Values')
plt.xlabel('Value')
plt.ylabel('Count')

# Save the chart
plt.savefig('chart.png')

# Return a summary
summary = data.describe().to_string()
summary
"""
)
```

## Integration with Other Tools

The Python execution tool can be used in conjunction with other tools to perform complex tasks. For example:

- **Browser Tool**: Use the browser tool to extract data from web pages, then use the Python execution tool to process and analyze that data.
- **File Saver Tool**: Use the Python execution tool to generate content, then use the File Saver tool to save that content to a file.
- **Google Search Tool**: Use the Google Search tool to find information, then use the Python execution tool to process and analyze that information.

## Security Considerations

The Python execution tool executes arbitrary Python code, which can pose security risks. Here are some security considerations:

- **Sandbox environment**: The tool should be run in a sandboxed environment to prevent malicious code from affecting the host system.
- **Resource limits**: The tool should impose limits on CPU, memory, and execution time to prevent resource exhaustion.
- **Access controls**: The tool should restrict access to sensitive files and system resources.

## Advanced Usage

For advanced usage, you can customize the Python execution tool by extending the `PythonExecute` class or by modifying its parameters. For example:

```python
from app.tool.python_execute import PythonExecute

# Create a custom Python execution tool with specific options
python_tool = PythonExecute(
    timeout=60,  # Set a longer timeout
    max_output_size=10000,  # Set a larger output size limit
    allowed_modules=["pandas", "numpy", "matplotlib"]  # Restrict allowed modules
)
```

## Best Practices

When using the Python execution tool, follow these best practices:

- **Keep code simple**: Write simple, focused code that accomplishes a specific task.
- **Handle errors**: Include error handling in your code to gracefully handle unexpected situations.
- **Limit resource usage**: Be mindful of resource usage, especially when processing large datasets.
- **Document your code**: Include comments to explain what the code is doing.
- **Use standard libraries**: Prefer standard libraries and well-known packages over custom or obscure ones.

## Example Use Cases

Here are some example use cases for the Python execution tool:

### Data Analysis

```python
import pandas as pd

# Read the data
data = pd.read_csv('data.csv')

# Perform analysis
summary = data.describe()
correlations = data.corr()

# Return results
f"Summary:\n{summary}\n\nCorrelations:\n{correlations}"
```

### Web Scraping

```python
import requests
from bs4 import BeautifulSoup

# Fetch the page
response = requests.get('https://example.com')
soup = BeautifulSoup(response.text, 'html.parser')

# Extract information
title = soup.title.text
headings = [h.text for h in soup.find_all('h1')]

# Return results
f"Title: {title}\n\nHeadings: {headings}"
```

### File Operations

```python
import os
import json

# Create a directory
os.makedirs('output', exist_ok=True)

# Create a file
data = {'key': 'value'}
with open('output/data.json', 'w') as f:
    json.dump(data, f, indent=2)

# Return results
"File created successfully"
```

### Visualization

```python
import matplotlib.pyplot as plt
import numpy as np

# Generate data
x = np.linspace(0, 10, 100)
y = np.sin(x)

# Create a plot
plt.figure(figsize=(10, 6))
plt.plot(x, y)
plt.title('Sine Wave')
plt.xlabel('x')
plt.ylabel('sin(x)')

# Save the plot
plt.savefig('sine_wave.png')

# Return results
"Plot saved to sine_wave.png"
``` 