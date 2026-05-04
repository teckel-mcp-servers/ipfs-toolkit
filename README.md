# ipfs-toolkit
Interplanetary File System (IPFS) MCP toolkit

# Teckel IPFS API Reference

This document describes all RESTful API endpoints for managing IPFS content via the teckel platform. All methods support authentication via API key and are available as MCP tools for voice agents and automated workflows.

## Authentication

All endpoints require authentication via a teckel API key. The API key must be provided in the HTTP Authorization header using Bearer token authentication:

```
Authorization: Bearer YOUR_API_KEY
```

## Base URL

```
https://mcp-servers.bh.tkllabs.io:9780
```

---

## 1. Get IPFS Content for Account

**Endpoint**: `/get_ipfs_content_for_apikey`  
**HTTP Method**: `POST`  
**MCP Tool**: `get_my_ipfs_content`  

**Description**: Retrieve a list of all IPFS files in your account with optional filtering by search string, pinned state, encrypted state, and content type.

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `search_string` | string | (empty) | Search for this string in the file nickname. Leave blank to return all files. |
| `pinned_state` | integer | 0 | Filter by pinned status: 0 = all files, 1 = only pinned files, 2 = only unpinned files |
| `encrypted_state` | integer | 0 | Filter by encryption status: 0 = all files, 1 = only encrypted files, 2 = only unencrypted files |
| `content_type` | string | (empty) | Filter by content type (e.g., "image", "audio", "video", "document"). Leave blank to ignore. |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/get_ipfs_content_for_apikey?search_string=vacation&pinned_state=1&encrypted_state=0&content_type=image" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Response

```json
{
  "result": [
    {
      "cid": "QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3",
      "nickname": "vacation-photo-001",
      "iqyu_type": "GENERAL",
      "is_encrypted": 0,
      "wallet_address": "0x8f29aced081d2bfd695a85ababf944c2d28ffb4a",
      "filename": "photo.png",
      "filesize_GB": 0.001612847,
      "content_type": "image/png",
      "create_date": "Sat, 29 Nov 2025 12:51:28 GMT",
      "is_pinned": 1
    }
  ]
}
```

---

## 2. Remove IPFS Files from Account

**Endpoint**: `/remove_ipfs_files_from_account`  
**HTTP Method**: `POST`  
**MCP Tool**: `remove_my_ipfs_content`  

**Description**: Unpin and permanently remove IPFS files from your account matching specified filters. Files removed from the teckel node may still exist on the decentralized IPFS network if pinned elsewhere.

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `search_string` | string | (empty) | Search for this string in the file nickname to filter removal candidates. Leave blank to remove all files. |
| `pinned_state` | integer | 0 | Filter by pinned status: 0 = all files, 1 = only pinned files, 2 = only unpinned files |
| `encrypted_state` | integer | 0 | Filter by encryption status: 0 = all files, 1 = only encrypted files, 2 = only unencrypted files |
| `content_type` | string | (empty) | Filter by content type (e.g., "image", "audio"). Leave blank to ignore. |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/remove_ipfs_files_from_account?search_string=old&pinned_state=2" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Response

```json
{
  "message": "Successfully removed 3 IPFS files from your account",
  "files_removed": 3
}
```

---

## 3. Pin IPFS Files

**Endpoint**: `/pin_ipfs_files_for_apikey`  
**HTTP Method**: `POST`  
**MCP Tool**: `pin_ipfs_cid`  

**Description**: Pin IPFS files in your account matching specified filters. Pinning prevents automatic deletion of files from the teckel IPFS node.

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `search_string` | string | (empty) | Search for this string in file nicknames to filter which files to pin. Leave blank to pin all files. |
| `pinned_state` | integer | 0 | Filter by current pinned status: 0 = all files, 1 = only already pinned files, 2 = only unpinned files |
| `encrypted_state` | integer | 0 | Filter by encryption status: 0 = all files, 1 = only encrypted files, 2 = only unencrypted files |
| `content_type` | string | (empty) | Filter by content type (e.g., "image", "video"). Leave blank to ignore. |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/pin_ipfs_files_for_apikey?search_string=important&pinned_state=2" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Response

```json
{
  "message": "Successfully pinned 2 IPFS files",
  "files_pinned": 2
}
```

---

## 4. Unpin IPFS Files

**Endpoint**: `/unpin_ipfs_files_for_apikey`  
**HTTP Method**: `POST`  
**MCP Tool**: `unpin_ipfs_cid`  

**Description**: Unpin IPFS files in your account matching specified filters. Unpinning allows these files to be automatically garbage collected from the teckel IPFS node.

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `search_string` | string | (empty) | Search for this string in file nicknames to filter which files to unpin. Leave blank to unpin all files. |
| `pinned_state` | integer | 0 | Filter by current pinned status: 0 = all files, 1 = only pinned files, 2 = only unpinned files |
| `encrypted_state` | integer | 0 | Filter by encryption status: 0 = all files, 1 = only encrypted files, 2 = only unencrypted files |
| `content_type` | string | (empty) | Filter by content type (e.g., "image", "audio"). Leave blank to ignore. |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/unpin_ipfs_files_for_apikey?search_string=temp&encrypted_state=1" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Response

```json
{
  "message": "Successfully unpinned 1 IPFS file",
  "files_unpinned": 1
}
```

---

## 5. Pin Individual IPFS File by CID

**Endpoint**: `/pin_ipfs_cid_for_apikey`  
**HTTP Method**: `POST`  
**MCP Tool**: `pin_ipfs_cid`  

**Description**: Pin a specific IPFS file identified by its content identifier (CID).

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `cid` | string | (required) | IPFS content identifier of the file to pin |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/pin_ipfs_cid_for_apikey?cid=QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Response

