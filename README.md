# NOVA / FUTURE STICK — FastAPI + SQLite Backend

এই সংস্করণে MongoDB বা আলাদা database account লাগবে না। SQLite ফাইল `nova.db`-এ account ও favorites সংরক্ষণ হবে। মূল `2.html`-এর design রেখে backend integration যোগ করা হয়েছে।

## কী আছে
- FastAPI backend + SQLite database
- Register / login / logout / current user
- Password hashing এবং HttpOnly session cookie
- User-based favorites
- Eporner search server-side proxy
- Gemini AI server-side proxy (API key ঐচ্ছিক; key ছাড়া AI endpoint বন্ধ থাকবে)
- Basic rate limit, security headers, input validation
- `frontend/2.html` এবং `frontend/api-client.js`

## যা লাগবে
- Python 3.11 বা নতুন
- Internet connection
- Gemini AI ব্যবহার করতে চাইলে Gemini API key (ঐচ্ছিক)

## Install & run
টার্মিনাল খুলে `backend` ফোল্ডারে ঢুকুন:

```bash
python -m venv .venv
```
Windows:
```bash
.venv\Scripts\activate
```
macOS/Linux:
```bash
source .venv/bin/activate
```
তারপর:
```bash
pip install -r requirements.txt
```
`.env.example` কপি করে `.env` নাম দিন। `JWT_SECRET`-এ অন্তত 32 অক্ষরের random secret দিন। উদাহরণ তৈরি করতে Python চালিয়ে `secrets.token_urlsafe(48)` ব্যবহার করা যায়। Gemini না লাগলে `GEMINI_API_KEY` খালি রাখুন।

চালু করুন:
```bash
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```
তারপর ব্রাউজারে খুলুন:
- Website: http://127.0.0.1:8000/
- API docs: http://127.0.0.1:8000/docs
- Health: http://127.0.0.1:8000/api/health

প্রথম চালুতে `backend/nova.db` তৈরি হবে। SQLite ফাইলটি মুছে ফেললে স্থানীয় account/favorites মুছে যাবে—তাই production-এ backup রাখতে হবে।

## Gemini AI (ঐচ্ছিক)
`.env`-এ `GEMINI_API_KEY` সেট করুন। API key শুধু backend-এ রাখবেন; HTML/JavaScript বা public repository-তে দেবেন না। Key না থাকলে `/api/ai/chat` 503 দেবে এবং frontend-এর বিদ্যমান fallback চলতে পারে।

## Endpoints
| Method | Endpoint | কাজ |
|---|---|---|
| GET | `/api/health` | Health check |
| POST | `/api/auth/register` | Account তৈরি |
| POST | `/api/auth/login` | Login |
| POST | `/api/auth/logout` | Logout |
| GET | `/api/auth/me` | Current user |
| GET | `/api/videos/search?q=...` | Video search proxy |
| GET | `/api/favorites` | Favorites list |
| POST | `/api/favorites` | Favorite যোগ |
| DELETE | `/api/favorites/{video_id}` | Favorite মুছুন |
| POST | `/api/ai/chat` | Gemini AI (key লাগবে) |

## গুরুত্বপূর্ণ সীমাবদ্ধতা
- Uploaded video library আগের মতো browser IndexedDB-তে local; এই backend-এ video file upload/storage যোগ করা হয়নি।
- Third-party video providers-এর availability/terms/CORS আলাদা হতে পারে।
- এই ZIP তৈরি করা হয়েছে, কিন্তু তোমার ডিভাইসে install/run বা live hosting আমি এখান থেকে সরাসরি করতে পারি না। চালিয়ে দেখার পর কোনো error এলে error text দিলে ঠিক করতে সাহায্য করব।
- Production-এ HTTPS, secure cookie, backups, strict CORS এবং hosting provider অনুযায়ী persistent disk দরকার। SQLite-র জন্য persistent disk নিশ্চিত না হলে managed PostgreSQL ভালো বিকল্প।
