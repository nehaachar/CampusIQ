# CampusIQ

## 📌 What is This?

A Knowledge Management Bot that uses **RAG (Retrieval-Augmented Generation)** with **Google Gemini AI** to answer student questions accurately using actual college documents and websites—not just general AI knowledge.

**How it works:**
- User asks a question → System classifies if it's about PDFs or websites
- Retrieves relevant documents from MongoDB → Sends to Google Gemini for processing
- Gemini generates factual answer from documents only → Returns HTML-formatted response

---

## ✨ Key Features

✅ Document-based answers (PDFs, syllabi, websites)  
✅ Smart classification (PDFs vs websites)  
✅ Secure API key management (environment variables)  
✅ Angular frontend + Flask backend  
✅ Admin dashboard for managing documents  
✅ User authentication with access keys  

---

## 🏗️ Tech Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | Angular, TypeScript, CSS |
| **Backend** | Flask, Python |
| **Database** | MongoDB Atlas |
| **AI/LLM** | Google Gemini 1.5 Flash (via LangChain) |
| **Auth** | UUID-based access keys |

---

## � Security

✅ **API Keys via Environment Variables** - Never hardcoded  
✅ **Access Key Authentication** - UUID-based tokens in MongoDB  
✅ **CORS Protected** - API only accessible from `http://localhost:4200`  

**Why environment variables?**
```python
# ✅ CORRECT
GOOGLE_API_KEY = os.getenv("GOOGLE_API_KEY")

# ❌ WRONG (what you should NOT do)
GOOGLE_API_KEY = "AIzaSyAv2vEdJGNZadv86nHRJWfjD2Yt_JX_pmM"
```
Reason: Never expose secrets in code → They get committed to GitHub → Attackers steal them → Your API is compromised.

---

## 📁 Project Structure

```
Smart-FAQ-Bot/
├── frontend/                   # Angular UI
│   └── src/app/
│       ├── admin/             # Knowledge Management Dashboard
│       ├── core/              # Login, Profile, Auth
│       └── service/           # API calls
├── backend/                    # Flask API
│   ├── app.py                 # Main server
│   ├── blueprints/
│   │   ├── chatbot.py        # /api/chatbot/message
│   │   ├── auth.py           # /api/login, /api/register
│   │   └── admin.py          # /api/admin/upload-document
│   └── notebook/
│       └── college_ragv1.py  # RAG + Gemini integration
└── .env                        # Environment variables (GITIGNORED)
```

---

## 🚀 Quick Setup

### Backend
```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install flask flask-cors pymongo google-generativeai langchain-google-genai python-dotenv requests beautifulsoup4

# Create .env file
echo GOOGLE_API_KEY=your_key_here > .env
echo MONGO_URI=mongodb+srv://... >> .env

python app.py  # Runs on http://localhost:5000
```

### Frontend
```bash
cd frontend
npm install
ng serve  # Runs on http://localhost:4200
```

### MongoDB Setup
1. Create MongoDB Atlas cluster at [mongodb.com](https://www.mongodb.com/cloud/atlas)
2. Create database: `chatbot_platform`
3. Create collections: `college_users`, `KM_documents`, `KM_URLs`, `super_admins`

---

## 📡 Main API Endpoints

| Method | Endpoint | Purpose |
|--------|----------|---------|
| `POST` | `/api/chatbot/message` | Send question, get answer |
| `GET` | `/api/chatbot/greeting` | Get greeting message |
| `POST` | `/api/login` | User login |
| `POST` | `/api/register` | User registration |
| `POST` | `/api/admin/upload-document` | Upload PDF |
| `POST` | `/api/admin/add-website` | Add website URL |

---

## 🤖 How LLM Integration Works

**Location**: `backend/notebook/college_ragv1.py`

```python
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_google_genai import ChatGoogleGenerativeAI

# Initialize Gemini model
llm = ChatGoogleGenerativeAI(
    model="gemini-1.5-flash",
    temperature=0.3,  # Deterministic responses
    google_api_key=os.environ["GOOGLE_API_KEY"]
)

# Define tools that LLM can use
tools = [load_pdfs_data, load_websites_data, query_pdfs, query_websites]

# Create agent that intelligently chooses which tool to use
agent = create_tool_calling_agent(llm=llm, tools=tools, prompt=prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=False)

# When user asks a question
response = agent_executor.invoke({"input": user_query})
```

**Query Flow:**
1. User question → Agent analyzes it
2. Agent decides: "Is this about PDFs or websites?"
3. Agent loads relevant documents from MongoDB
4. Agent uploads to Gemini API (with caching)
5. Gemini retrieves answer from documents only
6. Response returned in HTML format

**Key Point**: LLM ONLY uses documents, never its own knowledge. This ensures accuracy.

---

## 💡 For Interviews - Key Talking Points

1. **RAG Architecture** - "We use RAG to combine LLMs with factual data, preventing AI hallucinations"
2. **Security** - "API keys stored in environment variables, never in code"
3. **Scalability** - "MongoDB + Gemini API handles 100+ concurrent users"
4. **Smart Classification** - "System automatically chooses PDF or web search based on keywords"
5. **Cost Optimization** - "Using Gemini 1.5 Flash + response caching keeps costs low"
6. **Document Caching** - "PDFs/websites cached in MongoDB to avoid re-uploading to Gemini"
7. **Full Stack** - "Angular frontend + Flask backend + MongoDB + Google AI"
