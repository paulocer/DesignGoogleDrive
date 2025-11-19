# CHAPTER 15: DESIGN GOOGLE DRIVE

In recent years, cloud storage services such as **Google Drive, Dropbox, Microsoft OneDrive, and Apple iCloud** have become very popular .
In this chapter, you are asked to design Google Drive .
Let us take a moment to understand Google Drive before jumping into the design .
Google Drive is a **file storage and synchronization service** that helps you store documents, photos, videos, and other files in the cloud .
You can access your files from any computer, smartphone, and tablet .
[cite_start]You can easily share those files with friends, family, and coworkers[cite: 1, 7].
Figure 15-1 and 15-2 show what Google drive looks like on a browser and mobile application, respectively .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-1.png" alt="Figure 15-1: Google Drive browser interface"/>
</p>

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-2.png" alt="Figure 15-2: Google Drive mobile application interface"/>
</p>

## Step 1 - Understand the problem and establish design scope

Designing a Google drive is a big project, so it is important to ask questions to narrow down the scope .

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
| Interviewer | Yes, files must be **10 GB or smaller** . |
| Candidate | How many users does the product have? |
| Interviewer | **10M DAU** . |

In this chapter, we focus on the following features :
* **Add files**. The easiest way to add a file is to drag and drop a file into Google drive .
* **Download files** .
* **Sync files across multiple devices**. When a file is added to one device, it is automatically synced to other devices .
* **See file revisions** .
* **Share files** with your friends, family, and coworkers .
* **Send a notification** when a file is edited, deleted, or shared with you .

Features not discussed in this chapter include :
* **Google doc editing and collaboration**. Google doc allows multiple people to edit the same document simultaneously . This is out of our design scope .

Other than clarifying requirements, it is important to understand **non-functional requirements** :
* **Reliability**. Reliability is extremely important for a storage system. Data loss is unacceptable .
* **Fast sync speed**. If file sync takes too much time, users will become impatient and abandon the product .
* **Bandwidth usage**. If a product takes a lot of unnecessary network bandwidth, users will be unhappy, especially when they are on a mobile data plan .
* **Scalability**. The system should be able to handle high volumes of traffic .
* **High availability**. Users should still be able to use the system when some servers are offline, slowed down, or have unexpected network errors .

### Back of the envelope estimation

* Assume the application has **50 million signed up users** and **10 million DAU** .
* Users get **10 GB free space** .
* Assume users upload **2 files per day**. The average file size is **500 KB** .
* **1:1 read to write ratio** .

* **Total space allocated**: 50 million * 10 GB $=500$ Petabyte 
* **QPS for upload API**: 10 million * 2 uploads / 24 hours / 3600 seconds $=\sim240$ 
* **Peak QPS** = QPS * 2 = 480 

---

## Step 2 - Propose high-level design and get buy-in

Instead of showing the high-level design diagram from the beginning, we will use a slightly different approach .
We will start with something simple: build everything in a **single server** .
Then, gradually scale it up to support millions of users .
Let us start with a single server setup as listed below :
* A **web server** to upload and download files .
* A **database** to keep track of metadata like user data, login info, files info, etc .
* A **storage system** to store files .

We allocate **1TB** of storage space to store files .
A directory called `/drive` is the root directory to store uploaded files .
Under `/drive` directory, there is a list of directories, known as **namespaces** .
Each namespace contains all the uploaded files for that user .
The filename on the server is kept the same as the original file name .
Each file or folder can be uniquely identified by joining the namespace and the relative path .
Figure 15-3 shows an example of how the `/drive` directory looks like on the left side and its expanded view on the right side .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-3.png" alt="Figure 15-3: Directory structure for /drive showing user namespaces"/>
</p>

### APIS

We primary need 3 APIs: **upload a file, download a file, and get file revisions** .

**1. Upload a file to Google Drive** 
Two types of uploads are supported :
* **Simple upload**: Use this upload type when the file size is small .
* **Resumable upload**: Use this upload type when the file size is large and there is a high chance of network interruption .

An example of resumable upload API is: `https://api.example.com/files/upload?uploadType=resumable` .
[cite_start]A resumable upload is achieved by the following 3 steps[cite: 2, 144]:
* Send the initial request to retrieve the resumable URL .
* Upload the data and monitor upload state .
* If upload is disturbed, resume the upload .

**2. Download a file from Google Drive** 
Example API: `https://api.example.com/files/download` .

**3. Get file revisions** 
Example API: `https://api.example.com/files/list_revisions` .

