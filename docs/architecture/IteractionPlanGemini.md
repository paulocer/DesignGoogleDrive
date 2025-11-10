Escreva um plano de iterações seguindo o processo de Attribute-Driven Design (ADD). Cada iteração deve abordar drivers prioritários (requisitos funcionais, cenários de atributos de qualidade, preocupações e restrições) para construir incrementalmente a arquitetura do sistema.

---

Based on the Attribute-Driven Design (ADD) methodology from *Software Architecture in Practice* and the system requirements from the *Design Google Drive* document, here is an incremental iteration plan for designing the architecture.

This plan prioritizes drivers to build the system from its core functionality (storage) outward to more complex features (sync, sharing) and systemic qualities (scalability, reliability).

---

## Overview of the Process

Each iteration will follow the core steps of Attribute-Driven Design (ADD):
1.  **Review Inputs:** Identify all drivers for the system (functional requirements, quality attribute scenarios, constraints).
2.  **Establish Iteration Goal:** Select a high-priority subset of drivers to focus on.
3.  **Choose Elements to Refine:** Select the part of the system (or the whole system, if new) to design.
4.  **Choose Design Concepts:** Select architectural patterns, tactics, and technologies to satisfy the selected drivers.
5.  **Instantiate & Define:** Instantiate architectural elements, allocate responsibilities, and define their interfaces.
6.  **Sketch Views & Record Decisions:** Document the new design (e.g., C&C views, DB schemas) and the rationale.
7.  **Perform Analysis:** Review the design against the iteration's drivers to ensure they are met.

---

## Iteration 1: Core File Storage and Retrieval

This iteration focuses on the most fundamental use case: getting a file into and out of the system for a single user.

* **Iteration Goal:** Establish the backbone for file upload and download, ensuring data is stored securely and reliably.
* **Drivers Addressed:**
    * **Functional:** Upload files, Download files.
    * **Quality Attributes:**
        * **Reliability (Storage):** "Data loss is unacceptable."
        * **Security:** "Files in the storage must be encrypted."
    * **Constraints:** File size limit (10 GB), Encryption required.
* **Design Activities & Concepts:**
    * **Refine:** The entire system ("greenfield").
    * **Concepts:** Client-Server, Load Balancing, Object Storage, Metadata Database.
    * **Instantiate (Elements):**
        * **API Servers:** (Stateless) To handle user requests.
        * **Load Balancer:** To distribute traffic to API servers.
        * **Metadata Database:** To store file information (name, size, path, user_id).
        * **Cloud Storage (S3):** To store the actual file data.
    * **Define (Interfaces/Decisions):**
        * Define basic REST APIs for `upload` and `download`.
        * Decide on using Amazon S3 for its high durability and built-in encryption.
        * Define the initial `File` and `User` tables in the Metadata DB.
* **Views Sketched:** A basic Component-and-Connector (C&C) view showing the flow: Client → Load Balancer → API Server → (Metadata DB + Cloud Storage).

---

## Iteration 2: Multi-Device Synchronization

This iteration introduces the core "sync" feature, moving from a simple storage service to a synchronization service.

* **Iteration Goal:** Enable changes (adds, edits) from one client to be automatically propagated to other clients registered to the same user.
* **Drivers Addressed:**
    * **Functional:** Sync files across multiple devices, Send notifications (on file edit/delete).
    * **Quality Attributes:**
        * **Fast Sync Speed:** Changes should propagate quickly.
        * **High Availability:** The notification system must be reliable.
* **Design Activities & Concepts:**
    * **Refine:** Client application, API Servers, and introduce a notification mechanism.
    * **Concepts:** Publish-Subscriber, Long Polling (as chosen in the design doc), Caching.
    * **Instantiate (Elements):**
        * **Notification Service:** To manage open connections with clients.
        * **Offline Backup Queue:** To hold notifications for disconnected clients.
        * **Metadata Cache:** To speed up retrieval of file info for sync checks.
    * **Define (Interfaces/Decisions):**
        * Define the long polling mechanism: Client establishes a connection to the Notification Service.
        * Define the Pub/Sub flow: API Server publishes a "change" event (on upload/edit) to the Notification Service, which then informs relevant clients.
        * Define the client-side logic for "pulling" changes upon receiving a notification.
* **Views Sketched:** Updated C&C view showing the Notification Service and its connections. A sequence diagram illustrating the end-to-end sync flow.

