# n8n-automation

This repo contains four n8n workflows that connect Gmail, Telegram, LLMs, and Google Sheets to triage emails, draft replies, and manage edits.

Deployment folder: [deployment](deployment/)

**Workflows**

1. **Edit Session V1** (`Edit Session V1.json`)
- Runs every minute.
- Reads `edit_sessions` in Google Sheets and finds rows in `waiting_edit` mode that have expired.
- Marks the session as `Consumed`, updates the expiration time, and notifies the user in Telegram that edit mode ended.

2. **Email to Telegram V1** (`Email to Telegram V1.json`)
- Triggered by Gmail polling every minute.
- Fetches the full email, summarizes attachments (PDF/image) via OpenAI when present, and builds cleaned email context.
- Classifies each email into `IGNORE`, `FYI`, or `ACTION NEEDED`.
- Routes results to Telegram; for `ACTION NEEDED` it drafts a reply with LLMs, creates a Gmail draft, and posts with inline buttons (Approve/Edit/History/Style).
- Logs drafts and Telegram metadata in Google Sheets.

3. **Fetch Old Email V1** (`Fetch Old Email V1.json`)
- Triggered by a Telegram "fetch unread" button.
- Pulls up to 20 unread Gmail messages, processes them one by one, and marks them as read.
- Summarizes attachments, classifies, and posts summaries to Telegram.
- For `ACTION NEEDED`, drafts a reply and creates a Gmail draft, then logs results to Google Sheets.

4. **Telegram to Email V1** (`Telegram to Email V1.json`)
- Processes Telegram messages and callback buttons (Approve/Edit/History/Style).
- Supports voice edits by downloading voice messages, converting to WAV, and transcribing with Deepgram.
- Creates and validates 5-minute edit sessions, applies edit instructions via LLM, updates Gmail drafts, and cleans up old Telegram messages.
- Manages writing style preferences through a Telegram menu and persists them for future drafts.

**Data Stores**
- Google Sheets is used to persist edit sessions, draft metadata, Telegram message IDs, and writing preferences.
