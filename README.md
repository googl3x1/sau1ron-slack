# sau1ron

An autonomous Slack AI Agent built on the **OpenClaw** framework and powered by **DeepSeek-v4-flash**. It is designed for multi-user chat engagement within a workspace while restricting executive administrative commands exclusively to a single designated commander.

---

## 🚀 Features

* **Low-Latency Conversational Engine:** Optimized with `reasoning: false` to bypass intensive multi-step thinking delays, ensuring near-instantaneous Slack interactions.
* **Open Access, Single Commander:** Configured with an open direct message policy (`dmPolicy: "open"`) allowing anyone in your workspace to chat with the agent, while strictly locking down internal operations and configuration triggers to your unique Slack Member ID.
* **Live Web Grounding:** Fully integrated with DuckDuckGo search tools to pull real-time information and answer dynamic queries accurately.
* **Hardware & Safety Sandboxing:** Restricts dangerous system operations (such as camera control, audio recording, and screen capturing) to maintain high local privacy standards.

---

## 🛠️ System Architecture

* **Core Gateway:** OpenClaw Engine (manages tool routing, channel communication, and socket listeners).
* **LLM Backend:** DeepSeek V4 Flash (handles text parsing and function generation via an OpenAI-compliant API structure).
* **Search Engine:** DuckDuckGo Web Tooling Profile.
* **Integration Layer:** Slack Socket Mode via App Tokens.

---

## ⚙️ Configuration Blueprint (`openclaw.example.json`)

Use this safe template to configure your deployment workspace. Copy this file into your active machine profile directory as `openclaw.json` and populate your secrets.

```json
{
  "agents": {
    "defaults": {
      "workspace": "C:\\Users\\DELL\\.openclaw\\workspace",
      "model": {
        "primary": "deepseek/deepseek-v4-flash"
      }
    }
  },
  "models": {
    "mode": "merge",
    "providers": {
      "deepseek": {
        "baseUrl": "[https://api.api.deepseek.com](https://api.api.deepseek.com)",
        "apiKey": "YOUR_DEEPSEEK_API_KEY_HERE",
        "api": "openai-completions",
        "models": [
          {
            "id": "deepseek-v4-flash",
            "name": "DeepSeek V4 Flash",
            "reasoning": false
          }
        ]
      }
    }
  },
  "commands": {
    "ownerAllowFrom": [
      "slack:YOUR_SLACK_MEMBER_ID_HERE"
    ]
  },
  "channels": {
    "slack": {
      "enabled": true,
      "mode": "socket",
      "botToken": "YOUR_SLACK_BOT_TOKEN_HERE",
      "appToken": "YOUR_SLACK_APP_TOKEN_HERE",
      "dmPolicy": "open",
      "groupPolicy": "open",
      "allowFrom": "*",
      "capabilities": {
        "interactiveReplies": true
      }
    }
  },
  "gateway": {
    "mode": "local",
    "auth": {
      "mode": "token",
      "token": "YOUR_LOCAL_GATEWAY_TOKEN_HERE"
    },
    "port": 18789,
    "bind": "loopback",
    "tailscale": {
      "mode": "off",
      "resetOnExit": false
    },
    "nodes": {
      "denyCommands": [
        "camera.snap",
        "camera.clip",
        "screen.record",
        "contacts.add",
        "calendar.add",
        "reminders.add",
        "sms.send",
        "sms.search"
      ]
    },
    "controlUi": {
      "allowInsecureAuth": true
    }
  },
  "session": {
    "dmScope": "per-channel-peer"
  },
  "tools": {
    "profile": "coding",
    "web": {
      "search": {
        "provider": "duckduckgo",
        "enabled": true
      }
    }
  },
  "plugins": {
    "entries": {
      "slack": { "enabled": true },
      "google": { "enabled": true },
      "duckduckgo": { "enabled": true }
    }
  }
}