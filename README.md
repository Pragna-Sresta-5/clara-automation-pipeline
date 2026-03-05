# Clara Automation Pipeline

A fully automated agent configuration and onboarding pipeline powered by AI, built with free-tier tools.

---

## Architecture Overview

### Pipeline A: Demo Call → Preliminary Agent

```
┌─────────────────────────────────────────────────────────────────┐
│                      PIPELINE A FLOW                             │
└─────────────────────────────────────────────────────────────────┘

  INPUT: Transcript Text (Ben's Electric Solutions)
    │
    ▼
  [TRIGGER] n8n Manual Trigger
    │
    ▼
  ┌───────────────────────────────────────────┐
  │ STEP 1: EXTRACTION                        │
  │ HTTP Request → Groq AI                    │
  │ (llama-3.3-70b-versatile)                 │
  │ Extract account info from transcript      │
  └───────────────────────────────────────────┘
    │
    ▼
  ┌───────────────────────────────────────────┐
  │ STEP 2: PARSE JSON                        │
  │ Code Node → Parse Groq response           │
  └───────────────────────────────────────────┘
    │
    ▼
  ┌───────────────────────────────────────────┐
  │ STEP 3: STORE MEMO                        │
  │ Supabase → accounts table                 │
  │ Fields: account_id, company_name, hours   │
  │ Version: 1                                │
  └───────────────────────────────────────────┘
    │
    ▼
  ┌───────────────────────────────────────────┐
  │ STEP 4: GENERATE AGENT SPEC               │
  │ HTTP Request → Groq AI                    │
  │ Create Retell agent spec from memo        │
  └───────────────────────────────────────────┘
    │
    ▼
  ┌───────────────────────────────────────────┐
  │ STEP 5: PARSE AGENT SPEC                  │
  │ Code Node → Parse JSON response           │
  └───────────────────────────────────────────┘
    │
    ▼
  ┌───────────────────────────────────────────┐
  │ STEP 6: UPDATE STORAGE                    │
  │ Supabase → Update agent_spec_json field   │
  └───────────────────────────────────────────┘
    │
    ▼
  ┌───────────────────────────────────────────┐
  │ STEP 7: NOTIFY                            │
  │ Gmail → Send configuration notification   │
  │ "New account: Ben's Electric v1"          │
  └───────────────────────────────────────────┘
    │
    ▼
  OUTPUT:
    ✓ memo.json saved in GitHub
    ✓ agent_spec.json saved in GitHub
    ✓ Data stored in Supabase
    ✓ Email notification sent
```

---

### Pipeline B: Onboarding → Agent Update

```
┌─────────────────────────────────────────────────────────────────┐
│                      PIPELINE B FLOW                             │
└─────────────────────────────────────────────────────────────────┘

  INPUT: Updated onboarding info (v2 data)
    │
    ▼
  [TRIGGER] n8n Manual Trigger
    │
    ▼
  ┌───────────────────────────────────────────┐
  │ STEP 1: UPDATE ACCOUNT                    │
  │ Supabase → accounts table                 │
  │ Version: 2 (from v1)                      │
  │ Update: memo_json, agent_spec_json        │
  └───────────────────────────────────────────┘
    │
    ▼
  ┌───────────────────────────────────────────┐
  │ STEP 2: SAVE CHANGELOG                    │
  │ Supabase → accounts_changelog table       │
  │ Log: v1 → v2 transition                   │
  │ Fields updated                            │
  └───────────────────────────────────────────┘
    │
    ▼
  OUTPUT:
    ✓ v2 memo.json saved in GitHub
    ✓ v2 agent_spec.json saved in GitHub
    ✓ changelog saved in GitHub
    ✓ Supabase updated to v2
```

---

## 💾 Storage Architecture

### GitHub Repository Structure

```
clara-automation-pipeline/
├── 📁 workflows/
│   ├── pipeline_a.json          (n8n workflow export)
│   └── pipeline_b.json          (n8n workflow export)
│
├── 📁 outputs/
│   └── 📁 accounts/
│       └── 📁 ben_electric/
│           ├── 📁 v1/
│           │   ├── memo.json
│           │   └── agent_spec.json
│           └── 📁 v2/
│               ├── memo.json
│               └── agent_spec.json
│
├── 📁 changelog/
│   └── ben_electric_changes.json
│
├── 📁 scripts/
│   └── retell_setup.md
│
└── README.md
```

