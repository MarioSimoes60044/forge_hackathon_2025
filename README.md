# VoiceFlow Insurance Assistant

**Project — FORGe Hackathon 2025**  
**Team:** André Sousa, Mário Simões, Sunday Onwe, Tomás Ferreira

---

## Overview

This project was developed for a hackathon focused on integrating AI and automation to improve workflows in an insurance company.

Our goal was to create a voice-to-voice assistant that helps employees work faster and with less stress. The assistant communicates in audio, recognizes and translates multiple languages, and automates common insurance tasks such as contacting clients, reviewing claims, and registering new ones.

---

## Core Features

- **Voice-to-Voice Communication**  
  Receives and responds to employees via audio messages (Telegram + ElevenLabs + Twilio).

- **Auto Language Recognition**  
  Detects spoken language automatically and translates to the employee’s preferred language.

- **Task Automation**  
  - Contact customers whose policies are about to expire (within 30 days).  
  - Contact customers who still have unpaid fees.  
  - Retrieve claims currently under review and provide them as a CSV file.  
  - Create a new claim, automatically generating a formatted PDF report.

---

## Architecture

We used **n8n** as the backend automation engine.  
The workflow starts when a Telegram message is received by our bot.

1. **Trigger:** Telegram message → determines conversation state (`new` or `creating_claim`)  
2. **Processing:**  
   - If `new`: an AI bot analyzes intent and directs the flow according to the following ordered paths:
     1. Fail to understand question  
     2. Contact customers with expiring policies  
     3. Retrieve claims under review (CSV)  
     4. Contact customers with unpaid fees  
     5. Enter “create new claim” mode  
   - If `creating_claim`: the claim creation assistant handles the input and follows the leave/invalid/valid claim steps as appropriate.

---

## Conversation Paths (ordered workflow)

### 1. Fail to Understand Question
When the assistant cannot parse the employee's intent, it sends an audio message (in the employee’s language) describing the available options and how to trigger them.

### 2. Contact Customers (Expiring Policies)
- Fetches customer and policy data from **Airtable**.  
- Filters customers whose policy expires in the next 30 days.  
- Sends the relevant data to a voice AI agent (ElevenLabs + Twilio).  
- The AI calls customers and notifies them about their expiring policy.  
> Currently supports English only; multi-language support is planned.

### 3. Retrieve Claims Under Review (CSV)
- Queries **Airtable** for claims with a status marked as "Under Review".  
- Exports these records into a **CSV file**.  
- Sends the file back to the employee via chat for quick access to pending claims.

### 4. Contact Customers (Unpaid Fees)
- Fetches customer and payment data from **Airtable**.  
- Filters customers with outstanding or overdue fees.  
- Passes the filtered data to a voice AI agent (ElevenLabs + Twilio).  
- The AI calls customers to remind them of pending payments and provides guidance on how to proceed.  
> Future improvement: enable multilingual and personalized call messages.

### 5. Enter “Create New Claim” Mode
Prompts the employee (via audio) for the information required to register a claim and changes the conversation state to `creating_claim`.

#### Sub-Paths (handled within claim creation)
- **Leave Creating Claim** → Cancels the claim creation process and returns to the `new` state.  
- **Invalid Claim** → Requests missing or corrected data if information is insufficient.  
- **Valid Claim** → Fetches related policy ID, creates a record in **Airtable**, sends an audio confirmation, and generates a PDF report.

---

## Current Limitations

- The **Telegram trigger must be manually re-enabled** after each run (not persistent), which prevents a seamless always-listening flow.  
- **Airtable cannot check for missing records** in the current setup, limiting reliable validation (e.g., unknown customers or chat IDs).  
- The **frontend is not yet connected** to the backend.

---

## Frontend (Stitch Prototype)

A basic frontend prototype was developed in **Stitch (VS Code)**.

Planned features for employees:
- Login and profile management  
- View and filter claims, customers, and policies  
- Create new claims  
- Chat assistant using our n8n backend  
- Flag urgent business and display recent activities  

Customer side (future):
- Check policy details and claim status  
- Submit new claims  
- Receive automated, friendly voice assistance  
- Potential “sentient” urgency detection (e.g., for emergency claims)

---

## Technologies Used

| Component | Technology |
|------------|-------------|
| Automation / Logic | n8n |
| Messaging | Telegram Bot API |
| Voice / Speech | ElevenLabs, Twilio |
| Database | Airtable |
| Frontend | Stitch (VS Code) |
| PDF Generation | HTML → PDF conversion |
| File Output | CSV export via n8n |
| AI Processing | Custom intent recognition bot |

---

## How to Run

1. Open the **n8n** workflow.  
2. Manually enable the **Telegram trigger** associated with the bot.  
3. Send a voice message to the bot to start a conversation.  
4. Follow audio instructions to perform one of the supported actions (call about expiring policies, fetch claims CSV, call about fees, create new claim, etc.).

---

## Future Improvements

- Persistent Telegram webhook listener to maintain continuous conversation flow.  
- Airtable record validation and robust error handling for missing or duplicated records.  
- Multi-language support for the voice agent and call personalization.  
- Personalized message templates for fee reminders and claim notifications.  
- Full frontend-backend integration and employee authentication.  
- Customer-facing chat and claim management portal.  
- Smarter voice intent classification and configurable CSV/PDF export options.
