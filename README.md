# Local AI Automation Infrastructure (n8n + Ollama)

Self-hosted automation stack integrated with a local LLM inference server, deployed for internal use in an engineering environment. This repository documents the configuration, deployment decisions, and operational notes for a fully local AI infrastructure — **zero external API calls for inference, no vendor lock-in.**

---

## 🛠 Stack

| Service | Role |
|---|---|
| **n8n** | Workflow automation engine (self-hosted) |
| **Ollama** | Local LLM inference server |
| **Supabase** | PostgreSQL database + pgvector for RAG |
| **Qdrant** | Vector store for semantic search |
| **Open WebUI** | Chat interface for local models |
| **Langfuse** | LLM observability and tracing |
| **Neo4j** | Knowledge graph (GraphRAG) |
| **SearXNG** | Private web search |
| **Portainer** | Docker container management |

All services run as Docker containers via [coleam00/local-ai-packaged](https://github.com/coleam00/local-ai-packaged).

---

## 🖥 Hardware Configuration

- **GPU:** NVIDIA GeForce RTX 5090 (32 GB VRAM)
- **CPU:** AMD Ryzen 7 9700X (Zen 5, AVX-512)
- **RAM:** 64 GB DDR5 6000 MHz
- **SSD:** Samsung 990 EVO Plus 1TB (NVMe, PCIe 4.0)
- **OS:** Ubuntu 24.04 LTS

---

## 🧠 Models (Ollama)

Models selected for dense architecture (non-MoE preference) optimized for 32 GB VRAM:

| Model | Quantization | VRAM | Notes |
|---|---|---|---|
| **Qwen3-32B** | Q6_K_L | ~27 GB | Primary reasoning model |
| **DeepSeek-R1-Distill-Qwen-32B** | Q6_K | ~27 GB | Full chain-of-thought |
| **Mistral-Small-3.2-24B** | Q8_0 | ~25 GB | Tool calling, structured output |
| **Gemma-3-27B-IT** | Q8_0 | ~29 GB | Classification, JSON tasks |
| **QwQ-32B** | Q6_K | ~25 GB | Reasoning specialist |
| **qwen2.5:7b-instruct** | Q4_K_M | ~5 GB | Lightweight auxiliary tasks |
| **nomic-embed-text** | F16 | <1 GB | Embeddings for RAG |

---

## 🔌 MCP Integration (Model Context Protocol)

n8n is connected to **Claude Code** via [n8n-mcp](https://github.com/czlonkowski/n8n-mcp), enabling natural language workflow creation and management.

### Claude Code Environment

Claude Code routes through local **Qwen3-Coder (30B)** running on the RTX 5090 instead of the Anthropic API:

```powershell
# Pointing Claude Code to local Ollama instance
$env:ANTHROPIC_AUTH_TOKEN="ollama"
$env:ANTHROPIC_BASE_URL="http://[INTERNAL_IP]:11434"
claude --model qwen3-coder:30b
```

### Server Configuration (Example)

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["-y", "n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "N8N_API_URL": "http://[INTERNAL_IP]:5678",
        "N8N_API_KEY": "YOUR_N8N_API_KEY"
      }
    }
  }
}
```

---

## 🚀 Deployment & Operational Notes

### Supabase Pooler — CRLF fix (Linux)
If the `supabase-pooler` container keeps restarting on Linux due to line ending issues:
```bash
sed -i 's/\r$//' supabase/docker/volumes/pooler/pooler.exs
```

### n8n LAN Access (HTTP)
For access within a local network without SSL:
```bash
# .env or docker-compose.override.yml
N8N_SECURE_COOKIE=false
```

### Missing Supabase Storage Variables
Required variables for the local storage engine:
```bash
REGION=us-east-1
STORAGE_TENANT_ID=stub
S3_PROTOCOL_ACCESS_KEY_ID=stub
S3_PROTOCOL_ACCESS_KEY_SECRET=stub
```

---

## 📊 Projects & Benchmarks

### GitHub Repository Classifier
An n8n workflow designed to iteratively fetch and categorize 1,000+ GitHub repositories:
* **Pipeline:** GitHub API → README extraction → Local LLM classification → Notion DB.
* **Results:** Identified ~150 specific workflow collections.
* **Benchmark:** Practical test of context window and token limits for long-running iterative tasks on local hardware.

---

## 💾 Backup Strategy

1.  **Folder Archive:** Compressed `tar.gz` of the full project directory (including volumes).
2.  **System Snapshots:** Timeshift for OS and NVIDIA driver stability.
3.  **Bare Metal:** Rescuezilla for full 1:1 disk cloning.

---

### Resources
* [coleam00/local-ai-packaged](https://github.com/coleam00/local-ai-packaged)
* [czlonkowski/n8n-mcp](https://github.com/czlonkowski/n8n-mcp)
* [czlonkowski/n8n-skills](https://github.com/czlonkowski/n8n-skills)



# Local AI Automation Infrastructure (n8n + Ollama)

Self-hosted automation stack integrated with a local LLM inference server, deployed for internal use in an engineering environment. This repository documents the configuration, deployment decisions, and operational notes for a fully local AI infrastructure — **zero external API calls for inference, no vendor lock-in.**

---

## 🛠 Stack

| Service | Role |
|---|---|
| **n8n** | Workflow automation engine (self-hosted) |
| **Ollama** | Local LLM inference server |
| **Supabase** | PostgreSQL database + pgvector for RAG |
| **Qdrant** | Vector store for semantic search |
| **Open WebUI** | Chat interface for local models |
| **Langfuse** | LLM observability and tracing |
| **Neo4j** | Knowledge graph (GraphRAG) |
| **SearXNG** | Private web search |
| **Portainer** | Docker container management |

All services run as Docker containers via [coleam00/local-ai-packaged](https://github.com/coleam00/local-ai-packaged).

---

## 🖥 Hardware Configuration

- **GPU:** NVIDIA GeForce RTX 5090 (32 GB VRAM)
- **CPU:** AMD Ryzen 7 9700X (Zen 5, AVX-512)
- **RAM:** 64 GB DDR5 6000 MHz
- **SSD:** Samsung 990 EVO Plus 1TB (NVMe, PCIe 4.0)
- **OS:** Ubuntu 24.04 LTS

---

## 🧠 Models (Ollama)

Models selected for dense architecture (non-MoE preference) optimized for 32 GB VRAM:

| Model | Quantization | VRAM | Notes |
|---|---|---|---|
| **Qwen3-32B** | Q6_K_L | ~27 GB | Primary reasoning model |
| **DeepSeek-R1-Distill-Qwen-32B** | Q6_K | ~27 GB | Full chain-of-thought |
| **Mistral-Small-3.2-24B** | Q8_0 | ~25 GB | Tool calling, structured output |
| **Gemma-3-27B-IT** | Q8_0 | ~29 GB | Classification, JSON tasks |
| **QwQ-32B** | Q6_K | ~25 GB | Reasoning specialist |
| **qwen2.5:7b-instruct** | Q4_K_M | ~5 GB | Lightweight auxiliary tasks |
| **nomic-embed-text** | F16 | <1 GB | Embeddings for RAG |

---

## 🔌 MCP Integration (Model Context Protocol)

n8n is connected to **Claude Code** via [n8n-mcp](https://github.com/czlonkowski/n8n-mcp), enabling natural language workflow creation and management.

### Claude Code Environment

Claude Code routes through local **Qwen3-Coder (30B)** running on the RTX 5090 instead of the Anthropic API:

```powershell
# Pointing Claude Code to local Ollama instance
$env:ANTHROPIC_AUTH_TOKEN="ollama"
$env:ANTHROPIC_BASE_URL="http://[INTERNAL_IP]:11434"
claude --model qwen3-coder:30b
```

### Server Configuration (Example)

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["-y", "n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "N8N_API_URL": "http://[INTERNAL_IP]:5678",
        "N8N_API_KEY": "YOUR_N8N_API_KEY"
      }
    }
  }
}
```

