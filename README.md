# cql-fhir-flink
CQL evaluator configured as an Apache Flink task to continuously evaluate incoming FHIR patient resources in an input directory.

Change directory paths appropriately in `Application.java`.

## Steps

1. Copy relevant valueset files in the `valuesets` directory.
2. Copy measure CQL definitions in the `cql` directory.
3. Start the application by running `Application.java`.
4. Place patient FHIR JSON formatted files in the `input` directory.

**TODO:** Make measurement period a variable to the application.



# Currently under Development: GCP Deployment Design for CQL-FHIR-Flink



## High-Level Architecture

```mermaid
flowchart TD
    A[FHIR Resource Upload]-->|New file|B[Cloud Storage Bucket]
    B-->|Event Trigger|C[Cloud Function/Eventarc]
    C-->|Start Job|D[Dataproc Flink Cluster]
    D-->|Evaluated Results|E[BigQuery]
    D-->|Real-time Stream|F[Pub/Sub]
    E-->|REST API Query|G[Frontend Cloud Run or App Engine]
    F-->|WebSocket/API|G
    G-->|Visualization|H[User Dashboard]
```

---

## Data Flow Sequence

```mermaid
sequenceDiagram
    participant User
    participant GCS as Cloud Storage
    participant CF as Cloud Function
    participant Flink as Dataproc Flink
    participant BQ as BigQuery
    participant PubSub
    participant FE as Frontend

    User->>GCS: Upload FHIR Resource
    GCS->>CF: Event trigger on new file
    CF->>Flink: Start Flink evaluation job
    Flink->>BQ: Write evaluation results
    Flink->>PubSub: Publish real-time results
    FE->>BQ: Query historical results (REST API)
    FE->>PubSub: Subscribe to real-time updates (WebSocket/API)
    FE->>User: Visualize results/dashboard
```

---

## Deployment Steps Overview

```mermaid
graph TD
    subgraph GCP
        A[Build & Containerize Flink Java App] --> B[Deploy to Dataproc]
        C[Create Cloud Storage Bucket] --> D[Setup Cloud Function Trigger]
        B --> E[Connect to Cloud Storage for Input]
        B --> F[Output to BigQuery]
        B --> G[Publish to Pub/Sub]
        H[Develop Frontend App] --> I[Deploy to Cloud Run]
        I --> J[Implement API for BigQuery and Pub/Sub]
        J --> K[User Dashboard]
    end
```

---

## GCP Services Overview

| Function            | GCP Service                              |
|---------------------|------------------------------------------|
| File Storage        | Cloud Storage                            |
| Event Trigger       | Cloud Function/Eventarc                  |
| Stream Processing   | Dataproc (Flink)                         |
| Result Storage      | BigQuery                                 |
| Real-Time Streaming | Pub/Sub                                  |
| Frontend Hosting    | Cloud Run / App Engine                   |
| Authentication      | Identity Platform / Firebase Auth        |
| Monitoring          | Cloud Monitoring / Logging               |

---

## Frontend Visualization Features

- Real-time dashboard for CQL evaluation results per patient (WebSocket for live updates)
- Historical analytics/search (REST API to backend querying BigQuery)
- Alerts and notifications for critical evaluations
- Authentication for secure access

---
