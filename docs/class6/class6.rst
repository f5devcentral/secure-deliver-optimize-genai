Class 6: AI Red Team
====================

..  image:: ./_static/mission6.png


.. NOTE::
       AI governance itself is a huge topic. Hence, we will only focus on the technical controls such as AI Red Team.


.. NOTE::
       No hands on lab for this class partly due to extensive resources required for scanning an AI apps / LLM. Demo and discussion will be conducted in class.
       


What is AI Governance?
~~~~~~~~~~~~~~~~~~~~~~
AI governance refers to the **frameworks**, **policies**, and **practices** established to guide the **ethical development, deployment, and use of artificial intelligence systems**. Its goal is to ensure that AI technologies are aligned with societal values, legal requirements, and organizational objectives while mitigating risks and addressing challenges related to bias, transparency, respect human rights, accountability, and security.

What is AI Assurance?
~~~~~~~~~~~~~~~~~~~~~
AI assurance focuses on testing, validating, and verifying that AI systems actually behave as intended — safely, reliably, and in line with governance policies.

The purpose is to prove and demonstrate that an AI system meets defined trust, safety, and compliance requirements

**Key elements**

- Model validation (accuracy, robustness, fairness)
- Security & safety testing (e.g., red teaming, prompt injection testing)
- Explainability and auditability checks
- Continuous monitoring and reporting

**F5 AI Guardrails** and **F5 AI Red Team** together form a pragmatic, end-to-end solution for AI governance and assurance: Guardrails **define and enforce safe policies at scale**, while Red Teaming **stress-tests models and AI apps to prove those controls work** — reducing regulatory, security, and reputational risk so your AI products can be trusted in production.

AI Risk/Safety vs AI Security
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**AI Safety/Risk**

Focus on preventing unintended harm caused by the AI services, ensuring it operates ethically and aligns with human values, considering broder societal impacts beyond just technical vulnerabiliites. - Protecting human using AI. (responsible use of AI and adherence to ethical standards). There are standard like NIST AI 600-1 that covered the Risk Management Framework for the AI Safety.


**AI Security**

Focus to secure AI services from external threats like cyberattack and data breach, safeguarding the confidentiality and integrity of the AI services. General framework that address this is OWASP Top 10 for LLM, MITRE ATLAS ( Adversarial Threat Landscape for Artificial Intelligence System) - Protecting AI system use by human.



What is F5 AI Red Team?
~~~~~~~~~~~~~~~~~~~~~~~

F5 AI Red Team tool is designed to help organizations proactively test and secure their AI applications, models and agents. It enables teams to simulate adversarial attacks, such as prompt injections and jailbreaks - at high scale and speed, to uncover potential risks, vulnerabilities in AI systems. It's similar to cybersecurity red teaming - defensive (Red) vs offensive (Blue), but focused specifically on finding ways AI apps might breaks or compromised.

Key offering includes:-

- A large and constantly updated threat-library and attack-agent swarm for testing existing and emerging AI vulnerabilities
- Continuous assessment capabilities so defenses evolve as AI threats evolve, across pilot to production environments
- Real-world scenario testing where it leverage 10,000+ monthly attack prompts and detailed “agentic fingerprint” insights (exploit paths, chain of reasoning)


1 - Explore F5 AI Red Team Interface
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Reports** is the place where all your attack campaign results come together for a victory lap (or a post-mortem)

**Attack campaigns** is where you orchestrate, launch, and track adversarial attacks against your AI systems

..  image:: ./_static/class6-redteam-1.png


Some reports include a CASI (Comprehensive AI Security Index) score to help you quickly gauge how well your model performed:-

- Critical (0 - 69) - Highly vulnerable, meaning it is susceptible to even basic attacks and is **not recommended for production use**.
  
- Warning (70 - 85) - Moderate vulnerable, meaning it has some common vulnerabilites. It's recommended to conduct more testing and **add safeguards before deploying them in production**.

- Good (85 - 99) - Secure against most attacks but should still be evaluated for highly complex or novel attack vectors depending on your use case. In addition, high CASI score may potentially impact user expereince as it potentially blocks some valid/legitimate requests or overly sensitive to certain inputs evaluation. Balancing security and usability should be considered.

CASI scoring only applied to Signature attacks, not Agentic Warfare attack.

.. NOTE::
       CASI evaluates several several critical factors beyond simple success rates:-

       - **Severity**: The potential impact of a successful attack (e.g., bicycle lock vs. nuclear launch codes)
  
       - **Complexity**: The sophistication of the attack being assessed (e.g. plain text vs. complex encoding)
  
       - **Defensive Breaking Point (DPB)**: Identifies the weakest link in the model’s defences, focusing on the path of least resistance and considering factors like computational resources required for a successful attack

..  image:: ./_static/class6-redteam-2.png

**Attack Campaign** is where you set up and launch your adversarial attacks against your AI systems. You can choose from a variety of attack types.

..  image:: ./_static/class6-redteam-3.png

**Signature Attacks** - These are pre-defined attack prompts designed to exploit known vulnerabilities in AI models. They are typically based on common attack patterns and techniques that have been observed in the wild.

..  image:: ./_static/class6-redteam-2-1.png

**Agentic Warfare** - This is a more advanced and dynamic form of attack where autonomous agents (a group of Red Agent) are deployed to interact with the AI model or AI apps in a more sophisticated manner. These agents can adapt their strategies based on the model's responses, making them more effective at uncovering vulnerabilities that may not be exposed by static signature attacks.

..  image:: ./_static/class6-redteam-2-2.png

2 - Signature Attacks Report
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Select **Reports** and filter based on **complete** reports.

..  image:: ./_static/class6-redteam-4-01.png

Click on **Run date** to sort the report and select report name **foobz-nim-meta-llama3** report.

..  image:: ./_static/class6-redteam-4-02.png       

Report dashboard shown respective information about the scanned result.

Report of an Attack Campaign. Click **View raw data** to see more details of every prompt used in the attack campaign.

..  image:: ./_static/class6-redteam-4.png

Details of the attack prompts used in the campaign including the response from the AI model, the attack type, severity level, and whether the attack was successful or not. 

..  image:: ./_static/class6-redteam-4-1.png

3 - Agentic Warfare Report
~~~~~~~~~~~~~~~~~~~~~~~~~~

Select **Reports** and search the following report name in the search bar and selecte the respective report.

.. code-block:: bash

   agentic-genearate-harmful-code-2

..  image:: ./_static/class6-redteam-ars-01-0.png

Respective information of the agentic warfare scanning displayed. Select **View agentic fingerprints**.

..  image:: ./_static/class6-redteam-ars-01.png

Feel free to explore the Red Agent chain of thought. Its the internal reasoning process: the step-by-step logic, decisions, and planning it uses to achieve a task. 

In agentic AI, this covers every mental step the agent takes—from forming questions and evaluating options to deciding on an action. This process is crucial for transparency, debugging, and explainability. Technologies like Agentic Fingerprints let you see each branch or step an agent considered, providing a clear decision tree of its entire thought process leading to a final result

.. Note:: 
  The scenario was intentionally crafted to request Python exploit code for CVE-2025-20333 as part of a **sanctioned security validation**. The objective was to **ensure AI models and applications are resilient and will not generate malicious code**.

..  image:: ./_static/class6-redteam-ars-02.png

Another example Agentic Warfare scanning with slight different intent.

..  image:: ./_static/class6-redteam-ars-03.png


|
|

..  image:: ./_static/mission6-1.png

.. toctree::
   :maxdepth: 1
   :glob:

