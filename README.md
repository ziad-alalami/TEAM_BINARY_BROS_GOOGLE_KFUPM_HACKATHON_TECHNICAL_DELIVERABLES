# Binary Bros: Epidemic Simulation & Prediction Platform
**Google KFUPM Hackathon | Week 1 Deliverable**

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

[IMAGE_DESCRIPTION_PLACEHOLDER: A flowchart showing the Google Cloud architecture. It depicts the React Client connecting to a Cloud Load Balancer, which forwards requests to a FastAPI backend on Cloud Run. The backend connects to Vertex AI services (Agent, Prediction, Training) and Data Persistence services (Neo4j, BigQuery, Cloud Storage).]

---

## 3. User Interface & Experience
The dashboard provides an immersive command center for health officials. It features a high-fidelity **3D Map** that visualizes infection hotspots in real-time using color-coded nodes (Red for high infection, Green for low).

### UI Layout & Features
The interface is designed for "Dark Mode" scientific visualization to reduce eye strain during monitoring.
* **Left Sidebar (Analytics):** Displays granular node details (e.g., "Riyadh-North" infection stats, virality scores).
* **Center Canvas (Visualization):** An interactive 3D globe powered by **Google Maps Platform**, allowing users to zoom into specific districts.
* **Right Sidebar (Controls):** Contains sliders for hyperparameters (R0, Incubation period) and toggles for policy interventions (School Closures, Mask Mandates).
* **Bottom Panel (Timeline):** A playback control to view the spread evolution over the 30-day horizon.

[IMAGE_DESCRIPTION_PLACEHOLDER: A wireframe diagram or screenshot of the UI. It shows a layout with a top header, a left sidebar for statistics, a central 3D map container, a right sidebar for simulation controls, a bottom timeline slider, and a chat widget in the bottom right corner.]

---

## 4. Simulation Workflows

### A. Core Simulation Loop
When a user triggers a simulation, the system iterates through a 30-day cycle. The **Backend API** acts as the controller, fetching the initial graph state from **Neo4j**, passing it to the **Vertex AI** model for inference, and storing the daily predictions back into the database.

**Process Flow:**
1.  User sets parameters (e.g., R0 = 2.5).
2.  Backend fetches the current graph topology (Day 0) from Neo4j.
3.  Vertex AI predicts the next day's state (S, E, I, R counts) using the Temporal GNN.
4.  Results are stored and sent back to the UI for rendering.

[IMAGE_DESCRIPTION_PLACEHOLDER: A sequence diagram illustrating the simulation flow. The User clicks 'Run', the UI calls the Backend API, the API fetches the graph state from Neo4j, sends it to the Vertex AI Model for prediction loop (30 days), and returns the forecast series to the UI.]

### B. AI Agent Interaction
We integrated **Vertex AI Agent Builder** to allow natural language interaction. The user can ask complex questions or trigger actions. Crucially, the **Backend API** mediates all requests, ensuring security and proper tool execution.

**Use Cases:**
* **Q&A:** "Why is the infection rate high in Riyadh?" (The agent queries Neo4j via the backend to explain mobility patterns).
* **Action:** "Simulate a scenario where we close schools." (The agent identifies the intent and triggers the simulation endpoint with specific parameters).

[IMAGE_DESCRIPTION_PLACEHOLDER: A sequence diagram for the AI Agent. It shows two scenarios: 1. A Q&A flow where the Agent queries Neo4j to answer a user question. 2. An action flow where the Agent triggers the 'simulate' tool on the Backend API to start a new job based on user instructions.]
