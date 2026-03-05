# Clara Automation Pipeline

## What This Pipeline Does
Automatically processes client onboarding transcripts and generates:
- Account memo JSON (extracted client info)
- Retell AI agent spec JSON
- Stores everything in Supabase
- Tracks changes between versions

## How to Run
1. Open n8n
2. Import `/workflows/pipeline_a.json`
3. Add your environment variables
4. Paste transcript into HTTP Request node
5. Click "Execute Workflow"

## Environment Variables Needed
- `GROQ_API_KEY` - Get from console.groq.com
- `SUPABASE_URL` - Your Supabase project URL
- `SUPABASE_ANON_KEY` - Your Supabase anon key

## Output Structure
```
/outputs/accounts/ben_electric/v1/memo.json
/outputs/accounts/ben_electric/v1/agent_spec.json
/outputs/accounts/ben_electric/v2/memo.json
/outputs/accounts/ben_electric/v2/agent_spec.json
/changelog/ben_electric_changes.json
```

## Known Limitations
- Timezone not always in transcript
- Transfer number pending from client
- Audio transcription not yet automated

## Loom Video
[Add your Loom video link here]
