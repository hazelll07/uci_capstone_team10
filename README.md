# Reddit Toxicity Dashboard & Repair Agent (Hazel's Starter Kit)

This is a minimal, local-first prototype for:
1) **Streaming** Reddit comments (via PRAW) into a local SQLite DB.
2) **Classifying** toxicity with a Hugging Face transformer pipeline.
3) **Repair Agent**: simple normalization + slang lexicon to improve recall; propose new terms from misclassifications.
4) **Dashboard**: Streamlit app for live monitoring + quick repair updates.

> Works on CPU. You can later swap in LoRA adapters + GPU if needed.

## Quickstart

### 0) Python env
```bash
python -m venv .venv
source .venv/bin/activate  # on Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### 1) Set Reddit API creds
Copy `.env.example` to `.env` and fill in values from your Reddit app:
```
REDDIT_CLIENT_ID=...
REDDIT_CLIENT_SECRET=...
REDDIT_USER_AGENT=uci-team10-toxicity-dashboard
SUBREDDITS=all   # comma-separated list (e.g., "all" or "AskReddit,news")
```

### 2) Start the streamer (terminal 1)
```bash
python app/reddit_stream.py
```

### 3) Launch the dashboard (terminal 2)
```bash
streamlit run app/streamlit_app.py
```

### 4) Optional: test local classification
```bash
python -m app.classifier "You are awful!"
```

## Notes
- First run will download a small transformer model; subsequent runs are faster.
- The dashboard auto-refreshes every few seconds. Use the **Repair** tab to:
  - Add slang/leet mappings to `lexicon.json`
  - Re-run classification on recent rows to see impact
- Data persists locally in `events.db` (SQLite). Safe to delete if you want a clean slate.

## Roadmap (easy → advanced)
- [x] CPU baseline, simple lexicon repair
- [ ] Add per-subreddit heatmap + trend lines
- [ ] Feedback buttons (Correct / Incorrect) to collect labels
- [ ] Batch nightly "ADAPT": fine-tune with LoRA on flagged/confirmed samples
- [ ] Deploy: ECS Fargate + S3 (logs) + RDS/SQLite → Postgres
