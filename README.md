# n8n-challenge
n8n + GitHub Advanced Challenge: AI-Powered Issue Triage

# GitHub Issues → Supabase → Slack (n8n Workflow)

This workflow connects GitHub, Supabase, and Slack to help track issues in a clear and organized way.  
It automatically collects new GitHub issues, summarizes them with AI, stores them in a Supabase database,  
and sends a message to Slack so the team stays updated in real time.

## How it Works
1. **Trigger (Cron)**  
   The workflow runs on a schedule (every 1 hour, but can be adjusted).  
   This makes sure new GitHub issues are checked regularly.

2. **GitHub Issues**  
   The workflow fetches the latest issues from the GitHub repository.  
   If no issues are found, an **If node** cleanly stops the workflow so later steps (AI, DB, Slack) are skipped.  

3. **AI Summarizer**  
   The issue body is summarized into one sentence.  
   - We also classify the issue as **Bug**, **Feature Request**, or **Question**.  
   - Assign a role like **Engineering**, **Support**, or **Sales**.  
   - Add a **sentiment** (Positive, Neutral, Negative).  
   - Generate short **tags** (e.g. `ui`, `api`, `critical`).  



4. **Supabase (Database)**  
   All results are stored in an `issues` table inside Supabase.  
   We use a `TEXT[]` column for tags, stored in the `{tag1,tag2}` format which is valid for PostgreSQL arrays.  
   This makes searching and filtering easier later.

5. **Slack Notification**  
  * A nicely formatted message is posted to Slack:
    ```
    🚨 New GitHub Issue for 'Engineering'
    Title: Issue title here
    Summary: One-line summary
    Category: Bug
    Sentiment: Negative
    <url|View Issue>
    ```

## AI Prompt
```
From the following GitHub issue body:

1. Generate a concise one-sentence summary.  
2. Categorize it as a type of issue (e.g., "Bug", "Feature Request", "Question").  
3. Assign it to a role:  
   - Engineering → for bugs and technical feature requests  
   - Sales → for pricing, demos, or business use  
   - Support → for how-to questions or user guidance  
4. Classify the sentiment of the issue body as "Positive", "Neutral", or "Negative".  
5. Generate 2–5 short lowercase tags that describe the issue (e.g., ["ui", "api", "critical"]).  

Respond ONLY as JSON with the following keys:  
summary, category, assigned_role, sentiment, tags.  

Issue body:  
{{$json["body"]}}
```

## Bonus Challenges Tackled
- ✅ Sentiment Analysis → AI node classifies Positive / Neutral / Negative.  
- ✅ Dynamic Tagging → tags are extracted and stored in a `TEXT[]` column in Supabase.  
- ✅ Smart Notifications → each role (Engineering, Support, Sales) is routed to its own Slack channel.  
