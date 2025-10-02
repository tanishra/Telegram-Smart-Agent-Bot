# 🤖 Telegram SmartAgent Bot

**SmartAgent Bot** is an intelligent, AI-driven Telegram bot powered by [n8n](https://n8n.io) and [Gemini 2.5](https://deepmind.google/technologies/gemini/). It can process both voice and text messages, route tasks to smart agents (like Email, Calendar, Stocks, and more), generate AI-based responses or images, and log everything in Google Sheets.

---

## 🧠 What It Does

SmartAgent Bot acts as a **unified interface for task automation**, using AI orchestration and tool-using agents. Users can interact via Telegram using voice or text, and the bot will understand their intent and respond appropriately — whether it's:

- Answering questions via LLM
- Managing emails
- Scheduling calendar events
- Looking up stock prices
- Generating creative prompts or images
- Saving outputs to Google Sheets / Drive

---

## 🏗️ Architecture Overview

### 1. Message Input
- User sends either a **text** or **voice** message via Telegram.
- If it's a **voice message**, it's transcribed to text using **Gemini**.

### 2. Message Type Check (Switch Node)
- The system checks if the user is requesting an image or not.

---

### 🖼️ If Image Generation is Requested:
- Generate a prompt using **Gemini 2.5 Flash**.
- Generate a filename using **Gemini 2.5 Flash**.
- Generate the image using **Flux.1-Schnell** model.
- Download the image.
- Send the image to the user via Telegram.
- Save the image to **Google Drive**.
- Log the metadata in **Google Sheets**.

---

### 🤖 If Image Generation is Not Requested:
- Query is passed to the **AI Orchestrator** (Gemini 2.5 Flash).
- Orchestrator intelligently chooses the correct agent:
  - 📧 **Email Agent**
  - 📅 **Calendar Agent**
  - 📈 **Stock Agent**
  - 📊 **Growth Agent**
  - ✍️ **Prompt Agent**
  - Or falls back to **LLM** if no agent is relevant.

- Once the task is processed:
  - Extract clear information for the user.
  - Append the data to **Google Sheets**.
  - Send a reply via **Telegram**.
  - If sending fails, send an **email notification** to you.

---

## 🧩 Agents & Their Capabilities

Each agent is powered by its own **Gemini 2.5 Flash model** and has specific tools:

### 📧 Email Agent
Handles tasks related to Gmail.

- `Get Emails`
- `Label Emails`
- `Mark as Unread`
- `Create Draft`
- `Send Email`
- `Get Labels`
- `Reply to Emails`

### 📅 Calendar Agent
Manages events in Google Calendar.

- `Create Event`
- `Create Event with Attendee`
- `Update Event`
- `Delete Event`
- `Get Events`

### 📈 Stock Agent
Fetches live stock data using SerpAPI.

- `Web Search Tool` (real-time stock prices, market news)

### 📊 Growth Agent
Finds growth strategies and trends using SerpAPI.

- `Web Search Tool` (business ideas, market research)

### ✍️ Prompt Agent
Creative writing, brainstorming, or prompt generation.

- Just LLM access (Gemini 2.5 Flash)

---

## 🖼️ Image Generation Workflow

When the user asks to generate an image:

1. **Prompt Generation** → Gemini 2.5 Flash generates a creative prompt.
2. **File Name Creation** → Another Gemini 2.5 Flash instance generates a Google Drive-safe file name.
3. **Image Generation** → Uses `Flux.1-Schnell` model to generate the image.
4. **Image Handling**:
   - Extract image URL
   - Download image
   - Send it to user in Telegram
   - Save it in Google Drive
   - Log metadata to Google Sheets

---

## ✅ Features

- 🎙️ Voice-to-text via Gemini
- 🧠 AI-powered Orchestration
- 🧩 Modular Agent Design
- 📤 Auto Logging to Google Sheets
- 🖼️ Image Generation Support
- 📧 Email + 📅 Calendar Automation
- 🌐 Live Web Search via SerpAPI
- ⚠️ Fallback Notifications via Email

---

## ⚙️ Setup Guide

### 1. 📦 Requirements

- [n8n](https://n8n.io)
- Telegram Bot Token
- Gemini API Key
- Google APIs (Sheets, Drive, Gmail, Calendar)
- SerpAPI Key

### 2. 📁 Clone the Repo

```bash
git clone https://github.com/tanishra/Telegram-Smart-Agent-Bot.git
cd Telegram-Smart-Agent-Bot
```

### 3. 🛠️ Environment Setup
Create a .env file based on env.example:

- TELEGRAM_BOT_TOKEN= your_bot_token
- GEMINI_API_KEY= your_gemini_key
- GOOGLE_SHEETS_ID= your_sheet_id
- GOOGLE_DRIVE_FOLDER_ID= folder_id
- SERPAPI_KEY= your_serpapi_key
- YOUR_EMAIL= your_email_for_fallbacks

4. 🔄 Import Workflow into n8n
- Open your n8n instance
- Go to Workflows → Import
- Upload workflows/Telegram_Smart_Agent_Bot.json

5. Start Execution

--- 

## 🧪 Testing the Bot

1. **Start the Telegram bot** and send a message (text or voice).

2. **Try these example prompts:**
   - 📈 “What is the stock price of Apple?”
   - 📅 “Schedule a meeting tomorrow at 5 PM”
   - 📧 “Reply to this email with thanks”
   - 🖼️ “Generate an image of a robot meditating on a cloud”

3. **Observe the outputs:**
   - ✅ Response in Telegram chat
   - 📊 Log entry in Google Sheets
   - 📁 Image saved to Google Drive (if applicable)
   - ✉️ Email sent to you in case of failure


---

## 🧯 Troubleshooting

| Issue                            | Possible Cause                                       | Solution                                                                 |
|----------------------------------|------------------------------------------------------|--------------------------------------------------------------------------|
| No response in Telegram          | Bot not connected or token invalid                   | Double-check the `TELEGRAM_BOT_TOKEN` and ensure the bot is started     |
| Voice not transcribed            | Gemini API limit or voice file not accessible        | Check Gemini API usage and file permissions                             |
| AI Orchestrator returns error    | Gemini model failure or bad input                    | Add validation and fallback handling in the workflow                    |
| Email actions fail               | Google API scopes or token expired                   | Re-authenticate Gmail node with correct OAuth scopes                    |
| Calendar events not created      | Date format or missing fields                        | Ensure date/time is parsed and formatted correctly                      |
| Stock or growth agent returns empty| SerpAPI limit reached or query too vague          | Check SerpAPI usage dashboard and refine queries                        |
| Image not generated              | Prompt too generic or file name issues               | Improve prompt clarity and ensure valid file name                       |
| File not saved to Drive          | Incorrect folder ID or Google Drive API error        | Double-check `GOOGLE_DRIVE_FOLDER_ID` and folder permissions            |
| Sheet row not appended           | Google Sheet ID missing or incorrect columns         | Ensure the sheet ID is correct and columns match expected format        |
| Telegram message fails to send   | Chat ID missing or too large file                    | Check Telegram node config and reduce message/image size                |
| Fallback email not sent          | Email not authenticated or SMTP error                | Verify Gmail connection and check fallback node                         |
| n8n crashes on load              | Too many simultaneous tasks or memory leak           | Enable queuing, optimize nodes, increase server memory                  |


--- 

## 🤝 Contribution

Contributions, ideas, and improvements are welcome!

### 🧾 Ways to Contribute

- 🐛 Report bugs via [Issues](https://github.com/tanishra/Telegram-Smart-Agent-Bot/issues)
- ✨ Suggest new agents, tools, or features
- 🛠️ Submit a pull request to improve code or docs
- 📄 Improve the documentation or add diagrams