# HireLens

**Multi-agent recruitment screening pipeline built with CrewAI.** Parses a job description and a stack of resumes, scores each candidate against the role, and generates tailored interview questions per candidate.

Turns "read 200 resumes" into "review 200 scored candidates with question sets already drafted."

---

## Why this exists

Resume screening is high-volume, low-consistency work. Two reviewers rank the same candidate differently; the same reviewer ranks differently at 9am and 6pm. HireLens makes the criteria explicit, applies them uniformly, and shows its reasoning — so a human can audit a decision instead of re-deriving it.

## Architecture

```
  Job Description ──►┌─────────────────┐
      (PDF/DOCX)     │   JD Parser     │  extracts role, must-haves, nice-to-haves
                     └────────┬────────┘
                              ▼
                     ┌─────────────────┐
   Resumes  ────────►│ Resume Parser   │  normalises to structured candidate profile
   (PDF/DOCX)        └────────┬────────┘
                              ▼
                     ┌─────────────────┐
                     │ Skills Matcher  │  requirement-by-requirement comparison
                     └────────┬────────┘
                              ▼
                     ┌─────────────────┐
                     │   Candidate     │  weighted score + written rationale
                     │   Evaluator     │
                     └────────┬────────┘
                              ▼
                     ┌─────────────────┐
                     │   Question      │  interview questions targeting the
                     │   Generator     │  candidate's specific gaps and claims
                     └────────┬────────┘
                              ▼
                        Scorecard + Question Set
```

## Agents

| Agent | Responsibility |
|-------|----------------|
| `jd_parser` | Extracts structured requirements from a job description — separates hard requirements from preferences |
| `resume_parser` | Normalises heterogeneous resume formats into a consistent candidate schema |
| `skills_matcher` | Compares candidate profile against parsed requirements, requirement by requirement |
| `candidate_evaluator` | Produces a weighted fit score with written justification per dimension |
| `question_generator` | Drafts interview questions probing the candidate's specific gaps and unverified claims |

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Agent Framework | **CrewAI** (sequential crew with typed task outputs) |
| Backend | FastAPI, Python |
| Frontend | React + Vite |
| Validation | Pydantic schemas (`src/models/schemas.py`) |
| File Handling | PDF and DOCX extraction (`src/file_extractor.py`) |
| Input Safety | Dedicated validation layer (`src/input_validator.py`) |

## Project Structure

```
src/
├── agents/
│   ├── jd_parser.py            # JD → structured requirements
│   ├── resume_parser.py        # Resume → candidate profile
│   ├── skills_matcher.py       # Requirement-level matching
│   ├── candidate_evaluator.py  # Weighted scoring + rationale
│   └── question_generator.py   # Targeted interview questions
├── api/main.py                 # FastAPI routes
├── models/schemas.py           # Pydantic contracts between agents
├── file_extractor.py           # PDF / DOCX ingestion
├── input_validator.py          # Input sanitisation
└── pipeline.py                 # Crew orchestration
frontend/                       # React + Vite UI
```

## Engineering Notes

- **Pydantic schemas are the contract between agents** — each agent's output is validated before the next consumes it, so a malformed LLM response fails fast at the boundary rather than corrupting downstream reasoning.
- **Scoring is explained, not just emitted** — the evaluator returns rationale per dimension. A hiring manager can disagree with a specific judgement instead of discarding the whole score.
- **Question generation is candidate-specific** — questions target that candidate's gaps and unverified claims, rather than reusing a fixed bank.
- **Input validation is a separate layer** — resumes are untrusted user uploads, and prompt injection through an uploaded document is a real vector.

## Getting Started

```bash
pip install -r requirements.txt
cp .env.example .env          # add LLM credentials
uvicorn src.api.main:app --reload --port 8000
```

```bash
cd frontend
npm install
npm run dev
```

## Author

**NagaVenkatesh Arigala** — AI/GenAI Engineer, Chennai, India

- Email: [arigalanagavenkatesh@gmail.com](mailto:arigalanagavenkatesh@gmail.com)
- Phone / WhatsApp: [+91 79890 06929](tel:+917989006929)
- LinkedIn: [nv-arigala0801](https://www.linkedin.com/in/nv-arigala0801/)
- GitHub: [Venki0987](https://github.com/Venki0987)

---

## Source code access

This repository is a **documentation and architecture showcase**. It covers the problem, the
system design, the agent topology, and the engineering decisions behind the project.

**The full source code is in a private repository.** If you want to see it, just ask — I am happy to
grant read access, walk through the implementation, or screen-share a live demo. Fastest ways to
reach me:

| | |
|---|---|
| Email | [arigalanagavenkatesh@gmail.com](mailto:arigalanagavenkatesh@gmail.com) |
| Phone / WhatsApp | [+91 79890 06929](tel:+917989006929) |
| LinkedIn | [nv-arigala0801](https://www.linkedin.com/in/nv-arigala0801/) |

I usually reply the same day.

All rights reserved — see [LICENSE](LICENSE).
