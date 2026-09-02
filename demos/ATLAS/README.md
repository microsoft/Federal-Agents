# ATLAS — Access, Travel, Logistics & Assignment System

ATLAS is a conversational courier readiness and trip support agent that helps couriers prepare for assigned courier runs. It provides itinerary details, site-specific guidance, access requirements, parking information, badge requirements, escort requirements, points of contact, and delivery instructions — all through natural conversation.

ATLAS helps couriers quickly understand what they need to know before arriving at a destination. Whether reviewing an upcoming itinerary, checking site access requirements, locating parking, or understanding delivery procedures, ATLAS provides a single place to access courier-related information and readiness details.

ATLAS acts as an assistant, not an autonomous coordinator: the courier makes every operational decision. ATLAS gathers information, surfaces relevant logistics and readiness details, answers questions, and helps couriers prepare for upcoming assignments, but does not schedule, approve, or modify courier activities without user direction.

> ⚠️ **Disclaimer:** ATLAS is inspired by common courier operations and site access workflows but is intended for demonstration and training scenarios. ATLAS is not an authoritative operational or security system and should not be used as the sole source for access, security, or logistical decisions. Always follow current organizational procedures, site instructions, security requirements, and official guidance for real-world courier operations.

## What ATLAS Does

- Provides courier itineraries including schedules, destinations, and assignment details.
- Explains site requirements such as access procedures, badging requirements, escort requirements, and visitor instructions.
- Shares logistical information including parking details, directions, building information, and points of contact.
- Answers courier questions about assigned deliveries, destinations, and site-specific procedures.
- Surfaces readiness information to help couriers prepare before travel.
- Provides a single view of trip information from approved operational and knowledge sources.
- Delivers site-specific guidance to improve situational awareness before arrival.

## Operating Modes

| Mode | Purpose |
| --- | --- |
| **Courier Support** (default) | Provides itineraries, site information, logistics details, and readiness guidance. |
| **Site Information** | Focuses on destination details such as access requirements, parking, contacts, and building guidance. |
| **Training** | Provides courier support while explaining the procedures, terminology, and reasoning behind the information presented. |

Modes are mutually exclusive and only change when you explicitly ask.

### Enabling and Disabling Modes

Mode switches are driven entirely by what you say. ATLAS never changes modes on its own.

| To Enable | Say |
| --- | --- |
| Site Information Mode | "Switch to Site Information Mode" or "Enter Site Information Mode" |
| Training Mode | "Switch to Training Mode" or "Enter Training Mode" |
| Courier Support Mode | "Switch to Courier Support Mode", "Return to normal mode", or "Exit Training Mode" |

While a mode is active, ATLAS tags its responses with `[SITE INFORMATION MODE]` or `[TRAINING MODE]` so you always know where you are.

## Tools ATLAS Uses

| Tool | Purpose |
| --- | --- |
| Dataverse / SharePoint | Stores courier assignments, itineraries, and supporting trip information |
| Knowledge Sources | Provides site guidance, access requirements, parking instructions, and FAQs |
| Site Information Repository | Supplies destination-specific logistics and readiness information |
| Microsoft Copilot Studio | Powers conversational interactions and orchestration |

## Data Sources

`CourierAssignments`, `CourierRuns`, `Sites`, `Locations`, `Contacts`, `AccessRequirements`, `ParkingGuidance`, `ReadinessInformation`

## Your First Courier Run

New to ATLAS? This walkthrough helps you prepare for an upcoming courier assignment. Just talk to ATLAS naturally — you don't need to memorize commands.

1. **Say hello** — Start the conversation to see what ATLAS can do:
   - "Hi" · "Help" · "What can you do?"
   - ATLAS responds with a capabilities overview.
2. **View your assignments** — Ask ATLAS about your upcoming courier runs:
   - "What courier runs am I assigned to?"
   - "Show me my upcoming deliveries."
3. **Review your itinerary** — ATLAS can summarize the details of an assignment:
   - "Show me my itinerary for tomorrow."
   - "What sites am I visiting this week?"
4. **Learn about a destination** — Get information about a specific site:
   - "Tell me about Site Alpha."
   - "What do I need to know before visiting Building 7?"
   - 💡 ATLAS can provide access requirements, parking guidance, points of contact, and important site instructions.
5. **Check access requirements** — Review site-specific requirements:
   - "Do I need a badge for this location?"
   - "Is an escort required?"
   - "What are the site access procedures?"
6. **Review logistics information** — Ask about parking, directions, and contacts:
   - "Where should I park?"
   - "Who is my point of contact?"
   - "How do I access the delivery entrance?"
7. **Prepare for departure** — Review readiness information before travel:
   - "Am I missing anything for this courier run?"
   - "Summarize everything I need to know before arrival."

ATLAS helps ensure you arrive prepared with the information needed to complete your assignment successfully.

## Quick Reference — Handy Prompts

| Goal | Try Saying |
| --- | --- |
| See capabilities | "Help" |
| View assignments | "What courier runs am I assigned to?" |
| View itinerary | "Show me my itinerary" |
| List destinations | "What sites am I visiting?" |
| Site information | "Tell me about Site Alpha" |
| Badge requirements | "Do I need a badge?" |
| Escort requirements | "Will I need an escort?" |
| Parking guidance | "Where should I park?" |
| Site contacts | "Who is my point of contact?" |
| Access procedures | "How do I access the site?" |
| Delivery details | "What deliveries am I making today?" |
| Readiness summary | "What do I need to know before departure?" |
| Learn site procedures | "Switch to Training Mode" |

---

ATLAS helps couriers stay informed, prepared, and mission-ready by providing a single destination for itinerary details, site information, access requirements, and logistical guidance.
