# Clara Automation Pipeline

ARCHITECTURE

PIPELINE A - Demo Call → Preliminary Agent
==========================================

[INPUT]
Transcript Text (Ben's Electric Solutions)
         |
         ↓
[TRIGGER]
n8n Manual Trigger
         |
         ↓
[STEP 1 - EXTRACTION]
HTTP Request → Groq AI (llama-3.3-70b-versatile)
Prompt: Extract account info from transcript
         |
         ↓
[STEP 2 - PARSE]
Code Node → Parse JSON response
         |
         ↓
[STEP 3 - STORE MEMO]
Supabase → Create row in accounts table
- account_id: ben_electric_001
- company_name, business_hours, pricing_info
- version: 1
         |
         ↓
[STEP 4 - GENERATE AGENT SPEC]
HTTP Request → Groq AI (llama-3.3-70b-versatile)
Prompt: Generate Retell agent spec from memo
         |
         ↓
[STEP 5 - PARSE AGENT SPEC]
Code Node → Parse JSON response
         |
         ↓
[STEP 6 - UPDATE STORAGE]
Supabase → Update row with agent_spec_json
         |
         ↓
[STEP 7 - NOTIFY]
Gmail → Send email notification
"New account configured: Ben's Electric v1"
         |
         ↓
[OUTPUT]
memo.json saved in GitHub
agent_spec.json saved in GitHub
Data stored in Supabase
Email notification sent


PIPELINE B - Onboarding → Agent Update
=======================================

[INPUT]
Updated onboarding info (v2 data)
         |
         ↓
[TRIGGER]
n8n Manual Trigger
         |
         ↓
[STEP 1 - UPDATE ACCOUNT]
Supabase → Update accounts table
- version: 2
- memo_json: updated
- agent_spec_json: updated v2
         |
         ↓
[STEP 2 - SAVE CHANGELOG]
Supabase → Create row in accounts_changelog
- from_version: 1
- to_version: 2
- changes: list of updated fields
         |
         ↓
[OUTPUT]
v2 memo.json saved in GitHub
v2 agent_spec.json saved in GitHub
changelog saved in GitHub
Supabase updated to v2


STORAGE ARCHITECTURE
====================

GitHub Repo
├── /workflows
│   ├── pipeline_a.json
│   └── pipeline_b.json
├── /outputs
│   └── /accounts
│       └── /ben_electric
│           ├── /v1
│           │   ├── memo.json
│           │   └── agent_spec.json
│           └── /v2
│               ├── memo.json
│               └── agent_spec.json
├── /changelog
│   └── ben_electric_changes.json
├── /scripts
│   └── retell_setup.md
└── README.md

Supabase Database
├── accounts table
│   ├── account_id (primary key)
│   ├── company_name
│   ├── version
│   ├── memo_json
│   ├── agent_spec_json
│   └── created_at
└── accounts_changelog table
    ├── id
    ├── account_id
    ├── from_version
    ├── to_version
    ├── changes
    └── created_at


TOOLS USED (ALL FREE)
=====================
- n8n Cloud → Automation orchestrator
- Groq AI → LLM extraction (free tier)
- Supabase → Database storage (free tier)
- GitHub → File storage and versioning
- Gmail → Task notifications
- Retell → Agent spec (manual import)

## How to Run Locally
1. Sign up at n8n.io (free cloud version)
2. Import `/workflows/pipeline_a.json`
3. Import `/workflows/pipeline_b.json`
4. Add environment variables (see below)
5. Click "Execute Workflow"

## Environment Variables Needed
- `GROQ_API_KEY` - Get from console.groq.com (free)
- `SUPABASE_URL` - Your Supabase project URL
- `SUPABASE_ANON_KEY` - Your Supabase anon key

## How to Plug in Dataset Files
1. Open Pipeline A in n8n
2. Go to HTTP Request node
3. Replace transcript text in the content field
4. Click Execute Workflow

## Where Outputs Are Stored
- `/outputs/accounts/<account_id>/v1/memo.json`
- `/outputs/accounts/<account_id>/v1/agent_spec.json`
- `/outputs/accounts/<account_id>/v2/memo.json`
- `/outputs/accounts/<account_id>/v2/agent_spec.json`
- `/changelog/`

## Known Limitations
- Timezone not always present in transcript
- Transfer number pending from client
- Gmail OAuth not configured - tracking done manually
- Currently configured for 1 account (Ben's Electric)
- Remaining 4 accounts pending transcript delivery

## What I Would Improve With Production Access
- Retell API integration for programmatic agent creation
- Whisper for automatic audio transcription
- Automated Gmail/Slack notifications
- Batch processing for all 10 accounts
- Dashboard to view all accounts

## Task Tracking
- Account configured: ben_electric_001
- Status: Complete
- Version: v1 and v2
- Agent spec generated and stored in Supabase

## Loom Video
[https://www.loom.com/share/6d9925255bfb44c1bb98fff8a917db90]