### Supabase Database Schema

**accounts table:**
| Column | Type | Notes |
|--------|------|-------|
| `account_id` | TEXT | Primary Key (e.g., ben_electric_001) |
| `company_name` | TEXT | Company name |
| `version` | INT | Current version (1, 2, etc.) |
| `memo_json` | JSONB | Extracted account information |
| `agent_spec_json` | JSONB | Retell agent specification |
| `created_at` | TIMESTAMP | Creation timestamp |

**accounts_changelog table:**
| Column | Type | Notes |
|--------|------|-------|
| `id` | SERIAL | Primary Key |
| `account_id` | TEXT | Foreign Key to accounts |
| `from_version` | INT | Previous version |
| `to_version` | INT | New version |
| `changes` | JSONB | Changed fields |
| `created_at` | TIMESTAMP | Update timestamp |

---

## Tools Used (All Free Tier)

| Tool | Purpose
|------|---------
| **n8n Cloud** | Automation orchestrator
| **Groq AI** | LLM extraction (llama-3.3-70b)
| **Supabase** | Database storage & versioning 
| **GitHub** | File storage & version control 
| **Gmail** | Task notifications
| **Retell** | Agent spec (manual import)

---

## Quick Start Guide

### Prerequisites
- n8n Cloud account (free)
- Groq API key (free from console.groq.com)
- Supabase project (free)
- GitHub repository access

### Setup Steps

1. **Sign up at n8n.io**
   ```
   https://n8n.io/
   ```

2. **Import workflows**
   - Copy `workflows/pipeline_a.json` into n8n
   - Copy `workflows/pipeline_b.json` into n8n

3. **Configure environment variables**
   ```
   GROQ_API_KEY=your_key_here
   SUPABASE_URL=your_url_here
   SUPABASE_ANON_KEY=your_key_here
   ```

4. **Execute workflow**
   - Open Pipeline A in n8n
   - Click "Execute Workflow"

---

## 📝 How to Use

### Adding a New Transcript

1. Open **Pipeline A** in n8n
2. Navigate to the **HTTP Request** node
3. Replace the transcript text in the content field
4. Click **"Execute Workflow"**
5. Monitor execution in the n8n dashboard

### Updating Onboarding Info

1. Open **Pipeline B** in n8n
2. Provide updated onboarding information
3. Click **"Execute Workflow"**
4. Version automatically increments (v1 → v2)
5. Changelog is automatically saved

---

## Output Locations

All generated files are stored in the repository:

```
/outputs/accounts/<account_id>/v1/memo.json
/outputs/accounts/<account_id>/v1/agent_spec.json
/outputs/accounts/<account_id>/v2/memo.json
/outputs/accounts/<account_id>/v2/agent_spec.json
/changelog/ben_electric_changes.json
```

---

##  Known Limitations

- Timezone not always present in transcript
- Transfer number pending from client
- Gmail OAuth not configured (manual tracking)
- Currently configured for 1 account (Ben's Electric)
- Remaining 4 accounts pending transcript delivery

---

## Production Roadmap

**Features to implement with production access:**

- Retell API integration for programmatic agent creation
- Whisper integration for automatic audio transcription
- Automated Gmail/Slack notifications
- Batch processing for all 10 accounts
- Dashboard to view and manage all accounts
- Real-time synchronization
- Advanced error handling & retry logic

---

## Current Status

| Item | Status | Details |
|------|--------|---------|
| Account Configuration | ✅ Complete | ben_electric_001 |
| Version | ✅ v1 & v2 | Both versions working |
| Agent Spec | ✅ Generated | Stored in Supabase |
| GitHub Integration | ✅ Active | Files versioned |
| Changelog | ✅ Tracking | Change history recorded |

---

## Demo & Resources

**Loom Video Walkthrough:**
[https://www.loom.com/share/6d9925255bfb44c1bb98fff8a917db90](https://www.loom.com/share/6d9925255bfb44c1bb98fff8a917db90)

---

## Architecture Highlights

 **Zero-Cost Infrastructure** - All tools have free tiers  
 **Versioning System** - Track all account changes with changelogs  
 **AI-Powered** - Groq AI handles intelligent extraction and spec generation  
 **Version Control** - GitHub stores all outputs with full history  
 **Data Persistence** - Supabase provides reliable database backend  
 **Automated Workflow** - n8n orchestrates the entire pipeline  

---