---

## Iteration 3: Performance, Efficiency, and Large Files

This iteration optimizes the sync process from Iteration 2, focusing on network efficiency and robustness for large files.

* **Iteration Goal:** Reduce bandwidth usage and improve sync speed by only transferring changes. Support large, resumable uploads.
* **Drivers Addressed:**
    * **Functional:** Support for large files (resumable upload).
    * **Quality Attributes:**
        * **Low Bandwidth Usage:** "If a product takes a lot of unnecessary network bandwidth, users will be unhappy."
        * **Fast Sync Speed:** (Achieved via delta sync).
    * **Concerns:** Handling network interruptions during 10 GB uploads.
* **Design Activities & Concepts:**
    * **Refine:** The file upload/download flow.
    * **Concepts:** File Chunking (Blocking), Delta Sync, Compression, Resumable Upload (Pattern).
    * **Instantiate (Elements):**
        * **Block Servers:** A new service to handle the heavy lifting of chunking, compression, encryption, and delta calculation.
    * **Define (Interfaces/Decisions):**
        * Define the "Resumable Upload" API (3-step process from the doc).
        * Define the `Block` table in the Metadata DB to track file blocks.
        * Reroute the upload flow: Client → Block Server → Cloud Storage (as in Fig. 15-14).
        * Decide on "delta sync": Only modified blocks are re-uploaded.
* **Views Sketched:** Detailed sequence diagrams for the upload flow (Fig. 15-14) and download flow (Fig. 15-15), highlighting the role of the Block Servers.

---

## Iteration 4: Collaboration and File History

With a robust sync system in place, this iteration adds collaboration and versioning features.

* **Iteration Goal:** Allow users to share files with others and access previous versions of a file.
* **Drivers Addressed:**
    * **Functional:** Share files, See file revisions, Send notifications (on file share).
    * **Quality Attributes:**
        * **Reliability (Versioning):** Ability to recover previous file states.
        * **Security:** Enforcing access controls for shared files.
    * **Concerns:** Handling sync conflicts when two users edit the same file.
* **Design Activities & Concepts:**
    * **Refine:** Metadata Database, API Servers.
    * **Concepts:** Access Control Lists (ACLs) / Permissions, File Versioning.
    * **Instantiate (Elements):**
        * No new *services*, but significant refinement of the `Metadata DB` schema.
    * **Define (Interfaces/Decisions):**
        * Add `file_version` table to the DB (Fig. 15-13) to store revision history.
        * Add tables/fields to manage sharing permissions (e.g., linking users to files with specific roles like 'viewer' or 'editor').
        * Define new APIs: `share_file()`, `list_revisions()`.
        * Define the conflict resolution strategy: "First version that gets processed wins" (Fig. 15-8).
* **Views Sketched:** Updated database schema (Fig. 15-13). Flow diagrams for sync conflict resolution.

---

## Iteration 5: Scalability, Availability, and Cost

This final iteration "hardens" the entire system to meet the massive scale and high availability requirements.

* **Iteration Goal:** Ensure the system can scale to 10M DAU, remain available during failures, and optimize storage costs.
* **Drivers Addressed:**
    * **Quality Attributes:**
        * **Scalability:** "10M DAU."
        * **High Availability:** "Users should still be able to use the system when some servers are offline."
        * **Reliability:** (Failure handling for all components).
    * **Concerns:** High storage costs for 500 Petabytes, database hotspots.
* **Design Activities & Concepts:**
    * **Refine:** All components, especially `Metadata DB` and `Cloud Storage`.
    * **Concepts:** Database Sharding, Database Replication (Master-Slave), Horizontal Scaling, Redundancy, Data De-duplication, Cold Storage (Tiering).
    * **Instantiate (Elements):**
        * **Cold Storage (S3 Glacier):** For moving infrequently used data.
    * **Define (Interfaces/Decisions):**
        * Sharding strategy for the `Metadata DB` (e.g., by `user_id`).
        * Replication strategy for DB (promote slave to master) and Caches.
        * Failure handling playbooks for each component (e.g., LB failure, API server failure).
        * Data de-duplication strategy (at the block level).
        * Data tiering policy (e.g., move files/versions not accessed in 90 days to Cold Storage).
* **Views Sketched:** Deployment/Allocation view showing sharded and replicated databases across multiple data centers. C&C view showing the new Cold Storage tier.
