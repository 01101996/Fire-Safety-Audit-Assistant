# 🔥 Fire Safety Audit Assistant

A Streamlit app that helps you conduct fire safety audits. It uses **RAG (retrieval-augmented generation)** over your own reference documents (standards, regulations, company SOPs) to identify the relevant clauses for each non-compliance, suggest corrective actions, and produce a dashboard and a Word report.

## Features

- **Audit checklist**: pre-built fire safety checklist (detection, extinguishers, escape routes, emergency lighting, compartmentation, electrical, housekeeping, emergency management). Editable; add your own rows.
- **RAG non-compliance analysis**: for each non-compliant item, retrieves the most relevant clauses from your reference documents and drafts the clause reference, finding, risk rating and corrective action.
- **Ask the standards**: chat with your reference documents, with answers citing document and page.
- **Dashboard**: compliance score, status by category, risk distribution, open and overdue corrective actions.
- **Report**: downloadable Word (.docx) report. Audit can be saved to CSV and resumed later.
- **Works without an API key**: falls back to showing the most relevant clauses (retrieval only). Add an Anthropic API key for written analysis.

## How it works

1. Reference documents (PDF, DOCX, TXT) are split into overlapping passages and indexed with TF-IDF (lightweight, no model downloads, suits Streamlit Cloud's memory limits).
2. For each non-compliant checkpoint, the top passages are retrieved and sent to Claude with instructions to use **only** those excerpts.
3. Results are written back into the checklist for you to review and edit.

## Repository structure

```
.
├── app.py
├── requirements.txt
├── README.md
└── reference_docs/        # optional: put your PDFs/DOCX/TXT here
```

## Run locally

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
streamlit run app.py
```

Optional: set your key as an environment variable (`ANTHROPIC_API_KEY`) or enter it in the sidebar.

## Deploy on Streamlit Community Cloud

1. Create a GitHub repository and upload `app.py`, `requirements.txt`, `README.md` (and `reference_docs/` if you want documents bundled).
2. Go to <https://share.streamlit.io>, sign in with GitHub, click **Create app**, choose the repo, branch `main`, main file `app.py`.
3. Under **Advanced settings → Secrets**, add:
   ```toml
   ANTHROPIC_API_KEY = "your-key-here"
   ```
4. Click **Deploy**.

> **Never commit your API key** to GitHub. Use Streamlit secrets.
> If your reference documents are confidential, keep the repository **private** or upload documents through the sidebar at runtime instead of committing them.

## Using reference documents

- **Upload in the sidebar** each session, or
- **Commit to `reference_docs/`** so they load automatically on startup.

Use text-based PDFs. Scanned PDFs without a text layer cannot be read; run OCR first.

## Typical workflow

1. Enter site details and upload reference documents in the sidebar.
2. Go through the **Audit checklist**: set status, risk and observations.
3. Click **AI-assess non-compliances** to attach clauses, findings and corrective actions; review and edit.
4. Add responsible persons and due dates.
5. Check the **Dashboard**, then download the **Word report**.
6. Save the audit as CSV; reload it later from the sidebar.

## Notes and limitations

- Streamlit Cloud storage is not persistent; save your audit CSV before closing.
- AI output is a drafting aid. A competent auditor must verify findings against the applicable standards and local law.
- Retrieval uses keyword matching (TF-IDF). For very large document sets or heavy paraphrasing, consider upgrading to embeddings with a vector store (e.g. ChromaDB or FAISS).
- Default model is `claude-sonnet-5-5`; change it in the sidebar if needed.

## Customising the checklist

Edit the `CHECKLIST` dictionary at the top of `app.py` to match your standards (e.g. NFPA, local building codes, company requirements).
