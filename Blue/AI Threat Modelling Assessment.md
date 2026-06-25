# AI Threat Modelling Assessment (Blue Team)

## Task 1 – Assessment

### Q1. Which component is most exposed to prompt injection?

**Answer:** LLM Agent

### Q2. What vulnerability occurs when internal financial records are returned?

**Answer:** Sensitive Information Disclosure

### Q3. Which component exposes confidential data from stored embeddings?

**Answer:** Retrieval System

### Q4. What control prevents fake user behavior attacks?

**Answer:** Anomaly detection on user behavior

### Q5. What control prevents large-scale scraping attacks?

**Answer:** Rate limiting and API authentication

### Q6. What attack involves malicious data inserted into training data?

**Answer:** Data Poisoning

### Q7. What is the risk level of fake accounts manipulating rankings?

**Answer:** High Risk

---

## Task 2 – Threat Modelling

### Prompt Injection

**Affected Components:**

* Prompt
* LLM Agent

**Reason:** Malicious instructions can manipulate the prompt and cause the model to ignore intended behavior or expose sensitive information.

### Sensitive Data Leakage

**Affected Components:**

* LLM
* Retrieval System
* Database

**Reason:** Weak filtering may allow confidential information stored in databases or retrieved data to be included in model responses.

### Data Poisoning

**Affected Components:**

* Retrieval System
* Database

**Reason:** Malicious data inserted into stored datasets can influence model outputs and behavior.

---

## Conclusion

This assessment identified several AI security threats, including prompt injection, sensitive information disclosure, and data poisoning. Applying security controls such as input validation, anomaly detection, rate limiting, and proper data filtering helps reduce these risks and improves the security of AI systems.