```json
{
  "message": "Successfully pinned file",
  "cid": "QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3"
}
```

---

## 6. Remove Individual IPFS File by CID

**Endpoint**: `/remove_ipfs_cid_from_account`  
**HTTP Method**: `POST`  
**MCP Tool**: `remove_ipfs_cid_from_account`  

**Description**: Unpin and remove a specific IPFS file identified by its content identifier (CID) from your account.

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `cid` | string | (required) | IPFS content identifier of the file to remove |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/remove_ipfs_cid_from_account?cid=QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Response

```json
{
  "message": "Successfully removed IPFS file from account",
  "cid": "QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3"
}
```

---

## 7. Publish IPFS File to Web Server

**Endpoint**: `/publish_ipfs_file_to_webserver`  
**HTTP Method**: `POST`  
**MCP Tool**: `publish_ipfs_file_to_webserver`  

**Description**: Retrieve an IPFS file from the teckel IPFS node and publish it on the teckel web server. If the file is encrypted, it is automatically decrypted. Returns a browseable URL for public access.

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `cid` | string | (required) | IPFS content identifier of the file to publish |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/publish_ipfs_file_to_webserver?cid=QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Response

```json
{
  "message": "File published to web server",
  "cid": "QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3",
  "web_url": "https://teckel.web-server.io/files/QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3"
}
```

---

## 8. Upload File to IPFS

**Endpoint**: `/upload_sender_file_to_ipfs_for_apikey`  
**HTTP Method**: `POST`  
**MCP Tool**: `upload_file_to_ipfs`  

**Description**: Upload a file to the teckel IPFS node. The file is validated for malware and content acceptability before upload. Supports optional encryption.

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `file` | file | (required) | The file to upload (multipart form data) |
| `nicknameOnIPFS` | string | (auto-generated) | Nickname for the file in IPFS inventory. If blank, a unique name is automatically generated. |
| `doEncryptOnIPFS` | string | "false" | Whether to encrypt the file before uploading: "true" or "false" |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/upload_sender_file_to_ipfs_for_apikey?nicknameOnIPFS=my_document&doEncryptOnIPFS=true" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -F "file=@/path/to/document.pdf"
```

### Response

```json
{
  "hash": "QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3",
  "message": "Successfully uploaded file to IPFS",
  "filename": "document.pdf",
  "encrypted": true
}
```

---

## 9. Upload Base64-Encoded File to IPFS

**Endpoint**: `/upload_sender_base64_to_ipfs_for_apikey`  
**HTTP Method**: `POST`  
**MCP Tool**: `upload_base64_file_to_ipfs`  

**Description**: Upload a base64-encoded file to the teckel IPFS node. Useful for uploading files from applications that don't support multipart form uploads. File type is automatically detected from the binary data. Supports optional encryption.

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `base64_data` | string | (required) | Base64-encoded file data |
| `nicknameOnIPFS` | string | (auto-generated) | Nickname for the file in IPFS inventory. If blank, a unique name is automatically generated. |
| `doEncryptOnIPFS` | string | "false" | Whether to encrypt the file before uploading: "true" or "false" |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/upload_sender_base64_to_ipfs_for_apikey?base64_data=iVBORw0KGgoAAAANSUhEUgAAAAUA...&nicknameOnIPFS=my_image&doEncryptOnIPFS=false" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Response

```json
{
  "hash": "QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3",
  "message": "Successfully uploaded file to IPFS",
  "filename": "file.png",
  "encrypted": false
}
```

---

## 10. Retrieve IPFS File

**Endpoint**: `/retrieve_ipfs_file_for_apikey`  
**HTTP Method**: `POST`  
**MCP Tool**: `retrieve_ipfs_file`  

**Description**: Retrieve the contents of an IPFS file from your account. If the file is encrypted, it is automatically decrypted before return. Only files originally uploaded via teckel are supported.

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `cid` | string | (required) | IPFS content identifier of the file to retrieve |

### CURL Example

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/retrieve_ipfs_file_for_apikey?cid=QmbKz8vXPDmWtD7tszmETuDqSfxpYae5jPuXJRwDLFP7d3" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  --output downloaded_file.pdf
```

### Response

The file contents are returned directly. Use `--output` flag to save to a file.

---

## Error Handling

All endpoints return standard HTTP status codes:

| Status Code | Meaning |
|-------------|---------|
| 200 | Success |
| 400 | Bad request (invalid parameters) |
| 403 | Forbidden (invalid or missing API key) |
| 404 | Not found (CID or file does not exist) |
| 422 | Unprocessable entity (validation failed, e.g., malware/content check) |
| 500 | Internal server error |

### Error Response Example

```json
{
  "message": "Missing or invalid APIKEY.",
  "status_code": 403
}
```

---

## File Validation

When uploading files, the following automatic checks are performed:

1. **Malware Scanning**: Files are scanned using VirusTotal API (if enabled)
2. **Content Acceptability**: Images are scanned using Google Safe Images, videos using Google Safe Videos
3. **File Type Detection**: File type is detected from magic bytes/signatures

Files failing these checks will be rejected with HTTP 422.

---

## Billing

All file operations are subject to billing based on file size and operations performed:

- **Upload**: Charged per GB based on file size
- **Storage**: Monthly charges for pinned content
- **Bandwidth**: Retrieval and publishing operations are charged separately
- **Scanning**: Malware and content acceptability checks incur separate charges

Billing is automatically applied to your teckel account and deducted from your teckel credits.

---

## Authentication for MCP Clients

For MCP tool integration with voice agents and n8n workflows, include the API key in the HTTP Authorization header:

```
Authorization: Bearer YOUR_API_KEY
```
