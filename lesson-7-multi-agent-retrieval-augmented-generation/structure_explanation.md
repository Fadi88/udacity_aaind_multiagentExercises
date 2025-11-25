# Lesson 7: Multi-Agent RAG System Explanation

## Problem Statement
The code simulates a **Multi-Agent RAG (Retrieval-Augmented Generation) System** for a health insurance company. The core problem it addresses is **automating the resolution of customer complaints regarding denied insurance claims**.

Key challenges simulated:
*   **High Denial Rate:** The system simulates a strict policy where ~98% of claims are initially denied.
*   **Complex Information:** Resolving complaints requires accessing various data sources: patient records, claim details, insurance policies, and historical precedents (similar past claims).
*   **Privacy & Access Control:** Different actors (Customer Service, Medical Reviewers) have different permission levels (e.g., a Customer Service agent shouldn't see sensitive financial data that a Medical Reviewer might).

## Code Structure

The code is organized into four main layers:

### 1. Data Layer (Mock Database)
*   **Models:** Classes like `Claim`, `PatientRecord`, and `ComplaintRecord` define the data structure.
*   **Access Control:** The `PrivacyLevel` class and `AccessControl` logic ensure agents only access data they are authorized to see (e.g., `AGENT` vs. `CUSTOMER` level).
*   **Database:** A simple in-memory `Database` class stores all records and enforces these access rules during retrieval.
*   **DataGenerator:** Populates the system with random patients, claims, and complaints for testing.

### 2. RAG Layer (Vector Search)
This layer powers the "intelligence" of the agents by allowing them to find relevant context.
*   **`VectorKnowledgeBase`:** Stores general insurance policies and procedures. It uses TF-IDF and Cosine Similarity to let agents search for rules like "appeal process" or "coverage verification".
*   **`VectorClaimSearch`:** Indexes historical claims. This allows agents to find **similar past claims** to ensure consistent decision-making (e.g., "How did we handle other claims with this procedure code?").

### 3. Tools Layer
These are specific functions decorated with `@tool` that agents can call. They act as the bridge between the agents and the data/RAG layers.
*   **Examples:**
    *   `search_knowledge_base`: Queries the policy documents.
    *   `find_similar_claims`: Looks up precedents.
    *   `get_claim_details` / `get_patient_info`: Retrieves specific records.
    *   `submit_complaint` / `respond_to_complaint`: Modifies the state of the system.

### 4. Agent Layer
The system uses specialized agents, each with a specific role and set of tools:
*   **`ClaimProcessingAgent`:** Handles the initial adjudication of claims.
*   **`CustomerServiceAgent` (Access: CUSTOMER):** The front-line interface. It can look up basic info and submit complaints but has limited access to deep medical/financial data.
*   **`MedicalReviewAgent` (Access: AGENT):** The expert. It reviews denied claims, checks medical necessity, and uses the RAG tools to find similar cases and policy justifications.
*   **`ComplaintResolutionOrchestrator`:** The manager. It coordinates the workflow:
    1.  Identifies the claim from a user's complaint.
    2.  Submits the formal complaint.
    3.  Triggers a medical review.
    4.  Generates a final, empathetic response back to the customer.

## Execution Flow (`run_demo`)
The script runs a simulation where it:
1.  Populates the database with mock data.
2.  Generates a random complaint about a denied claim.
3.  Spins up the `ComplaintResolutionOrchestrator`.
4.  Runs the full "Complaint -> Review -> Response" pipeline to demonstrate how the agents collaborate to resolve the issue.
