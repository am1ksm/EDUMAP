# EduMap

A school discovery platform for parents in Kazakhstan: search, filter and compare schools, see them on a map, get AI-assisted recommendations and send consultation requests. Live at **[edu-map.kz](https://edu-map.kz)**.

Built solo by [Amirlan Smagulov](https://www.linkedin.com/in/amirlan-smagulov-075486292) as a founder and developer, using AI-assisted development.

> 🇷🇺 **Кратко:** EduMap помогает родителям в Казахстане выбрать школу: поиск и фильтры, карта, сравнение школ, AI-подбор и заявки на консультацию. В репозитории мобильное приложение, веб-панель для школ и администраторов, API-сервер и Telegram-бот.

## Features

- **Search and filters** by city, district, school type and tuition range
- **Map view** of schools with location-based discovery
- **Side-by-side comparison** of schools
- **AI Match and AI chat**: a natural-language request is parsed by an LLM into search criteria, and the chat answers from the current list of schools
- **School profiles** with admission information, clubs and news
- **Consultation requests** with WhatsApp Business notifications
- **Rating surveys** and engagement analytics for schools
- **Role-based flows** for parents, school administrators and moderators
- **Email verification codes** for sign-up and sign-in
- **Telegram bot (MVP)**: step-by-step school search with inline buttons
- Interface in Russian and English

## Architecture

| Part | Location | Stack |
|---|---|---|
| Mobile app | `app/` (Expo entry point in the repository root) | Expo, React Native 0.81, React 19, NativeWind, React Navigation |
| Web panel | `admin-web/` | Next.js, TypeScript: registration, onboarding and admin screens |
| School registration site | `ed/` | Next.js |
| API server | `server/` | Node.js, Express, PostgreSQL (`pg`) |
| Telegram bot | `server/src/telegramBot.js` | Node.js |
| Auth and file storage | Supabase | Auth, sessions, password recovery, uploads |

A few design decisions:

- **One API for product data.** In `admin-web`, Supabase is used only for auth, sessions, password recovery and storage. All product data goes through the server API (see `admin-web/ARCHITECTURE.md`).
- **LLM-agnostic AI layer.** The server talks to any OpenAI-compatible endpoint, configured through environment variables (`LLM_PROVIDER`, `LLM_BASE_URL`, `LLM_MODEL`).
- **Graceful degradation.** If WhatsApp credentials are not set, notifications are skipped and consultation requests are still saved.

## API overview

The server mounts these routes under `/api`:

`consultations` · `auth` · `ai` · `schools` · `schools/rating-surveys` · `news` · `chat` · `site-settings`

The AI routes are `POST /api/ai/school-query` (turns a free-text request into search criteria) and `POST /api/ai/school-chat` (answers based on a list of schools).

## Running locally

**API server**

```bash
cd server
cp .env.example .env   # fill in the values you need
npm install
npm run dev            # http://localhost:4000
```

**Telegram bot**

```bash
cd server
TELEGRAM_BOT_TOKEN=<token from @BotFather> npm run telegram
```

**Mobile app**

```bash
npm install
npx expo start
```

**Web panel**

```bash
cd admin-web
npm install
npm run dev            # http://localhost:3000
```

More details: [`server/README.md`](server/README.md) and [`server/TELEGRAM_BOT.md`](server/TELEGRAM_BOT.md) (in Russian).

## Configuration

Server settings live in `server/.env.example`: port and allowed origins, WhatsApp Business API credentials, LLM provider settings, SendGrid and verification-code limits. Never commit a real `.env` file.

## Author

**Amirlan Smagulov**: [LinkedIn](https://www.linkedin.com/in/amirlan-smagulov-075486292) · [Amirlan.sm@mail.ru](mailto:Amirlan.sm@mail.ru)
