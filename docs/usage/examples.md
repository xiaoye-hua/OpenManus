# OpenManus Examples

This document provides detailed information about the examples included in OpenManus, which demonstrate its capabilities and how to use it for various tasks.

## Overview

OpenManus includes several examples that demonstrate its capabilities and how to use it for various tasks. These examples are located in the `examples` directory and cover a range of use cases, from travel planning to data analysis.

## Japan Travel Plan Example

The Japan travel plan example demonstrates how OpenManus can be used for travel planning. It shows how the agent can:

1. Research destinations in Japan
2. Find accommodations
3. Suggest activities
4. Plan transportation
5. Create a detailed itinerary

### Running the Example

To run the Japan travel plan example:

```bash
cd examples/japan-travel-plan
python run.py
```

This will start the OpenManus agent with a prompt to plan a trip to Japan.

### Example Output

The example output includes:

- A detailed itinerary for a trip to Japan
- Recommendations for accommodations
- Suggestions for activities and attractions
- Transportation options
- Estimated costs

### Key Features Demonstrated

This example demonstrates several key features of OpenManus:

- **Web Research**: The agent uses web browsing and Google search to find information about Japan
- **Planning**: The agent breaks down the travel planning task into manageable steps
- **Information Synthesis**: The agent combines information from multiple sources to create a comprehensive plan
- **Output Formatting**: The agent formats the output in a clear and organized way

## Other Examples

OpenManus includes other examples that demonstrate its capabilities:

### Data Analysis Example

This example demonstrates how OpenManus can be used for data analysis tasks, such as:

- Reading and processing data from CSV files
- Creating visualizations
- Extracting insights
- Generating reports

### Web Scraping Example

This example demonstrates how OpenManus can be used for web scraping tasks, such as:

- Navigating to websites
- Extracting information from web pages
- Processing and analyzing the extracted data
- Saving the results to files

### Code Generation Example

This example demonstrates how OpenManus can be used for code generation tasks, such as:

- Creating a simple web application
- Generating database schemas
- Writing API endpoints
- Creating user interfaces

## Creating Your Own Examples

You can create your own examples to demonstrate specific use cases or capabilities of OpenManus. To create a new example:

1. Create a new directory in the `examples` directory
2. Create a `run.py` file that sets up and runs the OpenManus agent
3. Add any additional files or resources needed for the example
4. Document the example in a `README.md` file

Here's a template for a new example:

```python
# examples/my-example/run.py
import asyncio

from app.agent.manus import Manus
from app.logger import logger

async def main():
    # Create the agent
    agent = Manus()
    
    # Define the prompt
    prompt = """
    This is a prompt for my custom example. It should describe the task in detail,
    including any specific requirements or constraints.
    """
    
    # Run the agent
    logger.info("Running my custom example...")
    await agent.run(prompt)

if __name__ == "__main__":
    asyncio.run(main())
```

## Best Practices for Examples

When creating examples, follow these best practices:

- **Clear Purpose**: Each example should have a clear purpose and demonstrate a specific capability
- **Self-Contained**: Examples should be self-contained and not depend on external resources
- **Well-Documented**: Examples should be well-documented with clear instructions
- **Realistic**: Examples should be realistic and demonstrate practical use cases
- **Reproducible**: Examples should be reproducible and produce consistent results

## Using Examples as Templates

You can use the included examples as templates for your own projects. For example, you can:

1. Copy an example directory to a new location
2. Modify the `run.py` file to suit your needs
3. Add or remove tools as needed
4. Customize the prompts and other parameters

This allows you to quickly get started with OpenManus for your specific use case.

## Example: Creating a Custom Example

Here's an example of creating a custom example for a recipe generation task:

```python
# examples/recipe-generator/run.py
import asyncio

from app.agent.manus import Manus
from app.logger import logger

async def main():
    # Create the agent
    agent = Manus()
    
    # Define the prompt
    prompt = """
    Generate a recipe for a vegetarian dinner that:
    1. Uses ingredients commonly found in most kitchens
    2. Can be prepared in under 30 minutes
    3. Is nutritionally balanced
    4. Serves 4 people
    
    Include:
    - A list of ingredients with quantities
    - Step-by-step preparation instructions
    - Nutritional information
    - Serving suggestions
    """
    
    # Run the agent
    logger.info("Generating recipe...")
    await agent.run(prompt)

if __name__ == "__main__":
    asyncio.run(main())
```

This example demonstrates how to use OpenManus for a specific task (recipe generation) with a detailed prompt that specifies the requirements. 