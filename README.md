# VoiceFlow Insurance Assistant

**Project — FORGe Hackathon 2025**  
**Team:** André Sousa, Mário Simões, Sunday Onwe, Tomás Ferreira

---

## Overview

This project was developed for a hackathon focused on integrating AI and automation to improve workflows in an insurance company.

Our goal was to create a voice-to-voice assistant that helps employees work faster and with less stress. The assistant communicates in audio, recognizes and translates multiple languages, and automates common insurance tasks such as contacting clients, reviewing active claims, and registering new ones.

---

## Core Features

- **Voice-to-Voice Communication**  
  Receives and responds to employees via audio messages (Telegram + ElevenLabs + Twilio).

- **Auto Language Recognition**  
  Detects spoken language automatically and translates to the employee’s preferred language.

- **Task Automation**
  - Contact customers whose policies are about to expire (within 30 days).  
  - Retrieve customers with claims currently under review and provide them in a CSV file.  
  - Create a new claim, automatically generating a formatted PDF report.

---

## Architecture

We used **n8n** as the backend automation engine.  
The workflow starts when a Telegram message is received by our bot.

1. **Trigger:** Telegram message → determines conversation state (`new` or `creating_claim`)  
2. **Processing:**  
   - If `new`: an AI bot analyzes intent → possible paths:
     - Fail to understand question  
     - Contact customers with expiring policies  
     - Provide a CSV list of claims under review  
     - Enter “create new claim” mode  
   - If `creating_claim`: the claim creation assistant handles the input.

---

## Conversation Paths

### 1. Fail to Understand Question
Sends an audio message (in the employee’s language) describing available options.

### 2. Contact Customers (Expiring Policies)
- Fetches customer and policy data from **Airtable**.  
- Filters customers whose policy expires in the next 30 days.  
- Sends the relevant data to a voice AI agent (ElevenLabs + Twilio).  
- The AI calls customers and notifies them about their expiring policy.  
> Currently supports English only, but multi-language support is planned.

### 3. Retrieve Claims Under Review (CSV)
- Queries **Airtable** for claims with a status marked as "Under Review".  
- Exports these records into a **CSV file**.  
- Sends the file back to the employee via chat, allowing quick access to all pending claims.

### 4. Enter “Create New Claim” Mode
Prompts the employee for the information required to register a claim.

### 5. Leave Claim Creation
Allows the employee to cancel the process and return to the main state.

### 6. Invalid Claim
Informs the employee (via audio) that the provided data was insufficient.

### 7. Valid Claim
- Fetches the related policy ID from Airtable.  
- Creates a new record in the **Claims** table.  
- Sends an audio confirmation.  
- Generates an HTML report, converts it to PDF, and sends it back in chat.

---

## Current Limitations

- The **Telegram trigger must be manually re-enabled** after each run (not persistent).  
- **Airtable cannot check for missing records**, limiting validation (e.g., unknown customers or chat IDs).  
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
| File Output | CSV Export via n8n |
| AI Processing | Custom intent recognition bot |

---

## How to Run

1. Open the **n8n** workflow.  
2. Manually enable the **Telegram trigger** associated with the bot.  
3. Send a voice message to the bot to start a conversation.  
4. Follow audio instructions to perform one of the supported actions.

---

## Future Improvements

- Persistent Telegram webhook listener  
- Airtable record validation  
- Multi-language support for the voice agent  
- Full frontend-backend integration  
- Customer-facing chat and claim management portal  
- Smarter voice intent classification  
- Dynamic CSV and PDF export customization

---

## Team

- André Sousa
- Mário Simões
- Sunday Onwe
- Tomás Ferreira
