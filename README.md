# SalesAgentsAI-HomeCredit-VoiceBot
SalesAgentsAI company's hiring process Assignment

Title: Voice Agent Architecture & State Management Strategy 
Candidate: Abhijeet Ovhale
Role: AI Intern (Prompt Engineer)
1. Design Philosophy: State Machine over Decision Tree The primary challenge of this use case is managing non-linear human conversation. Traditional voice bots fail because they rely on rigid, step-by-step decision trees. If a user provides information out of order or multiple data points at once, a standard decision tree forces the AI to repeat questions, creating a frustrating loop.
To solve this, I designed the System Prompt as a State Machine driven by an internal "Execution Loop."
2. The Execution Loop Architecture Instead of giving the LLM a script to read, the prompt forces the AI to execute a silent backend algorithm before generating every spoken response:
•	Step 1 (Extract): Parse the {{customer_utterance}} for any of the 7 eligibility data points, regardless of order.
•	Step 2 (Override Check): Scan for absolute diversions (Busy, Existing Loan/EMI Reduction).
•	Step 3 (Validate): Run extracted facts against hard constraints (e.g., Cash Income = Disqualify; Agricultural Property = Disqualify).
•	Step 4 (Update State): Silently check off answered items in the Phase 2 checklist.
•	Step 5 (Generate Response): Ask only the first missing item in the sequence.
3. Handling Edge Cases & Global Overrides To prevent the AI from hallucinating or being bullied by aggressive users, I segmented the rules into "Global Overrides" and "Call Flow States."
•	The Soft Disqualification Trap: LLMs are naturally "helpful" and often attempt to negotiate or offer alternatives when a user fails a check. The IMMEDIATE DISQUALIFICATION global override forces a hard stop, ensuring ineligible leads (e.g., cash-income earners) are instantly terminated without wasting compute time.
•	The >75L Boundary Exception: Implemented a specific logic gate for users requesting over 75 Lakhs, forcing the AI to explicitly secure consent for the 75L maximum before proceeding.
•	The Handoff Gate: The AI is strictly forbidden from triggering the final transfer script until all 7 variables in the internal state are validated, preventing premature handoffs.
4. Production Readiness The prompt is template-ready. All dynamic data (customer names, dates, RAG context) is structured using {{ }} syntax for seamless API injection in a production environment.
