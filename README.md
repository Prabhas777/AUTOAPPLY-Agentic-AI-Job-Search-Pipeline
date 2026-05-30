🚀 Autonomous Agentic Job Search Pipeline

<img width="1598" height="768" alt="image" src="https://github.com/user-attachments/assets/ec0daeb3-9faa-4c8a-960d-169aca37bd24" />


An end-to-end, LLM-powered orchestration pipeline that scrapes, filters, scores,
and dynamically generates pixel-perfect LaTeX resumes without hallucination.

🛑 The Problem: The "STEM OPT" Job Hunt Grind

As an international student in the US on a STEM OPT visa, the job search is
fundamentally broken. It requires navigating three massive hurdles:

1.  Visa Constraints: I am legally prohibited from accepting 1099, Independent
    Contractor, or Corp-to-Corp (C2C) roles. I also cannot apply for defense
    roles requiring Active Clearances.
2.  The ATS Black Hole: To pass corporate Applicant Tracking Systems (ATS), your
    resume must perfectly mirror the job description's keywords.
3.  Time: Manually reading JDs, cross-referencing visa requirements, and
    tailoring a resume takes ~45 minutes per application.

The Solution: I stopped manually applying and treated my job search like an
enterprise engineering problem. I built an autonomous AI workflow to do it for
me while I sleep.

🛠️ Tech Stack

  - Orchestration: n8n (Automated cron-based workflows)
  - LLMs & Multi-Agent Systems: Google Vertex AI (Gemini 2.5 Pro), LangChain
  - Data Processing: JavaScript, Regex, robust JSON parsing
  - Headless Scraping: Jina AI REST API
  - Document Compilation: LaTeX, YtoTech API
  - Cloud & Delivery: Google Drive API, Google Sheets API, Gmail OAuth

🧠 System Architecture: Step-by-Step

Here is exactly how this pipeline evaluates opportunities and generates
applications.

Phase 1: Headless Sourcing & Scraping

The workflow wakes up every hour during US business hours.

  - Smart Sourcing: A custom JavaScript node generates a dynamic LinkedIn Search
    URL, appending strict anti-spam operators (NOT Turing NOT BairesDev) and
    restricting the search window to jobs posted in the last hour (f_TPR=r3600).
  - Jina AI Ingestion: To bypass LinkedIn's aggressive anti-bot firewalls, the
    pipeline feeds the URLs to the r.jina.ai API, which extracts clean,
    Markdown-formatted text of the Job Description.

Phase 2: The Deterministic Firewall (Cost Optimization)

Before passing anything to an expensive LLM, the data hits a deterministic
JavaScript Regex rules engine.

  - The Visa Check: It instantly drops any job containing strings like "U.S.
    Citizen", "Top Secret", "1099", "C2C", or "No Sponsorship".
  - The Padding Check: It drops jobs that LinkedIn's algorithm sneakily injects
    (like "Promoted" ads or jobs posted "weeks ago").
  - Impact: This drops 90% of junk jobs for $0.00 in compute costs, ensuring LLM
    tokens are only spent on high-quality leads.

Phase 3: Agent 1 (The ATS Gatekeeper)

If a job survives the firewall, it is passed to Google Vertex AI (Gemini 2.5
Pro).

  - The Task: Agent 1 parses the Job Description and mathematically scores it (0
    to 100) against my actual profile.
  - The Rubric: It scores based on Core Tech Stack (40 pts), Applied Engineering
    Impact (30 pts), Years of Experience (20 pts), and Education (10 pts).
  - Only jobs that score a >70% Match are allowed to proceed.

Phase 4: Agent 2 (The Anti-Hallucination Curator)

If you tell a generative AI to "rewrite my resume to match this job," it will
lie to get a 100% match. To solve this, I built the Master Resume Bank Strategy.

  - I maintain a static, 5-page document containing every true project, job, and
    metric I have ever achieved.
  - The Task: Agent 2 acts as a Space-Constrained Curator. It is strictly
    forbidden from writing new text. It must read the Job Description, scan
    my 5-page Master Bank, and mathematically select:
      - Exactly 3 Jobs
      - Exactly 2 Projects
      - Exactly 3 Bullets per item
      - Exactly 15-20 Skills categorized into precise buckets.
  - Impact: A perfectly tailored application with 0% hallucination risk that
    safely passes corporate background checks.

Phase 5: Programmatic PDF Compilation & Cloud Delivery

  - JSON to LaTeX: Agent 2 outputs a highly structured JSON object. A custom
    JavaScript parser intercepts this, runs a brace-counting algorithm to strip
    conversational AI artifacts, escapes all dangerous characters (like & and
    %), and maps the data into a pristine LaTeX template.
  - Compilation: An HTTP POST request sends the raw LaTeX to an external
    compiler API, which returns a binary PDF file.
  - Delivery: The workflow uploads the tailored PDF to Google Drive, logs the
    job details and Match Score in a Google Sheet, and triggers a Gmail
    notification to my phone.

🚀 How to Import and Use This Workflow

Prerequisites

1.  An active n8n instance (Cloud or Self-Hosted).
2.  A Google Cloud Console account (for Vertex AI API and Google Workspace
    OAuth).
3.  A free API key from Jina AI.

Installation

1.  Clone this repository or copy the contents of workflow.json.
2.  Open your n8n workspace, navigate to your canvas, and paste (Ctrl+V /
    Cmd+V).
3.  Connect your Google Sheets, Google Drive, and Gmail credentials inside n8n.
4.  Add your Google Vertex AI credentials to the LangChain Agent nodes.
5.  Add your Jina AI API key to the HTTP Request Header Parameters as Bearer
    YOUR_API_KEY.

Customization

  - Your Master Resume: You must provide your own factual background. Paste your
    resume text into the Edit Fields node located before the AI Agents (or
    hardcode it directly into the Agent prompt).
  - Search Parameters: Update the initial Google Sheet (or hardcode the
    JavaScript LinkedIn Search URL node) with your target titles and locations.

Built by Prabhas — Let's connect on LinkedIn: https://www.linkedin.com/in/prabhas-sama1/.
