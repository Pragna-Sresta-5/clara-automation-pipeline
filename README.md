# Clara Automation Pipeline

## Architecture and Data Flow
1. Transcript input → n8n Pipeline A
2. Groq AI extracts account memo JSON
3. Groq AI generates Retell agent spec JSON
4. Data stored in Supabase
5. Outputs saved in GitHub repo
6. Pipeline B updates account to v2 with changelog

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
