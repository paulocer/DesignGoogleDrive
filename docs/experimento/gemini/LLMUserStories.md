# LLMUserStories.md

# User Stories

## User Stories by Epic

### Epic 1: File Ingestion and Retrieval

#### US-1.1: Reliable Large File Upload
**As a** Content Creator
**I want to** upload files up to 10 GB in size with the ability to resume the upload if my network connection is interrupted
**So that** I do not lose progress or waste bandwidth restarting the upload of large video or data files.

**Acceptance Criteria:**
* The system accepts files up to 10 GB.
* If the internet disconnects at 50%, the upload automatically resumes from 50% upon reconnection (Resumable Upload).
* The user receives a visual confirmation when the file status changes to "uploaded".

---

#### US-1.2: Drag and Drop Ingestion
**As a** Desktop User
**I want to** drag and drop files directly into the application interface
**So that** I can quickly add documents to my drive without navigating through file system menus.

**Acceptance Criteria:**
* Dragging a file into the UI triggers the "Simple upload" process for small files.
* The UI provides immediate feedback that the file is being processed.

---

#### US-1.3: Secure File Download
**As a** Registered User
**I want to** download files to my local device that are identical to the versions stored in the cloud
**So that** I can access and use my data offline or on different machines.

**Acceptance Criteria:**
* The downloaded file is a byte-for-byte match of the original.
* The system handles the decryption and reassembly of blocks transparently before the file reaches the user's local storage.

---

### Epic 2: Synchronization and Consistency

#### US-2.1: Cross-Device Propagation
**As a** Multi-device User
**I want** a file added to my laptop to automatically appear on my smartphone
**So that** I have seamless access to my latest work regardless of which device I am using.

**Acceptance Criteria:**
* Data synchronization occurs automatically without manual refresh.
* Files appear on the secondary device shortly after upload to the primary device completes.

---

#### US-2.2: Bandwidth Efficient Updates (Delta Sync)
**As a** Mobile Data User
**I want** the system to only upload the specific parts of a file that I changed, rather than the whole file
**So that** I minimize my data usage and sync times when editing large documents.

**Acceptance Criteria:**
* Modifying a small section of a large file results in network traffic significantly smaller than the total file size (Delta Sync).
* The server reconstructs the new file version using the existing blocks and the new delta blocks.

---

#### US-2.3: Conflict Resolution
**As a** Collaborator
**I want to** be notified if my edits conflict with another user's edits
**So that** I can choose to either merge the changes or keep my version separate.

**Acceptance Criteria:**
* If a concurrent edit occurs, the system flags a sync conflict.
* The user is presented with the option to keep their local copy or overwrite it with the server version.

---

### Epic 3: Collaboration and History

#### US-3.1: File Revision History
**As a** Document Editor
**I want to** view a list of previous versions of a file and restore an older version
**So that** I can recover data if I make a mistake or accidentally delete important content.

**Acceptance Criteria:**
* A "Version History" view is available for every file.
* Selecting a previous version allows the user to download it or revert the current file to that state.

---

#### US-3.2: Sharing with Peers
**As a** Team Lead
**I want to** share specific files with my coworkers
**So that** they can view or edit the documents required for our joint projects.

**Acceptance Criteria:**
* The user can input a coworker's identifier to grant access.
* The shared file appears in the coworker's file list.

---

#### US-3.3: Real-time Activity Notifications
**As a** Collaborator
**I want to** receive a notification immediately when a shared file is edited or deleted by someone else
**So that** I am aware that the content I am viewing might be outdated.

**Acceptance Criteria:**
* Notifications are received via the notification service (Long Polling) when an event occurs.
* If the user is offline, the notification is queued and delivered upon reconnection (Offline backup queue).

---