# 🤖 n8n Workflow Creator Skill

A comprehensive Claude Code skill that transforms natural language requests into fully functional n8n workflows through a systematic 9-phase process.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude-Code-purple)](https://claude.ai/code)

## 📋 Overview

This skill enables Claude Code to automatically create production-ready n8n workflows from simple natural language descriptions. It leverages four MCP (Model Context Protocol) servers:

- **Memory MCP**: Context management and state tracking
- **Sequential Thinking MCP**: Strategic planning and problem-solving
- **Tavily MCP**: Information gathering and research
- **n8n MCP**: Workflow creation, validation, and deployment

## ✨ Key Features

- 🎯 **Natural Language Input**: Describe your automation in plain language
- 🔄 **9-Phase Systematic Process**: From requirements to deployment
- ✅ **Automatic Validation**: Built-in quality assurance and error checking
- 🎨 **Smart Layout**: Optimized visual positioning of workflow nodes
- 📚 **Complete Documentation**: Generated setup guides and best practices
- 🚀 **Production-Ready**: Immediately deployable workflows

## 📥 Installation

### Using Claude Code `/plugin` Command

```bash
/plugin https://github.com/FLOWIT28999/n8n-workflow-skill.git
```

That's it! The skill will be automatically installed and ready to use.

### Manual Installation

1. Clone this repository:
```bash
git clone https://github.com/FLOWIT28999/n8n-workflow-skill.git
```

2. Copy the skill to your Claude Code skills directory:
```bash
cp -r n8n_workflow_skill/.claude/skills/n8n-workflow-creator ~/.claude/skills/
```

3. Restart Claude Code

## 🔧 Prerequisites

### Required MCP Servers

This skill requires the following MCP servers to be configured in your Claude Code setup:

1. **Memory MCP**: For context management
2. **Sequential Thinking MCP**: For strategic planning
3. **Tavily MCP**: For information gathering
4. **n8n MCP**: For workflow operations

### Installation Guide for MCP Servers

Add these to your Claude Code configuration file (`~/.config/claude/config.json`):

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-memory"]
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-sequential-thinking"]
    },
    "tavily-mcp": {
      "command": "npx",
      "args": ["-y", "@tavily/mcp-server"],
      "env": {
        "TAVILY_API_KEY": "your-tavily-api-key"
      }
    },
    "n8n-mcp": {
      "command": "npx",
      "args": ["-y", "@n8n/mcp-server"],
      "env": {
        "N8N_API_BASE_URL": "http://localhost:5678",
        "N8N_API_KEY": "your-n8n-api-key"
      }
    }
  }
}
```

### Getting API Keys

- **Tavily API**: Sign up at [tavily.com](https://tavily.com)
- **n8n API Key**: Generate in your n8n instance (Settings → API)

## 🚀 Usage

Once installed, simply describe your workflow in natural language:

### Example 1: Simple Integration

```
Create a workflow that receives Slack messages and saves them to Google Sheets
```

### Example 2: AI Agent Workflow

```
Build an AI agent workflow that:
- Monitors email inbox
- Categorizes emails using AI
- Routes to appropriate team channels in Slack
- Logs all actions to a database
```

### Example 3: Data Processing

```
Create a workflow to automate data processing:
- Fetch data from PostgreSQL daily
- Transform and enrich using AI
- Generate reports and send via email
```

## 📖 How It Works

The skill follows a systematic 9-phase process:

### Phase 1: Requirements Understanding
- Analyzes your natural language request
- Identifies key requirements and constraints
- Clarifies ambiguities

### Phase 2: Task Decomposition
- Breaks down the workflow into discrete tasks
- Maps task dependencies
- Creates execution sequence

### Phase 3: Node Matching
- Searches n8n node database
- Selects optimal nodes for each task
- Considers alternatives and trade-offs

### Phase 4: Node Configuration
- Configures each node with proper parameters
- Sets up authentication and credentials
- Validates configurations

### Phase 5: Connection Building
- Creates connections between nodes
- Ensures proper data flow
- Handles error paths

### Phase 6: Layout Optimization
- Positions nodes for clarity
- Creates visual hierarchy
- Optimizes readability

### Phase 7: Workflow Validation
- Validates structure and connections
- Checks for errors and warnings
- Suggests improvements

### Phase 8: Testing & Refinement
- Tests workflow execution
- Identifies and fixes issues
- Optimizes performance

### Phase 9: Documentation & Deployment
- Generates setup guide
- Creates deployment instructions
- Provides best practices

## 📚 Generated Documentation

For each workflow, the skill generates:

- **Setup Guide**: Step-by-step configuration instructions
- **API Keys Required**: List of necessary credentials
- **Configuration Details**: Node-by-node setup
- **Testing Instructions**: How to validate the workflow
- **Troubleshooting**: Common issues and solutions
- **Best Practices**: Security and optimization tips

## 🎯 Use Cases

- **Integration Automation**: Connect different services and APIs
- **AI Agent Workflows**: Build intelligent automation with LLMs
- **Data Processing**: ETL pipelines and data transformations
- **Notification Systems**: Multi-channel alert workflows
- **Content Management**: Automated content workflows
- **Customer Support**: Automated ticketing and routing

## 🔍 Example Workflows Created

The skill has been used to create:

- **Samsung Portfolio Manager**: Automated stock analysis with AI
- **RSS to Social Media**: Auto-posting from RSS feeds to X (Twitter)
- **Email to Task Manager**: Convert emails to Trello/Asana tasks
- **Customer Feedback Analyzer**: Collect and analyze customer feedback with AI
- **Multi-channel Notification Hub**: Centralized notification distribution

## 🛠️ Advanced Features

### Context-Aware Design

The skill maintains context throughout the creation process using Memory MCP, enabling:
- Consistent design decisions
- Reference to previous choices
- Incremental refinement

### Intelligent Node Selection

Uses comprehensive n8n node database with:
- 525+ total nodes
- 263 AI-capable tools
- 104 trigger nodes
- Real-world usage examples

### Automatic Error Handling

Built-in error handling includes:
- Configuration validation
- Connection verification
- Expression syntax checking
- Automatic fix suggestions

## 📝 Project Structure

```
n8n_workflow_skill/
├── .claude/
│   └── skills/
│       └── n8n-workflow-creator/
│           ├── SKILL.md              # Main skill definition
│           ├── assets/               # Supporting files
│           └── references/           # Reference documentation
├── skill.json                        # Skill manifest
├── README.md                         # This file
├── QUICKSTART.md                     # Quick start guide
└── CHANGELOG.md                      # Version history
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues and pull requests.

