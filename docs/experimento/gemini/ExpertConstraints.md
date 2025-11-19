# LLMConstraints.md

# Architectural Constraints

## C-1: Technology Stack & Infrastructure
* **Storage Service**: The system must use **Amazon S3** for active file storage to leverage its scalability and replication capabilities.
* **Cold Storage**: Inactive data must be moved to **Amazon S3 Glacier** to reduce storage costs.
* **Database Type**: The system must use a **Relational Database** for metadata to natively support ACID (Atomicity, Consistency, Isolation, Durability) properties.
* **Communication Protocol**: The Notification Service must use **Long Polling** instead of WebSockets because communication is not bi-directional and notifications are infrequent.

## C-2: Business Rules & Usage Limits
* **File Size Limit**: The system must strictly enforce a maximum file size of **10 GB** per upload.
* **Storage Quota**: The design must account for **10 GB of free space** allocated per user.
* **Scope Limitation**: Real-time **Google Doc editing and collaboration** is explicitly defined as out of scope for this specific design phase.

## C-3: Security & Data Management
* **Encryption at Rest**: All files stored in the storage system (S3) must be **encrypted**.
* **Transport Security**: All data transfers between clients and backend servers must be protected using **HTTPS** and **Secure Sockets Layer (SSL)**.
* **Consistency Model**: The system is constrained to enforce **Strong Consistency** by default; it is unacceptable for different clients to see different versions of a file simultaneously.