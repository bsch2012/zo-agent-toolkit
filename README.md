# Zo Agent Toolkit

Open-source collection of workflow templates, API integrations, and deployment guides for AI agent development.

## Features
- 🤖 Pre-built agent templates (Zo, Claude Code, custom agents)
- 🔌 API integration examples (Anthropic, OpenAI, local models)
- 🌐 Multilingual support (English/Bahasa Indonesia)
- 📚 Community-contributed recipes
- 🛠️ Deployment guides (Mac/Linux/Windows)

## Agent Templates

### Zo Agent Workflows
- Content creation agents
- Research & analysis workflows
- Automation & task management
- Custom agent configurations

### Claude Integration
- Claude API workflows
- Claude Code integration
- Multi-agent orchestration
- Prompt engineering templates

### Custom Agent Development
- Agent architecture patterns
- State management examples
- Tool integration guides
- Error handling & recovery

## Tech Stack
- **Python** - Core agent development
- **Anthropic API** - Claude integration
- **Zo Platform** - Agent orchestration
- **OpenAI API** - Alternative model support
- **LangChain** - Agent framework (optional)

## Getting Started

### Prerequisites
```bash
# Python 3.9+
python --version

# Install dependencies
pip install anthropic openai python-dotenv
```

### Quick Setup
```bash
# Clone repository
git clone https://github.com/[your-username]/zo-agent-toolkit.git
cd zo-agent-toolkit

# Install requirements
pip install -r requirements.txt

# Configure API keys
cp .env.example .env
# Add your API keys to .env
```

### Basic Agent Example
```python
from anthropic import Anthropic

client = Anthropic(api_key="your-api-key")

# Simple agent workflow
response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Your task here"}
    ]
)

print(response.content)
```

## Project Structure
```
zo-agent-toolkit/
├── templates/         # Pre-built agent templates
├── workflows/         # Agent workflow examples
├── integrations/      # API integration guides
├── docs/             # Documentation (EN & ID)
├── examples/         # Working code examples
└── tools/            # Utility scripts & helpers
```

## Use Cases
- Automated content creation
- Research & data analysis
- Customer service automation
- Development workflow optimization
- Multi-agent collaboration systems

## Agent Recipes

### Content Creation Agent
- Blog post generation
- Social media content
- Video script writing
- SEO optimization

### Research Agent
- Web research automation
- Data collection & analysis
- Report generation
- Source verification

### Task Automation Agent
- Email management
- Calendar scheduling
- File organization
- API orchestration

## Status
🚧 **Work in Progress** - Templates being developed. Contributions welcome!

**Coming Soon:**
- Zo agent workflow templates
- Claude Code integration guide
- Multi-agent orchestration examples
- Bahasa Indonesia documentation
- Advanced prompt engineering tutorials

## Contributing
We welcome contributions from:
- Agent developers (workflow templates)
- API integration experts
- Documentation writers (especially Bahasa Indonesia)
- Community members with real-world use cases

**How to contribute:**
1. Fork this repository
2. Create feature branch (`git checkout -b feature/AgentWorkflow`)
3. Commit changes (`git commit -m 'Add agent workflow'`)
4. Push to branch (`git push origin feature/AgentWorkflow`)
5. Open Pull Request

## Resources
- [Anthropic API Documentation](https://docs.anthropic.com)
- [Claude Code Guide](https://docs.anthropic.com/claude/docs/claude-code)
- [Zo Platform Docs](https://zo.dev)
- [Agent Development Best Practices](docs/best-practices.md)

## Documentation
- [English Documentation](docs/README-EN.md)
- [Dokumentasi Bahasa Indonesia](docs/README-ID.md)
- [API Integration Guide](docs/api-integration.md)
- [Deployment Guide](docs/deployment.md)

## License
MIT License - free to use, fork, and develop!

## Community
- [GitHub Discussions](../../discussions) - Ask questions & share workflows
- [Issues](../../issues) - Report bugs or request features

---

**Built with ❤️ for the AI agent community**
```

---

### STEP 4: Commit

**Message:**
```
Initial README - AI agent templates & workflow guides
