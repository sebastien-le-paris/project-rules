# project-rules
Understanding Rules for AI

References:
https://docs.cursor.com/context/rules-for-ai
https://cursor.directory/
https://cursor.directory/rules

We have Project Rules and Global Rules.

Project Rules are specific to the project.
Global Rules are general rules that apply to all projects.

# Project Rules
Project rules offer a powerful and flexible system with path specific configurations. Project rules are stored in the .cursor/rules directory and provide granular control over AI behavior in different parts of your project.

Here’s how they work
+ Semantic Descriptions: Each rule can include a description of when it should be applied
+ File Pattern Matching: Use glob patterns to specify which files/folders the rule applies to
+ Automatic Attachment: Rules can be automatically included when matching files are referenced
+ Reference files: Use @file in your project rules to include them as context when the rule is applied.


# Global Rules  

Global rules can be added by modifying the Rules for AI section under Cursor Settings > General > Rules for AI. This is useful if you want to specify rules that should always be included in every project like output language, length of responses etc.


Note that: For backward compatibility, you can still use a .cursorrules file in the root of your project. We will eventually remove .cursorrules in the future, so we recommend migrating to the new Project Rules system for better flexibility and control.

# Model Context Protocol

Connect external tools and data sources to Cursor using the Model Context Protocol (MCP) plugin system

https://docs.cursor.com/context/model-context-protocol

## What is MCP?

The Model Context Protocol (MCP) is an open protocol that standardizes how applications provide context and tools to LLMs. Think of MCP as a plugin system for Cursor - it allows you to extend the Agent’s capabilities by connecting it to various data sources and tools through standardized interfaces.

## How does MCP work? and uses

MCP allows you to connect Cursor to external systems and data sources. This means you can integrate Cursor with your existing tools and infrastructure, instead of having to tell Cursor what the structure of your project is outside of the code itself.

MCP servers can be written in any language that can print to stdout or serve an HTTP endpoint. This flexibility allows you to implement MCP servers using your preferred programming language and technology stack very quickly.

## Architecture

MCP servers are lightweight programs that expose specific capabilities through the standardized protocol. They act as intermediaries between Cursor and external tools or data sources.

Cursor supports two transport types for MCP servers:

### SSE Transport

- Can run locally or remotely

- Managed and run by you

- Communicates over the network

- Can be shared across machines

Input: URL to the /sse endpoint of an MCP server external to Cursor

### stdio Transport

- Runs on your local machine

- Managed automatically by Cursor

- Communicates directly via stdout

- Only accessible by you locally

Input: Valid shell command that is ran by Cursor automatically

## Configuring MCP Servers

The MCP configuration file uses a JSON format with the following structure:

```json
// This example demonstrated an MCP server using the stdio format
// Cursor automatically runs this process for you
// This uses a Python server, ran with `python`
{
  "mcpServers": {
    "server-name": {
      "command": "python",
      "args": ["mcp-server.py"],
      "env": {
        "API_KEY": "value"
      }
    }
  }
}

## Configuration Locations

You can place this configuration in two locations, depending on your use case:

### Global Configuration

For tools that you want to use across all projects, create a \~/.cursor/mcp.json file in your home directory. This makes MCP servers available in all your Cursor workspaces.

### Project Configuration

For tools specific to a project, create a .cursor/mcp.json file in your project directory. This allows you to define MCP servers that are only available within that specific project.

## Using MCP Tools in Agent

The Composer Agent will automatically use any MCP tools that are listed under Available Tools on the MCP settings page if it determines them to be relevant. To prompt tool usage intentionally, simply tell the agent to use the tool, referring to it either by name or by description.

### Tool Approval

By default, when Agent wants to use an MCP tool, it will display a message asking for your approval. You can use the arrow next to the tool name to expand the message, and see what arguments the Agent is calling the tool with.

### Yolo Mode

You can enable Yolo mode to allow Agent to automatically run MCP tools without requiring approval, similar to how terminal commands are executed. Read more about Yolo mode and how to enable it here (https://docs.cursor.com/chat/agent#yolo-mode).

### Tool Response

When a tool is used Cursor will display the response in the chat. This image shows the response from the sample tool, as well as expanded views of the tool call arguments and the tool call response.

## Limitations

MCP is a very new protocol and is still in active development. There are some known caveats to be aware of:

### Tool quantity

Some MCP servers, or user’s with many MCP servers active, may have many tools available for Cursor to use. Currently, Cursor will only send the first 40 tools to the Agent.

### Remote development

Cursor directly communicates with MCP servers from your local machine, either directly through stdio or via the network using sse. 
Therefore, MCP servers may not work properly when accessing Cursor over SSH or other development environments. We are hoping to improve this in future releases.

### MCP resources

MCP servers offer two main capabilities: tools and resources. Tools are availabe in Cursor today, and allow Cursor to execute the tools offered by an MCP server, and use the output in it’s further steps. 
However, resources are not yet supported in Cursor. We are hoping to add resource support in future releases.

