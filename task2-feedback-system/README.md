# Task 2: AI-Powered Feedback System

A production-grade Two Dashboard System with AI-powered feedback analysis, built with Next.js 14, MongoDB, and Groq LLM.

## Live Demo

- **User Dashboard**: [https://your-app.vercel.app](https://your-app.vercel.app)
- **Admin Dashboard**: [https://your-app.vercel.app/admin](https://your-app.vercel.app/admin)

---

## Features

### User Dashboard (`/`)
- Interactive 5-star rating system with hover effects
- Optional text review with character counter
- Real-time AI-generated responses based on sentiment
- Smooth animations with Framer Motion
- Fully responsive design

### Admin Dashboard (`/admin`)
- Real-time analytics with interactive charts
- Rating distribution bar chart
- Sentiment analysis pie chart
- Advanced filtering (rating, sentiment, date range)
- Paginated feedback table with sorting
- Auto-refresh every 30 seconds
- Live clock display

---

## Tech Stack

| Technology | Purpose |
|------------|---------|
| Next.js 14 | React framework with App Router |
| TypeScript | Type-safe development |
| MongoDB Atlas | Cloud database for feedback storage |
| Groq LLM | AI-powered sentiment analysis (Llama 3.3 70B) |
| Tailwind CSS | Utility-first styling |
| Framer Motion | Smooth animations |
| Recharts | Data visualization |
| Radix UI | Accessible UI components |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Frontend (Next.js)                      │
├─────────────────────────────┬───────────────────────────────┤
│     User Dashboard (/)      │    Admin Dashboard (/admin)   │
│   - Star Rating Component   │   - Analytics Charts          │
│   - Review Form             │   - Feedback Table            │
│   - AI Response Display     │   - Filters & Pagination      │
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
│   - Analytics Queries   │     │   - AI Response Generation  │
└─────────────────────────┘     └─────────────────────────────┘
```

---

## Project Structure

```
task2-feedback-system/
├── src/
│   ├── app/
│   │   ├── page.tsx              # User Dashboard
│   │   ├── admin/
│   │   │   └── page.tsx          # Admin Dashboard
│   │   ├── api/
│   │   │   ├── feedback/
│   │   │   │   └── route.ts      # Submit feedback API
│   │   │   └── admin/
│   │   │       ├── feedbacks/
│   │   │       │   └── route.ts  # Get feedbacks API
│   │   │       └── analytics/
│   │   │           └── route.ts  # Analytics API
│   │   ├── layout.tsx
│   │   └── globals.css
│   ├── components/
│   │   └── FeedbackForm.tsx
│   ├── lib/
│   │   ├── mongodb.ts
│   │   └── groq.ts
│   └── models/
│       └── Feedback.ts
├── .env.local
├── package.json
└── tsconfig.json
```

---

## Getting Started

### Prerequisites
- Node.js 18+
- MongoDB Atlas account
- Groq API key

### Installation

```bash
# Clone the repository
git clone https://github.com/yashodipmore/Feedback-System.git
cd Feedback-System/task2-feedback-system

# Install dependencies
npm install

# Set up environment variables
# Create .env.local file with:
# MONGODB_URI=your_mongodb_connection_string
# GROQ_API_KEY=your_groq_api_key

# Run development server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) for User Dashboard and [http://localhost:3000/admin](http://localhost:3000/admin) for Admin Dashboard.

---

## API Endpoints

### POST `/api/feedback`
Submit user feedback with AI analysis.

```json
// Request
{
  "rating": 4,
  "review": "Great product, loved the quality!"
}

// Response
{
  "success": true,
  "feedback": {
    "_id": "...",
    "rating": 4,
    "review": "Great product, loved the quality!",
    "sentiment": "positive",
    "aiResponse": "Thank you for your wonderful feedback!...",
    "createdAt": "2026-01-06T..."
  }
}
```

### GET `/api/admin/feedbacks`
Fetch feedbacks with filters and pagination.

Query params: `page`, `limit`, `rating`, `sentiment`, `startDate`, `endDate`

### GET `/api/admin/analytics`
Get dashboard analytics including total feedbacks, average rating, sentiment breakdown, and rating distribution.

---

## Edge Cases Handled

| Scenario | Handling |
|----------|----------|
| Empty review | AI generates response based on rating only |
| Very long review | Truncated to 2000 chars for LLM |
| LLM API failure | Graceful fallback response |
| Invalid rating | Server-side validation (1-5 range) |
| Database error | Proper error messages |

---

## Deployment

Deployed on **Vercel**:

1. Connect GitHub repository to Vercel
2. Set environment variables: `MONGODB_URI`, `GROQ_API_KEY`
3. Set root directory to `task2-feedback-system`
4. Deploy

---

## Features

- [x] Production web application built with Next.js 14
- [x] Two separate dashboards (User + Admin)
- [x] Star rating input (1-5)
- [x] Optional text review
- [x] AI-generated response displayed to user
- [x] Server-side LLM calls only
- [x] Admin analytics with charts
- [x] Filter by rating, sentiment, date
- [x] Edge cases handled
- [x] Deployed on Vercel

---

## Author

**Yashodip More**

Built by Yashodip More
