# Local Mac Setup for Zo Agents

Run AI agent automation workflows locally on Mac (M1/M2/M4) without cloud costs.

## Prerequisites

**Hardware:**
- Mac with Apple Silicon (M1/M2/M4)
- 16GB+ RAM recommended
- 50GB+ free storage

**Software:**
- macOS 13.0+ (Ventura or newer)
- Homebrew package manager
- Node.js 18+ or Python 3.9+

## Installation

### 1. Install Homebrew
```bash
# If not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 2. Install Node.js
```bash
# Install via Homebrew
brew install node

# Verify installation
node --version  # Should be 18.0.0 or higher
npm --version
```

### 3. Install Zo CLI
```bash
# Install Zo CLI globally
npm install -g @anthropic/zo-cli

# Verify installation
zo --version

# Login to Zo
zo login
# Follow authentication prompts
```

### 4. Install Python (Optional)
```bash
# Install Python 3.9+
brew install python@3.11

# Verify
python3 --version

# Install pip packages
pip3 install anthropic python-dotenv schedule
```

## Zo Agent Configuration

### Basic Setup

**1. Create project directory:**
```bash
mkdir ~/zo-automation
cd ~/zo-automation
```

**2. Initialize Zo project:**
```bash
zo init content-automation

# Select options:
# - Agent type: Content Generator
# - Model: Claude Sonnet 4
# - Tools: Web search, file operations
```

**3. Configure agent:**

Edit `zo-config.json`:
```json
{
  "name": "BSCH Content Generator",
  "model": "claude-sonnet-4-20250514",
  "temperature": 0.7,
  "max_tokens": 2000,
  "system_prompt": "You are BSCH.VAULT content strategist. Generate AI news content ideas daily. Style: Bold, edgy, CRT character voice. Output: JSON with hooks, scripts, captions.",
  "tools": ["web_search"]
}
```

## Automation Workflows

### Workflow 1: Daily Content Generation

**File:** `generate_content.sh`
```bash
#!/bin/bash

# Daily content generation script
DATE=$(date +%Y-%m-%d)
OUTPUT_DIR="$HOME/zo-automation/output"

# Create output directory if doesn't exist
mkdir -p "$OUTPUT_DIR"

# Run Zo agent
zo run --config zo-config.json \
  "Generate 5 AI news content ideas for today. Include: 1) Hook (3-5 words), 2) Script (30-45 sec), 3) Caption (<150 chars), 4) Hashtags (5 tags). Format as JSON." \
  > "$OUTPUT_DIR/content_$DATE.json"

echo "✅ Content generated: $OUTPUT_DIR/content_$DATE.json"

# Optional: Send notification
osascript -e 'display notification "Daily content ready!" with title "Zo Agent"'
```

**Make executable:**
```bash
chmod +x generate_content.sh
```

**Test run:**
```bash
./generate_content.sh
```

### Workflow 2: Python Automation Script

**File:** `daily_automation.py`
```python
#!/usr/bin/env python3
"""
Daily content automation using Zo agents
Run locally on Mac for $0/month operation
"""

import subprocess
import json
import os
from datetime import datetime
from pathlib import Path

class ZoContentAutomation:
    def __init__(self, output_dir='output'):
        self.output_dir = Path.home() / 'zo-automation' / output_dir
        self.output_dir.mkdir(parents=True, exist_ok=True)
    
    def generate_content(self, topic=None):
        """Generate content using Zo agent"""
        
        prompt = "Generate 5 BSCH.VAULT content ideas. Format: JSON with 'ideas' array containing {hook, script, caption, hashtags}"
        
        if topic:
            prompt = f"Generate 5 BSCH.VAULT content ideas about {topic}. Format: JSON with 'ideas' array."
        
        try:
            # Run Zo CLI
            result = subprocess.run(
                ['zo', 'run', '--config', 'zo-config.json', prompt],
                capture_output=True,
                text=True,
                timeout=60
            )
            
            if result.returncode == 0:
                return self._parse_output(result.stdout)
            else:
                print(f"❌ Error: {result.stderr}")
                return None
                
        except subprocess.TimeoutExpired:
            print("❌ Timeout: Zo agent took too long")
            return None
    
    def _parse_output(self, output):
        """Parse Zo agent output"""
        try:
            # Extract JSON from output
            start = output.find('{')
            end = output.rfind('}') + 1
            json_str = output[start:end]
            
            data = json.loads(json_str)
            return data
            
        except json.JSONDecodeError:
            # Fallback: save raw output
            return {'raw_output': output}
    
    def save_content(self, content):
        """Save generated content to file"""
        timestamp = datetime.now().strftime('%Y-%m-%d_%H-%M')
        filename = self.output_dir / f'content_{timestamp}.json'
        
        with open(filename, 'w', encoding='utf-8') as f:
            json.dump(content, f, indent=2, ensure_ascii=False)
        
        print(f"✅ Content saved: {filename}")
        return filename
    
    def run_daily_generation(self):
        """Main daily workflow"""
        print("🤖 Starting daily content generation...")
        
        # Generate content
        content = self.generate_content()
        
        if content:
            # Save to file
            filepath = self.save_content(content)
            
            # Send macOS notification
            self._send_notification(
                "Daily Content Ready!",
                f"Generated {len(content.get('ideas', []))} ideas"
            )
            
            return filepath
        else:
            print("❌ Content generation failed")
            return None
    
    def _send_notification(self, title, message):
        """Send macOS notification"""
        script = f'display notification "{message}" with title "{title}"'
        subprocess.run(['osascript', '-e', script])

