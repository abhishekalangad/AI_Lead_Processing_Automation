# 🚀 AI Lead Processing Automation (n8n + OpenRouter + Google Sheets)

## 📌 Overview

This project is an **AI-powered lead processing automation system** built using **n8n**.
It captures incoming leads via a webhook, processes them using an LLM (via OpenRouter), classifies them into **Hot / Warm / Cold**, and stores the structured data in **Google Sheets**.

Additionally, it triggers **real-time alerts for high-priority (Hot) leads**, making it suitable for real-world CRM workflows.

---

## 🎯 Key Features

* 🔗 Webhook-based lead ingestion
* 🤖 AI-powered lead classification (Hot / Warm / Cold)
* 🧠 Automated data extraction & structuring
* 📊 Google Sheets integration for storage
* ⚡ Real-time alert system for Hot leads
* 🧩 Modular workflow design using n8n

---

## 🏗️ Architecture

```
Webhook
   ↓
HTTP Request (AI via OpenRouter)
   ↓
Edit Fields (Structure Data)
   ↓
Edit Fields (Extract Lead Type)
   ↓
Google Sheets (Store Data)
   ↓
IF Node (Check Hot Lead)
   ↓
Gmail (Send Alert)
```

---

## 🛠️ Tech Stack

* **n8n** – Workflow automation
* **OpenRouter API** – LLM processing
* **Google Sheets API** – Data storage
* **Gmail API** – Email notifications
* **JavaScript Expressions (n8n)** – Data transformation

---

## ⚙️ Setup Instructions

### 1️⃣ Install n8n

```bash
npm install -g n8n
n8n
```

Access:

```
http://localhost:5678
```

---

### 2️⃣ Create Webhook Node

* Method: `POST`
* Path: `/lead-input`

Example Payload:

```json
{
  "name": "Abhishek",
  "email": "test@gmail.com",
  "company": "ABC Pvt Ltd",
  "message": "We are interested in your product pricing"
}
```

---

### 3️⃣ AI Processing (HTTP Request Node)

Use OpenRouter API:

* URL:

```
https://openrouter.ai/api/v1/chat/completions
```

* Method: `POST`

* Headers:

```json
{
  "Authorization": "Bearer YOUR_API_KEY",
  "Content-Type": "application/json"
}
```

* Body:

```json
{
  "model": "openai/gpt-3.5-turbo",
  "messages": [
    {
      "role": "user",
      "content": "Classify this lead as Hot, Warm, or Cold and summarize:\n\nName: {{$json[\"body\"][\"name\"]}}\nCompany: {{$json[\"body\"][\"company\"]}}\nMessage: {{$json[\"body\"][\"message\"]}}\n\nReturn format:\nLead Type: (Hot/Warm/Cold)\nSummary: ..."
    }
  ]
}
```

---

### 4️⃣ Structure Data (Edit Fields Node)

Create fields:

```js
Name → {{$json["body"]["name"]}}
Email → {{$json["body"]["email"]}}
Company → {{$json["body"]["company"]}}
AI_Output → {{$json["choices"][0]["message"]["content"]}}
```

---

### 5️⃣ Extract Lead Type

Add new field:

```js
Lead_Type → {{$json["AI_Output"]?.match(/Lead Type:\s*(Hot|Warm|Cold)/)?.[1] || "Unknown"}}
```

---

### 6️⃣ Google Sheets Setup

#### Create Sheet:

```
AI_Leads
```

#### Add Headers (Row 1):

```
Name | Email | Company | AI_Output | Lead_Type
```

---

### 7️⃣ Connect Google Sheets (OAuth)

* Enable APIs:

  * Google Sheets API
  * Google Drive API

* Configure OAuth:

  * Redirect URL:

```
http://localhost:5678/rest/oauth2-credential/callback
```

* Add yourself as **Test User**

---

### 8️⃣ Google Sheets Node

* Resource: `Sheet`
* Operation: `Append Row`

Mapping:

```js
Name → {{$json["Name"]}}
Email → {{$json["Email"]}}
Company → {{$json["Company"]}}
AI_Output → {{$json["AI_Output"]}}
Lead_Type → {{$json["Lead_Type"]}}
```

---

### 9️⃣ Add IF Node (Hot Leads)

Condition:

```js
{{$json["Lead_Type"]}} === "Hot"
```

---

### 🔟 Gmail Alert Node

* Trigger: IF → TRUE branch

Message:

```
🔥 New HOT Lead

Name: {{$json["Name"]}}
Company: {{$json["Company"]}}
Email: {{$json["Email"]}}

{{$json["AI_Output"]}}
```

---

## 🧪 Testing

### Sample Input

```json
{
  "name": "Rahul",
  "email": "rahul@test.com",
  "company": "Big Corp",
  "message": "We need pricing urgently"
}
```

### Expected Output

* Row added in Google Sheets ✅
* Lead classified as "Hot" ✅
* Email alert triggered ✅

---

## 📊 Output Example

| Name  | Email                                   | Company  | Lead_Type |
| ----- | --------------------------------------- | -------- | --------- |
| Rahul | [rahul@test.com](mailto:rahul@test.com) | Big Corp | Hot       |

---

## 🚧 Common Issues & Fixes

| Issue                   | Fix                       |
| ----------------------- | ------------------------- |
| OAuth Error             | Add Gmail as test user    |
| No columns found        | Add header row in Sheet   |
| Expression not working  | Switch to Expression mode |
| AI_Output parsing fails | Use safe regex            |

---

## 🔥 Future Enhancements

* CRM Integration (ERPNext / Zoho)
* WhatsApp / Slack alerts
* Lead scoring system
* Dashboard (React + Supabase)
* Deployment on cloud

---

## 📌 Project Value

This project demonstrates:

* Workflow automation
* API integration
* AI-based decision systems
* Real-time event processing
* Practical business use-case

---

## ⭐ If you found this useful, give it a star!
