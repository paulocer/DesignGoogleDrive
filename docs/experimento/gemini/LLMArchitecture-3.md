# LLMArchitecture-3.md

# Google Drive System - Architecture Document

## Table of Contents

1. [Introduction](#1-introduction)
2. [Context Diagram](#2-context-diagram)
3. [Architectural Drivers](#3-architectural-drivers)
 - [User Story Priorities](#user-story-priorities)
 - [Quality Attribute Scenario Priorities](#quality-attribute-scenario-priorities)
 - [Architectural Constraints](#architectural-constraints)
4. [Views of the module viewtype](#4-views-of-the-module-viewtype)
5. [Views of the component-and-connector viewtype](#5-views-of-the-component-and-connector-viewtype)
6. [Views of the allocation viewtype](#6-views-of-the-allocation-viewtype)
7. [Sequence Diagrams](#7-sequence-diagrams)
8. [Interfaces](#8-interfaces)
9. [Design Decisions](#9-design-decisions)

## 1. Introduction

This document defines the software architecture for the Google Drive-like system, a scalable file storage and synchronization service. It provides a comprehensive technical blueprint, detailing the system's structure, behavior, and deployment. This document is intended for software engineers, DevOps engineers, and stakeholders to understand the architectural decisions, the decomposition of the system into components, and how these components interact to satisfy the functional requirements (User Stories) and non-functional requirements (Quality Attribute Scenarios) defined in the project scope.

This document defines the software architecture for the Google Drive-like system. This version includes the core storage logic, synchronization subsystems, and the **Distributed Infrastructure** required to support 10M DAU and 500PB of data.

## 2. Context Diagram

The following diagram illustrates the system context for the Google Drive System. It depicts the boundaries of the system and its interactions with external entities (Actors), including the end-users and third-party infrastructure services required for storage (Amazon S3, Glacier) and notifications (Push Service).

### Diagram

```mermaid
graph TD
    subgraph "Case Study System"
        System[Google Drive System]
    end

    subgraph "External Actors"
        User[User]
        S3[Cloud Storage Provider<br> Amazon S3]
        Glacier[Cold Storage Provider<br> Amazon S3 Glacier]
        Push[Push Notification Service]
    end

    User -- "Uploads, downloads, syncs & shares files" --> System
    System -- "Stores & retrieves encrypted file blocks" --> S3
    System -- "Archives inactive/cold data" --> Glacier
    System -- "Delivers mobile alerts" --> Push
```
	
### External Actors	

| Actor                                     | Description                                                                                                                                                       |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| User                                      | The primary user of the system who accesses Google Drive via web browsers or mobile applications to upload, download, synchronize, and share files.              |
| Cloud Storage Provider (Amazon S3)        | An external object storage service used to store active file blocks securely. It provides high availability, scalability, and replication.                        |
| Cold Storage Provider (Amazon S3 Glacier) | An external storage service optimized for data archiving. The system moves infrequently used data here to reduce costs.                                           |
| Push Notification Service                 | An external infrastructure service (e.g., APNS, FCM) used to deliver real-time notifications to user mobile devices.                                              |

## 3. Architectural Drivers

This section summarizes the driving requirements for the architecture, derived from the expert analysis of the case study.

### User Story Priorities

| ID      | User Story Name          | Priority | Description                                                      |
|---------|--------------------------|----------|------------------------------------------------------------------|
| US-1.1  | Simple File Upload       | P1       | Core capability to upload files (drag-and-drop, any format).     |
| US-1.2  | Resumable Upload         | P1       | Handling large file uploads (up to 10GB) with resume capability. |
| US-1.3  | File Retrieval           | P1       | Downloading files to local devices.                              |
| US-2.1  | Multi-Device Sync        | P1       | Automatic propagation of file changes across devices.            |
| US-2.2  | Bandwidth Efficient Sync | P1       | Delta synchronization to transfer only modified blocks.          |
| US-2.3  | Conflict Resolution      | P1       | Handling concurrent edits with a "first wins" strategy.          |
| US-3.1  | File Revision History    | P2       | Viewing and restoring previous file versions.                    |
| US-3.2  | File Sharing             | P2       | Sharing files with specific users.                               |
| US-3.3  | Activity Notifications   | P1       | Alerts for file edits, deletions, or shares.                     |

### Quality Attribute Scenario Priorities

| ID       | Scenario Name                           | Quality Attribute    | Priority |
|----------|-----------------------------------------|----------------------|----------|
| QAS-001  | Storage Node Hardware Failure           | Reliability          | P1       |
| QAS-002  | Regional Data Center Outage             | Reliability          | P1       |
| QAS-003  | Network Interruption (Upload)           | Reliability          | P1       |
| QAS-004  | Web/API Server Failure                  | Availability         | P1       |
| QAS-005  | Metadata DB Master Failure              | Availability         | P1       |
| QAS-006  | High Traffic Surge                      | Availability         | P1       |
| QAS-007  | User Base Growth to 50M                 | Scalability          | P1       |
| QAS-008  | Massive Data Accumulation (500 PB)      | Scalability          | P1       |
| QAS-009  | Handling Peak Throughput                | Scalability          | P1       |
| QAS-010  | Delta Synchronization                   | Perf. Efficiency     | P1       |
| QAS-011  | Data Compression                        | Perf. Efficiency     | P2       |
| QAS-012  | Sync Speed / Update Latency             | Perf. Efficiency     | P1       |
| QAS-013  | Cache Invalidation on Write             | Data Consistency     | P1       |
| QAS-014  | Concurrent Edit Conflict Resolution     | Data Consistency     | P1       |
| QAS-015  | ACID Transaction Integrity              | Data Consistency     | P1       |
| QAS-016  | Data Confidentiality at Rest            | Security             | P1       |
| QAS-017  | Data Security in Transit                | Security             | P1       |
| QAS-018  | Unauthorized Access Control             | Security             | P1       |

### Architectural Constraints

| ID   | Constraint             | Description                                                                            |
|------|------------------------|----------------------------------------------------------------------------------------|
| C-1  | Technology Stack       | Must use Amazon S3, S3 Glacier, Relational DB (ACID), and Long Polling.                |
| C-2  | Business Rules         | Max file size 10GB; 10GB free space/user; Google Doc editing is out of scope.          |
| C-3  | Security & Consistency | Encryption at rest; HTTPS/SSL in transit; Strong Consistency required.                 |

**Key Priorities for Current Architecture:**

  * **US-1.1**: Simple File Upload (P1)
  * **US-1.2**: Resumable Upload (P1)
  * **US-2.2**: Delta Sync (P1)
  * **QAS-008**: Massive Data Accumulation / Sharding (P1)
  * **QAS-005**: DB Master Failure / Replication (P1)
  * **QAS-002**: Regional Outage / CRR (P1)

## 4. Views of the module viewtype

*(Section currently empty - Reserved for Code Structure/Classes)*

## 5. Views of the component-and-connector viewtype

### 5.1 Core System Decomposition (Service-Based Architecture)

This view illustrates the separation of the system into a **Control Plane** (handling metadata and user interactions) and a **Data Plane** (handling file content processing). This separation is critical for scaling the CPU-intensive encryption tasks independently of the IO-intensive metadata tasks.

<p align="center">
  <img src="docs/experimento/gemini/distributed_system_architecture.jpg" alt="Figure 1: Distributed System Architecture"/>
</p>

```mermaid
graph TD
    subgraph "Client Side"
        Client[Client App]
    end

    subgraph "DMZ / Network Boundary"
        LB[Load Balancer<br>SSL Termination]
    end

    subgraph "Application Layer"
        API[API Server Cluster<br> Control Plane]
        Block[Block Server Cluster<br> Data Plane]
    end

    subgraph "Data Persistence Layer"
        DB[Metadata DB<br>Relational]
        S3[Amazon S3<br>Encrypted Blocks]
    end

    Client -- 1. Metadata Req HTTPS --> LB
    LB -- Plain HTTP --> API
    API -- Read/Write --> DB
    
    Client -- 2. File Content HTTPS --> LB
    LB -- Plain HTTP --> Block
    Block -- 3. Encrypt & Upload --> S3
    Block -- 4. Status Update --> API
```

#### Component Responsibilities

| Component | Responsibilities |
| :--- | :--- |
| **Client Application** | Initiates upload/download requests; handles user interactions. |
| **Load Balancer** | Distributes traffic; performs **SSL Termination** to satisfy QAS-017. |
| **API Server** | Authenticates users; manages file **Metadata** (name, size, owner); orchestrates status updates. |
| **Block Server** | Receives file streams; **Splits** files into blocks; **Encrypts** blocks (QAS-016); Uploads to S3. |
| **Metadata DB** | Stores relational data (User, File, Block mappings); ensures ACID compliance. |
| **Cloud Storage (S3)** | Persists encrypted file blocks; ensures high durability. |

### 5.2 System Decomposition (Sync & Notification Enabled)

This view extends the previous architecture by adding the **Notification Service** and **Message Queue** to handle the "Control Plane" events.

```mermaid
graph TD
    subgraph "Client Side"
        Client["Client App<br>\(Sync Agent\)"]
    end

    subgraph "DMZ / Network Boundary"
        LB["Load Balancer<br>\(SSL Termination\)"]
    end

    subgraph "Application Layer"
        API["API Server Cluster<br>\(Sync & Metadata\)"]
        Block["Block Server Cluster<br>\(Encryption & Storage\)"]
        Notif["Notification Service<br>\(Long Polling\)"]
        Queue["Message Queue<br>\(Pub/Sub\)"]
    end

    subgraph "Data Persistence Layer"
        DB[Metadata DB<br>Versioning Enabled]
        S3[Amazon S3]
    end

    %% File Data Flow
    Client -- HTTPS --> LB
    LB --> Block
    Block --> S3

    %% Metadata & Sync Flow
    LB --> API
    API --> DB
    
    %% Notification Flow
    API -- "File Updated" --> Queue
    Queue --> Notif
    Client -- "Long Poll \(Hold\)" --> LB
    LB --> Notif
    Notif -- "Push Event" --> Client
```

#### Component Responsibilities (Updated)

| Component | Responsibilities |
| :--- | :--- |
| **API Server** | Authenticates users; **Calculates Deltas**; **Checks Versions** (Optimistic Locking); Publishes events to Queue. |
| **Notification Service** | Manages **Long Polling** connections; Delivers real-time events to clients. |
| **Message Queue** | Decouples API Server (Write) from Notification Service (Push). |
| **Metadata DB** | Stores file metadata, **Block Hashes**, and **Version Numbers**. |
| **Client (Sync Agent)** | Maintains local DB; Calculates local block hashes; Initiates Long Poll; Handles 409 Conflicts. |

### 5.3 Distributed System Topology

This view illustrates the **Sharding** and **Replication** strategy. It shows how the monolithic "Metadata DB" and "S3" from previous iterations are actually deployed as clusters to ensure scale and reliability.

[Image of distributed system architecture]

```mermaid
graph TD
    subgraph "Traffic Entry"
        LB_A["Load Balancer \(Active\)"]
        LB_P["Load Balancer (\Passive\)"]
        LB_A -. Heartbeat .- LB_P
    end

    subgraph "Compute Layer"
        API["API Server Cluster<br>\(Stateless\)"]
        Block["Block Server Cluster<br>\(Stateful Uploads\)"]
        Notif[Notification Service]
    end

    subgraph "Data Layer (Sharded & Replicated)"
        subgraph "Shard 1 (User ID % N = 0)"
            M1["(\Master DB\)"]
            S1_1["\(Slave 1\)"]
            M1 -. Sync .-> S1_1
        end
        
        subgraph "Shard N (User ID % N = X)"
            MN[(Master DB)]
            SN_1[(Slave 1)]
            MN -. Sync .-> SN_1
        end
    end
    
    subgraph "Storage Layer (Geo-Redundant)"
        S3_Pri["S3 Bucket \(Primary Region\)"]
        S3_Sec["S3 Bucket \(Backup Region\)"]
        S3_Pri -. Async CRR .-> S3_Sec
    end

    LB_A --> API
    LB_A --> Block
    LB_A --> Notif
    
    API -- "Route by UserID" --> M1
    API -- "Route by UserID" --> MN
    
    Block --> S3_Pri
```

#### Component Responsibilities (Updated)

| Component | Responsibilities |
| :--- | :--- |
| **Load Balancer (HA)** | Entry point; SSL Termination; **Health Monitoring**; **Failover**. |
| **API Server** | **Sharding Router** (Determines which DB Shard to query based on UserID); Sync Logic. |
| **Block Server** | **Resumable Session Manager**; Encrypts/Decrypts; Uploads to S3. |
| **DB Shards (1..N)** | Stores metadata for a specific subset of users; supports linear scaling. |
| **DB Slaves** | Read-only replicas for high availability and offloading read traffic. |
| **S3 (Primary/Secondary)** | Physically stores encrypted blocks; replicates data across regions for disaster recovery. |

## 6. Views of the allocation viewtype

*(Section currently empty - Reserved for Deployment/Infrastructure)*

## 7. Sequence Diagrams

This section details the dynamic behavior of the system for the critical User Stories and Quality Attribute Scenarios identified in the Iteration Plan.

### 7.1 Iteration 1 Drivers (Core Structure)

#### US-1.1: Simple File Upload

This sequence demonstrates the split flow: Metadata first, then Data.

```mermaid
sequenceDiagram
    participant Client
    participant LB as Load Balancer
    participant API as API Server
    participant Block as Block Server
    participant S3 as Amazon S3
    participant DB as Metadata DB

    Note over Client, DB: Phase 1: Metadata Initialization
    Client->>LB: POST /files metadata
    LB->>API: Forward Request
    API->>DB: INSERT file status="pending"
    DB-->>API: file_id
    API-->>Client: upload_token, file_id

    Note over Client, DB: Phase 2: Data Upload
    Client->>LB: PUT /blocks content
    LB->>Block: Forward Stream
    loop Chunking & Encryption
        Block->>Block: Split into blocks
        Block->>Block: Compress & Encrypt AES
        Block->>S3: PutObject Encrypted Block
        S3-->>Block: Ack
    end

    Note over Client, DB: Phase 3: Finalization
    Block->>API: Update Status uploaded
    API->>DB: UPDATE file SET status="uploaded"
    Block-->>Client: 200 OK
```

#### US-1.3: File Retrieval (Download)

```mermaid
sequenceDiagram
    participant Client
    participant LB as Load Balancer
    participant API as API Server
    participant Block as Block Server
    participant S3 as Amazon S3
    participant DB as Metadata DB

    Client->>LB: GET /files/{id}
    LB->>API: Forward Request
    API->>DB: SELECT blocks FROM file_map
    DB-->>API: List of Block IDs & Hashes
    API-->>Client: Metadata Block List

    loop For each Block
        Client->>LB: GET /blocks/{block_id}
        LB->>Block: Forward Request
        Block->>S3: GetObject Encrypted
        S3-->>Block: Encrypted Stream
        Block->>Block: Decrypt & Decompress
        Block-->>Client: Plaintext Block Stream
    end
```

### 7.2 Iteration 2 Drivers (Sync & Consistency)

#### US-2.1 & US-3.3: Notification & Pull

This sequence shows how Device B gets updated when Device A writes.

```mermaid
sequenceDiagram
    participant DeviceB
    participant Notif as Notification Svc
    participant Queue
    participant API
    
    Note over DeviceB: Long Poll Established
    DeviceB->>Notif: GET /poll (timeout=60s)
    Notif->>Notif: Hold Connection...

    Note over API: Device A updates File X
    API->>Queue: Publish "File X Changed"
    Queue->>Notif: Consume Event
    
    Note over Notif: Release Hold
    Notif-->>DeviceB: 200 OK { file_id: "X", type: "update" }
    
    DeviceB->>API: GET /files/X (Pull Metadata)
    API-->>DeviceB: New Metadata
```

#### US-2.2: Delta Sync Upload (Bandwidth Optimization)

This sequence shows how the client avoids uploading the full file.

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant Block
    participant DB

    Client->>Client: Calc Block Hashes [H1, H2, H3]
    Client->>API: POST /sync (ver=5, hashes=[H1, H2, H3])
    
    Note over API, DB: Optimistic Lock Check
    API->>DB: Check version == 5?
    DB-->>API: OK (Match)
    
    Note over API: Delta Calculation
    API->>DB: Get existing hashes
    API->>API: Compare. Missing = [H2]
    API-->>Client: 200 OK { missing: [H2], upload_token: "abc" }
    
    Note over Client: Upload ONLY H2
    Client->>Block: Upload Block H2
    Block->>S3: Persist H2
    Block->>API: Confirm H2
    API->>DB: Update File (ver=6, blocks=[H1, H2, H3])
```

#### US-2.3: Conflict Resolution (First-Write-Wins)

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant DB

    Note over DB: Current Version = 10
    Client->>API: POST /sync (ver=10, hashes=...)
    
    Note over API: Simulating Race Condition
    Note right of DB: Another user just updated ver to 11
    
    API->>DB: UPDATE ... WHERE ver=10
    DB-->>API: Rows Affected: 0 (Fail)
    
    API-->>Client: 409 CONFLICT { server_ver: 11 }
    Note over Client: User must resolve
```

### 7.3 Iteration 3 Drivers (Reliability)

#### US-1.2: Resumable Upload (3-Step Protocol)

This sequence shows how the system handles a network interruption.

```mermaid
sequenceDiagram
    participant Client
    participant Block as Block Server
    participant S3

    Note over Client, Block: Step 1: Initiation
    Client->>Block: POST /upload/init (size=10GB)
    Block-->>Client: 200 OK { upload_id: "sess_123" }

    Note over Client, Block: Step 2: Upload Chunks
    Client->>Block: PUT /upload/sess_123 (bytes 0-5MB)
    Block->>Block: Buffer & Encrypt
    Block-->>Client: 308 Resume Incomplete (Range: 0-5MB)
    
    Note over Client: NETWORK CRASH!
    Note over Client: ... Reconnects ...

    Note over Client, Block: Step 3: Resume
    Client->>Block: GET /upload/sess_123 (Status?)
    Block-->>Client: 200 OK { saved_range: "0-5MB" }
    
    Client->>Block: PUT /upload/sess_123 (bytes 5MB-10MB)
    Block->>Block: Append
    
    Note over Block: Buffer Full (Block Size Reached)
    Block->>S3: PutObject (Encrypted Block)
    Block-->>Client: 308 Resume Incomplete (Range: 0-10MB)
```
		
### 7.4 Iteration 4 Drivers (Security & Optimization)

#### US-3.1: File Revision History

Snippet de código

```mermaid
sequenceDiagram
    %% Empty placeholder for US-3.1 sequence
```
	
#### US-3.2: File Sharing

Snippet de código

```mermaid
sequenceDiagram
    %% Empty placeholder for US-3.2 sequence
```
	
#### QAS-011: Data Compression

Snippet de código

```mermaid
sequenceDiagram
    %% Empty placeholder for QAS-011 sequence
```
	
#### QAS-015: ACID Transaction Integrity

Snippet de código

```mermaid
sequenceDiagram
    %% Empty placeholder for QAS-015 sequence
```
	
#### QAS-018: Unauthorized Access Control

Snippet de código

```mermaid
sequenceDiagram
    %% Empty placeholder for QAS-018 sequence
```

## 8. Interfaces

### 8.1 API Server Interfaces

#### I-01: File Metadata API

  * **Protocol**: REST / HTTPS
  * **Function**: Initializes file upload.
  * **Input**: JSON `{"name": "doc.pdf", "folder_id": "root"}`
  * **Output**: JSON `{"file_id": "uuid", "status": "pending"}`

### 8.2 Block Server Interfaces

#### I-02: Block Upload API

  * **Protocol**: REST / HTTPS
  * **Function**: Accepts binary file streams.
  * **Headers**: `Authorization: Bearer <token>`, `X-File-Id: <uuid>`
  * **Body**: Binary Data

### 8.3 Synchronization Interfaces

#### I-04: Long Polling Interface

  * **Endpoint**: `GET /notifications/poll`
  * **Headers**: `Timeout: 60`
  * **Behavior**: Holds connection until event or timeout.

#### I-05: Delta Sync Proposal API

  * **Endpoint**: `POST /files/{id}/sync`
  * **Input**: `{"base_version": 5, "block_hashes": ["h1", "h2"]}`
  * **Output**: `{"status": "ok", "missing_blocks": ["h2"]}` OR `409 Conflict`

### 8.4 Resumable Upload Interfaces

#### I-07: Resumable Init API

  * **Endpoint**: `POST /files/upload?resumable=true`
  * **Output**: `upload_id`

#### I-08: Chunk Upload API

  * **Endpoint**: `PUT /upload/{upload_id}`
  * **Header**: `Content-Range: bytes start-end/total`
  * **Response**: `308 Resume Incomplete` (standard protocol)

## 9. Design Decisions

### 9.1 Iteration 1 Decisions

| Driver | Decision | Rationale | Discarded Alternative |
| :--- | :--- | :--- | :--- |
| **US-1.1, QAS-009** | **Separation of API & Block Servers** | Decouples CPU-heavy encryption/compression logic (Block Server) from IO-bound metadata logic (API Server), allowing independent scaling. | **Monolithic Arch**: Cannot scale resources independently. |
| **US-1.1, US-2.2** | **Block-Based Storage** | Splitting files enables parallel uploads, granular encryption, and future optimizations like Delta Sync. | **Whole File Storage**: Inefficient for large file updates. |
| **QAS-016** | **Server-Side Encryption (Block Server)** | Centralizes security control. Avoids reliance on client-side implementation which is error-prone across multiple platforms (iOS/Android/Web). | **Client-Side Encryption**: Too complex to maintain consistency across platforms. |
| **QAS-016** | **Proxy Upload (Client -\> Block -\> S3)** | Required to perform server-side chunking and encryption. | **Direct S3 Upload**: Forces client to handle chunking/encryption, increasing client complexity. |

### 9.2 Iteration 2 Decisions (Sync)

| Driver | Decision | Rationale | Discarded Alternative |
| :--- | :--- | :--- | :--- |
| **US-2.2** | **Block-Level Delta Sync** | Comparing hashes of blocks allows transferring only changed data, reducing bandwidth usage by orders of magnitude. | **Whole File Sync**: Too bandwidth intensive. |
| **US-2.3** | **Optimistic Locking** | Enforces "First-Write-Wins" via database versioning. Ensures strong consistency without heavy pessimistic locks. | **Last-Write-Wins**: Risks "lost updates". |
| **Constraint C-1** | **Long Polling** | Required by constraints. Efficient for uni-directional, infrequent notifications. | **WebSockets**: Prohibited by project constraints. |
| **US-3.3** | **Pub/Sub Pattern** | Decouples the write path (API) from the notification path, ensuring write latency isn't affected by notification delivery speed. | **Synchronous Calls**: Tightly couples components. |

### 9.3 Iteration 3 Decisions (Scale & Reliability)

| Driver | Decision | Rationale | Discarded Alternative |
| :--- | :--- | :--- | :--- |
| **QAS-008** | **Sharding by `user_id`** | Keeps all user metadata on one node, optimizing common queries ("List my files"). Scaling is linear by adding shards. | **Sharding by `file_id`**: Causes massive "scatter-gather" query overhead for file listings. |
| **QAS-005** | **Master-Slave Replication** | Ensures DB availability during Master failure and scales read throughput for high traffic. | **Multi-Master**: Too complex for consistency requirements. |
| **US-1.2** | **Resumable Uploads** | 3-step protocol (Init, Upload, Query) allows recovering large transfers on unstable networks. | **Stream Upload**: Fails completely on net drop. |
| **QAS-002** | **S3 Cross-Region Replication** | Uses native cloud capabilities to protect against regional disasters. | **Custom Backup**: High maintenance/cost. |
