# MetasploitMCP — Setup Guide

Connect Metasploit to Claude AI via MCP and run exploits using plain English.

> ⚠️ **Disclaimer:** This tool is intended strictly for authorized penetration testing, security research, and educational purposes. Only use it against systems you own or have explicit written permission to test. Unauthorized access to computer systems is illegal and unethical. The authors take no responsibility for misuse.

---

## Requirements

- Kali Linux with Metasploit installed
- Claude Desktop
- Python 3.10+

---

## 1. Clone & Install

```bash
git clone https://github.com/GH05TCREW/MetasploitMCP.git
cd MetasploitMCP
python -m venv myenv
source myenv/bin/activate
pip install -r requirements.txt
```

---

## 2. Configure Environment Variables

```bash
MSF_PASSWORD=yourpassword
MSF_SERVER=127.0.0.1
MSF_PORT=55553
MSF_SSL=false
PAYLOAD_SAVE_DIR=/root/mcp_fanda/MetasploitMCP
```

---

## 3. Start Metasploit RPC

```bash
msfrpcd -P yourpassword -S -a 127.0.0.1 -p 55553
```

> Keep this running in a separate terminal or tmux session.

---

## 4. Configure Claude Desktop

Open your Claude config file:
- **Linux/Mac:** `~/.config/claude/claude_desktop_config.json`
- **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

Add this block (update the paths and password to match your setup):

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
  },
  "preferences": {
    "coworkWebSearchEnabled": true,
    "coworkScheduledTasksEnabled": false,
    "ccdScheduledTasksEnabled": false
  }
}
```

> **Important:** `command` must point to the Python binary inside your virtualenv (`myenv/bin/python`), not your system Python. This ensures all installed packages are available.

Save the file and restart Claude Desktop.

---

## 5. Verify Connection

Click the **+** icon in Claude Desktop → **Connectors** → you should see **metasploit** listed as enabled. You're good to go.

---

## 6. Example Prompts

```
Run the vsftpd exploit on my lab host 192.168.0.200 and create a file at /tmp/alexuuhat.txt with the content "Hello Eshikhon"
```

```
List available Metasploit exploits for SMB
```

```
Scan 192.168.0.0/24 and suggest exploits for any vulnerable services
```

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

We used Python directly instead of `uv`. If you still want to try `uv`:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
which uv
# → /root/.local/bin/uv
```

Then update your Claude config `command` to `/root/.local/bin/uv` with these args:

```json
{
  "mcpServers": {
    "metasploit": {
      "command": "/root/.local/bin/uv",
      "args": [
        "--directory",
        "/root/mcp_fanda/MetasploitMCP",
        "run",
        "MetasploitMCP.py",
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

> If you get `ModuleNotFoundError` with `uv`, stick with the `myenv/bin/python` approach — it's simpler and more reliable.

---

## License

MIT License — see [LICENSE](LICENSE) for details.
