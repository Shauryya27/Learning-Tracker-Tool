# Learning-Tracker-Tool
## Minimum Viable Product(MVP)
### Data model (simple, scalable)
- `Language` (JavaScript, Python, Rust…)
- `Module` (Basics, OOP, Async, Memory…)
- `Lesson` (concept page: explanation, examples, visuals)
- `Quiz` (optional)
- `UserProgress` (completed lessons, streak, XP)
- `Questions` (what user asked the agent)
- `Embeddings` (vector rows for lessons)
### Data model (minimal)
- `User` (from auth provider)
- `Course` (name, goal, startDate)
- `Topic` (courseId, name, difficulty, tags)
- `StudySession` (topicId, date, minutes, note)
- `WeeklyRecap` (userId, weekStart, recapText, nextStepsText)
### API surface (simple)
- `/api/courses` CRUD
- `/api/topics` CRUD
- `/api/sessions` CRUD
- `/api/recap/generate` (calls LLM, stores result)
### Core features (MVP)
- Browse languages → modules → lessons
- Mark lesson complete + streak/XP
- Dashboard: progress + weekly chart
- Ask AI: “Explain closures like I’m 12” (RAG over your lesson content)
## Stack (keep it simple, ship fast)
- **Next.js + TypeScript**
- **Tailwind CSS + shadcn/ui**
- *3D on the website*:
	- **react-three-fiber** (React renderer for three.js) [Poimandres Documentation+1](https://r3f.docs.pmnd.rs/?utm_source=chatgpt.com)
	- **@react-three/drei** helper components (cameras, controls, loaders, etc.) [Poimandres Documentation](https://drei.docs.pmnd.rs/?utm_source=chatgpt.com)
	- Optional animation: **react-spring** for physically-realistic motion in R3F scenes [React Spring](https://react-spring.dev/docs/guides/react-three-fiber?utm_source=chatgpt.com)
- **Postgres** (Neon / Supabase / Railway): Database
- **Prisma** ORM or Drizzle ORM
- **Auth**: Clerk (fastest)
- Charts: Recharts
- Deploy: Vercel
- **OpenAI API** for chat + embeddings: **pgvector inside Neon Postgres** as your vector store (RAG)
- *Tool decisions for the AI feature (keep it simple)*: Vector store: start with Postgres, not Pinecone. Your earlier stack already includes **Neon Postgres**. So instead of adding Pinecone, you can store vectors in Postgres (pgvector) and keep one database.
	- Why this matters: fewer services = less confusion. If later you need huge scale, you can switch to Pinecone. For your sprint, one DB wins.
	- *Streaming chat Vercel AI SDK*: Your summary called it “Versa AI SDK,” but what’s widely used in Next.js is **Vercel AI SDK** with `useChat` + server-side streaming helpers. [AI SDK+2Vercel+2](https://ai-sdk.dev/cookbook/next/stream-text-with-chat-prompt?utm_source=chatgpt.com)
## Videos to follow
### [Codebox-style fullstack SaaS](https://youtu.be/rdaSPdCkoFQ?si=Zt9YNRQzVVUIUQZI)
**Follow it for:**
1. **Auth + protected routes (copy pattern, not UI)**: Clerk login + middleware-protected routes is a great “ship fast” move and looks legit on resume. 
2. **Postgres + ORM + schema relationships**: Neon Postgres + a typed ORM is perfect for your tracker (users → goals/topics → sessions → recaps). If the video uses *Drizzle*, that’s fine; if you prefer Prisma, also fine. The important part is **relational modeling + migrations + clean queries**.
	- (Neon + Clerk + Drizzle is a known combo and even has example repos you can reference.) [GitHub+1](https://github.com/raoufchebri/neon-clerk-drizzle-nextjs?utm_source=chatgpt.com)
3. **Progress tracking, streaks, XP (gamification-lite)**: For Learning Tracker, keep this minimal:
	- streak = “days with ≥1 session”
	- XP = minutes or session count
	- weekly chart = minutes/day
4. **Deployment discipline**: The “deploy with env vars + verify local build” workflow is exactly what makes your project look real.
**Skip:**
- billing/subscriptions
- code editor playground
- anything “SaaS monetization” (excess for sprint)
### ["Chat with PDF” SaaS (Elliot)](https://youtu.be/bZFedu-0emE?si=uKTI6QTVs7aQyoiD)
**Follow it for:**
- the RAG pipeline architecture: chunk → embed → retrieve → answer
- streaming chat UI structure
- message persistence patterns(so chats are saved per user / per lesson or per language)
- **Auth-protected routes** (Clerk pattern)
That video is literally the “RAG app wiring” pattern in Next.js.
**Don’t follow literally:**
- S3 uploads (not needed unless users upload PDFs)
- Pinecone (skip for MVP; use pgvector in Neon instead)
- Stripe subscriptions (skip)
### [Semantic search repo (dabit3)](https://youtu.be/6_mfYPPcZ60?si=-mS8ykz2lDNrTEUk)
**Use it as a reference** (not a “follow along”):
- chunking best practices
- simple indexing workflow (load docs → embed → upsert vectors)
- query workflow (embed query → similarity search → feed to LLM)
It’s a clean “minimum viable semantic search” reference. [GitHub](https://github.com/dabit3/semantic-search-nextjs-pinecone-langchain-chatgpt?utm_source=chatgpt.com)
**Do NOT follow**:
- treating this like a separate app; you’re embedding this inside your Learning Tracker, not building a standalone demo.
## Github Workflow
Rule 1: main is protected (conceptually)
- Nobody commits directly to main
- Only PRs go into main
Rule 2: one task = one branch
Examples:
- feature/auth-clerk
- feature/db-schema
- fix/readme
Rule 3: always start from latest main
- Before starting a task:
  - git switch main
  - git pull origin main
  - git switch -c feature/<task-name>
Rule 4: commit locally, push branch, open PR
- git add .
- git commit -m "Describe change"
- git push -u origin HEAD
Then open PR on GitHub → base: main
Rule 5: review + merge, then clean up
- After PR merge:
  - git switch main
  - git pull origin main
  - git branch -d feature/<task-name>
  - git push origin --delete feature/<task-name>
How you both work at the same time without stepping on each other
You pick different tasks.
Each task is on a separate branch.
If both need to touch the same file, you coordinate or one rebases.
If your branch falls behind:
- git fetch origin
- git rebase origin/main
fix conflicts if any
- git push --force-with-lease
