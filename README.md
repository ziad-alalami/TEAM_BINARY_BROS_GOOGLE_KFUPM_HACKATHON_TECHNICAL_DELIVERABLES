# Binary Bros: Epidemic Simulation & Prediction Platform
**Google KFUPM Hackathon | Technical Requirements Deliverable**

## 1. Project Overview
We have designed a scalable, cloud-native architecture for simulating non-zoonotic respiratory virus outbreaks (e.g., ILI, RSV) in major Saudi Arabian cities (Riyadh, Jeddah, Eastern Province). Our solution utilizes a **Hybrid Temporal Graph Neural Network (GNN)**, combining epidemiological domain knowledge (SEIR models) with deep learning for high-accuracy 30-day forecasts.

The system empowers health officials to:
* **Visualize** spread dynamics on a high-fidelity 3D globe.
* **Simulate** intervention policies (e.g., lockdowns, mask mandates) using adjustable hyperparameters.
* **Interact** with a GenAI agent to query data and trigger complex simulation scenarios using natural language.

---

## 2. System Architecture
Our infrastructure is built entirely on **Google Cloud Platform (GCP)**, ensuring scalability and seamless integration of AI services. We leverage **Vertex AI** for model orchestration and **Neo4j** for graph data persistence, bridging the gap between graph theory and deep learning.

### High-Level Cloud Architecture
The system architecture separates the interactive client layer from the heavy computation and data layers. The **FastAPI Backend** (hosted on Cloud Run) acts as the central orchestrator, managing secure communication between the user interface, the AI Agent, and the GNN Model.

**Key Components:**
* **Frontend:** React Web App hosted on Firebase, featuring Google Maps Photorealistic 3D Tiles.
* **Backend:** Serverless FastAPI on Cloud Run for auto-scaling.
* **AI/ML:** Vertex AI Agent Builder (Gemini 1.5 Pro) for the chatbot and Vertex AI Prediction for the GNN model.
* **Data:** Neo4j (Graph DB) on GKE for storing district nodes and mobility edges; BigQuery for historical analytics.

![system_arhitecutre.svg](https://github.com/ziad-alalami/TEAM_BINARY_BROS_GOOGLE_KFUPM_HACKATHON_TECHNICAL_DELIVERABLES/blob/main/system_architecture.svg)

---

## 3. User Interface & Experience
The dashboard provides an immersive command center for health officials. It features a high-fidelity **3D Map** that visualizes infection hotspots in real-time using color-coded nodes (Red for high infection, Green for low).

### UI Layout & Features
The interface is designed for "Dark Mode" scientific visualization to reduce eye strain during monitoring.
* **Left Sidebar (Analytics):** Displays granular node details (e.g., "Riyadh-North" infection stats, virality scores).
* **Center Canvas (Visualization):** An interactive 3D globe powered by **Google Maps Platform**, allowing users to zoom into specific districts.
* **Right Sidebar (Controls):** Contains sliders for hyperparameters (R0, Incubation period) and toggles for policy interventions (School Closures, Mask Mandates).
* **Bottom Panel (Timeline):** A playback control to view the spread evolution over the 30-day horizon.

![Basic UI image](https://github.com/ziad-alalami/TEAM_BINARY_BROS_GOOGLE_KFUPM_HACKATHON_TECHNICAL_DELIVERABLES/blob/main/basic_ui.png)

---

## 4. MLOps & Model Lifecycle
To ensure the reliability and accuracy of our epidemic forecasts over time, we have implemented a robust MLOps pipeline using **Vertex AI Pipelines**. This automates the journey from raw data to a deployed model, ensuring our predictions adapt to changing real-world conditions (e.g., new virus mutations or mobility shifts).

### Pipeline Components
* **Data Ingestion & Preprocessing:** We use **Dataflow** to ingest raw mobility and demographic data, clean it, and structure it into graph snapshots stored in **Neo4j**.
* **Training & Evaluation:** A **Vertex AI Training** job extracts the latest graph states from Neo4j. It trains the Temporal GNN on GPU clusters and evaluates it against historical outbreak data.
* **Model Registry:** Successful models (meeting the $R^2 > 0.80$ target) are versioned and pushed to the **Vertex AI Model Registry**.
* **Continuous Monitoring:** Once deployed, **Vertex AI Model Monitoring** tracks the live model for **data drift** (e.g., if population mobility drastically changes due to a holiday). If drift is detected, an alert triggers a retraining pipeline.

![MLOps Flow](https://github.com/ziad-alalami/TEAM_BINARY_BROS_GOOGLE_KFUPM_HACKATHON_TECHNICAL_DELIVERABLES/blob/main/mlops_pipeline.svg)

---

## 5. Simulation Workflows

### A. Core Simulation Loop
When a user triggers a simulation, the system iterates through a 30-day cycle. The **Backend API** acts as the controller, fetching the initial graph state from **Neo4j**, passing it to the **Vertex AI** model for inference, and storing the daily predictions back into the database.

**Process Flow:**
1.  User sets parameters (e.g., R0 = 2.5).
2.  Backend fetches the current graph topology (Day 0) from Neo4j.
3.  Vertex AI predicts the next day's state (S, E, I, R counts) using the Temporal GNN.
4.  Results are stored and sent back to the UI for rendering.
![Simulation Sequence Flow](https://github.com/ziad-alalami/TEAM_BINARY_BROS_GOOGLE_KFUPM_HACKATHON_TECHNICAL_DELIVERABLES/blob/main/simulate_usecase.svg)

### B. AI Agent Interaction
We integrated **Vertex AI Agent Builder** to allow natural language interaction. The user can ask complex questions or trigger actions. Crucially, the **Backend API** mediates all requests, ensuring security and proper tool execution.

**Use Cases:**
* **Q&A:** "Why is the infection rate high in Riyadh?" (The agent queries Neo4j via the backend to explain mobility patterns).
* **Action:** "Simulate a scenario where we close schools." (The agent identifies the intent and triggers the simulation endpoint with specific parameters).

![Agent Sequence Flow](https://github.com/ziad-alalami/TEAM_BINARY_BROS_GOOGLE_KFUPM_HACKATHON_TECHNICAL_DELIVERABLES/blob/main/agent_usecase.png)
