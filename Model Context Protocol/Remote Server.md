# Connect to Remote MCP Servers

> Learn how to connect Claude to remote MCP servers and extend its capabilities with internet-hosted tools and data sources

Remote MCP servers extend AI applications' capabilities beyond your local environment, providing access to internet-hosted tools, services, and data sources. By connecting to remote MCP servers, you transform AI assistants from helpful tools into informed teammates capable of handling complex, multi-step projects with real-time access to external resources.

Many clients now support remote MCP servers, enabling a wide range of integration possibilities. This guide demonstrates how to connect to remote MCP servers using [Claude](https://claude.ai/) as an example, one of the [many clients that support MCP](/clients). While we focus on Claude's implementation through Custom Connectors, the concepts apply broadly to other MCP-compatible clients.

---

## Understanding Remote MCP Servers

Remote MCP servers function similarly to local MCP servers but are hosted on the internet rather than your local machine. They expose tools, prompts, and resources that Claude can use to perform tasks on your behalf. These servers can integrate with various services such as project management tools, documentation systems, code repositories, and any other API-enabled service.

**Key advantage:** Accessibility.  
Unlike local servers that require installation and configuration on each device, remote servers are available from any MCP client with an internet connection. This makes them ideal for:

- Web-based AI applications
- Integrations that emphasize ease-of-use
- Services that require server-side processing or authentication

---

## What are Custom Connectors?

Custom Connectors serve as the bridge between Claude and remote MCP servers. They allow you to connect Claude directly to the tools and data sources that matter most to your workflows.

With Custom Connectors, you can:

- [Connect Claude to existing remote MCP servers](https://support.anthropic.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp) provided by third-party developers
- [Build your own remote MCP servers](https://support.anthropic.com/en/articles/11503834-building-custom-connectors-via-remote-mcp-servers) to connect with any tool

---

## Connecting to a Remote MCP Server

### 1. Navigate to Connector Settings
Open Claude in your browser and navigate to the **Settings** page by clicking on your profile icon and selecting **Settings** from the dropdown.  
Go to the **Connectors** section in the sidebar.

---

### 2. Add a Custom Connector
Scroll to the bottom of the Connectors section and click **"Add custom connector"**.

![Add custom connector button in Claude settings](https://mintlify.s3.us-west-1.amazonaws.com/mcp/images/quickstart-remote/1-add-connector.png)

Enter the remote MCP server URL provided by the server developer/administrator, including the correct protocol (`https://`).

![Dialog for entering remote MCP server URL](https://mintlify.s3.us-west-1.amazonaws.com/mcp/images/quickstart-remote/2-connect.png)

---

### 3. Complete Authentication
Most remote MCP servers require authentication (OAuth, API keys, or username/password). Follow the prompts to authenticate.

![Authentication screen for remote MCP server](https://mintlify.s3.us-west-1.amazonaws.com/mcp/images/quickstart-remote/3-auth.png)

---

### 4. Access Resources and Prompts
Once connected, resources and prompts will be available via the paperclip icon in the Claude message input area.

![Attachment menu showing available resources](https://mintlify.s3.us-west-1.amazonaws.com/mcp/images/quickstart-remote/4-select-resources-menu.png)

You can select specific prompts/resources to add context to your conversation.

![Selecting specific resources and prompts from the menu](https://mintlify.s3.us-west-1.amazonaws.com/mcp/images/quickstart-remote/5-select-prompts-resources.png)

---

### 5. Configure Tool Permissions
Remote MCP servers may expose multiple tools. Control which tools Claude can use in the connector settings.

![Tool permission configuration interface](https://mintlify.s3.us-west-1.amazonaws.com/mcp/images/quickstart-remote/6-configure-tools.png)

---

## Best Practices for Using Remote MCP Servers

- **Security considerations:**  
  Only connect to trusted servers, verify authenticity, and review permissions before granting access.

- **Managing multiple connectors:**  
  You can connect to multiple remote MCP servers at once. Organize connectors by purpose or project, and periodically remove unused ones.

---

## Next Steps

Now that you’ve connected Claude to a remote MCP server, you can:

- Automate tasks  
- Access external data  
- Integrate with your existing workflows

---

### Resources
- [Build your own remote server](https://support.anthropic.com/en/articles/11503834-building-custom-connectors-via-remote-mcp-servers) – Create custom MCP servers for proprietary tools and services.
- [Explore available servers](https://github.com/modelcontextprotocol/servers) – Browse official and community-created MCP servers.
- [Connect local servers](/quickstart/user) – Learn how to connect Claude Desktop to local MCP servers.
- [Understand the architecture](/docs/learn/architecture) – Dive deeper into MCP architecture.

---

Remote MCP servers unlock powerful possibilities for extending Claude's capabilities. As you become familiar with these integrations, you'll discover new ways to streamline your workflows and accomplish complex tasks more efficiently.
