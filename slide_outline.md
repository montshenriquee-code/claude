# Slide outline — KYC Sanctions & Adverse Media Screening (Agentic AI)

## 1. Title
- Project title, course (Hult MFIN — AI Trends in Finance, Assignment A2), group members, date.

## 2. Audience & assumptions
- Audience: KYC/compliance analysts and team leads (primary); compliance execs/regulators (secondary).
- Assumptions: all client data synthetic; OFAC SDN list is real public data; output is decision-support only,
  human review is mandatory.

## 3. The business problem
- Manual KYC screening (sanctions checks + adverse media search) is slow, inconsistent, and doesn't scale with
  onboarding volume.
- Business value: automated first-pass triage that prioritizes analyst attention, cuts manual search time, and
  produces an auditable rationale (the memo) for every tier assigned.

## 4. Domain & data
- Domain: Financial Compliance Monitoring → Sanctions & Adverse Media Screening.
- Data: (1) synthetic 17-client "news article" corpus (clean / homonym-risk / genuine-risk mix), (2) real public
  OFAC SDN list. No real client data used.

## 5. Hugging Face models used — what's new vs. class
- Class already used: `all-MiniLM-L6-v2` (embeddings), `distilgpt2` (generation) — reused here, not new.
- New for this project:
  - `MoritzLaurer/mDeBERTa-v3-base-mnli-xnli` — zero-shot risk classification (why: lets us define our own risk
    taxonomy without training a classifier; multilingual).
  - `Davlan/bert-base-multilingual-cased-ner-hrl` — NER confirmation (why: guards against homonym false positives).

## 6. System architecture (workflow diagram)
- Diagram: corpus → chunk → embed → MongoDB `$vectorSearch` ⟶ zero-shot classifier ⟶ NER check
  ⟶ **agentic router** ⟶ distilgpt2 memo ⟶ consolidated dataframe.
- Callout box on the router: conditional branches + the self-correction loop (2nd query on ambiguous retrieval).

## 7. Live workflow demonstration (screenshots / video cutaways)
- Show: corpus generation, vector search test, one "Escalate" client walkthrough (sanctions hit),
  one "Clear" client, one homonym-risk client where NER correctly suppresses a false flag.

## 8. Risk-tier dashboard
- Bar chart: risk tier distribution across the 17 clients.
- Scatter: sanctions score vs. adverse-media confidence, colored by tier, with threshold lines.
- One sample generated memo shown in full.

## 9. Financial / business insights
- E.g., "X% of clients cleared automatically without analyst time," "homonym cases correctly avoided
  false escalation thanks to NER," "self-correction loop recovered Y ambiguous case(s) that a single-shot
  RAG query would have missed."

## 10. Limitations & risks
- Name-matching false positives/negatives; news staleness; distilgpt2 hallucination risk; language-coverage
  bias; small demo corpus; human review always required.

## 11. Closing / next steps
- What a production version would need: real news feed integration, calibrated thresholds against labeled
  cases, larger SDN coverage, human-in-the-loop review UI.
