# CHAPTER 15: DESIGN GOOGLE DRIVE

In recent years, cloud storage services such as Google Drive, Dropbox, Microsoft OneDrive, and Apple iCloud have become very popular. In this chapter, you are asked to design Google Drive. Let us take a moment to understand Google Drive before jumping into the design. Google Drive is a file storage and synchronization service that helps you store documents, photos, videos, and other files in the cloud. You can access your files from any computer, smartphone, and tablet. You can easily share those files with friends, family, and coworkers. Figure 15-1 and 15-2 show what Google drive looks like on a browser and mobile application, respectively.

![Google Drive browser interface Figure 15-1](https://github.com/paulocer/DesignGoogleDrive/blob/main/Figure%2015-1.png)

<p align="center">
  <img src="https://github.com/paulocer/DesignGoogleDrive/blob/main/Figure%2015-2.png" alt="Google Drive browser interface Figure 15-2"/>
</p>

---

## Step 1 - Understand the problem and establish design scope

Designing a Google Drive is a big project, so it is important to ask questions to narrow down the scope.

| Role | Question/Answer |
| :--- | :--- |
| Candidate | What are the most important features? |
| Interviewer | Upload and download files, file sync, and notifications. |
| Candidate | Is this a mobile app, a web app, or both? |
| Interviewer | Both. |
| Candidate | What are the supported file formats? |
| Interviewer | Any file type. |
| Candidate | Do files need to be encrypted? |
| Interviewer | Yes, files in the storage must be encrypted. |
| Candidate | Is there a file size limit? |
| Interviewer | Yes, files must be **10 GB or smaller**. |
| Candidate | How many users does the product have? |
| Interviewer | **10M DAU** (Daily Active Users). |

In this chapter, we focus on the following features:
* Add files. The easiest way to add a file is to drag and drop a file into Google Drive.
* Download files.
* Sync files across multiple devices. When a file is added to one device, it is automatically synced to other devices.
* See file revisions.
* Share files with your friends, family, and coworkers.
* Send a notification when a file is edited, deleted, or shared with you.

Features not discussed in this chapter include:
* Google doc editing and collaboration. Google doc allows multiple people to edit the same document simultaneously. This is out of our design scope.

Other than clarifying requirements, it is important to understand **non-functional requirements**:
* **Reliability**. Reliability is extremely important for a storage system. Data loss is unacceptable.
* **Fast sync speed**. If file sync takes too much time, users will become impatient and abandon the product.
* **Bandwidth usage**. If a product takes a lot of unnecessary network bandwidth, users will be unhappy, especially when they are on a mobile data plan.
* **Scalability**. The system should be able to handle high volumes of traffic.
* **High availability**. Users should still be able to use the system when some servers are offline, slowed down, or have unexpected network errors.

### Back of the envelope estimation

* Assume the application has **50 million signed up users** and **10 million DAU**.
* Users get **10 GB free space**.
* Assume users upload **2 files per day**. The average file size is **500 KB**.
* **1:1 read to write ratio**.

* **Total space allocated**: 50 million * 10 GB $= 500$ Petabytes
* **QPS for upload API**: 10 million * 2 uploads / 24 hours / 3600 seconds $\approx 240$
* **Peak QPS** = QPS * 2 = 480

---

## Step 2 - Propose high-level design and get buy-in

Instead of showing the high-level design diagram from the beginning, we will use a slightly different approach. We will start with something simple: build everything in a **single server**. Then, gradually scale it up to support millions of users.

Let us start with a single server setup as listed below:
* A web server to upload and download files.
* A database to keep track of metadata like user data, login info, files info, etc.
* A storage system to store files.

We allocate **1TB** of storage space to store files. A directory called `drive/` is the root directory to store uploaded files. Under the `drive/` directory, there is a list of directories, known as **namespaces**. Each namespace contains all the uploaded files for that user. The filename on the server is kept the same as the original file name. Each file or folder can be uniquely identified by joining the namespace and the relative path.



### APIs

We primarily need 3 APIs: upload a file, download a file, and get file revisions.

**1. Upload a file to Google Drive**
Two types of uploads are supported:
* **Simple upload**: Use this upload type when the file size is small.
* **Resumable upload**: Use this upload type when the file size is large and there is a high chance of network interruption.

An example of a resumable upload API is `https://api.example.com/files/upload?uploadType=resumable`.
A resumable upload is achieved by the following 3 steps:
1. Send the initial request to retrieve the resumable URL.
2. Upload the data and monitor upload state.
3. If upload is disturbed, resume the upload.

**2. Download a file from Google Drive**
Example API: `https://api.example.com/files/download`.

**3. Get file revisions**
Example API: `https://api.example.com/files/list_revisions`.

All the APIs require user **authentication** and use **HTTPS**. Secure Sockets Layer (SSL) protects data transfer between the client and backend servers.

### Move away from single server

As more files are uploaded, eventually the storage space fills up, as shown in Figure 15-4.



Only 10 MB of storage space is left. The first solution is to **shard the data** so it is stored on multiple storage servers. Figure 15-5 shows an example of sharding based on `user_id`.



To worry about potential data losses, many leading companies use **Amazon S3** for storage. Amazon S3 supports **same-region** and **cross-region replication**. Redundant files are stored in multiple regions to guard against data loss and ensure availability. A **bucket** is like a folder in file systems.



After moving files to S3, the following improvements are applied to the single server setup:
* **Load balancer**: Added to distribute network traffic evenly and redistribute traffic if a web server goes down.
* **Web servers (API servers)**: More web servers can be added/removed easily, depending on the traffic load.
* **Metadata database**: Moved out of the server to avoid a single point of failure. Data replication and sharding are set up.
* **File storage**: Amazon S3 is used for file storage, and files are replicated in two separate geographical regions to ensure availability and durability.

The updated design is shown in Figure 15-7.



### Sync conflicts

Sync conflicts happen when two users modify the same file or folder at the same time.
* **Strategy**: The **first version that gets processed wins**, and the version processed later receives a conflict.
* **Resolution**: The system presents both copies of the same file to the user who received the conflict: their local copy and the latest version from the server. That user has the option to merge both files or override one version with the other.





### High-level design

Figure 15-10 illustrates the proposed high-level design.



**Components**

* **User**: Uses the application through a browser or mobile app.
* **Block servers**: Upload blocks to cloud storage. A file is split into blocks, each with a unique hash value, and stored in the metadata database. Each block is treated as an independent object and stored in the storage system (S3). Blocks are joined in a particular order to reconstruct a file.
* **Cloud storage**: Where files, split into smaller blocks, are stored.
* **Cold storage**: A system for storing inactive data (files not accessed for a long time).
* **Load balancer**: Evenly distributes requests among API servers.
* **API servers**: Responsible for user authentication, managing user profile, updating file metadata, etc.—almost everything other than the uploading flow.
* **Metadata database (DB)**: Stores metadata of users, files, blocks, versions, etc. **Files are stored in the cloud**, and the DB only contains metadata.
* **Metadata cache**: Caches some metadata for fast retrieval.
* **Notification service**: A publisher/subscriber system that notifies relevant clients when a file is added/edited/removed elsewhere so they can pull the latest changes.
* **Offline backup queue**: Stores file change info if a client is offline, so changes will be synced when the client is online.

---

## Step 3 - Design deep dive

### Block servers

To minimize network traffic, two optimizations are proposed:
* **Delta sync**: When a file is modified, only **modified blocks** are synced instead of the whole file.
* **Compression**: Applied on blocks to significantly reduce the data size, using algorithms like gzip and bzip2 for text files.

Block servers do the heavy lifting for uploading files. They process files by **splitting** a file into blocks, **compressing** each block, and **encrypting** them. Only modified blocks are transferred to the storage system.

Figure 15-11 shows the process when a new file is added:
1. A file is **split** into smaller blocks.
2. Each block is **compressed**.
3. Each block is **encrypted** for security before being sent to cloud storage.
4. Blocks are uploaded to the cloud storage.



Figure 15-12 illustrates **delta sync**, where only modified blocks (highlighted as "block 2" and "block 5") are transferred to cloud storage.



### High consistency requirement

The system requires **strong consistency** by default. To achieve strong consistency with a metadata cache:
* Data in cache replicas and the master must be consistent.
* **Invalidate caches on database write** to ensure cache and database hold the same value.

Relational databases are chosen because they natively support **ACID** (Atomicity, Consistency, Isolation, Durability) properties, which helps achieve strong consistency.

### Metadata database

Figure 15-13 shows a simplified database schema design.



* **User**: Contains basic user info.
* **Device**: Stores device info, including `push_id` for mobile push notifications. A user can have multiple devices.
* **Workspace**: The root directory of a user (namespace).
* **File**: Stores everything related to the latest file.
* **File_version**: Stores the version history of a file. Existing rows are read-only to keep the integrity of the file revision history.
* **Block**: Stores everything related to a file block. A file of any version can be reconstructed by joining all the blocks in the correct order.

### Upload flow

Figure 15-14 shows the sequence diagram for an upload flow. Two requests are sent in parallel from Client 1: **add file metadata** and **upload the file to cloud storage**.



**Add file metadata**:
1. Client 1 sends a request to add the metadata of the new file.
2. API Servers store the new file metadata in Metadata DB and change the file upload status to "pending".
3. API Servers notify the notification service that a new file is being added.
4. The notification service notifies relevant clients (Client 2) that a file is being uploaded.

**Upload files to cloud storage**:
2.1. Client 1 uploads the content of the file to block servers.
2.2. Block servers chunk, compress, and encrypt the blocks, and upload them to cloud storage.
2.3. Cloud storage triggers an upload completion callback to API servers.
2.4. File status is changed to "uploaded" in Metadata DB.
2.5. API Servers notify the notification service that a file status is changed to "uploaded".
2.6. The notification service notifies relevant clients (Client 2) that a file is fully uploaded.

### Download flow

The download flow is triggered when a file is added or edited elsewhere. A client is informed of changes in two ways:
* **Online**: Notification service informs the client to pull the latest data.
* **Offline**: Data is saved to the offline backup queue; the client pulls the latest changes when it comes back online.

The client first requests metadata via API servers, then downloads blocks to construct the file. Figure 15-15 shows the detailed flow.



1. Notification service informs Client 2 that a file is changed.
2. Client 2 sends a request to fetch metadata.
3. API servers fetch metadata from Metadata DB.
4. Metadata is returned to Client 2.
5. Client 2 sends requests to block servers to download blocks.
6. Block servers download blocks from cloud storage.
7. Cloud storage returns blocks to the block servers.
8. Client 2 downloads all the new blocks to reconstruct the file.

### Notification service

The Notification service informs other clients of any file mutation to reduce conflicts.

The system opts for **Long polling** over WebSocket:
* Communication for the notification service is **not bi-directional** (server sends changes to client, but not vice versa).
* Notifications are sent **infrequently** with no burst of data, unlike a chat app where WebSocket is more suited.

With long polling, each client establishes a long poll connection to the notification service. If changes are detected, the connection closes, and the client must connect to the metadata server to download the latest changes. The client immediately sends a new request after receiving a response or reaching a connection timeout to keep the connection open.

### Save storage space

To reduce storage costs while supporting file version history and reliability, three techniques are proposed:
* **De-duplicate data blocks**: Eliminating redundant blocks at the account level. Two blocks are identical if they have the same hash value.
* **Adopt an intelligent data backup strategy**:
    * **Set a limit**: The oldest version is replaced when a set limit for the number of versions to store is reached.
    * **Keep valuable versions only**: Limit the number of saved versions for frequently edited documents, giving more weight to recent versions.
* **Moving infrequently used data to cold storage**: Cold data (inactive for months or years) is moved to cheaper cold storage like Amazon S3 Glacier.

### Failure Handling

Design strategies are adopted to address failures:
* **Load balancer failure**: A secondary load balancer becomes active. Load balancers monitor each other using a heartbeat.
* **Block server failure**: Other servers pick up unfinished or pending jobs.
* **Cloud storage failure**: S3 buckets are replicated multiple times in different regions; files can be fetched from other regions if unavailable in one.
* **API server failure**: It is a stateless service. The load balancer redirects traffic to other API servers.
* **Metadata cache failure**: Cache servers are replicated. If a node goes down, other nodes can still provide data, and a new server is brought up to replace the failed one.
* **Metadata DB failure**:
    * **Master down**: Promote one of the slaves to act as a new master and bring up a new slave node.
    * **Slave down**: You can use another slave for read operations and bring another database server to replace the failed one.
* **Notification service failure**: All long poll connections are lost, and clients must reconnect to a different server. Reconnecting all lost clients at once is a relatively slow process.
* **Offline backup queue failure**: Queues are replicated. Consumers may need to re-subscribe to the backup queue.

---

## Step 4 - Wrap up

The proposed system design for Google Drive emphasizes **strong consistency, low network bandwidth, and fast sync**. It contains two flows: **manage file metadata** and **file sync**. The **Notification service** uses long polling to keep clients up to date with file changes.

An alternative design choice is to upload files **directly to cloud storage from the client** instead of going through block servers.
* **Advantage**: File upload is faster because the file is transferred once to cloud storage.
* **Drawbacks**:
    1. The chunking, compression, and encryption logic must be implemented on different platforms (iOS, Android, Web), which is error-prone and requires more engineering effort. In the proposed design, this logic is centralized in the block servers.
    2. Implementing encryption logic on the client side is not ideal as a client can be easily hacked or manipulated.

Another evolution is moving online/offline logic to a separate service, called a **presence service**, so the functionality can be easily integrated by other services.

---

## Reference materials

* [1] Google Drive: https://www.google.com/drive/
* [2] Upload file data: https://developers.google.com/drive/api/v2/manage-uploads
* [3] Amazon S3: https://aws.amazon.com/s3
* [4] Differential Synchronization https://neil.fraser.name/writing/sync/
* [5] Differential Synchronization youtube talk https://www.youtube.com/watch?v=S2Hp_1jqpY8
* [6] How We've Scaled Dropbox: https://youtu.be/PE4gwstWhmc
* [7] Tridgell, A., & Mackerras, P. (1996). The rsync algorithm.
* [8] Librsync. (n.d.). Retrieved April 18, 2015, from https://github.com/librsync/librsync
* [9] ACID: https://en.wikipedia.org/wiki/ACID
* [10] Dropbox security white paper: https://www.dropbox.com/static/business/resources/Security Whitepaper.pdf
* [11] Amazon S3 Glacier: https://aws.amazon.com/glacier/faqs/

---
