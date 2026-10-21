# Accounts and assessments

## Knowledge base and accounts

1. Sign in as the administrator.
2. Open **Knowledge Base** and upload the approved policy PDFs and `BACKEND/HECVAT_Template.xlsx`.
3. Open **User Access** and create accounts for assessors and viewers.
4. Check readiness:

   ```bash
   curl "http://localhost:8080/health?strict=1"
   ```

Strict readiness returns HTTP 200 when the models and knowledge base are ready. An empty knowledge
base returns HTTP 503.

| Role | Access |
| --- | --- |
| Admin | Assessments, accounts and knowledge base |
| Assessor | Own assessment sessions, uploads, follow-ups and reports |
| Viewer | Completed sessions assigned by an administrator |

Public sign-up creates Viewer accounts. Passwords are stored as salted hashes. Sessions use cookies
and CSRF protection and expire after eight hours by default.

## Run an assessment

1. Upload a completed `.xlsx` or `.xlsm` HECVAT and enter the vendor or service name.
2. Start analysis. Sedona parses the controls, retrieves policy context and scores the answers.
3. Attach supporting PDFs to the assessment session as needed.
4. Answer, edit or skip follow-up questions. Saved answers can be paused and resumed.
5. Generate the report and download the PDF, PPTX and CSV.

Wait for the active assessment to finish before starting another. Closing the browser does not
stop the assessment. Open the session from History to continue. Cancelling an initial scan deletes
that session's data.

Uploads are capped at 50 MB. Supporting evidence is stored per session. The PDF and CSV contain
all controls; the PPTX is an executive briefing. Reports are frozen when the session is complete.
