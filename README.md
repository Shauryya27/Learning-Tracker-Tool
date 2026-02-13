# Learning-Tracker-Tool

# Full Stack + Gen AI
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
