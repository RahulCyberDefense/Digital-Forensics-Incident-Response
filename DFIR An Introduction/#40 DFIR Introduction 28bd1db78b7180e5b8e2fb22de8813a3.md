# #40: DFIR: Introduction

---

## **Task 1: Introduction**

In this room, I learned the **foundational concepts** of **Digital Forensics and Incident Response (DFIR)** — a vital skill in defensive cybersecurity.

**Topics covered:**

- Introduction to DFIR
- Core DFIR concepts
- Industry-standard **Incident Response processes**
- Common **DFIR tools**

### Key Idea

Even with strong defenses, **breaches still happen** — DFIR helps us **respond, investigate, and recover** effectively.

---

## **Task 2: The Need for DFIR**

### **What is DFIR?**

![e2f249385735b24d59cac9e7f7cddcdf.png](e2f249385735b24d59cac9e7f7cddcdf.png)

**DFIR = Digital Forensics and Incident Response**

It’s the combined process of:

- **Collecting and analyzing forensic evidence** from digital devices (computers, servers, phones).
- **Investigating incidents** to understand attacker activity, scope, and methods.

### **Why DFIR is Needed**

DFIR helps in:

- Finding **real attacker activity** vs false alarms.
- **Removing attackers** and their footholds.
- Identifying **extent, timeline, and impact** of breaches.
- Finding **loopholes** that enabled attacks.
- Understanding attacker **TTPs (Tactics, Techniques, Procedures)**.
- Sharing intel with the **security community**.

### **Who Performs DFIR**

![6e2d93818b9a293a246d9db91fad6aea.png](6e2d93818b9a293a246d9db91fad6aea.png)

- **Digital Forensics Experts:** Focus on evidence collection and analysis from systems.
- **Incident Responders:** Focus on threat identification and mitigation.

DFIR pros combine both skills — **Forensics + Cybersecurity** — to detect, contain, and recover from incidents.

---

**✅ Answers:**

- **2.1:** Digital Forensics and Incident Response
- **2.2:** Incident Response

---

## **Task 3: Basic Concepts of DFIR**

### **Artifacts**

- Artifacts = **traces or evidence** of human/attacker activity on a system.
- Examples: Registry keys, log entries, network connections, or memory data.
- Used to **support claims** about how an attack occurred.

Artifacts come from:

- **File system**
- **Memory (RAM)**
- **Network activity**

---

### **Evidence Preservation**

- Always **protect evidence integrity**.
- Original evidence is **write-protected** and untouched.
- Analysis is performed on a **copy**.
- If a copy is corrupted → recreate it from preserved original.

---

### **Chain of Custody**

- Tracks **who handled the evidence** and when.
- Prevents contamination or tampering.
- Any mishandling (e.g., by unauthorized person) **breaks the chain** and weakens legal credibility.

---

### **Order of Volatility**

- Refers to **how quickly data can be lost**.
- **More volatile = lost faster.**
- Example:
    - **RAM** → highly volatile (erased on shutdown)
    - **Hard Drive** → less volatile (persistent storage)

So, **RAM must be captured before disk data**.

---

### **Timeline Creation**

- Once artifacts are collected → organize all events **chronologically**.
- Aids in **storytelling** and **understanding attacker sequence**.
- Helps correlate events from different sources.

---

**✅ Answers:**

- **3.1:** RAM
- **3.2:** THM{DFIR_REPORT_DONE}

---

## **Task 4: DFIR Tools**

### **1. Eric Zimmerman’s Tools**

![1a89eb9ecd42f493fcdf7d105b41dba7.jpg](1a89eb9ecd42f493fcdf7d105b41dba7.jpg)

- Suite of utilities for **Windows forensics** (registry, file system, timeline analysis).
- Commonly used in **Windows Forensics 1 & 2** learning paths.

---

### **2. KAPE (Kroll Artifact Parser & Extractor)**

![0c82470617c7ebdbfcf956ee9d9547cf.png](0c82470617c7ebdbfcf956ee9d9547cf.png)

- Automates **artifact collection and parsing**.
- Helps build **timelines** quickly and efficiently.

---

### **3. Autopsy**

![ea383ef541cfaf0cbbc40a64af0f47ba.jpg](ea383ef541cfaf0cbbc40a64af0f47ba.jpg)

- Open-source **GUI-based forensic analysis** tool.
- Extracts data from digital media (disks, mobiles).
- Supports many **plugins** for advanced analysis.

---

### **4. Volatility**

![c25405634d4fda8271d8df63a252d16a.jpg](c25405634d4fda8271d8df63a252d16a.jpg)

- Framework for **memory forensics** (RAM analysis).
- Extracts processes, network connections, passwords, and more.
- Works for **Windows & Linux**.

---

### **5. Redline**

![622d38cddd96ddcc98b8002da9920eec.jpg](622d38cddd96ddcc98b8002da9920eec.jpg)

- **FireEye’s free tool** for forensic data collection and analysis.
- Ideal for **incident response investigations**.

---

### **6. Velociraptor**

![fca62b1d63bfcd0e11557606e451c412.png](fca62b1d63bfcd0e11557606e451c412.png)

- Open-source **endpoint forensics and monitoring platform**.
- Allows live investigation, triage, and evidence collection across multiple systems.

---

## **Task 5: Incident Response (IR) Process**

### **Purpose**

To **contain, eradicate, and recover** from incidents while learning from them.

### **Industry Frameworks**

- **NIST SP 800-61:**
    1. Preparation
    2. Detection & Analysis
    3. Containment, Eradication, Recovery
    4. Post-Incident Activity
- **SANS (PICERL):**
    
    ![f714eea27bb41e82ce3d3e56ea41a9ab.png](f714eea27bb41e82ce3d3e56ea41a9ab.png)
    
1. **P**reparation
2. **I**dentification
3. **C**ontainment
4. **E**radication
5. **R**ecovery
6. **L**essons Learned

🔸 *Both frameworks are equivalent; SANS just separates more steps for clarity.*

---

### 🔄 **Explanation of Each Step**

1. **Preparation:**
    
    Build processes, assign roles, and set up tools before an incident occurs.
    
2. **Identification:**
    
    Detect possible incidents, verify indicators, and eliminate false positives.
    
3. **Containment:**
    
    Stop the spread — short-term (e.g., isolate machine) and long-term fixes.
    
4. **Eradication:**
    
    Remove the root cause and ensure no backdoors remain.
    
5. **Recovery:**
    
    Restore systems to normal operations safely.
    
6. **Lessons Learned:**
    
    Review the incident, document it, and strengthen defenses.
    

---

**✅ Answers:**

At what stage of the IR process are disrupted services brought back online as they were before the incident?

- **5.1:** Recovery

At what stage of the IR process is the threat evicted from the network after performing the forensic analysis?

- **5.2:** Eradication

What is the NIST-equivalent of the step called "Lessons learned" in the SANS process?

- **5.3:** Post-incident Activity

---

## **Task 6: Conclusion**

### **What I Learned**

- The definition and purpose of **DFIR**.
- The importance of **artifacts, evidence handling, and timelines**.
- The **tools** that power real-world forensics (EZ Tools, KAPE, Autopsy, Volatility, Redline, Velociraptor).
- The **PICERL** framework for structured incident response.

### **Final Takeaway**

> DFIR = The bridge between finding what happened and restoring what was lost — mastering it helps me think like both a detective and a defender.
> 

---