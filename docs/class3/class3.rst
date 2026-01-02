Class 3: Architect, build and deploy AI Services
================================================

..  image:: ./_static/mission3.png

With the growing popularity of Generative AI and AI agents, your organization has decided to upgrade the Arcadia trading platform by integrating a Generative AI agent-powered chatbot. Below is the conceptual architecture of the AI Services setup.


1 - Conceptual Architecture of AI Services
------------------------------------------

..  image:: ./_static/class3-1.png

- The end user submits a query/prompt through the Arcadia Financial AI Agent Chatbot.
- The LLM Orchestrator (FlowiseAI) receives the query and determines the next action.
- If the query requires proprietary data, the Orchestrator retrieves relevant information from the Vector Database (Qdrant) using similarity search.
- The retrieved data is combined with the user query to form an enriched, context-aware prompt.
- This enriched prompt is sent to the LLM provider, fronted by the F5 data plane, which ensures security and resiliency of the AI services.
- The F5 data plane inspects the request, extracts relevant content, detects malicious intent, and enforces AI Guardrails policies to ensure safe and compliant usage.
- If the prompt violates safety or governance rules, the data plane takes the appropriate action (e.g., block, redact, or modify the prompt).
- If the prompt is safe and compliant, it is forwarded to the LLM model to generate a response.
- The LLM model generates a response and sends it back to the Orchestrator through the F5 data plane.
- The F5 data plane can optionally re-inspect the LLM response for malicious content or policy violations before releasing it.
- Finally, the LLM Orchestrator returns the validated response to the end user through the Arcadia Financial AI Agent Chatbot.

What You’ll Learn in This Lab
-----------------------------
In this lab, you will learn how to:

..  image:: ./_static/class3-1-0.png

1. Architect AI/Agent Services for Generative AI applications
2. Deploy LLM orchestrator service (Flowise AI)
3. Deploy Vector Database (Qdrant) and build RAG pipeline
4. Integrate LLM orchestrator with Vector Database to build GenAI RAG Chatbot


.. Note:: 
   For Lab purposes, shared server will be used instead of a dedicated server for each components.

   **AI Apps Service - ai-apps**

   - Arcadia Financial Modern apps
   - Langchain (FlowiseAI)
   - Vector DB (Qdrant)
   - Linux Jumphost

   **Model Inference Service**

   - Model repository
   - Harbor Registry server


2 - Deploy LLM orchestrator service (Flowise AI)
------------------------------------------------

..  image:: ./_static/class3-13-0.png

