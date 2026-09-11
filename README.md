# 🤖 ResumeIQ AI Resume Screening & Job Matching System

An **AI-powered resume screening system** that analyzes job descriptions and candidate resumes, extracts structured information, evaluates candidate-job compatibility, and ranks candidates based on an overall match score.

The project uses **LLMs through Groq** to automate resume parsing and candidate evaluation, while **Pydantic** ensures structured and validated outputs.

---

## 🚀 Features

* 📋 **Job Description Analysis**

  * Extracts the role
  * Required skills
  * Preferred skills
  * Education requirements
  * Experience requirements
  * Key responsibilities

* 📄 **Resume Parsing**

  * Supports **PDF** and **DOCX** resumes
  * Extracts candidate information
  * Identifies technical skills
  * Extracts work experience and internships
  * Extracts education, projects, and certifications

* 🎯 **AI-Based Candidate Matching**

  * Compares resumes against job descriptions
  * Identifies matching skills
  * Detects missing skills
  * Evaluates experience requirements
  * Generates an overall match percentage
  * Provides a recruiter-friendly verdict

* 🏆 **Candidate Ranking**

  * Automatically sorts candidates by match score
  * Displays the **Top 2 candidates**
  * Displays the **Lowest 2 candidates**

* 🔐 **Environment Variable Support**

  * API credentials are loaded securely using `.env`

---

## 🛠️ Tech Stack

| Technology              | Purpose                                |
| ----------------------- | -------------------------------------- |
| **Python**              | Core programming language              |
| **Groq API**            | LLM inference                          |
| **OpenAI GPT-OSS 120B** | AI model used for analysis             |
| **Pydantic**            | Data validation and structured outputs |
| **pypdf**               | PDF text extraction                    |
| **python-docx**         | DOCX text extraction                   |
| **python-dotenv**       | Environment variable management        |
| **JSON**                | Structured AI responses                |

The project uses the `openai/gpt-oss-120b` model through Groq.

---

## 🧠 How It Works

The system follows a multi-stage AI pipeline:

```text
                    ┌──────────────────┐
                    │  Job Description │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │  AI Job Parser   │
                    └────────┬─────────┘
                             │
                             ▼
                  Structured Job Profile
                             │
                             │
┌──────────────┐             │
│ PDF / DOCX   │             │
│   Resumes    │             │
└──────┬───────┘             │
       │                     │
       ▼                     │
┌──────────────────┐         │
│ Resume Extraction│         │
└────────┬─────────┘         │
         │                   │
         ▼                   │
┌──────────────────┐         │
│  AI Resume Parser│         │
└────────┬─────────┘         │
         │                   │
         ▼                   │
 Structured Resume
         │
         └──────────────┬──────────────┐
                        ▼              │
                 ┌──────────────┐      │
                 │ AI Matching  │◄─────┘
                 └──────┬───────┘
                        │
                        ▼
                 Match Percentage
                        │
                        ▼
                Candidate Ranking
```

---

## 📌 Project Workflow

### 1. Job Description Parsing

The job description is passed to an LLM with a structured Pydantic schema.

The system extracts:

```text
Role
Required Skills
Preferred Skills
Minimum Experience
Education Requirements
Responsibilities
```

The project uses a `JobD` Pydantic model to enforce this structure.

---

### 2. Resume Processing

The application scans the `resumes/` directory and processes supported files.

Currently supported:

* `.pdf`
* `.docx`

PDFs are processed using `pypdf`, while DOCX files are processed using `python-docx`.

Example directory structure:

```text
project/
│
├── resumes/
│   ├── candidate1.pdf
│   ├── candidate2.docx
│   └── candidate3.pdf
│
├── main.py
├── .env
├── requirements.txt
└── README.md
```

---

### 3. AI Resume Parsing

The extracted resume text is sent to the LLM and converted into structured information.

The system extracts:

```text
Name
Email
Phone
Total Experience
Skills
Experiences
Education
Projects
Certifications
```

## Internships are also treated as experience, and skills can be extracted from different parts of the resume rather than relying only on a dedicated Skills section.

### 4. Candidate Matching

After parsing the resume, the system compares the structured candidate profile with the structured job profile.

The AI evaluates:

* Matching skills
* Missing important skills
* Whether the experience requirement is satisfied
* Overall match percentage
* Final candidate verdict

The matching response is validated using the `MatchResult` Pydantic model.

---

### 5. Candidate Ranking

All candidates are collected and sorted by their match score:

```python
all_results.sort(
    key=lambda candidate: candidate["score"],
    reverse=True
)
```

The system then identifies:

```text
🏆 Top 2 Candidates
⚠️ Lowest 2 Candidates
```

This makes the tool useful as an initial screening layer for recruiters.

---

## 🔑 Environment Setup

Create a `.env` file in the root directory:

```env
GROQ_API_KEY=your_groq_api_key
```

The application loads the API key using `python-dotenv` and initializes the Groq client.

> ⚠️ Never commit your `.env` file or API key to GitHub.

Add this to `.gitignore`:

```gitignore
.env
__pycache__/
*.pyc
```

---

## 📦 Installation

Clone the repository:

```bash
git clone https://github.com/your-username/ai-resume-screening.git
cd ai-resume-screening
```

Create a virtual environment:

```bash
python -m venv venv
```

Activate it:

### Windows

```bash
venv\Scripts\activate
```

### macOS / Linux

```bash
source venv/bin/activate
```

Install dependencies:

```bash
pip install groq pydantic python-dotenv pypdf python-docx
```

---

## ▶️ Running the Project

Place candidate resumes inside:

```text
resumes/
```

Then run:

```bash
python main.py
```

The application processes each supported resume, generates an AI match score, and prints the ranked candidates.

---

## 📊 Example Output

```text
Processing: candidate1.pdf
Score: 87.5

Processing: candidate2.pdf
Score: 72.0

Processing: candidate3.docx
Score: 91.0


TOP 2 CANDIDATES

Candidate 3 - 91.0%
{
    "matching_skills": [...],
    "missing_skills": [...],
    "experience_requirement_met": true,
    "verdict": "Strong match"
}

Candidate 1 - 87.5%
{
    "matching_skills": [...],
    "missing_skills": [...],
    "experience_requirement_met": true,
    "verdict": "Good match"
}


LOWEST 2 CANDIDATES

Candidate 2 - 72.0%
...
```

---

## 🧩 Pydantic Data Models

### Job Description

```python
class JobD(BaseModel):
    role: str
    required_skills: list[str]
    preferred_skills: list[str]
    minimum_experience: float | None
    education_requirements: list[str]
    responsibilities: list[str]
```

### Resume

```python
class Resume(BaseModel):
    name: str | None = None
    email: str | None = None
    phone: str | None = None
    total_experience_years: float | None = None
    skills: list[str] = []
    experiences: list[Experience] = []
    education: list[str] = []
    projects: list[str] = []
    certifications: list[str] = []
```

## These schemas allow the LLM responses to be converted into predictable, validated Python objects instead of relying on unstructured text.

## ⚡ Key Design Decisions

### Structured LLM Output

The application requests JSON responses from the LLM and validates the returned data with Pydantic.

This reduces the risk of inconsistent output formats and makes the AI results easier to process programmatically.

### Semantic Resume Parsing

The resume parser does not depend solely on exact section names. It is instructed to recognize variations such as:

```text
Experience
Professional Experience
Work History
Employment
Internships
```

This helps accommodate different resume formats.

### Two-Stage AI Pipeline

The system separates:

```text
Job Parsing
     ↓
Resume Parsing
     ↓
Candidate Matching
```

This keeps the matching stage focused on already-structured information rather than repeatedly interpreting raw documents.

---

## 🔮 Future Improvements

Potential improvements include:

* 🌐 Build a web interface using **React**
* ⚡ Add **FastAPI** backend
* 📊 Create recruiter dashboards and analytics
* 💾 Store candidates in **PostgreSQL / MongoDB**
* 🔍 Add semantic/vector search for large candidate pools
* 📑 Generate detailed candidate reports
* 📧 Automate interview invitation emails
* 🔐 Add authentication and role-based access
* ☁️ Deploy using AWS
* 🧵 Add asynchronous processing for large batches
* 📈 Track candidate scoring history
* 🤖 Add configurable scoring weights
* 🧪 Add automated tests and evaluation datasets

---

## ⚠️ Limitations

This project is designed as an **AI-assisted screening tool**, not a replacement for human hiring decisions.

LLM-generated scores and recommendations can contain errors or reflect biases present in the input data or model. Candidate evaluation should therefore include appropriate human review.

---

## 📁 Suggested Project Structure

```text
ai-resume-screening/
│
├── resumes/
│   ├── candidate1.pdf
│   ├── candidate2.pdf
│   └── candidate3.docx
│
├── main.py
├── .env
├── .gitignore
├── requirements.txt
└── README.md
```

---

## 👨‍💻 Author

**Kritik Kaushik**

If you found this project useful, consider ⭐ starring the repository.
