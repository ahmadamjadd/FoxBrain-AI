<p align="center">
  <img src="https://img.shields.io/badge/status-under%20construction-orange?style=for-the-badge" alt="Under Construction" />
</p>

<h1 align="center">🦊 FoxBrain AI</h1>
<h3 align="center"><em>RAG-Powered AI Agent & Knowledge Assistant for Team Foxtrot GIKI</em></h3>

<p align="center">
  <img src="https://img.shields.io/badge/n8n-Workflow%20Automation-FF6D5A?style=flat-square&logo=n8n&logoColor=white" />
  <img src="https://img.shields.io/badge/Pinecone-Vector%20DB-00C7B7?style=flat-square" />
  <img src="https://img.shields.io/badge/Google%20Gemini-LLM-4285F4?style=flat-square&logo=google&logoColor=white" />
  <img src="https://img.shields.io/badge/HuggingFace-Embeddings-FFD21E?style=flat-square&logo=huggingface&logoColor=black" />
  <img src="https://img.shields.io/badge/React-Frontend-61DAFB?style=flat-square&logo=react&logoColor=black" />
  <img src="https://img.shields.io/badge/AWS-Cloud-FF9900?style=flat-square&logo=amazonaws&logoColor=white" />
</p>

<p align="center">
  <b>🚧 This project is actively under construction. Features and documentation may change frequently. 🚧</b>
</p>

---

## 📖 Table of Contents

- [The Problem](#-the-problem)
- [The Solution](#-the-solution)
- [Architecture Overview](#-architecture-overview)
- [Project Structure](#-project-structure)
- [Data Ingestion Pipeline](#-data-ingestion-pipeline)
  - [Parent Workflow](#parent-workflow--foxtrot_dataflow_parent)
  - [Child Workflow](#child-workflow--foxtrot_dataflow_child)
- [RAG Agent](#-rag-agent)
- [Tech Stack](#-tech-stack)
- [Setup & Installation](#-setup--installation)
- [Roadmap & Next Steps](#-roadmap--next-steps)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🎯 The Problem

**Team Foxtrot** is a multidisciplinary engineering team at **GIKI (Ghulam Ishaq Khan Institute)** with subteams spanning Software Development and Embedded Systems (SDES). The team maintains a growing number of repositories containing Python scripts, Lua autopilot code, Jupyter notebooks, drone waypoint files, configuration files, and extensive documentation.

Every year, **freshers (new recruits)** face a recurring set of challenges:

| Pain Point | Description |
|---|---|
| **Information Overload** | Dozens of repositories with thousands of files — no single entry point to understand what's going on. |
| **Lack of Technical Depth** | Freshers often don't have the background to understand the projects at first glance, even after senior members explain them. |
| **Fear of Asking Again** | Many freshers feel **shy or hesitant** to ask seniors to re-explain concepts they didn't fully grasp the first time. |
| **Knowledge Silos** | Tribal knowledge lives in the heads of senior members and gets lost when they graduate. |
| **Onboarding Bottleneck** | Seniors spend significant time repeatedly answering the same foundational questions every recruitment cycle. |

This creates a **knowledge gap** that slows down onboarding, reduces productivity, and makes it harder for new members to contribute meaningfully to ongoing projects.

---

## 💡 The Solution

**FoxBrain** is an AI-powered **Retrieval-Augmented Generation (RAG) chatbot** that serves as an always-available, intelligent knowledge assistant for Team Foxtrot.

Instead of asking a senior (and feeling awkward about it), freshers can simply **ask FoxBrain**:

> *"How does the PID controller work in our drone autopilot?"*
> *"What does the `mission_planner.py` script do?"*
> *"Explain the communication protocol between the ground station and the drone."*

FoxBrain will:
1. **Search** through all of Team Foxtrot's GitHub repositories
2. **Retrieve** the most relevant code snippets, documentation, and notebooks
3. **Generate** a clear, context-aware answer using Google Gemini LLM
4. **Remember** conversation context for natural follow-up questions

### Key Benefits

- 🕐 **Available 24/7** — No need to wait for a senior to be free
- 🤫 **No Judgment** — Ask the same question 100 times without feeling shy
- 📚 **Always Up-to-Date** — Automatically ingests the latest code from GitHub
- 🧠 **Context-Aware** — Understands code, docs, and notebooks in depth
- 💬 **Conversational** — Maintains chat history for natural dialogue

---

## 🏗 Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                        FoxBrain Architecture                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   ┌──────────┐    ┌──────────┐    ┌──────────────────────────┐     │
│   │  GitHub   │───▶│  n8n     │───▶│  Pinecone Vector DB      │     │
│   │  Repos    │    │  Pipeline│    │  (Index: foxtrot)        │     │
│   └──────────┘    └──────────┘    └────────────┬─────────────┘     │
│                                                 │                   │
│                                                 ▼                   │
│   ┌──────────┐    ┌──────────┐    ┌──────────────────────────┐     │
│   │  React   │───▶│  RAG     │───▶│  Google Gemini LLM       │     │
│   │  Frontend│    │  Agent   │    │  + Vector Store Retrieval │     │
│   └──────────┘    └──────────┘    └──────────────────────────┘     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

The system is composed of **two main pillars**:

1. **Data Ingestion Pipeline** — Crawls all GitHub repositories, extracts relevant files, and embeds them into Pinecone
2. **RAG Agent** — Accepts user questions, retrieves relevant context from Pinecone, and generates answers using Google Gemini

---

## 📁 Project Structure

```
SDES_Chatbot/
│
├── 📄 README.md                              # You are here
├── 📄 LICENSE                                 # Project license
├── 🤖 RAGAgent.json                          # n8n RAG Agent workflow
│
└── 📂 Dataflow/
    ├── 🔄 Foxtrot_DataFlow_Parent.json       # Parent orchestrator workflow
    └── 🔄 Foxtrot_DataFlow_Child.json        # Per-repo processing workflow
```

---

## 🔄 Data Ingestion Pipeline

The data pipeline is built using **n8n** (workflow automation platform) and follows a **Parent-Child architecture** for processing all repositories in the `Team-Foxtrot-GIKI` GitHub organization.

### Parent Workflow — `Foxtrot_DataFlow_Parent`

The orchestrator that drives the entire ingestion process.

```
Manual Trigger
      │
      ▼
Fetch All Repos (GitHub API)
      │
      ▼
Loop Over Items (batch size = 1)
      │
      ▼
Call Child Workflow (per repo)
      │
      ▼
   (repeat)
```

| Property | Value |
|---|---|
| **Organization** | `Team-Foxtrot-GIKI` |
| **Processing Mode** | Sequential (one repo at a time) |
| **Child Workflow** | `Foxtrot_DataFlow_Child` |
| **Error Handling** | `retryOnFail` enabled, `alwaysOutputData` for empty repos |
| **Est. Runtime** | ~5–10 min per repo |

### Child Workflow — `Foxtrot_DataFlow_Child`

Processes a **single repository** end-to-end: from file discovery to vector embedding.

```
Receive Repo from Parent
        │
        ▼
List Repo Contents (GitHub API)
        │
        ▼
  Route by Type ─────────────────┐
  │ (dir)                        │ (file)
  ▼                              ▼
Dir Exclusion Filter      File Type Filter
  │                              │
  ▼                              ▼
 (loop back to             Fetch File Content
  List Repo Contents)            │
                                 ▼
                        Embed into Pinecone
```

#### Directory Exclusion Filter

The following directories are **automatically skipped** to avoid noise:

```
.git  ·  .venv  ·  __pycache__  ·  site-packages  ·  node_modules
dist-info  ·  PackageCache  ·  Artifacts  ·  Logs  ·  UserSettings
assets  ·  Plugins  ·  Library  ·  mavlink  ·  .github
```

#### Supported File Types

| Extension | Type |
|---|---|
| `.py` | Python source code |
| `.md` | Markdown documentation |
| `.lua` | Lua scripts (drone autopilot) |
| `.txt` | Plain text files |
| `.yaml` / `.yml` | Configuration files |
| `.ipynb` | Jupyter Notebooks |
| `.waypoints` | Drone waypoint files |

All other file types (images, binaries, `.docx`, `.json`, etc.) are discarded.

#### Vector Storage

| Property | Value |
|---|---|
| **Vector Database** | Pinecone |
| **Index Name** | `foxtrot` |
| **Namespace Strategy** | One namespace per repository |
| **Embedding Model** | HuggingFace Inference API |
| **Document Loader** | Binary text loader (raw file content) |

---

## 🤖 RAG Agent

The RAG (Retrieval-Augmented Generation) Agent is the **conversational interface** that freshers interact with. It is built as an n8n AI Agent workflow.

### How It Works

```
User sends a message
        │
        ▼
  Chat Trigger (webhook)
        │
        ▼
    AI Agent ◄──── Google Gemini Chat Model (LLM)
        │     ◄──── Simple Memory (conversation buffer)
        │     ◄──── Vector Store Tool (Pinecone retrieval)
        │
        ▼
  Generated Response
```

### Components

| Component | Technology | Purpose |
|---|---|---|
| **LLM** | Google Gemini | Generates natural language answers from retrieved context |
| **Memory** | Buffer Window Memory | Maintains conversation history for contextual follow-ups |
| **Retrieval Tool** | Pinecone Vector Store | Searches embedded codebase for relevant snippets |
| **Embedding Model** | Google Gemini Embeddings | Converts queries into vectors for similarity search |
| **Secondary LLM** | Google Gemini (for Vector Store) | Powers the vector store question-answering tool |

### Vector Store Tool Description

The agent's retrieval tool is configured with the following instruction:

> *"Use this tool to search the Team Foxtrot repository. It contains Python source code, Jupyter Notebooks (.ipynb), and project documentation. Use it to answer technical questions about function implementations, logic flows, or the project's structure."*

This ensures the agent knows **when and how** to use the vector store to ground its answers in actual code and documentation.

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| **Workflow Automation** | [n8n](https://n8n.io/) |
| **Large Language Model** | [Google Gemini](https://deepmind.google/technologies/gemini/) |
| **Vector Database** | [Pinecone](https://www.pinecone.io/) |
| **Embeddings (Pipeline)** | [HuggingFace Inference API](https://huggingface.co/inference-api) |
| **Embeddings (Agent)** | Google Gemini Embeddings |
| **Source Control** | [GitHub](https://github.com/Team-Foxtrot-GIKI) |
| **Frontend** | [React](https://react.dev/) *(coming soon)* |
| **Cloud Platform** | [AWS](https://aws.amazon.com/) *(coming soon)* |

---

## ⚙️ Setup & Installation

### Prerequisites

- [n8n](https://n8n.io/) instance (self-hosted or cloud)
- [Pinecone](https://www.pinecone.io/) account with an index named `foxtrot`
- [Google Gemini API](https://ai.google.dev/) key
- [HuggingFace](https://huggingface.co/) API key
- GitHub Personal Access Token with `repo` scope for `Team-Foxtrot-GIKI`

### Step-by-Step Setup

1. **Import the Child Workflow**
   - Open your n8n instance
   - Import `Dataflow/Foxtrot_DataFlow_Child.json`
   - Copy the Workflow ID from the URL bar

2. **Import the Parent Workflow**
   - Import `Dataflow/Foxtrot_DataFlow_Parent.json`
   - Open the *"Call Foxtrot_DataFlow_Child"* node → paste the Child Workflow ID

3. **Import the RAG Agent**
   - Import `RAGAgent.json`

4. **Configure Credentials**

   | Credential | Where to Configure |
   |---|---|
   | GitHub API Token | Parent & Child workflows |
   | Pinecone API Key | Child workflow & RAG Agent |
   | Google Gemini API Key | RAG Agent |
   | HuggingFace API Key | Child workflow |

5. **Run the Data Pipeline**
   - Open the Parent workflow → click **Execute Workflow**
   - Wait for all repositories to be processed and embedded

6. **Test the RAG Agent**
   - Open the RAG Agent workflow → click **Chat** in the trigger node
   - Ask a question about Team Foxtrot's projects!

---

## 🗺 Roadmap & Next Steps

### 🔜 In Progress

- [ ] **React Frontend** — A polished web UI for the chatbot, replacing the raw n8n chat interface. Built with React for a seamless conversational experience.

### 🔮 Planned Features

- [ ] **CI/CD Embedding Pipeline** — Automated GitHub webhook integration so that every new commit/push automatically triggers re-embedding of changed files into Pinecone. No manual pipeline runs needed.

- [ ] **AWS Bedrock Integration** — Migrate from Google Gemini to **AWS Bedrock** for LLM inference, enabling access to models like Claude, Titan, and Llama with enterprise-grade security and scalability.

- [ ] **AWS Lambda Functions** — Serverless compute for the embedding pipeline, eliminating the need for an always-on n8n instance.

- [ ] **AWS S3 Document Store** — Persistent storage for raw documents, PDFs, and meeting notes that can also be ingested into the vector store.

- [ ] **Amazon API Gateway** — A secure, scalable API layer between the React frontend and the RAG backend.

- [ ] **AWS CloudWatch Monitoring** — Observability and logging for pipeline runs, agent queries, and error tracking.

- [ ] **Amazon Cognito Authentication** — Role-based access control so only verified Team Foxtrot members can access the chatbot.

- [ ] **Multi-Source Ingestion** — Expand beyond GitHub to ingest Google Drive docs, Notion pages, Confluence wikis, and Slack messages.

- [ ] **Feedback Loop** — Allow users to upvote/downvote responses to continuously improve retrieval quality.

- [ ] **Admin Dashboard** — A management panel to monitor pipeline health, view embedding statistics, and manage the knowledge base.

---

## 🤝 Contributing

This project is maintained by the **SDES (Software Development & Embedded Systems) subteam** of **Team Foxtrot, GIKI**.

If you're a team member and want to contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the terms specified in the [LICENSE](LICENSE) file.

---

<p align="center">
  <b>Built with ❤️ by Team Foxtrot GIKI — SDES Subteam</b>
  <br/>
  <em>Because no question is a dumb question, especially when a bot is answering it. 🦊</em>
</p>