Deploy LLM Orchstrator to facilitate AI component communication. Flowise AI - an open source low-code tool for developer to build customized LLM orchstration flow and AI agent is used. (https://flowiseai.com/). Flowise complements LangChain by offering a visual interface.


.. attention:: 

   A version of FlowiseAI (with appropriate LLM provider credential) already pre-installed (not configured) in **flowiseai** namespace. The following steps is to deploy FlowiseAI in a separate namespace (**flowiseai-dev**) for learning purpose only.


.. code-block:: bash

   cd ~/ai-apps/flowiseai-dev

.. code-block:: bash

   helm repo ls

.. code-block:: bash

   kubectl create ns flowiseai-dev

.. code-block:: bash

   helm -n flowiseai-dev install flowiseai-dev --values values.yaml cowboysysop/flowise

.. code-block:: bash

   kubectl -n flowiseai-dev get po,svc


..  image:: ./_static/class3-13.png


.. Note:: 
   Ensure all pods are in **Running** and **READY** state where all pods count ready before proceed.


Flowise is installed with the following custom values. Plese take notes of the password as you may need it for the next section.

values.yaml ::

   image:
     registry: reg.ai.local
     repository: flowiseai/flowise
     tag: 3.0.11

   serviceAccount:
     create: true   

   resources:
      limits:
        cpu: 4000m
        memory: 8Gi
      requests:
        cpu: 4000m
        memory: 8Gi   

   persistence:
     enabled: true
     size: 5Gi   

   config:
     username: "admin"
     password: "@F5Passw0rd123"   

   extraEnvVars:
    - name: LOG_LEVEL
      value: 'info'
    - name: DEBUG
      value: 'false'
    - name: NODE_TLS_REJECT_UNAUTHORIZED
      value: '0'

Create an Nginx ingress resource to **expose FlowiseAI/Langchain service** externally from the Kubernetes cluster.

.. code-block:: bash

   kubectl -n flowiseai-dev apply -f flowise-ingress.yaml

.. code-block:: bash

   kubectl -n flowiseai-dev get ingress

..  image:: ./_static/class3-14.png

Confirm that you can login and access to LLM orchestrator (flowise)

Input the following URL on a new browser tab

.. code-block:: bash

   https://llm-orch-dev.ai.local


.. attention:: 
   You will asked to register. This username and password will be use to login in future. Use the following suggested credential

   Administrator Email: f5ai@f5.com

   Password: @F5Passw0rd123


..  image:: ./_static/class3-15.png

..  image:: ./_static/class3-15-1.png   

You successfully learn and experience the deployment of LLM Orchestrator (FlowiseAI).


.. Attention:: 
   We are **NOT** going to use this FlowiseAI instance for the rest of the lab. We will be using the pre-installed FlowiseAI instance on **flowiseai** namespace instead - which already have the necessary LLM provider credential configured.


|
|

|
|

Pre-installed FlowiseAI instance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

From a new browser tab, login to the **pre-installed FlowiseAI instance**

+----------------+----------------------+
| **Email**      | f5ai@f5.com          |
+----------------+----------------------+
| **Password**   | @F5Passw0rd123       |
+----------------+----------------------+

..  image:: ./_static/class3-15-2.png  

Select **Agentflows** from the left menu

..  image:: ./_static/class3-16.png

Select **Add New** to create a new agentflow

..  image:: ./_static/class3-17-0.png

Import arcadia RAG agentflow into flowise. Select **Add New**, click **Settings icons** and **Load Agents**

A copy of the agemtflow located on the jumphost **Documents** directory. Select the chatflow json file.


.. Note:: 
   Ensure you choose the right json file.

   "*arcadia-agent-rag Agents.json*"


..  image:: ./_static/class3-17-1.png

Save the agentflow (arcadia-agent-rag)

..  image:: ./_static/class3-18.png


Ensure you save the agentflow with a name as shown.


..  image:: ./_static/class3-20.png

.. Note:: 
   We will return and continue to build RAG pipeline after we deploy vector database.  

3 - Deploy Vector Database (Qdrant)
-----------------------------------

..  image:: ./_static/class3-20-0.png

**Qdrant** is a vector similarity search engine and vector database. It provides a production-ready service with a convenient API to store, search, and manage vectors points.

|

.. code-block:: bash

   cd ~/ai-apps/qdrant-helm

.. code-block:: bash

   helm repo add qdrant https://qdrant.github.io/qdrant-helm

.. code-block:: bash

   helm repo list

.. code-block:: bash

   kubectl create ns qdrant

.. code-block:: bash

   helm -n qdrant install qdrant --values values.yaml qdrant/qdrant

.. code-block:: bash

   kubectl -n qdrant get po,svc


..  image:: ./_static/class3-21.png

.. Note:: 
   Ensure all pods are in **Running** and **READY** state where all pods count ready before proceed.

Create an Nginx ingress resource to **expose Qdrant VectorDB service** externally from the Kubernetes cluster.


.. code-block:: bash

   cd ~/ai-apps/nginx-ingress-qdrant

.. code-block:: bash

   kubectl -n qdrant apply -f qdrant-ingress-http.yaml

.. code-block:: bash

   kubectl -n qdrant apply -f qdrant-ingress-https-ui.yaml

.. code-block:: bash

   kubectl -n qdrant get ingress

..  image:: ./_static/class3-22.png

We have 2 ingress resource created to expose Qdrant service - Qdrant Vector DB API service (HTTP) and Qdrant Dashboard/UI (HTTPS).

Confirm that you can login to Qdrant vector database

.. attention:: 
   There is no authentication setup for qdrant console/dashboard. Hence, no login prompt. Its for lab and demo only. Ensure strong authentication is enforced in production environment.


..  image:: ./_static/class3-23.png


4 - Build FlowsieAI Documents Store
-----------------------------------

Retrival Augemented Generation (RAG) incorporate proprietary data to complement models and deliver more contextually aware AI outputs. However, in NLP (Neural Language Processing), AI don't understand human language. Those texts or knowledge need to be converted into an understandable language by NLP where the process called embedding required to convert text into series of vector array.


Select **Document Stores** from the left menu. Click **Add New** to create a new document store. Give it a name and click **Add**

..  image:: ./_static/class3-doc-store-01.png


Select **arcadia-team** (currently empty document store)

..  image:: ./_static/class3-doc-store-02.png

Click **Add Document Loader**. Search for keyword *text* and select **Text File**

..  image:: ./_static/class3-doc-store-03.png

Name the Text File Loader Name as **arcadia-team**. Select **Upload File** and choose the text file as shown. The text file located on the jumphost **Documents** directory.

..  image:: ./_static/class3-doc-store-04.png

Select **Recursive Chracter Text Splitter** and ensure the **Chunck Size** and **Chunk Overlap** are set as shown.

Click **Process** to upload chuck of text into document store.

..  image:: ./_static/class3-doc-store-05.png

Select **Upsert Chunks** to configure the vector database upsert action.

..  image:: ./_static/class3-doc-store-06.png

Start with **Select Embeddings**

..  image:: ./_static/class3-doc-store-07.png

Search for keyword **openai** and select **OpenAI Embeddings Custom**

..  image:: ./_static/class3-doc-store-08.png

Select **azure-open-ai** for the OpenAI API Key.

.. NOTE:: 

   **azure-open-ai** is a pre-configured secret that store the API key credential to access Azure OpenAI endpoint. This endpoint will be use for **model embedding** and **model inference**

   ..  image:: ./_static/class3-doc-store-08-1.png


For **BasePath**, input the following URL

.. code-block:: bash

   https://calypsotraining-openai.openai.azure.com/openai/v1

For **Model Name**, input the following model

.. code-block:: bash

   text-embedding-ada-002

**text-embedding-ada-002** is an embedding model that able to convert text into a vector array.


..  image:: ./_static/class3-doc-store-09.png


Click on **Select Vector Store**.

Search and select **Qdrant**

..  image:: ./_static/class3-doc-store-10.png

For **Qdrant Server URL**, input the following URL. This is the URL that you deployed earlier to access Qdrant vector database service. Please do note that for this lab, we are using HTTP protocol to access Qdrant service. In production environment, please ensure HTTPS is used to secure the communication.

.. code-block:: bash

   http://vectordb.ai.local

For **Qdrant Collection Name**, input the following name

.. code-block:: bash
   
   arcadia-team

For **Vector Dimension**, input the following value

.. code-block:: bash

   1536

Ensure that **Similarity** is set to **Cosine**

..  image:: ./_static/class3-doc-store-11.png

Click **Select Record Manager** and select **SQLite Record Manager**. Record Managers keep track of your indexed documents, preventing duplicated vector embeddings.

When document chunks are upserting, each chunk will be hashed using SHA-1 algorithm. These hashes will get stored in Record Manager. If there is an existing hash, the embedding and upserting process will be skipped

..  image:: ./_static/class3-doc-store-12.png

Click **Save Config** and **Upsert** to start the upsert process.

..  image:: ./_static/class3-doc-store-13.png

Once upsert completed, you will see the summary as shown.

..  image:: ./_static/class3-doc-store-14.png

To confirm the chunks upserted to Qdrant Vector Database, login to Qdrant Dashboard and select **Collections** from the left menu. **arcadia-team** collection should be visible.

..  image:: ./_static/class3-doc-store-15.png

Update Agentflows to connect to Qdrant Vector Database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Select **Agentflows** from the left menu. Select the previously created **arcadia-agent-rag** agentflow to edit.

..  image:: ./_static/class3-agentflow-01.png

Double click on the **QnA** node to edit the configuration.

..  image:: ./_static/class3-agentflow-01-1.png

On **ChatOpenAI Custom**, select **ChatOpenAI Custom Parameter** drop down.

..  image:: ./_static/class3-agentflow-01-0.png

Ensure the following parameters are set as shown.

Model Name

.. code-block:: bash

   gpt-4.1

Temperature

.. code-block:: bash

   0.3

Ensure **Streaming** is **Disable**

BasePath

.. code-block:: bash

   https://calypsotraining-openai.openai.azure.com/openai/v1



..  image:: ./_static/class3-agentflow-02.png

On **Document Store**, select **arcadia-team** document store created earlier.

..  image:: ./_static/class3-agentflow-03.png

.. NOTE:: 
   Click anywhere outside to exit from the pop-up



Click save to save the changes.

..  image:: ./_static/class3-agentflow-04.png



Validate your first GenAI RAG Chatbot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Click the Chat Icon

..  image:: ./_static/class3-agentflow-05.png

Input the flowing prompt to interact with the RAG chatbot

.. code-block:: bash

   who is chairman of the board

.. code-block:: bash

   get me details about tony smart

Suggested sample question ask to the RAG chatbot

.. Note:: 
   AI responses are non-deterministric. It means that give the same input, it can produce different output at different times - no gurantee to be consistent and can vary depending on internal factors within the model, like the order of data processing or random initilization. Hence, sometimes, you may need to ask twice for the language model to give an answer. 

.. code-block:: bash

   who are members of the board of arcadia

.. code-block:: bash

   who is chris wong

.. code-block:: bash

   tell me more about david strong

Source of inforamtion or "proprietary data" obtained from the text file store on Documents folder on the jumphost.

.. NOTE:: 
   You can clear the chat history with the middle red button on the chat window.

Here the documents uploaded into the document store earlier, which contains information about Arcadia Financial leadership team and associated sensitive information.

..  image:: ./_static/class3-34.png



|
|

**You have successfully build a GenAI RAG Chatbot**

|
|

..  image:: ./_static/mission3-1.png

.. toctree::
   :maxdepth: 1
   :glob:

