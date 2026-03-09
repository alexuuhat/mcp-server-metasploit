# MetasploitMCP — Setup Guide

Connect Metasploit to Claude AI via MCP and run exploits using plain English.

> ⚠️ **Disclaimer:** This tool is intended strictly for authorized penetration testing, security research, and educational purposes. Only use it against systems you own or have explicit written permission to test. Unauthorized access to computer systems is illegal and unethical. The authors take no responsibility for misuse.

---

## Requirements

- Kali Linux with Metasploit installed
- Claude Desktop
- Python 3.10+

---

## Setup

```bash
git clone https://github.com/GH05TCREW/MetasploitMCP.git
cd MetasploitMCP
python -m venv myenv
source myenv/bin/activate
pip install -r requirements.txt
```

---

## Start Metasploit RPC

```bash
msfrpcd -P yourpassword -S -a 127.0.0.1 -p 55553
```

> Keep this running in a separate terminal or tmux session.

---

## Configure Claude Desktop

Open your Claude config file:
- **Linux/Mac:** `~/.config/claude/claude_desktop_config.json`
- **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

Add this block (update the paths and password):

```json
{
  "mcpServers": {
    "metasploit": {
      "command": "/root/mcp_fanda/MetasploitMCP/myenv/bin/python",
      "args": [
        "/root/mcp_fanda/MetasploitMCP/MetasploitMCP.py",
        "--transport",
        "stdio"
      ],
      "env": {
        "MSF_PASSWORD": "yourpassword"
      }
    }
  }
}
```

Save and restart Claude Desktop.

---

## Verify

Click the **+** icon in Claude Desktop → **Connectors** → you should see **metasploit** listed as enabled. You're good to go.

---

## Example Prompts

```
Run the vsftpd exploit on my lab host 192.168.0.200 and create a file at /tmp/alexuuhat.txt with the content "Hello Eshikhon"
```

```
List available Metasploit exploits for SMB
```

```
Scan 192.168.0.0/24 and suggest exploits for any vulnerable services
```

> ⚠️ Only use against systems you own or have explicit permission to test.

---

## Troubleshooting

| Error | Fix |
|---|---|
| `No module named 'mcp'` | Make sure `command` points to `myenv/bin/python`, not system Python |
| `spawn uv ENOENT` | Use the direct Python approach above instead of `uv` |
| `No such file or directory` | Double-check all paths in your config match where you cloned the repo |
| RPC connection refused | Make sure `msfrpcd` is running: `ss -tlnp \| grep 55553` |

---

## Optional: Using uv Instead

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Then use `/root/.local/bin/uv` as the `command` with args `--directory /path/to/MetasploitMCP run MetasploitMCP.py --transport stdio`. Requires a valid `pyproject.toml` in the project — if you hit `ModuleNotFoundError`, stick with the `myenv/bin/python` approach above.
