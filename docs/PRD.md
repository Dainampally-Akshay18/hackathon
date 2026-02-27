# 🧠 DealMind AI – Automated Commercial Deal Review System

---

# 📄 PRODUCT REQUIREMENTS DOCUMENT (PRD)

---

# 1. Product Overview

## 1.1 Purpose

DealMind AI automates commercial deal documentation review by replacing manual workflows with AI-assisted processing.

It solves three major problems:

1. Manual data cleaning
2. Manual discrepancy detection
3. Manual extraction of key attributes from legal documents

---

## 1.2 Core Features (Hackathon Scope)

### 🧹 Cleaning Robot

* Remove duplicate Deal IDs
* Remove inactive deals
* Standardize fields

### 🔎 Lie Detector

* Match frontend and backend records
* Compare loan amount, borrower name, maturity date
* Generate discrepancy report

### 📜 AI Lawyer

* Extract structured attributes from legal PDF
* Convert unstructured text into JSON

---

# 2. System Architecture

Frontend (Vite + React + tailwind css )
↓
FastAPI Backend
↓
Modules:

* Cleaning Engine (Pandas)
* Discrepancy Engine (Pandas)
* Legal Extraction Engine (LangChain + Ollama)
  ↓
  Storage:
* SQLite (metadata)
* Local file system (uploads/outputs)

---

# 3. Functional Requirements

---

# 3.1 Data Upload

### Requirement

User must upload:

* Frontend CSV
* Backend CSV
* Legal PDF

---

# 3.2 Data Cleaning Module

### Inputs

CSV file

### Processing

* Drop duplicates by Deal_ID
* Remove Status != Active
* Standardize:

  * Loan_Amount → float
  * Dates → ISO format
  * Borrower_Name → Title case

### Output

Cleaned CSV

---

# 3.3 Discrepancy Detection Module

### Inputs

Cleaned frontend + cleaned backend

### Processing

* Inner join on Deal_ID
* Compare:

  * Loan_Amount
  * Borrower_Name
  * Maturity_Date
* Flag mismatches

### Output

Discrepancy Report JSON + CSV

---

# 3.4 Legal Document Extraction Module

### Input

PDF

### Processing

* Extract text
* Chunk text (if > 4000 tokens)
* Send to Ollama LLM via LangChain
* Structured extraction prompt

### Required Fields:

* Borrower Name
* Loan Amount
* Maturity Date
* Agreement Date

### Output

Structured JSON

---

# 4. Non-Functional Requirements

* Fully offline capable
* No paid APIs
* Response time < 5 seconds for CSV processing
* Secure file handling
* Modular architecture

---

# 5. Backend API Specification (FastAPI)

Now this is critical for AI IDE execution.

---

# Base URL

```
http://localhost:8000/api/v1
```

---

# 5.1 Health Check

### GET /health

Response:

```json
{
  "status": "ok"
}
```

---

# 5.2 Upload Files

### POST /upload

Multipart form-data:

* frontend_file: CSV
* backend_file: CSV
* legal_file: PDF

Response:

```json
{
  "frontend_path": "uploads/frontend.csv",
  "backend_path": "uploads/backend.csv",
  "legal_path": "uploads/legal.pdf"
}
```

---

# 5.3 Run Cleaning

### POST /clean

Body:

```json
{
  "frontend_path": "string",
  "backend_path": "string"
}
```

Response:

```json
{
  "cleaned_frontend_path": "outputs/cleaned_frontend.csv",
  "cleaned_backend_path": "outputs/cleaned_backend.csv",
  "records_removed": {
    "duplicates": 10,
    "inactive": 4
  }
}
```

---

# 5.4 Run Discrepancy Detection

### POST /discrepancy

Body:

```json
{
  "cleaned_frontend_path": "string",
  "cleaned_backend_path": "string"
}
```

Response:

```json
{
  "total_records_compared": 100,
  "total_mismatches": 12,
  "discrepancy_report_path": "outputs/discrepancy_report.csv"
}
```

---

# 5.5 Run Legal Extraction

### POST /legal/extract

Body:

```json
{
  "legal_path": "string"
}
```

Response:

```json
{
  "borrower_name": "ABC Pvt Ltd",
  "loan_amount": 10000000,
  "maturity_date": "2028-01-01",
  "agreement_date": "2023-01-01"
}
```

---

# 5.6 Get Final Summary

### GET /report/summary

Response:

```json
{
  "cleaning_completed": true,
  "discrepancy_completed": true,
  "legal_extraction_completed": true,
  "mismatch_count": 12
}
```

---

# 6. Data Schemas

---

## Deal Schema

```json
{
  "Deal_ID": "string",
  "Borrower_Name": "string",
  "Loan_Amount": "float",
  "Maturity_Date": "date",
  "Status": "string"
}
```

---

## Discrepancy Schema

```json
{
  "Deal_ID": "string",
  "Loan_Mismatch": "boolean",
  "Name_Mismatch": "boolean",
  "Date_Mismatch": "boolean"
}
```

---

# 7. AI Extraction Prompt Template

System Prompt:

"You are a legal document parser. Extract structured financial attributes strictly in JSON."

User Prompt Template:

Extract the following fields:

* Borrower Name
* Loan Amount
* Maturity Date
* Agreement Date

Return ONLY valid JSON.

Document:
{document_text}

---


# 9. Frontend Requirements (Vite + React)

Pages:

1. Upload Page
2. Cleaning Results Page
3. Discrepancy Results Page
4. Legal Extraction Page
5. Final Summary Dashboard

Use:

* Axios for API calls
* React Router
* Tailwind CSS (optional)

---

# 10. Execution Flow

1. User uploads files
2. Call /clean
3. Call /discrepancy
4. Call /legal/extract
5. Show summary

---

# 11. Acceptance Criteria

Project is complete when:

* User uploads files
* Cleaned dataset generated
* Discrepancies correctly flagged
* Legal fields extracted correctly
* All APIs tested via Swagger
* Frontend displays all outputs

---

# 12. Deployment (Optional)

Backend:

```
uvicorn main:app --reload
```

Frontend:

```
npm run dev
```

---

Perfect. 🔥
We will now update the **PRD sections only**.

You said:

* Add `.env.example`
* Use **Groq API**
* Use **Pinecone**
* Keep authentication frontend-only (localStorage)
* Add AI Explainer module
* Keep hackathon scope clean

Below are the **UPDATED SECTIONS ONLY** to insert into your PRD.

---

# 🔥 UPDATED PRD SECTIONS

---

# 13. Environment Configuration

## 13.1 Environment Variables

The backend requires the following environment variables.

Create a file:

```
.env.example
```

### Required Variables

```env
# ==============================
# GROQ LLM CONFIGURATION
# ==============================

GROQ_API_KEY=your_groq_api_key_here
GROQ_MODEL=llama3-70b-8192

# ==============================
# PINECONE VECTOR DATABASE
# ==============================

PINECONE_API_KEY=your_pinecone_api_key_here
PINECONE_ENVIRONMENT=us-east-1
PINECONE_INDEX_NAME=dealmind-index

# ==============================
# APP CONFIG
# ==============================

APP_ENV=development
BACKEND_URL=http://localhost:8000
```

---

## 13.2 Groq Usage

Groq will be used for:

* Legal document extraction
* AI Explainer (mismatch reasoning)
* Structured JSON output generation

LangChain integration:

```
LangChain → Groq LLM → Structured Output Parser
```

---

## 13.3 Pinecone Usage

Pinecone will be used for:

* Storing legal document embeddings
* Chunk-based retrieval (RAG)
* Context-aware extraction

Flow:

Legal PDF
↓
Chunking
↓
Embedding generation
↓
Store in Pinecone
↓
Query relevant chunks
↓
Send to Groq

---

# 14. Authentication Strategy (Frontend Only)

For hackathon simplicity:

* No backend authentication
* No database users table
* No JWT
* No OAuth

---

## 14.1 User Registration (Frontend Only)

User details stored in:

```
localStorage
```

Stored Object Structure:

```json
{
  "name": "Akshay",
  "email": "akshay@email.com",
  "password": "hashed_or_plain_for_demo"
}
```

---

## 14.2 Login Logic

Frontend checks:

* Email match
* Password match

If valid:

```
localStorage.setItem("isLoggedIn", true)
```

Protected routes:

* Dashboard
* Upload Page
* Results Page

If not logged in:
Redirect to `/login`

---

## 14.3 Security Note

This is acceptable for hackathon prototype only.

Production version must use:

* JWT authentication
* Backend validation
* Encrypted password storage

---

# 15. AI Explainer Module (NEW FEATURE)

## 15.1 Purpose

Instead of just showing:

"Loan amount mismatch"

The system generates:

"Loan amount differs by 2,000,000. Backend likely updated after frontend export."

This provides interpretability.

Judges LOVE this.

---

## 15.2 AI Explainer Endpoint

### POST /ai/explain

Request:

```json
{
  "deal_id": "D001",
  "frontend_data": {
    "loan_amount": 10000000,
    "borrower_name": "ABC Pvt Ltd",
    "maturity_date": "2028-01-01"
  },
  "backend_data": {
    "loan_amount": 12000000,
    "borrower_name": "ABC Pvt Ltd",
    "maturity_date": "2028-01-01"
  }
}
```

Response:

```json
{
  "explanation": "The loan amount differs by 2,000,000 between systems. This may indicate delayed synchronization or manual override in backend."
}
```

---

## 15.3 AI Prompt Template for Explainer

System Prompt:

"You are a financial audit analyst. Explain discrepancies clearly and professionally."

User Prompt:

```
Frontend Data:
{frontend_json}

Backend Data:
{backend_json}

Explain the discrepancy in plain English.
Keep response under 120 words.
```

---

# 16. Updated Backend API List

Final Endpoint List:

| Method | Endpoint        | Purpose          |
| ------ | --------------- | ---------------- |
| GET    | /health         | Check server     |
| POST   | /upload         | Upload CSV & PDF |
| POST   | /clean          | Cleaning Robot   |
| POST   | /discrepancy    | Lie Detector     |
| POST   | /legal/extract  | AI Lawyer        |
| POST   | /ai/explain     | AI Explainer     |
| GET    | /report/summary | Final summary    |

---

# 17. Pinecone Legal Processing Flow

1. Upload PDF
2. Extract text
3. Split into 1000-character chunks
4. Generate embeddings using Groq-compatible embedding model
5. Store in Pinecone index
6. Query relevant chunks
7. Send to Groq for structured extraction

---

# 18. Updated Final Architecture

Frontend (Vite + React)
↓
FastAPI Backend
↓
Modules:

* Cleaning Service
* Discrepancy Service
* Legal Extraction Service (Groq + Pinecone)
* AI Explainer Service
  ↓
  Storage:
* Pinecone (vectors)
* Local file system (uploads)
  ↓
  User Auth:
* localStorage (frontend only)

---

# 19. Acceptance Criteria (Updated)

Project is complete when:

* User can register and login (frontend-only)
* CSV & PDF upload works
* Cleaning robot works
* Discrepancy report generated
* Legal extraction returns structured JSON
* AI explainer generates reasoning
* Pinecone stores embeddings successfully
* Groq LLM integration working

---


# 🧠 PROJECT ROOT STRUCTURE

```bash
dealmind-ai/
│
├── backend/
├── frontend/
├── uploads/
├── outputs/
├── .env
├── .env.example
├── README.md
```

---

# 🔥 BACKEND STRUCTURE (FastAPI + LangChain + Groq + Pinecone)

```bash
backend/
│
├── app/
│   ├── main.py
│   │
│   ├── core/
│   │   ├── config.py
│   │   ├── pinecone_client.py
│   │   ├── groq_client.py
│   │
│   ├── routers/
│   │   ├── health.py
│   │   ├── upload.py
│   │   ├── cleaning.py
│   │   ├── discrepancy.py
│   │   ├── legal.py
│   │   ├── ai_explainer.py
│   │   └── report.py
│   │
│   ├── services/
│   │   ├── cleaning_service.py
│   │   ├── discrepancy_service.py
│   │   ├── legal_service.py
│   │   ├── embedding_service.py
│   │   ├── explainer_service.py
│   │   └── report_service.py
│   │
│   ├── models/
│   │   ├── deal_schema.py
│   │   ├── discrepancy_schema.py
│   │   └── request_response_models.py
│   │
│   ├── utils/
│   │   ├── file_handler.py
│   │   ├── pdf_parser.py
│   │   ├── csv_utils.py
│   │   └── chunking.py
│   │
│   └── dependencies/
│       └── pinecone_dependency.py
│
├── requirements.txt
└── run.py
```

---

# 🧠 BACKEND LAYER EXPLANATION

## 1️⃣ main.py

* Initialize FastAPI app
* Include routers
* Add CORS middleware
* Load environment variables

---

## 2️⃣ core/

### config.py

