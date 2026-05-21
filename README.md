# ⚡ Customer Feedback Automation

An automated backend workflow built with **n8n** and **OpenAI (GPT-4o-mini)** that streamlines customer satisfaction survey collection. This system instantly captures user form submissions, logs responses into Google Sheets for data retention, and utilizes generative AI to extract and draft structured, professional confirmation emails.

---

## 🚀 Workflow Architecture

The workflow is completely event-driven and executes sequentially through the following pipeline stages:

1. **On Form Submission (Trigger):** Captures incoming data from the *ElectroHub Customer Satisfaction Survey* (Name, Address, Email, Phone Number, Satisfaction Level, and Suggestions).
2. **Append Row in Sheet:** Automatically maps and appends the raw customer responses to a centralized tracking Google Sheet for record-keeping and data analytics.
3. **AI Agent (OpenAI):** Forwards the form contents to an OpenAI model using a strict system prompt instruction set. The LLM processes the payload and returns a deterministic, structured JSON response containing a customized email subject line and a polite body summary.
4. **Edit Fields (Set Node):** Uses native JavaScript (`JSON.parse()`) within n8n to split the AI's response text into isolated, clean string variables (`Subject` and `Body`), stripping out raw format escape characters.
5. **Send a Message (Gmail):** Dispatches the professionally formatted acknowledgment email directly back to the customer's submitted email address.

---

## 🛠️ Tech Stack & Nodes Used

* **n8n** (Workflow Orchestration & Node Wiring)
* **n8n Form Trigger** (Front-end Data Intake)
* **Google Sheets Node** (Data Persistence)
* **Advanced AI Agent Node** + **OpenAI Chat Model** (Structured Content Engine)
* **Set Node** (Data Transformation & String Sanitization)
* **Gmail Node** (SMTP Output/Communication Layer)

---

## 📋 Survey Data Schema

The workflow dynamically processes the following fields from the submission payload:

| Field Label | Input Type | Description |
| :--- | :--- | :--- |
| **Full Name** | Text | The customer's legal name |
| **Address** | Text | Customer's delivery/location details |
| **Email** | Email | Target inbox for the confirmation message |
| **Phone Number** | Text | Contact number |
| **Satisfaction Level** | Radio | Options: `Satisfied` \| `Normal` \| `Not satisfied` |
| **Suggestion** | Text | Direct open-ended feedback text from the client |

---

## 🧠 AI Agent Prompt Engineering & JSON Structuring

To eliminate unparsed text and structural breakage (such as raw `\n` characters appearing in output items), the workflow enforces structured JSON generation through its **System Message**:

```text
You are a professional automated email assistant. Your job is to draft a polite, corporate-grade acknowledgment email to a user who has submitted a form.

CRITICAL REQUIREMENT: You must respond ONLY with a valid JSON object containing exactly two items: "subject" and "body". Do not include any introductory text, markdown code blocks, or conversational filler.

Format the "body" text professionally, using standard line breaks for paragraphs and clean bullet points for data presentation.
