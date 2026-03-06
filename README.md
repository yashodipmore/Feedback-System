# Feedback System

A comprehensive project featuring rating prediction via LLM prompting and a production-grade dual-dashboard feedback system.

![Python](https://img.shields.io/badge/Python-3.x-blue?style=flat-square&logo=python)
![Next.js](https://img.shields.io/badge/Next.js-14-black?style=flat-square&logo=next.js)
![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue?style=flat-square&logo=typescript)
![MongoDB](https://img.shields.io/badge/MongoDB-Atlas-green?style=flat-square&logo=mongodb)
![Groq](https://img.shields.io/badge/LLM-Llama%203.3%2070B-orange?style=flat-square)
![Vercel](https://img.shields.io/badge/Deployed-Vercel-black?style=flat-square&logo=vercel)

---

## Table of Contents

- [Live Demo](#live-demo)
- [Project Overview](#project-overview)
- [Task 1: Rating Prediction](#task-1-rating-prediction-via-prompting)
- [Task 2: Two-Dashboard System](#task-2-two-dashboard-ai-feedback-system)
- [Tech Stack](#tech-stack)
- [Setup & Installation](#setup--installation)
- [Project Structure](#project-structure)

---

## Live Demo

| Dashboard | URL |
|-----------|-----|
| User Dashboard | [https://feedback-system-taupe.vercel.app](https://feedback-system-taupe.vercel.app) |
| Admin Dashboard | [https://feedback-system-taupe.vercel.app/admin](https://feedback-system-taupe.vercel.app/admin) |

---

## Project Overview

This project consists of two tasks:

| Task | Description |
|------|-------------|
| **Task 1** | Rating Prediction via Prompting - Classify Yelp reviews into 1-5 stars using LLM |
| **Task 2** | Two-Dashboard AI Feedback System - Production web app with User & Admin dashboards |

---

## Task 1: Rating Prediction via Prompting

### Objective
Design prompts that classify Yelp reviews into 1-5 star ratings using LLM prompting techniques, returning structured JSON output.

### Dataset
- **Source**: Yelp Reviews (10,000 reviews)
- **Sampling**: Stratified - 40 reviews per star rating = 200 total samples
- **Model**: Llama 3.3 70B via Groq API

### Three Prompting Approaches

| Approach | Description |
|----------|-------------|
| **Zero-Shot** | Direct classification with rating scale definitions, no examples |
| **Few-Shot** | 5 calibrated examples (one per rating) + sentiment anchor keywords |
| **Chain-of-Thought** | 4-step structured analysis: Sentiment → Aspects → Intensity → Rating |

### Results

| Metric | Zero-Shot | Few-Shot | Chain-of-Thought |
|--------|-----------|----------|------------------|
| JSON Validity | **100%** | 44.5% | 9% |
| Exact Accuracy | 63.5% | 58.4% | **66.7%** |
| Within ±1 Star | 99% | 98.9% | **100%** |
| MAE | 0.38 | 0.43 | **0.33** |

### Per-Class Accuracy

| Star | Zero-Shot | Few-Shot | Chain-of-Thought |
|------|-----------|----------|------------------|
| 1 Star | 87.5% | 76.9% | **100%** |
| 2 Star | 47.5% | **50%** | 50% |
| 3 Star | 45% | 27.3% | **60%** |
| 4 Star | **40%** | 46.2% | 25% |
| 5 Star | **97.5%** | 95.2% | **100%** |

### Key Findings

1. **JSON Validity vs Accuracy Trade-off**: Simpler prompts produce better format compliance, complex reasoning improves accuracy but breaks JSON format

2. **Extreme Ratings are Easier**: 1-star and 5-star achieve 87-100% accuracy due to strong sentiment signals. Middle ratings (2,3,4) are harder with mixed sentiments

3. **Within ±1 Accuracy is Excellent**: 99-100% across all approaches - model rarely makes large prediction errors

### Recommendations

| Use Case | Recommended Approach | Reason |
|----------|---------------------|--------|
| Production (High Volume) | Zero-Shot | 100% JSON validity, good accuracy |
| Accuracy Critical | CoT + Post-processing | Best accuracy, extract JSON via regex |

### Output Format
```json
{
  "predicted_stars": 4,
  "explanation": "Positive sentiment with minor complaints indicates satisfied customer"
}
```

### Files
```
task1-rating-prediction/
├── Rating_Prediction_Prompting.ipynb   # Main notebook with all experiments
├── yelp.csv                            # Dataset (10,000 Yelp reviews)
├── prediction_results.json             # Saved metrics and sample predictions
└── README.md                           # Detailed documentation
```

---

## Task 2: Two-Dashboard AI Feedback System

### Features

#### User Dashboard (`/`)
- Interactive 5-star rating system with hover effects
- Optional review text input with character counter
- Real-time AI-generated personalized responses
- Success/error state handling
- Responsive design with animations

#### Admin Dashboard (`/admin`)
- Real-time analytics with interactive charts
- Rating distribution bar chart
- Sentiment analysis pie chart
- Filter by rating, sentiment, date range
- Paginated feedback table
- Auto-refresh (30 seconds) + Live clock
- Search functionality

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      CLIENT (Browser)                        │
├─────────────────────────────┬───────────────────────────────┤
│     User Dashboard (/)      │    Admin Dashboard (/admin)   │
│   - Star Rating             │   - Analytics Charts          │
│   - Review Input            │   - Feedback Table            │
│   - AI Response Display     │   - Filters & Search          │
└─────────────────────────────┴───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    API Routes (Server-Side)                  │
├─────────────────────────────────────────────────────────────┤
│  POST /api/feedback         - Submit feedback + AI analysis │
│  GET  /api/admin/feedbacks  - Fetch feedbacks with filters  │
│  GET  /api/admin/analytics  - Dashboard statistics          │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┴───────────────┐
              ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────────┐
│     MongoDB Atlas       │     │        Groq LLM API         │
│   - Feedback Storage    │     │   - Sentiment Analysis      │
│   - Indexed Queries     │     │   - AI Response Generation  │
└─────────────────────────┘     └─────────────────────────────┘
```

### API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/feedback` | POST | Submit feedback, get AI response |
| `/api/admin/feedbacks` | GET | Fetch feedbacks with filters & pagination |
| `/api/admin/analytics` | GET | Get dashboard statistics |

### Edge Cases Handled

| Scenario | Handling |
|----------|----------|
| Empty review | AI generates response based on rating only |
| Very long review | Truncated to 2000 chars for LLM |
| LLM API failure | Graceful fallback response |
| Invalid rating | Server-side validation (1-5 range) |
| Database error | Proper error messages returned |

---

## Tech Stack

### Task 1
| Technology | Purpose |
|------------|---------|
| Python 3 | Core language |
| Groq API | LLM inference (Llama 3.3 70B) |
| JSON/CSV | Data handling |

### Task 2
| Technology | Purpose |
|------------|---------|
| Next.js 14 | React framework with App Router |
| TypeScript | Type-safe development |
| MongoDB Atlas | Cloud database |
| Groq LLM | AI-powered analysis |
| Tailwind CSS | Styling |
| Framer Motion | Animations |
| Recharts | Data visualization |
| Vercel | Deployment |

---

## Setup & Installation

### Task 1 (Notebook)
```bash
cd task1-rating-prediction
# Open Rating_Prediction_Prompting.ipynb in Jupyter/VS Code
# Set your GROQ_API_KEY in the notebook
```

### Task 2 (Web App)
```bash
cd task2-feedback-system

# Install dependencies
npm install

# Create .env.local with:
# MONGODB_URI=your_mongodb_connection_string
# GROQ_API_KEY=your_groq_api_key

# Run development server
npm run dev
```

---

## Project Structure

```
Feedback-System/
├── README.md
│
├── task1-rating-prediction/
│   ├── Rating_Prediction_Prompting.ipynb
│   ├── yelp.csv
│   ├── prediction_results.json
│   └── README.md
│
└── task2-feedback-system/
    ├── src/
    │   ├── app/
    │   │   ├── page.tsx              # User Dashboard
    │   │   ├── admin/page.tsx        # Admin Dashboard
    │   │   └── api/
    │   │       ├── feedback/route.ts
    │   │       └── admin/
    │   │           ├── feedbacks/route.ts
    │   │           └── analytics/route.ts
    │   ├── lib/
    │   │   ├── mongodb.ts
    │   │   └── groq.ts
    │   └── models/
    │       └── Feedback.ts
    ├── package.json
    └── README.md
```

---

## Features

### Rating Prediction
- [x] Multiple prompting approaches (Zero-Shot, Few-Shot, CoT)
- [x] Structured JSON output format
- [x] Evaluation metrics (Accuracy, MAE, JSON validity)
- [x] Per-class accuracy analysis
- [x] Prompt iteration documentation

### Feedback System
- [x] Production web application
- [x] User Dashboard with star rating & review
- [x] AI-generated responses displayed to user
- [x] Admin Dashboard with analytics & charts
- [x] Filter by rating, sentiment, date
- [x] Server-side LLM calls only
- [x] Edge cases handled
- [x] Deployed on Vercel

---

## Author

**Yashodip More**

- GitHub: [@yashodipmore](https://github.com/yashodipmore)
- LinkedIn: [Yashodip More](https://linkedin.com/in/yashodipmore)

---

Built by Yashodip More
