Prerequisite
============

Readiness
---------
Ensure LAB ennvironment is ready and Running to execute subsequent task. 

**Blueprint**

..  image:: /_static/intro/intro-1.png


Remote RDP to Windows11 Jumphost.


..  image:: /_static/intro/intro-5.png

Windows11 RDP login password can be obtainsed as following

..  image:: /_static/intro/intro-6.png

Window 10 Jumphost

..  image:: /_static/intro/intro-7.png

Launch a putty session and login with the following credential.

+----------------+---------------+
| **Username**   | ubuntu        |
+----------------+---------------+
| **Password**   | HelloUDF      |
+----------------+---------------+

..  image:: /_static/intro/intro-2.png

You should be able to get the following prompt on "ai-apps" server.

..  image:: /_static/intro/intro-3.png



KASM desktop (Chrome Browser)
-----------------------------
Alternatively, if you don't have access to RDP or ssh client from your laptop due to company security policy, you can use KASM desktop (Chrome Browser) (via https) to do the lab.

Launch Chrome browser (via KASM-Desktop link).

..  image:: /_static/intro/kasm-0.png


Access Kasm Desktop from the bookmark as shown below.

You may be asked to allow text and images copies to the clipboard. Please allow it to enable copy and paste function between your laptop and KASM desktop.

.. Attention:: 
   You may need to refresh the chrome browser occasionally to ensure you can copy and paste content from your laptop to KASM desktop.

..  image:: /_static/intro/kasm-2.png



Windows11 Jumphost Console
--------------------------

As a last resort, you can also use Windows11 Jumphost Console to do the lab (QUEMU VNC). This options does not allow copy and paste function between your laptop and Windows11 Jumphost Console.

..  image:: /_static/intro/win-0.png


Lab Setup Environment
---------------------

+--------------------------------------------+----------------------+------------+--------------------------------------------------------+
| Service                                    | FQDN / URL           | IP Address |  Remark                                                |
+============================================+======================+============+========================================================+
| Open-WebUI Service                         | open-webui.ai.local  | 10.1.1.5   |  Web-UI frontend for AI Model (Ollama)                 |
+--------------------------------------------+----------------------+------------+--------------------------------------------------------+
| Ollama Service                             | ollama.ai.local      | 10.1.1.5   | Platform to run and host AI Model                      |
+--------------------------------------------+----------------------+------------+--------------------------------------------------------+
| Windows11 Jumphost                         |  NA                  | 10.1.1.6   | Windows11 Jumphost. Access via RDP from laptop         |
+--------------------------------------------+----------------------+------------+--------------------------------------------------------+
| Arcadia Financial Modern Apps              | arcadia.ai.local     | 10.1.1.4   | Sample Modern financial trading application            |
+--------------------------------------------+----------------------+------------+--------------------------------------------------------+
| Langchain / LLM Orchestrator               | llm-orch.ai.local    | 10.1.1.4   | LLM Orchestrator coordinate and streamline LLM task    |
+--------------------------------------------+----------------------+------------+--------------------------------------------------------+
| Vector Database                            | vectordb.ai.local    | 10.1.1.4   | Specialize type of DB for high dimensional vector data |
+--------------------------------------------+----------------------+------------+--------------------------------------------------------+
| Internal Registry Server                   | reg.ai.local         | 10.1.10.5  | Internal container image registry                      |
+--------------------------------------------+----------------------+------------+--------------------------------------------------------+
| F5 AI Guardrails SaaS                      | us1.calypsoai.app    | NA         | F5 AI Guardrails SaaS                                  |
+--------------------------------------------+----------------------+------------+--------------------------------------------------------+

.. toctree::
   :maxdepth: 1
   :glob:

