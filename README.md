# 🚀 AI Workflow Automation – Lead Processing System

## 📌 Project Overview

This project is an **AI-powered workflow automation system** built using **n8n**, designed to process inbound leads in real-time.

It captures incoming data via a **webhook**, processes it through a **Large Language Model (LLM)** using the OpenRouter API, classifies leads into **Hot / Warm / Cold**, structures the data, and stores it in **Google Sheets**.

Additionally, it implements a **conditional alert mechanism** to notify when high-priority (Hot) leads are detected.

---

## 🎯 Objectives

* Automate lead intake using webhooks
* Classify leads using AI (LLM integration)
* Structure unstructured input into usable data
* Store processed data in cloud storage
* Trigger alerts for high-value leads

---

## 🏗️ System Architecture

```
Client (POST Request)
        ↓
Webhook (n8n)
        ↓
HTTP Request (OpenRouter LLM API)
        ↓
Edit Fields (Data Structuring)
        ↓
Edit Fields (Lead Type Extraction)
        ↓
Google Sheets (Storage)
        ↓
IF Node (Lead Filtering)
        ↓
Gmail (Alert System)
```

---

## 🛠️ Tech Stack

| Component           | Technology                   |
| ------------------- | ---------------------------- |
| Workflow Automation | n8n                          |
| AI Processing       | OpenRouter (LLM API)         |
| Data Storage        | Google Sheets                |
| Notifications       | Gmail API                    |
| Data Handling       | JavaScript Expressions (n8n) |
| API Communication   | REST APIs                    |

---

## 📂 Project Structure

```
ai-lead-automation/
│
├── workflow.json        # Exported n8n workflow
├── README.md           # Project documentation
└── screenshots/        # Workflow and output screenshots
```

---

## ⚙️ Prerequisites

* Node.js (v16+ recommended)
* npm or npx
* Google account
* OpenRouter account

---

## 🚀 Installation & Setup

### 1️⃣ Install Node.js

Download from:
https://nodejs.org

Verify installation:

```bash
node -v
npm -v
```

---

### 2️⃣ Install & Run n8n

```bash
npx n8n
```

Open:

```
http://localhost:5678
```

---

### 3️⃣ Import Workflow

1. Open n8n UI
2. Click **Import**
3. Upload `workflow.json`

---

## 🔗 API Configuration (OpenRouter)

### Create API Key

1. Visit: https://openrouter.ai
2. Sign up / Login
3. Generate API key

---

### Configure HTTP Request Node

* Method: `POST`
* URL:

```
https://openrouter.ai/api/v1/chat/completions
```

* Headers:

```json
{
  "Authorization": "Bearer YOUR_API_KEY",
  "Content-Type": "application/json"
}
```

---

### Request Body

```json
{
  "model": "openai/gpt-3.5-turbo",
  "messages": [
    {
      "role": "user",
      "content": "Classify this lead and summarize:\n\nName: {{$json.body.name}}\nCompany: {{$json.body.company}}\nMessage: {{$json.body.message}}\n\nReturn strictly:\nLead Type: (Hot/Warm/Cold)\nSummary: ..."
    }
  ]
}
```

---

## 📊 Data Processing

### Field Structuring

```js
Name → {{$node["Webhook"].json["body"]["name"]}}
Email → {{$node["Webhook"].json["body"]["email"]}}
Company → {{$node["Webhook"].json["body"]["company"]}}
AI_Output → {{$json["choices"][0]["message"]["content"]}}
```

---

### Lead Type Extraction

```js
{{$json["AI_Output"]?.match(/Lead Type:\s*(Hot|Warm|Cold)/)?.[1] || "Unknown"}}
```

---

## 📄 Google Sheets Setup

### Create Sheet

Name:

```
AI_Leads
```

### Add Headers

```
Name | Email | Company | AI_Output | Lead_Type
```

---

## 🔐 Google OAuth Configuration

### Enable APIs:

* Google Sheets API
* Google Drive API

### OAuth Setup:

* Redirect URI:

```
http://localhost:5678/rest/oauth2-credential/callback
```

* Add your email as **Test User**

---

## 📥 Google Sheets Node Configuration

* Resource: `Sheet`
* Operation: `Append Row`

### Mapping:

```js
Name → {{$json["Name"]}}
Email → {{$json["Email"]}}
Company → {{$json["Company"]}}
AI_Output → {{$json["AI_Output"]}}
Lead_Type → {{$json["Lead_Type"]}}
```

---

## 🔁 Workflow Execution

### Start n8n

```bash
npx n8n
```

---

### Activate Workflow

Click:

```
Activate
```

---

### Send Test Request

#### Using curl:

```bash
curl -X POST http://localhost:5678/webhook/lead-input -H "Content-Type: application/json" -d "{\"name\":\"Abhishek\",\"email\":\"test@gmail.com\",\"company\":\"ABC Pvt Ltd\",\"message\":\"We need pricing urgently\"}"
```

---

### Using Postman:

* Method: POST
* URL:

```
http://localhost:5678/webhook/lead-input
```

* Body:

```json
{
  "name": "Abhishek",
  "email": "test@gmail.com",
  "company": "ABC Pvt Ltd",
  "message": "We need pricing urgently"
}
```

---

## ✅ Expected Output

| Name     | Email                                   | Company     | Lead_Type |
| -------- | --------------------------------------- | ----------- | --------- |
| Abhishek | [test@gmail.com](mailto:test@gmail.com) | ABC Pvt Ltd | Hot       |

---

## ⚡ Alert System

### IF Node Condition

```js
{{$json["Lead_Type"]}} === "Hot"
```

---

### Gmail Notification

Subject:

```
🔥 HOT Lead: {{$json["Company"]}}
```

Message:

```
New HOT Lead

Name: {{$json["Name"]}}
Email: {{$json["Email"]}}
Company: {{$json["Company"]}}

{{$json["AI_Output"]}}
```

---

## 🧪 Testing Scenarios

| Input Type | Expected Result      |
| ---------- | -------------------- |
| Hot Lead   | Stored + Email Alert |
| Warm Lead  | Stored Only          |
| Cold Lead  | Stored Only          |

---

## 🐞 Troubleshooting

| Issue                  | Solution              |
| ---------------------- | --------------------- |
| Webhook not triggering | Use POST, not browser |
| OAuth error            | Add test user         |
| No columns found       | Add header row        |
| Expression not working | Use Expression mode   |
| API error              | Check API key         |

---

## 🔥 Enhancements (Future Scope)

* CRM Integration (ERPNext / Zoho)
* WhatsApp notifications
* Dashboard UI (React + Supabase)
* Lead scoring system
* Cloud deployment

---

## 💡 Key Learnings

* Workflow automation design
* API integration & handling
* AI-based classification systems
* Event-driven architecture
* Data transformation pipelines

---

## ⭐ Support

If you find this project useful, consider giving it a ⭐ on GitHub!