---

## 🚀 Deployment & Operational Notes

### Supabase Pooler — CRLF fix (Linux)
If the `supabase-pooler` container keeps restarting on Linux due to line ending issues:
```bash
sed -i 's/\r$//' supabase/docker/volumes/pooler/pooler.exs
```

### n8n LAN Access (HTTP)
For access within a local network without SSL:
```bash
# .env or docker-compose.override.yml
N8N_SECURE_COOKIE=false
```

### Missing Supabase Storage Variables
Required variables for the local storage engine:
```bash
REGION=us-east-1
STORAGE_TENANT_ID=stub
S3_PROTOCOL_ACCESS_KEY_ID=stub
S3_PROTOCOL_ACCESS_KEY_SECRET=stub
```

---

## 📊 Projects & Benchmarks

### GitHub Repository Classifier
An n8n workflow designed to iteratively fetch and categorize 1,000+ GitHub repositories:
* **Pipeline:** GitHub API → README extraction → Local LLM classification → Notion DB.
* **Results:** Identified ~150 specific workflow collections.
* **Benchmark:** Practical test of context window and token limits for long-running iterative tasks on local hardware.

---

## 💾 Backup Strategy

1.  **Folder Archive:** Compressed `tar.gz` of the full project directory (including volumes).
2.  **System Snapshots:** Timeshift for OS and NVIDIA driver stability.
3.  **Bare Metal:** Rescuezilla for full 1:1 disk cloning.

---

### Resources
* [coleam00/local-ai-packaged](https://github.com/coleam00/local-ai-packaged)
* [czlonkowski/n8n-mcp](https://github.com/czlonkowski/n8n-mcp)
* [czlonkowski/n8n-skills](https://github.com/czlonkowski/n8n-skills)