All the APIs require **user authentication** and use **HTTPS** .
Secure Sockets Layer (SSL) protects data transfer between the client and backend servers .

### Move away from single server

As more files are uploaded, eventually you get the space full alert as shown in Figure 15-4 .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-4.png" alt="Figure 15-4: Hard drive icon showing storage nearly full"/>
</p>

Only **10 MB of storage space is left** !
The first solution comes to mind is to **shard the data**, so it is stored on multiple storage servers .
Figure 15-5 shows an example of sharding based on `user_id` .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-5.png" alt="Figure 15-5: Diagram showing data sharding based on user_id % 4"/>
</p>

Although sharding fixes the immediate capacity problem, you are still worried about potential data losses in case of storage server outage .
Many leading companies use **Amazon S3** for storage .
[cite_start]Amazon S3 is an object storage service that offers industry-leading scalability, data availability, security, and performance[cite: 3, 184].
Amazon S3 supports **same-region and cross-region replication** .
Redundant files are stored in multiple regions to guard against data loss and ensure availability .
A **bucket** is like a folder in file systems .
Figure 15-6 shows same-region and cross-region replication .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-6.png" alt="Figure 15-6: Diagram showing S3 same-region and cross-region replication"/>
</p>

Here are a few areas you find to improve the single server setup :
* **Load balancer**: Add a load balancer to distribute network traffic . A load balancer ensures evenly distributed traffic, and if a web server goes down, it will redistribute the traffic .
* **Web servers**: After a load balancer is added, more web servers (API servers) can be added/removed easily, depending on the traffic load .
* **Metadata database**: Move the database out of the server to avoid single point of failure . Set up data replication and sharding to meet the availability and scalability requirements .
* **File storage**: Amazon S3 is used for file storage . Files are replicated in two separate geographical regions to ensure availability and durability .

The updated design is shown in Figure 15-7 .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-7.png" alt="Figure 15-7: High-level system diagram with Load Balancer, API Servers, Metadata DB, and File Storage"/>
</p>

### Sync conflicts

For a large storage system like Google Drive, sync conflicts happen from time to time .
A conflict happens when two users modify the same file or folder at the same time .
The **strategy** is: **the first version that gets processed wins**, and the version that gets processed later receives a conflict .
Figure 15-8 shows an example of a sync conflict .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-8.png" alt="Figure 15-8: Three-stage diagram showing User 1's update synced and User 2's update conflicting"/>
</p>

In Figure 15-8, user 1 and user 2 tries to update the same file at the same time, but user 1's file is processed by our system first. User 1's update operation goes through, but, user 2 gets a sync conflict. How can we resolve the conflict for user 2? Our system presents both copies of the same file: user 2's local copy and the latest version from the server (Figure 15-9). User 2 has the option to merge both files or override one version with the other.

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-9.png" alt="Figure 15-9: Three-stage diagram showing User 1's update synced and User 2's update conflicting"/>
</p>

While multiple users are editing the same document at the same, it is challenging to keep the document synchronized. Interested readers should refer to the reference material [4] [5].

### High-level design

Figure 15-10 illustrates the proposed high-level design .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-10.png" alt="Figure 15-10: Detailed high-level system design diagram"/>
</p>

**Components**
* **User**: A user uses the application either through a browser or mobile app .
* **Block servers**: Block servers upload blocks to cloud storage . A file can be split into several blocks, each with a unique hash value, stored in our metadata database . Each block is treated as an independent object and stored in the storage system (S3) . To reconstruct a file, blocks are joined in a particular order .
* **Cloud storage**: A file is split into smaller blocks and stored in cloud storage .
* **Cold storage**: Cold storage is a computer system designed for storing inactive data, meaning files are not accessed for a long time .
* **Load balancer**: A load balancer evenly distributes requests among API servers .
* **API servers**: These are responsible for almost everything other than the uploading flow . API servers are used for user authentication, managing user profile, updating file metadata, etc .
* **Metadata database (DB)**: It stores metadata of users, files, blocks, versions, etc . Files are stored in the cloud, and the metadata database only contains metadata .
* **Metadata cache**: Some of the metadata are cached for fast retrieval .
* **Notification service**: It is a publisher/subscriber system that notifies relevant clients when a file is added/edited/removed elsewhere so they can pull the latest changes .
* **Offline backup queue**: If a client is offline and cannot pull the latest file changes, the offline backup queue stores the info so changes will be synced when the client is online .

---

## Step 3 - Design deep dive

In this section, we will take a close look at the following: block servers, metadata database, upload flow, download flow, notification service, save storage space and failure handling .

