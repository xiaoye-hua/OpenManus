# Browser Tool in OpenManus

This document provides detailed information about the browser tool in OpenManus, which enables agents to browse the web and interact with web pages.

## Overview

The browser tool in OpenManus is implemented as `BrowserUseTool` and is based on the `browser-use` library. It provides a wide range of capabilities for web browsing and interaction, including:

- Navigating to URLs
- Clicking on elements
- Filling forms
- Extracting information from web pages
- Taking screenshots
- And more

This tool is particularly useful for tasks that require web research, data extraction, or web automation.

## Implementation

The browser tool is implemented in `app/tool/browser_use_tool.py` and is based on the `browser-use` library, which provides a high-level API for browser automation.

The tool is integrated into the Manus agent by default, making it available for use in any task that requires web browsing.

## Capabilities

The browser tool provides the following capabilities:

### Navigation

- **Navigate to URL**: Navigate to a specific URL
- **Go back**: Go back to the previous page
- **Go forward**: Go forward to the next page
- **Refresh**: Refresh the current page

### Interaction

- **Click**: Click on an element
- **Type**: Type text into an input field
- **Select**: Select an option from a dropdown
- **Check/Uncheck**: Check or uncheck a checkbox
- **Submit**: Submit a form

### Extraction

- **Extract text**: Extract text from the page
- **Extract links**: Extract links from the page
- **Extract images**: Extract images from the page
- **Extract tables**: Extract tables from the page

### Other

- **Take screenshot**: Take a screenshot of the page
- **Execute JavaScript**: Execute JavaScript on the page
- **Wait for element**: Wait for an element to appear
- **Wait for navigation**: Wait for navigation to complete

## Usage

The browser tool can be used by the agent to perform web-related tasks. Here's an example of how it might be used:

```
Enter your prompt: Research the latest news about artificial intelligence and summarize the findings
```

The agent will:
1. Use the browser tool to navigate to a search engine
2. Search for "latest news about artificial intelligence"
3. Visit relevant news articles
4. Extract information from the articles
5. Summarize the findings

## Example Code

Here's an example of how the browser tool is used in the code:

```python
from app.tool.browser_use_tool import BrowserUseTool

# Create the browser tool
browser_tool = BrowserUseTool()

# Navigate to a URL
result = await browser_tool.execute(
    action="navigate",
    url="https://www.example.com"
)

# Click on a button
result = await browser_tool.execute(
    action="click",
    selector="button.submit"
)

# Extract text from the page
result = await browser_tool.execute(
    action="extract_text",
    selector="div.content"
)

# Take a screenshot
result = await browser_tool.execute(
    action="screenshot"
)
```

## Integration with Other Tools

The browser tool can be used in conjunction with other tools to perform complex tasks. For example:

- **Google Search Tool**: Use the Google Search tool to find relevant URLs, then use the browser tool to visit those URLs and extract information.
- **File Saver Tool**: Use the browser tool to extract information from web pages, then use the File Saver tool to save that information to a file.
- **Python Execute Tool**: Use the browser tool to extract data from web pages, then use the Python Execute tool to process and analyze that data.

## Limitations

The browser tool has some limitations:

- **JavaScript-heavy websites**: Some websites that rely heavily on JavaScript may not work correctly with the browser tool.
- **CAPTCHA and anti-bot measures**: Websites with CAPTCHA or other anti-bot measures may block the browser tool.
- **Authentication**: Some websites may require authentication, which can be challenging to handle with the browser tool.

## Advanced Usage

For advanced usage, you can customize the browser tool by extending the `BrowserUseTool` class or by modifying its parameters. For example:

```python
from app.tool.browser_use_tool import BrowserUseTool

# Create a custom browser tool with specific options
browser_tool = BrowserUseTool(
    headless=False,  # Show the browser window
    timeout=60000,   # Set a longer timeout
    user_agent="Custom User Agent"  # Set a custom user agent
)
```

## Troubleshooting

If you encounter issues with the browser tool, try the following:

- **Check the URL**: Make sure the URL is valid and accessible.
- **Check the selectors**: Make sure the selectors are correct and unique.
- **Increase the timeout**: Some websites may take longer to load, so try increasing the timeout.
- **Disable headless mode**: Try disabling headless mode to see what's happening in the browser.
- **Check for CAPTCHA**: Some websites may show CAPTCHA challenges, which can block the browser tool. 