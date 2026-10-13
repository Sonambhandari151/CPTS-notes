- Calculate security severity  of vulnerabilities.
- Often used together with **Microsoft Dread**
- **DREAD** is risk assessment system to evaluate severity.

- RIsk of threat/vuln is based on :
     
    Damage Potential
    Reproducibility
    Exploitability
    Affected Users
    Discoverability

---
# 📌 Vulnerability Severity Scoring

---

## 🔹 1. DREAD (Microsoft Model)

**Purpose**: Microsoft-developed risk assessment framework to help security teams evaluate and prioritize threats. Uses a **0–10 scoring scale** for each category.

**Five Factors**:

1. **Damage Potential** – How severe would the damage be if exploited?
    
    - Example: Can attacker steal sensitive data, or just minor info?
        
2. **Reproducibility** – How easily can the attack be repeated?
    
    - Example: Does it always work (high), or requires rare conditions (low)?
        
3. **Exploitability** – How difficult is the attack to perform?
    
    - Example: Requires advanced skills (low) vs. simple script (high).
        
4. **Affected Users** – How many users would be impacted?
    
    - Example: Single user (low), entire customer base (high).
        
5. **Discoverability** – How easy is it to find the vulnerability?
    
    - Example: Hidden deep in code (low) vs. obvious public-facing flaw (high).
        

👉 **Use**: Each factor is rated (0–10). The scores are averaged or summed to get a **risk severity rating**. Helps prioritize vulnerabilities when deciding patch order.

---

## 🔹 2. CVSS (Common Vulnerability Scoring System)

**Purpose**: Standardized method (industry-wide) to assign severity scores (0.0–10.0). Widely used by NVD (National Vulnerability Database), vulnerability scanners, and SOC teams.

**Three Main Metric Groups**:

---

### 🟢 Base Metrics (fundamental, technical nature of vuln)

**1. Exploitability Metrics** – Describe how an attack can be carried out:

- **Attack Vector (AV)**: Local, Adjacent, Network, Physical.
    
- **Attack Complexity (AC)**: Low (easy), High (special conditions).
    
- **Privileges Required (PR)**: None, Low, High.
    
- **User Interaction (UI)**: None (no victim action), Required (e.g., phishing click).
    

**2. Impact Metrics (CIA Triad)** – Describe effects of successful exploitation:

- **Confidentiality Impact (C)** – Unauthorized access to sensitive data.
    
    - High: passwords, encryption keys stolen.
        
    - Low: minor or non-sensitive info exposure.
        
- **Integrity Impact (I)** – Unauthorized modification of data.
    
    - High: critical files modified, financial records changed.
        
    - Low: attacker changes trivial/unimportant data.
        
- **Availability Impact (A)** – Loss of system availability.
    
    - High: complete system shutdown.
        
    - Low: partial service degradation.
        

---

### 🟡 Temporal Metrics (change over time)

**1. Exploit Code Maturity (E)** – Likelihood of exploitation.

- Unproven → Proof-of-Concept → Functional → High (widely available, reliable).
    

**2. Remediation Level (RL)** – Availability of fixes.

- Unavailable → Workaround → Temporary Fix → Official Fix.
    

**3. Report Confidence (RC)** – How reliable is the info?

- Unknown → Reasonable → Confirmed.
    

---

### 🔴 Environmental Metrics (org-specific context)

**Modified Base Metrics** – Org can adjust CIA triad values depending on local importance.

- High: major organizational/customer impact.
    
- Medium: significant but not catastrophic.
    
- Low: minor effect.
    
- Not Defined: skip.
    

👉 Example: A vuln with low “Availability” impact generally could be **High** for a hospital system if downtime blocks patient care.

---

## 🔹 3. CIA Triad Refresher

- **Confidentiality** – Protect data privacy & access control.
    
- **Integrity** – Ensure data accuracy, prevent tampering.
    
- **Availability** – Ensure uptime, access to services.
    

---

## 🔹 4. CVSS Scoring Scale

- **0.0** = None
    
- **0.1–3.9** = Low
    
- **4.0–6.9** = Medium
    
- **7.0–8.9** = High
    
- **9.0–10.0** = Critical
    

---

## 🔹 5. Example (Windows Print Spooler RCE)

- CVSS v3.1 Base Score: **8.8 (High)**.
    
- Based on: Remote attack vector, low complexity, high CIA triad impact.
    

---

## 🔹 6. DREAD vs CVSS (Comparison Table)

|Aspect|**DREAD**|**CVSS**|
|---|---|---|
|**Origin**|Microsoft|Industry standard (FIRST/NVD)|
|**Scoring Scale**|0–10 per category (averaged/summed)|0.0–10.0 overall score|
|**Focus**|Risk prioritization (business-oriented)|Severity measurement (technical + contextual)|
|**Factors**|Damage, Reproducibility, Exploitability, Affected Users, Discoverability|Exploitability + Impact + Temporal + Environmental|
|**Flexibility**|More subjective|More standardized & repeatable|
|**Usage Today**|Less common (deprecated by Microsoft)|Widely used in vulnerability scanners/databases|

---

👉 **In short:**

- **DREAD** = Microsoft model, good for risk analysis but subjective.
    
- **CVSS** = Standardized global framework, quantitative, used in most tools.
    
- Both help prioritize vulnerabilities but CVSS is more universally accepted.
    

---
