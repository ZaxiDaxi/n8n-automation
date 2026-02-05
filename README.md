# 🤖 n8n-automation

This repo contains **4 n8n workflows** that connect **Gmail, Telegram, LLMs, and Google Sheets** to:
- triage emails
- draft replies
- manage edits

📦 **Deployment folder:** [deployment](deployment/)  
⬇️ **Download deployment files:** [n8n_V1.zip](n8n_V1.zip)

---

## 🧩 Workflows

### 1) Edit Session V1 (`Edit Session V1.json`)
- Runs every minute
- Reads `edit_sessions` in Google Sheets and finds rows in `waiting_edit` that expired
- Marks the session as **Consumed**, updates expiration time
- Notifies the user in Telegram that edit mode ended

### 2) Email to Telegram V1 (`Email to Telegram V1.json`)
- Triggered by Gmail polling every minute
- Fetches full email
- If attachments exist (PDF/image), summarizes them via OpenAI
- Builds cleaned email context
- Classifies email into: `IGNORE`, `FYI`, or `ACTION NEEDED`
- Sends results to Telegram
- If `ACTION NEEDED`:
  - drafts a reply with LLM
  - creates a Gmail draft
  - posts inline buttons: **Approve / Edit / History / Style**
- Logs drafts + Telegram metadata into Google Sheets

### 3) Fetch Old Email V1 (`Fetch Old Email V1.json`)
- Triggered by Telegram “fetch unread” button
- Pulls up to 20 unread Gmail messages
- Processes them one by one and marks them as read
- Summarizes attachments and classifies emails
- Posts summaries to Telegram
- If `ACTION NEEDED`:
  - drafts reply
  - creates Gmail draft
  - logs results in Google Sheets

### 4) Telegram to Email V1 (`Telegram to Email V1.json`)
- Handles Telegram messages + callback buttons (Approve / Edit / History / Style)
- Supports voice edits:
  - download voice
  - convert to WAV
  - transcribe with Deepgram
- Creates + validates 5-minute edit sessions
- Applies edit instructions via LLM
- Updates Gmail drafts
- Cleans up old Telegram messages
- Manages writing style preferences and saves them for future drafts

---

## 🗃️ Data Stores
Google Sheets is used to store:
- edit sessions
- draft metadata
- Telegram message IDs
- writing preferences
