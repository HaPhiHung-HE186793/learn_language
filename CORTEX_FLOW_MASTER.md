# 🧠 CORTEX-FLOW: MASTER PROJECT PRD & SYSTEM ARCHITECTURE
**Role for AI Agent:** You are an elite Full-Stack Developer and AI Engineer. Read this entire document to understand the project's philosophy, tech stack, and architecture before executing any code.
**Project Type:** AI-Powered Gamified Language Learning Ecosystem (English & Japanese).
**Core Philosophy:** Hack the dopamine reward system. Zero-friction micro-learning (<60s sessions). AI-generated personalized content. The Feynman Technique for mastery.

---

## 1. TECH STACK REQUIREMENTS
- **Frontend (Mobile-First App/Web):** React Native (Expo) or Flutter (for mobile) / Next.js (for web).
  - *Must support:* 60fps animations, gesture handling (swipe left/right), and Haptic Feedback (device vibration).
- **Backend & API:** Node.js (NestJS/Express) or Python (FastAPI - highly recommended for AI/LLM integration).
- **Database:** PostgreSQL (Relational data) + Redis (Real-time game states/Leaderboards).
- **AI Core:** OpenAI (GPT-4o) or Anthropic (Claude 3.5) for text/logic. Whisper API for Voice-to-Text.

---

## 2. CORE MODULES & DATA FLOW

### MODULE A: AI SMART HUB (Teacher / Creator UGC Pipeline)
*Goal: Allow teachers to instantly create personalized learning material without manual data entry.*
1. **Input:** Teacher creates a `Hub` and uploads raw data (PDF, Text block, YouTube URL).
2. **AI Extractor (Backend):** AI parses the raw data -> Extracts Vocab (with IPA/Kanji), Core Grammar, and Idioms -> Saves as a structured JSON array (`LearningNodes`).
3. **AI Mutator (Runtime):** When a student joins, the LLM dynamically rewrites the example sentences based on the student's `interests` (e.g., Anime, K-Pop, Tech) while keeping the target grammar/vocab intact.

### MODULE B: DOPAMINE MICRO-GAMES (Student Loop)
*Constraint: UI must be TikTok-style. 1-thumb vertical swipe/tap. < 60s per session. Zero complex menus.*
- **Game 1: Vocab Tinder (Swipe Interface)**
  - *Mechanic:* Card in the center with a foreign word. Top text shows a native meaning (can be true or a trap).
  - *Action:* Swipe Right (Match), Swipe Left (Trap).
  - *Logic:* Speed increases. Haptic feedback on swipe. Tracks reaction time (hesitation latency) for the hidden Spaced Repetition System (SRS).
- **Game 2: Context Bomb (Grammar Defusal)**
  - *Mechanic:* A ticking bomb (10s timer). A sentence missing a word/particle. 3 colored wires (options).
  - *Action:* Tap to cut the correct wire. Correct = +3s, Coin sound. Wrong = Screen shakes, bomb explodes (Eustress mechanic).
- **Game 3: Ninja Shadowing (Pronunciation)**
  - *Mechanic:* Play native audio with extreme emotion. User holds the mic to mimic.
  - *Logic:* Whisper API checks pronunciation accuracy. >85% = Katana slash animation cuts the screen.

### MODULE C: FEYNMAN MASTERY MODE (AI NPC)
*Goal: Student verbally explains concepts to an AI to lock memory into the hippocampus.*
- *Trigger:* Unlocked when SRS mastery reaches >90%.
- *System Prompt for the LLM NPC (Inject this into the chat logic):*
  ```text
  [SYSTEM ROLE]
  You are "Taro" (Japanese learner) or "Alex" (English learner), a 12-year-old NPC student. You are lazy, literal-minded, but polite.
  
  [OBJECTIVE]
  The user is your Teacher. MAKE IT HARD FOR THEM. Force them to use real-life examples and simple words to explain the concept.
  
  [RULES]
  1. Deny understanding in the first 2 turns. Find a logical flaw or literal translation exception. (e.g., "Why is 'Piece of cake' easy? Baking is hard!").
  2. No Academic Jargon: If they use terms like "Relative clause", complain: "I'm 12, speak normal words!"
  3. Eureka Moment: ONLY when they give a brilliant real-life analogy, exclaim "Aha! I get it now!" and summarize simply.
  
  [OUTPUT FORMAT]
  Max 3 short sentences. GenZ tone, emojis. End with a question to keep them explaining (until Eureka).