# 🎫 Ticket Attachments Module - Cloud Support System

This document explains the design, flow, and considerations for implementing the **ticket attachment feature** in the Cloud Support/Ticketing System.

---

## 📦 Overview
Each support ticket can have **multiple attachments** (max 25MB each).  
Attachments are stored in **cloud storage (AWS S3 / Azure Blob)** for scalability and reliability.  
The system maintains **metadata** in the database and stores the **actual file** in cloud storage.

---

### 🗂 Storage Strategy
- **Cloud Storage**: S3 (AWS) or Blob (Azure)
- **DB Metadata Table**: 

### 🔗 File Access
Files are **retrieved via their stored URL** (`storage_url` field).  
For secure downloads, **signed URLs** should be generated with short expiry times.

---

## 🔒 Security Considerations

### 1. Malicious File Protection
- Restrict disallowed extensions (`.exe`, `.bat`, `.sh`, `.js`, etc.)
- Optionally integrate a **virus/malware scanner** for uploads.
- Sanitize file names before saving.

### 2. Encryption
- All file uploads and downloads must happen over **HTTPS/TLS**.
- Optionally enable **encryption at rest** in S3/Blob storage.

### 3. Access Control
- Only **authorized users** (ticket creator, assignee, watchers) can upload, view, or delete.
- Only the **attachment author** can delete their file.
- Use **role-based permissions** in backend APIs.

---

## ⚙️ Features & Operations

| Operation | Description | Trigger/Access | Notification | Notes |
|------------|--------------|----------------|---------------|--------|
| **Add Attachment (on ticket creation)** | Upload files while creating a new ticket | Ticket creator | No | Saves metadata + file in cloud |
| **Add Attachment (after creation)** | Add file to an existing ticket | Ticket creator / watchers / assignee | Yes → Notify creator + watchers | Supports multiple files |
| **Delete Attachment** | Remove an existing attachment | Author only | Yes → Notify watchers/creator | Deletes from DB + storage |
| **List Attachments (by Ticket)** | Get all attachments for a ticket | All ticket participants | No | Uses `findByTicketId` |
| **List Attachments (by User)** | (Optional) Get all attachments uploaded by a user | Admin / user | No | For analytics or management |
| **List Attachments (by File Type)** | (Optional) Filter attachments by type | Admin only | No | For advanced filtering |
| **Bulk Upload** | Upload multiple files in one request | Any ticket participant | Yes | Validates each file individually |
| **Download Attachment** | Secure download of file | Authorized users | No | Signed URL or direct HTTPS |
| **Preview / Thumbnail** | Generate preview for images or PDFs | UI enhancement | No | Optional feature |
| **Retention Policy** | Automatically delete old files after X days | System cleanup task | Yes | Configurable per project |
| **Audit Trail** | Track who uploaded/deleted each file | All operations | Logged | Stored in audit table or logs |

---

## 🧩 Validations
- Max file size: **25MB**
- Allowed types: `.pdf`, `.png`, `.jpg`, `.txt`, `.csv`, `.zip`, `.docx`, `.xlsx`, etc.
- Rejected types: `.exe`, `.bat`, `.sh`, `.js`, `.jar`, `.msi`, `.cmd`

---

## 🧰 Implementation Components

| Component | Responsibility |
|------------|----------------|
| **TicketAttachmentEntity** | Represents attachment metadata in DB |
| **TicketAttachmentRepository** | Manages CRUD operations on attachments |
| **TicketAttachmentService** | Handles business logic and file upload/download |
| **TicketAttachmentController** | REST endpoints for upload, list, delete, etc. |
| **CloudStorageService** | Handles actual upload/delete to S3 or Azure Blob |

---

## 🔄 Workflow Diagram

---

## 🚨 Future Enhancements
- Virus scanning integration
- Image compression before upload
- File versioning and rollback
- Attachment tagging (e.g., "logs", "screenshots")
- Search attachments by keyword or file name

---

## ✅ Summary
- Cloud storage is **scalable and secure** for attachments.  
- Database stores **metadata** for easy management.  
- Security and permissions are **crucial** to protect data.  
- Notifications keep users **informed of new or deleted files**.  
- Feature is **modular** and can evolve with future needs.

---