### Development Setup

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with Claude Code
5. Submit a pull request

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details

## 🐛 Troubleshooting

### Skill Not Loading

1. Verify the skill is in the correct directory
2. Check Claude Code logs for errors
3. Ensure all MCP servers are configured
4. Restart Claude Code

### MCP Connection Issues

1. Verify MCP server configurations
2. Check API keys are correct
3. Ensure n8n instance is accessible
4. Review Claude Code MCP logs

### Workflow Creation Failures

1. Check n8n instance is running
2. Verify n8n API key has proper permissions
3. Review error messages in Claude Code
4. Ensure required nodes are available in n8n

## 📞 Support

- **GitHub Issues**: [Report bugs or request features](https://github.com/FLOWIT28999/n8n-workflow-skill/issues)
- **Claude Code Community**: [Get help from the community](https://github.com/anthropics/claude-code/discussions)
- **n8n Community**: [n8n community forum](https://community.n8n.io)

## 🙏 Acknowledgments

- [Anthropic](https://anthropic.com) for Claude Code and MCP
- [n8n](https://n8n.io) for the amazing workflow automation platform
- [Tavily](https://tavily.com) for AI-powered search capabilities

## 🚀 Version History

See [CHANGELOG.md](CHANGELOG.md) for detailed version history.

## 📊 Stats

- **Total Nodes Available**: 525+
- **AI-Capable Tools**: 263
- **Trigger Nodes**: 104
- **Template Examples**: 1000+
- **Documentation Coverage**: 87%

---

**Made with ❤️ by FLOWIT28999**

**Happy Automating! 🎯🚀**