# CLI Usage
if __name__ == "__main__":
    import sys
    
    automation = ZoContentAutomation()
    
    if len(sys.argv) > 1:
        topic = ' '.join(sys.argv[1:])
        print(f"📝 Topic: {topic}")
        content = automation.generate_content(topic)
    else:
        content = automation.generate_content()
    
    if content:
        automation.save_content(content)
```

**Make executable:**
```bash
chmod +x daily_automation.py
```

**Test run:**
```bash
# General content
python3 daily_automation.py

# Specific topic
python3 daily_automation.py "Kling AI new features"
```

## Scheduling (cron)

### Setup Daily Automation

**Edit crontab:**
```bash
crontab -e
```

**Add schedule:**
```bash
# Run every day at 9:00 AM
0 9 * * * cd ~/zo-automation && ./generate_content.sh

# Or Python version
0 9 * * * cd ~/zo-automation && python3 daily_automation.py

# Run multiple times (morning + afternoon)
0 9,14 * * * cd ~/zo-automation && python3 daily_automation.py
```

**View scheduled jobs:**
```bash
crontab -l
```

## Integration with Notion

### Auto-sync to Notion

**Install Notion SDK:**
```bash
pip3 install notion-client
```

**Sync script:** `sync_to_notion.py`
```python
import json
import os
from notion_client import Client
from datetime import datetime

# Initialize Notion client
notion = Client(auth=os.getenv("NOTION_TOKEN"))
DATABASE_ID = os.getenv("NOTION_DATABASE_ID")

def sync_content_to_notion(content_file):
    """Sync generated content to Notion database"""
    
    with open(content_file, 'r') as f:
        content = json.load(f)
    
    for idea in content.get('ideas', []):
        # Create Notion page
        notion.pages.create(
            parent={"database_id": DATABASE_ID},
            properties={
                "Title": {"title": [{"text": {"content": idea['hook']}}]},
                "Script": {"rich_text": [{"text": {"content": idea['script']}}]},
                "Caption": {"rich_text": [{"text": {"content": idea['caption']}}]},
                "Status": {"select": {"name": "To Review"}},
                "Date": {"date": {"start": datetime.now().isoformat()}}
            }
        )
    
    print(f"✅ Synced {len(content.get('ideas', []))} ideas to Notion")

# Usage
if __name__ == "__main__":
    import sys
    sync_content_to_notion(sys.argv[1])
```

**Environment setup:**
```bash
# Add to ~/.zshrc or ~/.bash_profile
export NOTION_TOKEN="your_notion_integration_token"
export NOTION_DATABASE_ID="your_database_id"
```

## Monitoring & Logs

### Check Automation Status

**View cron logs:**
```bash
# macOS system log
log show --predicate 'process == "cron"' --last 1h

# Or check mail (cron sends output via mail)
mail
```

### Create Log File

**Updated cron entry:**
```bash
0 9 * * * cd ~/zo-automation && ./generate_content.sh >> logs/automation.log 2>&1
```

**View logs:**
```bash
tail -f ~/zo-automation/logs/automation.log
```

## Troubleshooting

### Common Issues

**Issue: "zo: command not found"**
```bash
# Fix: Add npm global bin to PATH
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

**Issue: "Permission denied"**
```bash
# Fix: Make script executable
chmod +x your_script.sh
```

**Issue: Cron job not running**
```bash
# Check cron service
sudo launchctl list | grep cron

# Test script manually first
cd ~/zo-automation && ./generate_content.sh
```

**Issue: Python module not found in cron**
```bash
# Use full Python path in crontab
which python3  # Get full path (e.g., /usr/local/bin/python3)

# Update crontab with full path
0 9 * * * cd ~/zo-automation && /usr/local/bin/python3 daily_automation.py
```

## Performance Optimization

### Mac Mini M4 Optimization

**Enable background processing:**
```bash
# Prevent Mac from sleeping (optional)
caffeinate -s &

# Or use pmset for scheduled wake
sudo pmset repeat wake MTWRFSU 08:55:00
```

**Resource monitoring:**
```bash
# Check CPU/Memory usage
top -pid $(pgrep -f "zo run")

# Or use Activity Monitor app
```

## Security Best Practices

**1. API Keys Management:**
```bash
# Store in .env file
echo "ANTHROPIC_API_KEY=your_key" >> .env
echo "NOTION_TOKEN=your_token" >> .env

# Never commit .env to git
echo ".env" >> .gitignore
```

**2. File Permissions:**
```bash
# Restrict access to config files
chmod 600 zo-config.json
chmod 600 .env
```

**3. Backup:**
```bash
# Regular backup of generated content
rsync -av ~/zo-automation/output ~/Backups/zo-content-$(date +%Y%m%d)
```

## Cost Comparison

### Local Mac vs Cloud

**Mac Mini M4 (Local):**
- Setup: $0 (one-time Mac purchase)
- Monthly: $0 (electricity ~$2-3/month)
- Control: Full
- Privacy: 100%

**Azure/DigitalOcean:**
- Setup: $0
- Monthly: $10-25/month
- Control: Limited
- Privacy: Depends on provider

**Winner: Local Mac for long-term!** 🏆

## Next Steps

1. Complete Zo CLI setup
2. Test automation scripts
3. Schedule daily runs
4. Monitor for 1 week
5. Optimize based on results

## Resources

- [Zo Documentation](https://zo.dev/docs)
- [Anthropic API Docs](https://docs.anthropic.com)
- [Mac Automation Guide](https://support.apple.com/guide/automator)

---

**Updated:** March 2026
```

---

**COMMIT MESSAGE:**
```
Add local Mac setup guide for Zo automation
