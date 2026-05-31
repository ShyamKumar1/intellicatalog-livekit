# Intelli Catalog — AI Voice & Chat Assistant (LiveKit)

An AI-powered voice and chat assistant for **Electronic Parts Catalogs (EPC)** built with the **LiveKit Agents framework**. Enables users to find spare parts, check availability, navigate catalog structures, and create orders using natural language — voice or chat.

## Architecture

```
User (Voice / Chat)
    │
    ▼
┌────────────────────────────────────────────────┐
│              LiveKit Room (WebRTC)              │
│  ┌──────────┐  ┌──────────┐  ┌──────────────┐ │
│  │  STT     │  │  LLM     │  │  TTS         │ │
│  │ Deepgram │─▶│ GPT-4o   │─▶│ Cartesia     │ │
│  │ Nova-3   │  │          │  │ Sonic-3      │ │
│  └──────────┘  └────┬─────┘  └──────────────┘ │
│                     │                           │
│              ┌──────▼──────┐                    │
│              │  Function   │                    │
│              │  Calling    │                    │
│              │  ┌────────┐ │                    │
│              │  │search  │ │                    │
│              │  │parts() │ │                    │
│              │  │details()│ │                   │
│              │  │cart()  │ │                    │
│              │  │avail() │ │                    │
│              │  └────────┘ │                    │
│              └─────────────┘                    │
└────────────────────────────────────────────────┘
    │
    ▼
┌────────────────────────────────────────────────┐
│              Parts Catalog (EPC)                │
│  • Part search & lookup                         │
│  • Availability & supersession                  │
│  • Cart & ordering                              │
│  • Category navigation                          │
│  • Multi-turn context                           │
└────────────────────────────────────────────────┘
```

## Features

- **🎤 Voice-first interface** — Speak naturally to find parts
- **💬 Chat mode** — Type queries when voice isn't practical
- **🔍 Natural language search** — "Find oil filters for Traxion 5200"
- **📋 Part details** — Prices, availability, supersessions, compatibility
- **🛒 Order management** — Add to cart, view cart, create orders
- **🧠 Multi-turn conversations** — Context retained across interactions
- **🔧 Function calling** — 8 tools for catalog interaction
- **📂 Category navigation** — Browse by Engine, Hydraulics, Brakes, etc.

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Agent Framework** | [LiveKit Agents](https://docs.livekit.io/agents/) (Python) |
| **LLM** | OpenAI GPT-4o (or any OpenAI-compatible provider) |
| **STT** | Deepgram Nova-3 |
| **TTS** | Cartesia Sonic-3 |
| **VAD** | Silero VAD |
| **Turn Detection** | LiveKit Multilingual Turn Detector |
| **Server** | LiveKit Agent Server (Python) |
| **Transport** | LiveKit WebRTC (room-based) |
| **Deployment** | Docker + LiveKit Cloud |

## Quick Start

### Prerequisites

- Python 3.10+
- LiveKit Cloud account (free tier available) OR self-hosted LiveKit server
- API keys: OpenAI, Deepgram, Cartesia

### Setup

```bash
# 1. Clone the repo
git clone https://github.com/ShyamKumar1/intellicatalog-livekit.git
cd intellicatalog-livekit

# 2. Install dependencies
pip install -r requirements.txt

# 3. Download model files
python -m livekit.agents download-files

# 4. Configure environment
cp .env.example .env.local
# Edit .env.local with your API keys

# 5. Run the agent
python agent.py dev
```

### Environment Variables

```env
# LiveKit (Cloud or self-hosted)
LIVEKIT_API_KEY=...
LIVEKIT_API_SECRET=...
LIVEKIT_URL=wss://your-project.livekit.cloud

# AI Providers
OPENAI_API_KEY=sk-...      # For GPT-4o LLM
DEEPGRAM_API_KEY=...        # For Nova-3 STT
CARTESIA_API_KEY=...        # For Sonic-3 TTS
```

## EPC Agent Tools

The agent exposes the following function-calling tools to the LLM:

| Tool | Description |
|------|-------------|
| `search_parts(query, category?)` | Search catalog by keyword/description |
| `get_part_details(part_number)` | Full part info: price, availability, supersessions |
| `check_availability(part_number)` | Real-time stock status |
| `add_to_cart(part_number, quantity)` | Add items to order |
| `view_cart()` | Review current cart contents |
| `list_categories()` | Browse available part categories |
| `get_parts_by_category(category)` | List all parts in a category |

## Demo

### Parts Catalog (Sample Data)

The demo includes a realistic EPC parts database for agricultural equipment:

| Part # | Name | Category | Price |
|--------|------|----------|-------|
| RE525600 | Engine Oil Filter | Engine | $24.99 |
| HY733200 | Hydraulic Pump Assembly | Hydraulics | $289.00 |
| TR441000 | Transmission Oil Filter | Transmission | $32.75 |
| BR551300 | Brake Pad Set (HD) | Brakes | $92.00 |
| EL301100 | Alternator 12V 120A | Electrical | $215.00 |
| TI991010 | Front Tire 14.9R28 | Tires/Wheels | $475.00 |

### Example Conversations

**User:** "I need an oil filter for my Traxion 5200"
**Agent:** "I found an Engine Oil Filter (RE525600) for $24.99. It's in stock and compatible with your Traxion 5200. Would you like to add it to your cart?"

**User:** "What about brake pads?"
**Agent:** "For the Traxion 5200, I have two options. The standard Brake Pad Set (BR551200) at $78.00, and the Heavy Duty version (BR551300) at $92.00. The heavy duty version supersedes the standard set. Which would you prefer?"

## Deployment

### Option 1: LiveKit Cloud (Recommended)

```bash
# 1. Authenticate with LiveKit Cloud
lk cloud auth

# 2. Deploy agent
lk agent deploy
```

### Option 2: Docker (Self-hosted)

```bash
docker compose up -d
python agent.py start
```

## Project Structure

```
intellicatalog-livekit/
├── agent.py              # Main LiveKit Agent with EPC tools
├── livekit.yaml          # LiveKit server config (self-hosted)
├── docker-compose.yml    # Docker setup
├── .env.example          # Environment template
├── requirements.txt      # Python dependencies
├── README.md             # This file
└── web/                  # Web frontend (React)
    └── index.html        # Simple demo page
```

## Why LiveKit?

LiveKit provides the complete real-time AI agent stack:

1. **WebRTC transport** — Low-latency audio streaming
2. **VAD + Turn detection** — Natural conversation flow
3. **Plugin ecosystem** — Deepgram, Cartesia, OpenAI, Anthropic
4. **Agent framework** — Python/Node.js SDK with function calling
5. **Cloud or self-hosted** — Flexibility for any deployment

## License

MIT
