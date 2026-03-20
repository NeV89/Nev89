# 📚 n8n & AI Ecosystem Intelligence Hub

This project is an automated research engine designed to solve the "information overload" in the AI automation space. Using local LLMs, I have mapped and categorized the entire n8n ecosystem.

## 🚀 The Core Project: GitHub Repository Classifier
I built a custom n8n engine that performed a massive iterative analysis of the open-source landscape.

### Technical Workflow
1. **Discovery:** The engine queries the GitHub API for any repository mentioning "n8n".
2. **Knowledge Extraction:** For each repo, the system fetches the `README.md` and metadata.
3. **Local Inference:** Data is passed to a local **Qwen3-32B** model running on my **RTX 5090** station.
4. **Semantic Classification:** The AI identifies tech stacks, unique nodes, and automation patterns.
5. **Output:** Results are ustructured into a hierarchical Notion database.

### Key Results
* **1,000+ Repositories** processed and verified.
* **40,000+ Automation Templates** identified across top-tier collections.
* **5-Tier Logic implementation** for AI Sales systems (Discovery, Enrichment, Intelligence, Generation, Lifecycle).

## 🗂️ Categorization Framework
The resources are organized into a logical roadmap for engineers:
* **Core & Apps:** Base n8n installations and enterprise unlocks.
* **Agentic Frameworks:** Autonomous browser operators (Skyvern, Airtop) and multi-agent orchestrators.
* **RAG Systems:** Vector database integrations (Qdrant, Pinecone, Supabase) and hybrid search patterns.
* **MCP Infrastructure:** A dedicated index of Model Context Protocol servers and bridges.

---
*Back to [Main README](../README.md)*