### Block servers

Two optimizations are proposed to minimize the amount of network traffic being transmitted :
* [cite_start]**Delta sync**: When a file is modified, only **modified blocks** are synced instead of the whole file[cite: 7, 8, 291].
* **Compression**: Applying compression on blocks can significantly reduce the data size .

Block servers do the heavy lifting work for uploading files .
Block servers process files by **splitting** a file into blocks, **compressing** each block, and **encrypting** them .
Instead of uploading the whole file, **only modified blocks are transferred** to the storage system .
Figure 15-11 shows how a block server works when a new file is added :
* A file is **split** into smaller blocks .
* Each block is **compressed** .
* To ensure security, each block is **encrypted** before it is sent to cloud storage .
* Blocks are uploaded to the cloud storage .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-11.png" alt="Figure 15-11: Flowchart showing file split, compress, encrypt, and upload"/>
</p>

Figure 15-12 illustrates **delta sync**, meaning only modified blocks (like "block 2" and "block 5") are transferred to cloud storage .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-12.png" alt="Figure 15-12: Diagram showing delta sync where only changed blocks are uploaded"/>
</p>

Block servers allow us to save network traffic by providing delta sync and compression .

### High consistency requirement

Our system requires **strong consistency** by default . It is unacceptable for a file to be shown differently by different clients at the same time .
To achieve strong consistency, we must ensure the following :
* Data in cache replicas and the master is consistent .
* **Invalidate caches on database write** to ensure cache and database hold the same value .

[cite_start]Relational databases are chosen because they natively support the **ACID** (Atomicity, Consistency, Isolation, Durability) properties[cite: 9, 347, 349].

### Metadata database

Figure 15-13 shows the simplified database schema design .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-13.png" alt="Figure 15-13: Database schema design showing tables: user, device, workspace, file, file_version, and block"/>
</p>

* **User**: Contains basic information about the user .
* **Device**: Stores device info, including `Push_id` for mobile push notifications . A user can have multiple devices .
* **Namespace (Workspace)**: A namespace is the root directory of a user .
* **File**: Stores everything related to the latest file .
* **File_version**: Stores version history of a file . Existing rows are read-only to keep the integrity of the file revision history .
* **Block**: Stores everything related to a file block . A file of any version can be reconstructed by joining all the blocks in the correct order .

### Upload flow

The sequence diagram for the upload flow is shown in Figure 15-14 .
Two requests are sent in parallel: **add file metadata** and **upload the file to cloud storage** . Both requests originate from Client 1 .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-14.png" alt="Figure 15-14: Sequence diagram for file upload flow"/>
</p>

**Add file metadata** :
1.  Client 1 sends a request to add the metadata of the new file .
2.  API servers store the new file metadata in Metadata DB and change the file upload status to "**pending**" .
3.  API servers notify the notification service that a new file is being added .
4.  The notification service notifies relevant clients (Client 2) that a file is being uploaded .

**Upload files to cloud storage** :
2.1. Client 1 uploads the content of the file to block servers .
2.2. Block servers chunk, compress, encrypt the blocks, and upload them to cloud storage .
2.3. Cloud storage triggers an upload completion callback, which is sent to API servers .
2.4. File status is changed to "**uploaded**" in Metadata DB .
2.5. API servers notify the notification service that the file status is changed to "uploaded" .
2.6. The notification service notifies relevant clients (Client 2) that a file is fully uploaded .

### Download flow

Download flow is triggered when a file is added or edited elsewhere .
There are two ways a client can know about changes :
* **Online**: If Client A is online, the notification service will inform Client A that changes were made, so it needs to pull the latest data .
* **Offline**: If Client A is offline, data will be saved to the cache (offline backup queue) . When the offline client is online again, it pulls the latest changes .

Once a client knows a file is changed, it first requests **metadata** via API servers, then downloads **blocks** to construct the file .
Figure 15-15 shows the detailed flow .

<p align="center">
  <img src="https://github.com/tecnico-softarch/template/blob/main/img/Figure%2015-15.png" alt="Figure 15-15: Sequence diagram for file download flow"/>
</p>

1.  Notification service informs Client 2 that a file is changed somewhere else .
2.  Client 2 sends a request to fetch metadata .
3.  API servers call Metadata DB to fetch metadata of the changes .
4.  Metadata is returned to the API servers .
5.  Client 2 gets the metadata .
6.  Client 2 sends requests to block servers to download blocks .
7.  Block servers first download blocks from cloud storage .
8.  Cloud storage returns blocks to the block servers .
9.  Client 2 downloads all the new blocks to reconstruct the file .

### Notification service

Notification service is built to inform other clients of any file mutation to reduce conflicts .
The system opts for **Long polling** over WebSocket for two reasons:
* Communication for notification service is **not bi-directional** . The server sends information about file changes to the client, but not vice versa .
* Notifications are sent **infrequently with no burst of data** . WebSocket is suited for real-time bi-directional communication, such as a chat app .

With long polling, each client establishes a long poll connection to the notification service .
If changes are detected, the client closes the long poll connection .
The client then connects to the metadata server to download the latest changes .
A client immediately sends a new request after receiving a response or reaching a connection timeout to keep the connection open .

### Save storage space

To support file version history and ensure reliability, multiple versions of the same file are stored .
Three techniques are proposed to reduce storage costs :
1.  **De-duplicate data blocks** : Eliminating redundant blocks at the account level . Two blocks are identical if they have the same hash value .
2.  **Adopt an intelligent data backup strategy** :
    * **Set a limit**: Set a limit for the number of versions to store; the oldest version is replaced when the limit is reached .
    * **Keep valuable versions only**: Limit the number of saved versions for frequently edited documents, giving more weight to recent versions .
3.  [cite_start]**Moving infrequently used data to cold storage** : Cold data (not active for months or years) is moved to cheaper cold storage like Amazon S3 Glacier[cite: 11, 525, 526].

### Failure Handling

Design strategies must be adopted to address failures in a large-scale system .

* **Load balancer failure**: If a load balancer fails, the secondary becomes active and picks up the traffic . They monitor each other using a heartbeat .
* **Block server failure**: Other servers pick up unfinished or pending jobs .
* **Cloud storage failure**: S3 buckets are replicated multiple times in different regions . Files can be fetched from different regions if they are not available in one .
* **API server failure**: It is a stateless service . If an API server fails, the traffic is redirected to other API servers by a load balancer .
* **Metadata cache failure**: Cache servers are replicated . If one node goes down, other nodes can still provide data, and a new server is brought up to replace the failed one .
* **Metadata DB failure** :
    * **Master down**: Promote one of the slaves to act as a new master and bring up a new slave node .
    * **Slave down**: Use another slave for read operations and bring another database server to replace the failed one .
* **Notification service failure**: If a server goes down, all the long poll connections are lost, so clients must reconnect to a different server . Reconnecting with all the lost clients is a relatively slow process .
* **Offline backup queue failure**: Queues are replicated multiple times . Consumers of the queue may need to re-subscribe to the backup queue if one fails .

---

## Step 4 - Wrap up

This chapter proposed a system design to support Google Drive .
The combination of **strong consistency, low network bandwidth, and fast sync** make the design interesting .
Our design contains two flows: **manage file metadata and file sync** .
The **Notification service** is an important component that uses **long polling** to keep clients up to date with file changes .

An alternative design choice is to upload files **directly to cloud storage from the client** instead of going through block servers .
**The advantage** is that it makes file upload faster because a file only needs to be transferred once to the cloud storage .
**The drawbacks** are:
* The same chunking, compression, and encryption logic must be implemented on different platforms (iOS, Android, Web) . This is error-prone and requires a lot of engineering effort, whereas our design centralizes this logic in block servers .
* Implementing encrypting logic on the client side is not ideal, as a client can easily be hacked or manipulated.

Another evolution is moving online/offline logic to a separate service, called a **presence service** . This allows the online/offline functionality to be easily integrated by other services .

---

## Reference materials

[1] Google Drive: https://www.google.com/drive/

[2] Upload file data: https://developers.google.com/drive/api/v2/manage-uploads

[3] Amazon S3: https://aws.amazon.com/s3

[4] Differential Synchronization https://neil.fraser.name/writing/sync/

[5] Differential Synchronization youtube talk https://www.youtube.com/watch?v=S2Hp_1jqpY8

[6] How We've Scaled Dropbox: https://youtu.be/PE4gwstWhmc

[7] Tridgell, A., & Mackerras, P. (1996). The rsync algorithm.

[8] Librsync. (n.d.). Retrieved April 18, 2015, from https://github.com/librsync/librsync

[9] ACID: https://en.wikipedia.org/wiki/ACID

[10] Dropbox security white paper: https://www.dropbox.com/static/business/resources/Security Whitepaper.pdf

[11] Amazon S3 Glacier: https://aws.amazon.com/glacier/faqs/ 

---
