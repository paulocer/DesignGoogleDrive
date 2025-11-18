# LLMConstraints.md

# Architectural Constraints

## C-1: Storage & Infrastructure Limits
* **Max File Size**: The system must enforce a hard limit of **10 GB** per individual file.
* **Storage Backend**: The system is constrained to use **Amazon S3** for active object storage and **Amazon S3 Glacier** for cold/archival storage.
* **Database Technology**: The metadata database must be a **Relational Database** to natively support ACID properties and ensure strong consistency.

## C-2: Scalability & Capacity
* **User Scale**: The architecture must support **50 million signed-up users** and **10 million Daily Active Users (DAU)**.
* **Throughput**: The system must handle an estimated average upload QPS of ~240 and a **peak QPS of 480**.
* **Total Storage**: The design must account for a total storage projection of **500 Petabytes**.

## C-3: Consistency & Synchronization Model
* **Strong Consistency**: The system must provide strong consistency by default; eventual consistency is not acceptable for this file storage use case. Database caches must be invalidated on write.
* **Delta Sync**: To meet bandwidth constraints, the system must not transfer whole files for minor edits; it is constrained to implement **block-level delta synchronization**.
* **Communication Protocol**: The notification service is constrained to use **Long Polling** rather than WebSockets, as communication is unidirectional (server-to-client) and notifications are infrequent.

## C-4: Security & Compliance
* **Encryption at Rest**: All files must be split into blocks and **encrypted** individually before being stored in the cloud.
* **Encryption in Transit**: All data transfers between the client and backend servers must be secured using **HTTPS/SSL**.
* **Client-Side Security**: Encryption logic should ideally be centralized in **Block Servers** rather than relying solely on client-side implementation to prevent hacking or manipulation.

## C-5: Application Constraints
* **Conflict Resolution Strategy**: The system must enforce a "**first version wins**" strategy for sync conflicts, requiring manual user intervention for subsequent conflicting versions.
* **Platform Support**: The system must support access via **Web, Mobile (iOS/Android), and Tablet**.