* Load `.env`
* Centralize:

  * GROQ_API_KEY
  * PINECONE_API_KEY
  * MODEL_NAME

---

### groq_client.py

* Initialize Groq LLM
* Used by:

  * legal_service
  * explainer_service

---

### pinecone_client.py

* Initialize Pinecone
* Create index if not exists
* Upsert vectors

---

## 3️⃣ routers/

Each router contains only:

* Endpoint definition
* Input validation
* Calls to services

Example:

```python
@router.post("/clean")
async def run_cleaning(payload: CleanRequest):
    return cleaning_service.clean_data(payload)
```

No logic inside routers.

---

## 4️⃣ services/

### cleaning_service.py

Handles:

* Drop duplicates
* Remove inactive
* Standardize data

Returns:

* cleaned paths
* counts

---

### discrepancy_service.py

Handles:

* Merge frontend/backend
* Compare fields
* Generate mismatch flags
* Save CSV

---

### legal_service.py

Handles:

* Extract PDF text
* Chunk text
* Generate embeddings
* Store in Pinecone
* Retrieve relevant chunks
* Structured extraction via Groq

---

### embedding_service.py

* Convert text chunks → embeddings
* Pinecone upsert

---

### explainer_service.py

Handles:

* Compare FE vs BE values
* Send both to Groq
* Return explanation

---

### report_service.py

* Aggregate outputs
* Provide summary

---

## 5️⃣ models/

Pydantic models for:

* CleanRequest
* DiscrepancyRequest
* LegalExtractRequest
* ExplainRequest
* SummaryResponse

---

## 6️⃣ utils/

Reusable helpers:

* file saving
* csv validation
* pdf text extraction
* chunking logic

---

# 🔥 FRONTEND STRUCTURE (Vite + React)

```bash
frontend/
│
├── public/
│
├── src/
│   ├── main.jsx
│   ├── App.jsx
│   │
│   ├── routes/
│   │   ├── ProtectedRoute.jsx
│   │
│   ├── pages/
│   │   ├── Login.jsx
│   │   ├── Register.jsx
│   │   ├── Dashboard.jsx
│   │   ├── UploadPage.jsx
│   │   ├── CleaningResults.jsx
│   │   ├── DiscrepancyResults.jsx
│   │   ├── LegalResults.jsx
│   │   └── Summary.jsx
│   │
│   ├── components/
│   │   ├── Navbar.jsx
│   │   ├── FileUploader.jsx
│   │   ├── ResultsTable.jsx
│   │   ├── LoadingSpinner.jsx
│   │   └── ExplanationModal.jsx
│   │
│   ├── api/
│   │   ├── axiosInstance.js
│   │   ├── uploadApi.js
│   │   ├── cleaningApi.js
│   │   ├── discrepancyApi.js
│   │   ├── legalApi.js
│   │   ├── explainerApi.js
│   │   └── reportApi.js
│   │
│   ├── context/
│   │   └── AuthContext.jsx
│   │
│   ├── hooks/
│   │   └── useAuth.js
│   │
│   ├── utils/
│   │   └── localStorageHelper.js
│   │
│   └── styles/
│       └── global.css
│
├── index.html
├── package.json
└── vite.config.js
```

---

# 🧠 FRONTEND ARCHITECTURE EXPLANATION

---

## 1️⃣ Authentication (Frontend Only)

AuthContext.jsx:

* Stores:

  * user
  * isLoggedIn
* Reads from localStorage

ProtectedRoute.jsx:

* Checks `isLoggedIn`
* Redirects to /login if false

---

## 2️⃣ Pages Flow

Login → Dashboard → Upload → Cleaning → Discrepancy → Legal → Summary

---

## 3️⃣ File Upload Flow

UploadPage.jsx:

* Upload FE CSV
* Upload BE CSV
* Upload PDF
* Call `/upload`
* Store returned paths in state

---

## 4️⃣ Discrepancy Page

* Show mismatch table
* Add “Explain” button per row
* Open ExplanationModal
* Call `/ai/explain`

🔥 This is where judges say WOW.

---

## 5️⃣ Summary Page

Display:

* Records cleaned
* Mismatches count
* Extracted legal fields
* AI explanation highlights

---

# 🔥 FINAL FLOW

User logs in
↓
Uploads files
↓
Run Cleaning
↓
Run Discrepancy
↓
Run Legal Extraction
↓
Click “Explain Mismatch”
↓
AI generates reasoning

---
