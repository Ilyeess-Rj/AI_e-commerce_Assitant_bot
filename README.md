# 🤖 Synapse Digital — AI Business Assistant

![n8n](https://img.shields.io/badge/Built%20with-n8n-EA4B71?logo=n8n\&logoColor=white)
![AI Agent](https://img.shields.io/badge/AI-Agent-8A2BE2)
![Telegram](https://img.shields.io/badge/Telegram-Bot-26A5E4?logo=telegram\&logoColor=white)
![Languages](https://img.shields.io/badge/Languages-Arabic%20%7C%20French%20%7C%20English-orange)
![MongoDB](https://img.shields.io/badge/Database-MongoDB-47A248?logo=mongodb\&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-4169E1?logo=postgresql\&logoColor=white)
![Google Calendar](https://img.shields.io/badge/Scheduling-Google%20Calendar-4285F4?logo=googlecalendar\&logoColor=white)
![License](https://img.shields.io/badge/License-Apache%202.0-blue)

**Synapse Digital** is a multilingual **AI business assistant and intelligent automation workflow** built with **n8n** for an electronics repair and sales business.

The assistant operates through **Telegram**, supports both **text and voice interactions**, and can handle customer appointments, repair requests, complaints, appointment modifications and cancellations.

Rather than acting as a simple chatbot, the AI Agent is connected to real business tools and follows predefined business rules before performing important operations.

The workflow combines:

**AI Agents + Business Rules + Databases + Calendar Automation + Voice AI + Telegram**

---

## 🌍 Multilingual AI Assistant

The assistant supports three languages:

* 🇹🇳 **Arabic**
* 🇫🇷 **French**
* 🇬🇧 **English**

The customer can communicate naturally in any of the supported languages.

The assistant detects the conversation language and responds accordingly.

The same business logic is applied regardless of the selected language.

---

# ✨ Key Features

## 💬 Natural Conversation

The assistant can understand customer requests and guide the conversation step by step.

It does not require the customer to know a specific command structure.

For example, a customer can simply ask to:

* Book a repair appointment
* Report a problem
* Check an existing booking
* Modify an appointment
* Cancel an appointment
* Ask about a repair
* Ask about an estimated repair price

The AI Agent determines the appropriate workflow and uses the required tools.

---

# 🎙️ Text & Voice Interaction

The assistant supports both:

### Text

```text
Telegram message
        ↓
AI Agent
        ↓
Telegram text response
```

### Voice

```text
Telegram voice message
        ↓
Speech-to-Text
        ↓
AI Agent
        ↓
Text-to-Speech
        ↓
Telegram audio response
```

Voice messages are transcribed before being processed by the AI Agent.

The generated response can then be converted back into speech and sent to the customer.

This allows the system to operate as a **voice-enabled virtual receptionist**.

---

# 📅 Intelligent Appointment Booking

The assistant can manage the complete appointment booking process.

It collects the required information step by step, including:

* Full name
* Phone number
* National ID / identification number
* Device brand and model
* Device problem
* Preferred date
* Preferred time

The assistant does not immediately create the appointment.

Instead, it validates the information and checks the required business conditions first.

---

# 🔐 Rule-Based Data Validation

The assistant uses explicit validation rules defined inside the **AI Agent system prompt**.

For example, the current configuration can require:

* A specific number of digits
* Specific allowed starting digits
* A specific phone-number format
* A specific national-ID format

The current example uses an **8-digit format with configured prefixes**.

However, these are **not hard-coded universal rules**.

The user deploying the workflow can modify the corresponding validation instructions directly inside the AI Agent prompt according to:

* Country
* Local phone-number format
* National identification system
* Business requirements

### Example

```text
Current configuration:
8 digits
Specific allowed prefixes

Can be changed to:
9 digits
10 digits
Different prefixes
Different validation rules
```

No change to the overall workflow architecture is required.

---

# 🛠️ Repair Verification & Pricing

The assistant does not freely invent repair prices.

It follows a predefined **accepted repair catalog** contained in the AI Agent instructions.

The catalog can contain supported:

* Smartphones
* iPhones
* Tablets
* Laptops
* MacBooks
* Desktop computers
* GPUs
* Monitors
* PlayStation 5
* Controllers
* Other supported devices

For a recognized device and problem, the assistant provides the corresponding configured price or price range.

If the requested repair is not part of the accepted repair list, the assistant does not pretend that the repair is supported.

---

# 🔎 Product Verification

For complaint processing, the assistant can verify the customer's product against a **MongoDB database**.

The workflow searches the `products` collection using the product information provided by the customer.

Conceptually:

```text
Customer provides product
          ↓
      MongoDB search
          ↓
     Product found?
       /       \
     No         Yes
     ↓           ↓
Ask customer    Continue
to verify       complaint
```

This prevents the complaint workflow from automatically accepting a product that cannot be found in the business records.

If the product cannot be found, the assistant asks the customer to verify the information or provide the appropriate proof of purchase.

---

# 📢 Complaint Management

The assistant can also manage customer complaints.

The complaint flow collects information such as:

* Customer identity
* Phone number
* National ID
* Product
* Problem type
* Detailed description

Before proceeding, the product is checked against MongoDB.

Once the required information has been collected and confirmed by the customer, the complaint can be registered.

The assistant generates a private complaint reference such as:

```text
REC-123456
```

The customer is instructed to keep the reference and present it when visiting the business, together with the required proof of purchase.

The assistant can also apologize for the inconvenience as part of the customer interaction.

---

# 🔑 Booking Reference

After a successful appointment confirmation, the workflow generates a private booking reference.

Example:

```text
SYNP12345
```

The customer is instructed to keep this reference.

When visiting the business, the customer may be required to provide:

* Booking reference
* Identification document

The booking reference is also stored in the Google Calendar event description so that the appointment can later be identified.

---

# ⚠️ Confirmation Before Execution

One of the important characteristics of the system is that the AI Agent does not immediately execute a booking after collecting the information.

The assistant first summarizes the customer's information.

For example:

```text
Name
Phone number
National ID
Device
Problem
Estimated price
Date
Time
```

The customer must confirm the information.

Only after confirmation can the workflow create the appointment.

This reduces the risk of creating appointments from incomplete or misunderstood information.

---

# 📆 Intelligent Scheduling

Appointment scheduling is controlled by explicit business rules and Google Calendar checks.

Before creating or modifying an appointment, the workflow checks the required conditions.

## 1. Sunday Check

Sunday is configured as the weekly closing day.

Appointments are therefore rejected for Sunday.

---

## 2. Holiday Check

The workflow checks whether the requested date corresponds to a holiday.

The current configuration includes:

* A configurable holiday list
* Google Calendar holiday verification

The Google Calendar holiday source can be changed according to the country where the workflow is deployed.

---

## 3. Business Hours

The current example uses:

| Day       | Opening Hours |
| --------- | ------------- |
| Monday    | 08:00 – 18:00 |
| Tuesday   | 08:00 – 18:00 |
| Wednesday | 08:00 – 18:00 |
| Thursday  | 08:00 – 18:00 |
| Friday    | 08:00 – 16:30 |
| Saturday  | 08:00 – 18:00 |
| Sunday    | Closed        |

These rules are defined in the AI Agent instructions and can be changed according to the business.

---

## 4. Existing Appointment Check

Before creating an appointment, the workflow checks Google Calendar.

If the requested time slot is already occupied:

```text
Requested slot
      ↓
Calendar availability check
      ↓
Already occupied?
      ↓
Yes → Reject requested slot
      ↓
Suggest available alternatives
```

The assistant can then provide available alternatives within the configured working hours.

---

# 🔄 Appointment Modification

Existing appointments can be modified.

The workflow first searches Google Calendar for the existing booking.

The booking can be identified using information stored inside the event, including:

* Booking reference
* National ID

Before moving the appointment to a new date/time, the workflow checks:

1. The existing booking
2. Holiday restrictions
3. Business hours
4. New slot availability
5. Customer confirmation

The original booking reference remains associated with the appointment.

---

# ❌ Appointment Cancellation

Customers can also request cancellation.

The workflow verifies the existing appointment before deleting it.

Cancellation requires explicit confirmation from the customer.

The workflow therefore avoids immediately deleting an appointment based on an ambiguous request.

---

# 🧠 AI Agent Tool Architecture

The AI Agent is connected to several specialized tools.

### Google Calendar

Used for:

* Creating appointments
* Reading appointments
* Updating appointments
* Cancelling appointments
* Checking availability
* Checking holidays

### MongoDB

Used for:

* Product verification
* Searching business product records

### PostgreSQL

Used for:

* Storing complaint information
* Creating structured records for future reporting and analytics

### ElevenLabs

Used for:

* Speech-to-text
* Text-to-speech

### Telegram

Used as the customer-facing interface.

### LLM Providers

The workflow supports configurable LLM connections through providers such as:

* OpenRouter
* Qwen Cloud

The model can be changed without redesigning the entire workflow.

---

# 🏗️ Architecture

The workflow contains separate processing paths for text and voice interactions while using the same overall business logic and tools.

![Synapse Digital Workflow](screenshots/workflow_overview_upscaled.png)

### High-level architecture

```text
                         TELEGRAM
                            │
                 ┌──────────┴──────────┐
                 │                     │
               TEXT                  VOICE
                 │                     │
                 │              Speech-to-Text
                 │                     │
                 └──────────┬──────────┘
                            ↓
                       AI AGENT
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ↓                 ↓                 ↓
   Google Calendar       MongoDB         PostgreSQL
          │                 │                 │
          │                 │                 │
   Scheduling          Product check     Complaints
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ↓
                    Business Rules
                            │
                  Customer Confirmation
                            │
                    ┌───────┴───────┐
                    │               │
                  TEXT            VOICE
                    │               │
                    │        Text-to-Speech
                    │               │
                    └───────┬───────┘
                            ↓
                         TELEGRAM
```

---

# 🗄️ PostgreSQL Complaint Storage

Confirmed complaints can be stored in PostgreSQL using the `reclamations` table.

This creates structured business data that can later be used for:

* Reporting
* Business intelligence
* Complaint statistics
* Historical analysis
* Customer service analytics
* Dashboards

The workflow therefore provides a foundation for extending the system beyond conversational automation into **business intelligence and analytics**.

---

# 🧩 Conversation Memory

The workflow uses n8n memory nodes to maintain conversation context.

This allows the assistant to remember information already provided by the customer during the current conversation instead of repeatedly asking for the same information.

For example:

```text
Customer: I want to repair my iPhone.
AI: What is your name?

Customer: Ahmed.
AI: What is your phone number?

Customer: 12345678.
AI: What is the problem with your iPhone?
```

The assistant can maintain the context of the conversation while collecting the required information.

---

# 🛡️ Controlled AI Behavior

The AI Agent is not intended to operate as an unrestricted general-purpose chatbot.

Its instructions define the role of the assistant and the business operations it is allowed to perform.

The workflow can therefore be configured to:

* Stay within the business scope
* Follow predefined validation rules
* Use specific tools for specific operations
* Require confirmation before critical actions
* Reject unsupported requests
* Avoid inventing repair prices
* Verify products before processing complaints
* Verify calendar availability before creating appointments

This creates a controlled **AI Agent + deterministic business workflow** architecture.

---

# 🛠️ Technology Stack

| Layer               | Technology              |
| ------------------- | ----------------------- |
| Automation          | n8n                     |
| AI Agent            | n8n AI Agent            |
| Customer Interface  | Telegram                |
| Speech-to-Text      | ElevenLabs              |
| Text-to-Speech      | ElevenLabs              |
| LLM                 | OpenRouter / Qwen Cloud |
| Scheduling          | Google Calendar         |
| Product Database    | MongoDB                 |
| Complaint Database  | PostgreSQL              |
| Conversation Memory | n8n Memory              |

---

# 📁 Project Structure

```text
synapse-digital-bot/
│
├── README.md
├── LICENSE
│
├── screenshots/
│   └── workflow_overview_upscaled.png
│
└── workflow/
    └── Synapse_Digital_Assistant.json
```

---

# 🚀 Installation

## 1. Install n8n

Install or run your own n8n instance.

The workflow is designed to be imported into n8n.

---

## 2. Import the Workflow

In n8n:

**Workflows → Import from File**

Then select:

```text
workflow/Synapse_Digital_Assistant.json
```

---

## 3. Configure Credentials

You must connect your own credentials for the services used by the workflow.

Required integrations may include:

* Telegram Bot API
* ElevenLabs
* Google Calendar OAuth2
* MongoDB
* PostgreSQL
* OpenRouter
* Qwen Cloud

The repository does not provide production credentials.

---

# ⚙️ Configuration

After importing the workflow, configure the credentials and business-specific values.

## Telegram

Connect your Telegram Bot credentials.

Create a bot using Telegram's official bot management interface and connect its credentials to the workflow.

---

## Google Calendar

Connect your Google Calendar account.

Configure the calendar used for appointments.

If required, configure the holiday calendar for the country where the business operates.

---

## MongoDB

Configure the MongoDB connection.

The workflow currently uses a:

```text
products
```

collection for product verification.

Adapt the collection and fields according to your database structure.

---

## PostgreSQL

Configure the PostgreSQL connection.

The workflow currently writes complaint records to:

```text
reclamations
```

Adapt the database schema if your business uses different fields.

---

## ElevenLabs

Connect your ElevenLabs credentials for:

* Speech-to-text
* Text-to-speech

---

## LLM Provider

Configure the desired LLM provider.

The workflow includes support for OpenRouter and Qwen Cloud connections.

The model can be changed according to your requirements, availability and provider configuration.

---

# 🎛️ Customization

A major design principle of this project is that many business rules are written directly inside the **AI Agent prompt**.

This means the workflow can be adapted without rewriting the entire automation architecture.

You can modify:

### Customer validation

* Phone number format
* National ID format
* Required prefixes
* Required customer information

### Business schedule

* Opening time
* Closing time
* Friday schedule
* Weekly closing days
* Holidays

### Repair catalog

* Supported devices
* Supported problems
* Price ranges

### Complaint rules

* Product verification
* Required information
* Required documents
* Customer instructions

### Booking rules

* Appointment duration
* Confirmation requirements
* Booking reference format
* Cancellation requirements
* Modification rules

### Languages

The current workflow supports:

```text
Arabic
French
English
```

Additional languages can be added by extending the corresponding AI Agent instructions and customer-facing responses.

---

# 🌍 Adapting the Project to Another Country

The workflow is not limited to the current country configuration.

For example, the current configuration may use:

```text
Phone / ID:
8 digits
Specific prefixes

Business:
08:00 → 18:00
Friday → 16:30
Sunday → Closed
```

A user deploying the project in another country can modify these rules directly inside the AI Agent prompt.

For example:

```text
Country A:
8 digits

Country B:
9 digits

Country C:
10 digits
```

The same concept applies to:

* Phone numbers
* National IDs
* Working hours
* Weekly holidays
* Public holidays
* Supported services
* Repair prices

The provided values are therefore **example business rules for the current deployment**, not universal requirements.

---

# 🔒 Security & Privacy

Before publishing the workflow, always verify that no private credentials or sensitive deployment information are present.

This repository is intended to contain:

* Workflow logic
* Configuration placeholders
* Documentation
* Screenshots

It should **not** contain:

* API keys
* Bot tokens
* OAuth access tokens
* OAuth refresh tokens
* Database passwords
* Private database connection strings
* Private production credentials
* Customer personal data
* Private chat IDs

### Important

n8n credential references are not the same thing as exposing the credential secret itself.

When deploying this workflow, reconnect your own credentials in n8n rather than publishing them inside the repository.

---

# ⚠️ Production Considerations

This project demonstrates an AI-powered business automation architecture.

Before using it in a real production environment, review:

* Personal-data protection
* Customer consent
* Data retention
* Database permissions
* Authentication
* Authorization
* Backup strategy
* Calendar permissions
* API permissions
* National-ID handling
* Complaint-data protection
* Logging and monitoring

The appropriate legal and privacy requirements depend on the country and business where the workflow is deployed.

---

# 🔮 Future Improvements

Possible future extensions include:

* WhatsApp integration
* Multimodal image understanding
* Receipt/image recognition
* Document processing
* Customer authentication
* Real inventory management
* Payment integration
* Automated appointment reminders
* CRM integration
* Customer notification system
* Human-agent escalation
* Advanced analytics
* Business intelligence dashboards
* Automatic reporting
* Customer history
* Role-based administration
* Multimodal AI Agents

---

# 🎯 Project Objectives

Synapse Digital demonstrates how an AI Agent can be integrated into an actual business workflow rather than being used only as a conversational interface.

The project combines:

```text
Artificial Intelligence
        +
AI Agents
        +
Workflow Automation
        +
Business Rules
        +
Databases
        +
Calendar Automation
        +
Voice AI
        +
Business Intelligence
```

The objective is to create a system capable of understanding customers naturally while still operating under controlled business rules and real backend verification.

---

# 📸 Workflow

The complete n8n workflow is included in this repository.

![Synapse Digital n8n Workflow](screenshots/workflow_overview_upscaled.png)

---

# 📄 License

This project is licensed under the **Apache License 2.0**.

See [`LICENSE`](LICENSE) for the complete license text.

You are free to use, modify and adapt the project according to the terms of the license.

---

# 👨‍💻 About the Project

**Synapse Digital — AI Business Assistant**

An AI-powered business automation system built with:

**n8n • AI Agents • Telegram • Google Calendar • MongoDB • PostgreSQL • ElevenLabs • OpenRouter / Qwen**

> **From conversational AI to real business automation.**
