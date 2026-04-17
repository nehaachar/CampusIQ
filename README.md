# CampusIQ

## 📌 What is This?

A Knowledge Management Bot that uses **RAG (Retrieval-Augmented Generation)** with **Google Gemini AI** to answer student questions accurately using actual college documents and websites—not just general AI knowledge.

**How it works:**
- User asks a question → System classifies if it's about PDFs or websites
- Retrieves relevant documents from MongoDB → Sends to Google Gemini for processing
- Gemini generates factual answer from documents only → Returns context aware response

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
6. Fully generated Response is returned 


---
## Output

**College Admin Page**:

<img width="729" height="562" alt="image" src="https://github.com/user-attachments/assets/52148114-bb0a-42e0-88f8-e76f12351ed6" />

**Super Admin Page**:
<img width="712" height="562" alt="image" src="https://github.com/user-attachments/assets/5a4ecf3a-929c-4185-8c62-a6f0d01340e9" />

**Upload Website links**:
<img width="717" height="533" alt="image" src="https://github.com/user-attachments/assets/01bffc07-caf3-4783-b655-798ba69ed62f" />

**Upload pdfs**:
<img width="717" height="533" alt="image" src="https://github.com/user-attachments/assets/a0bdd924-532f-47a7-bce4-2e4416f4c9ea" />

**Chatbot Response for website links**
<img width="716" height="565" alt="image" src="https://github.com/user-attachments/assets/494af07b-29ab-4202-822b-70ee6b6853d9" />

**Chatbot response for pdfs**
<img width="732" height="565" alt="image" src="https://github.com/user-attachments/assets/b1e2d230-1bcd-4908-908d-878ea6f4a48c" />





