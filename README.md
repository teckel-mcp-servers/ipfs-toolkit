# ipfs-toolkit
Interplanetary File System (IPFS) MCP toolkit

## How to Use the teckel Toolkits

### First download and install the teckel App.
[<img width="120" height="40" alt="Download_on_the_App_Store_Badge_US-UK_RGB_blk_092917" src="https://github.com/user-attachments/assets/ecbe6c7a-02c9-4212-b2ab-58dd90c91bca" />](https://apps.apple.com/gb/app/teckel/id6746805799)
[<img width="135" height="45" alt="GetItOnGooglePlay_Badge_Web_color_English" src="https://github.com/user-attachments/assets/9f9b8443-d759-41c1-9fce-399dc690b439" />](https://play.google.com/store/apps/details?id=io.teckel.app)

### Next generate an API key using the teckel App.

Do the following:

1. Navigate to the Accounts page by tapping on the wallet icon in the upper right corner of the App home screen. Access the API Key Manager for the created or imported Ethereum Account by tapping “Manage API Key” on the Accounts page.

2. Use the API key when making calls to the endpoints. All the available endpoints are available via the teckel App, incorporating the actual API key for the given Ethereum Account.

3. For illustrative purposes, we will use the following fake key d1e12345-c234-45a6-9b76-1234567891ff in the examples presented below. You would substitute your actual API key in place of this fake key.

### Next, decide if you are using the MCP servers or RESTful (API) to access the tools.

# Using the MCP Servers
## Configure your MCP client-of-choice
Whatever the client, the configuration is essentially the same. Namely, provide the client with the MCP server endpoint and access credentials. These are typically in the form of a configuration JSON snippet, using your API key as the “Bearer” token in the “Authorization” tag. For example, here is the precise configuration for use with the Cursor desktop app (navigate within the Cursor app to the Cursor Settings -> Tools & MCP -> + New MCP server and enter these details using your actual teckel API key to replace this fake one).
### MCP JSON configuration (example for use with Cursor)
```
{
 "mcpServers": {    
    "teckel-ipfs-toolkit": {
      "url": "https://mcp-servers.bh.tkllabs.io:9780/ipfs-mcp",
      "headers": {
        "Authorization": "Bearer d1e12345-c234-45a6-9b76-1234567891ff"
      }
    }   
  }
}
```
NOTE: This configuration assumes the HTTP(streamable) protocol. If your client requires the older (now legacy) SSE protocol, replace “ipfs-mcp” with “ipfs-sse”.

# Using the RESTful API Servers
The benefit of using the MCP servers is that the connected LLMs can parse the natural language in the user prompts, populate the required parameters for calling the toolbox API functions, then likewise unravel the returned JSON structure into natural language for presentation to the user. However, if you wish to utilise the teckel toolkits at a lower level (e.g., in your own app), you can call the functions directly using the RESTful (POST) API protocols. You are then responsible for populating the input parameters, and handling the returned JSON output.

## Pricing
Pricing (via _teckel credits_ purchased within the teckel App) for usage of tecekl IPFS services can be found at https://teckel.io/pricing/

## Rate Limiting
All MCP/API calls are subject to rate-limiting per API key. The thresholds are not published, but if a given API key has exceeded the rate limit, a 429 error code will be returned on the given call.

## Errors
If the MCP/API call fails due to user error resulting in a 4xx error code, the user is charged one Base Call Fee (see https://teckel.io/ai-mcp-services/). If the API call fails due to server error resulting in a 5xx error code, the user is not charged. If the given call is part of a cascade of nested calls, the above rules apply to the “outer” call: In other words, if the outer call fails with a 4xx error code, the user is charged one Base Call Fee (for the failed outer call), and there will be no charge for any of the cascaded calls (whether they succeeded or not). Likewise, if the outer call fails with a 5xx error code, the user is not charged at all (even if the cascaded calls succeed).

## Encryption
IPFS is a public service and all content is in principle available to anyone. Therefore if you deem your content to be private, you should make sure it is encrypted before uploading to IPFS. Teckel faciliates such encryption using your teckel API key to construct the encryption key. However, this does not provide end-to-end encryption of your content because your API key is known to the teckel servers. Instead, if you require end-to-end encryption, whereby only you (and not the teckel servers etc) can ever retreive your content, you must first encrypt your files offline using your own chosen method, before uploading to IPFS via teckel.

## Content moderation
To play our part in keeping the internet clean, teckel pre-scans all uploaded content for malware and inappropriate content (as per the teckel terms). Any content failing the pre-scan is rejected. The ethereum wallet address associated with the API key used for the attempted upload may be blacklisted at teckel's sole discretion.
 
## API Key Security
Should you have concerns that your API key has been compromised (and someone else may be consuming your teckel credits by using your key), simply replace the key via the API Key Manager → Replace API key in the teckel App. The old key will then be immediately disabled. IMPORTANT: if you change your API key, any IPFS content previously encrypted via teckel will no longer be retrievable since the API key is used for encryption. Before you change your API key, make sure you have retrieved (and unencrypted) your previous IPFS data and stored the unencrypted versions offline.


# teckel MCP Server — IPFS API Reference

**Base URL:** `https://mcp-servers.bh.tkllabs.io:9780`

---

## Authentication

All endpoints require a Bearer token in the `Authorization` header:

```
Authorization: Bearer YOUR_API_KEY
```

---

## Endpoint Index

| Method | Path | MCP Tool Name | Description |
|--------|------|---------------|-------------|
| POST | `/get_ipfs_content_for_apikey` | `list_ipfs_files` | List IPFS files for account |
| POST | `/remove_ipfs_files_from_account` | `remove_ipfs_files` | Remove multiple IPFS files by nickname search |
| POST | `/pin_ipfs_files_for_apikey` | `pin_ipfs_files` | Pin multiple IPFS files by nickname search |
| POST | `/unpin_ipfs_files_for_apikey` | `unpin_ipfs_files` | Unpin multiple IPFS files by nickname search |
| POST | `/pin_ipfs_cid_for_apikey` | `pin_ipfs_cid` | Pin a single IPFS file by CID |
| POST | `/remove_ipfs_cid_from_account` | `remove_ipfs_cid_from_account` | Remove a single IPFS file by CID |
| POST | `/publish_ipfs_file_to_webserver` | `publish_ipfs_file_to_webserver` | Publish an IPFS file to the teckel web server |
| POST | `/upload_sender_file_to_ipfs_for_apikey` | `upload_file_to_ipfs` | Upload a file to IPFS |
| POST | `/upload_sender_base64_to_ipfs_for_apikey` | `upload_base64_file_to_ipfs` | Upload base64-encoded file to IPFS |
| POST | `/retrieve_ipfs_file_for_apikey` | `retrieve_ipfs_file` | Retrieve an IPFS file by CID |

---

## Endpoints

---

### POST `/get_ipfs_content_for_apikey`

List IPFS files for the account associated with the API key, with optional filtering by nickname, pinned status, encrypted state, and content type.

**MCP tool:** `list_ipfs_files` — List IPFS files (selected on nickname via fuzzy search_string) for the account corresponding to the teckel API key used in the call.

**Parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `search_string` | string | No | `""` | Search for this string in the file nickname. Leave blank to return all. |
| `pinned_state` | integer | No | `0` | `0` = all entries; `1` = pinned only; `2` = unpinned only |
| `encrypted_state` | integer | No | `0` | `0` = all entries; `1` = encrypted only; `2` = unencrypted only |
| `content_type` | string | No | `""` | Filter by content type, e.g. `image`, `audio`, `video`. Leave blank to ignore. |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/get_ipfs_content_for_apikey?search_string=my_video&pinned_state=0&encrypted_state=0&content_type=video" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

---

### POST `/remove_ipfs_files_from_account`

Unpin and remove IPFS files (selected by fuzzy nickname search) from the teckel IPFS node and account inventory.

> **Note:** This only removes content from the teckel node and inventory. The content may still exist on the decentralised IPFS network.

**MCP tool:** `remove_ipfs_files` — Unpin and remove the IPFS files (selected on nickname via fuzzy search_string) from the teckel IPFS node and from the wallet account, for the account corresponding to the teckel API key used in the call. Note: this only removes the content from the teckel node and inventory. It may still exist on the IPFS de-centralized network.

**Parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `search_string` | string | No | `""` | Search for this string in the file nickname. Leave blank to match all. |
| `pinned_state` | integer | No | `0` | `0` = all entries; `1` = pinned only; `2` = unpinned only |
| `encrypted_state` | integer | No | `0` | `0` = all entries; `1` = encrypted only; `2` = unencrypted only |
| `content_type` | string | No | `""` | Filter by content type, e.g. `image`, `audio`, `video`. Leave blank to ignore. |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/remove_ipfs_files_from_account?search_string=old_test_files&pinned_state=0&encrypted_state=0" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

---

### POST `/pin_ipfs_files_for_apikey`

Pin IPFS files (selected by fuzzy nickname search) on the teckel IPFS node. Pinning prevents automatic garbage collection of the content.

**MCP tool:** `pin_ipfs_files` — Pin the IPFS files (selected on nickname via fuzzy search_string) on to the teckel IPFS node for the account corresponding to the teckel API key used in the call. Pinning prevents automatic deletion of the content from the IPFS node.

**Parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `search_string` | string | No | `""` | Search for this string in the file nickname. Leave blank to match all. |
| `pinned_state` | integer | No | `0` | `0` = all entries; `1` = pinned only; `2` = unpinned only |
| `encrypted_state` | integer | No | `0` | `0` = all entries; `1` = encrypted only; `2` = unencrypted only |
| `content_type` | string | No | `""` | Filter by content type, e.g. `image`, `audio`, `video`. Leave blank to ignore. |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/pin_ipfs_files_for_apikey?search_string=important_files&pinned_state=2" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

---

### POST `/unpin_ipfs_files_for_apikey`

Unpin IPFS files (selected by fuzzy nickname search) from the teckel IPFS node. Unpinned files are eligible for automatic garbage collection.

**MCP tool:** `unpin_ipfs_files` — Unpin the IPFS files (selected on nickname via fuzzy search_string) from the teckel IPFS node for the account corresponding to the teckel API key used in the call. Unpinning allows automatic deletion of the content from the IPFS node.

**Parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `search_string` | string | No | `""` | Search for this string in the file nickname. Leave blank to match all. |
| `pinned_state` | integer | No | `0` | `0` = all entries; `1` = pinned only; `2` = unpinned only |
| `encrypted_state` | integer | No | `0` | `0` = all entries; `1` = encrypted only; `2` = unencrypted only |
| `content_type` | string | No | `""` | Filter by content type, e.g. `image`, `audio`, `video`. Leave blank to ignore. |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/unpin_ipfs_files_for_apikey?search_string=old_drafts&pinned_state=1" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

---

### POST `/pin_ipfs_cid_for_apikey`

Pin the individual IPFS entity identified by its CID on the teckel IPFS node. Pinning prevents automatic deletion.

**MCP tool:** `pin_ipfs_cid` — Pin the individual entity identified by its IPFS CID on to the teckel IPFS node for the account corresponding to the teckel API key used in the call. Pinning prevents automatic deletion of the content from the IPFS node.

**Parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `cid` | string | No | `""` | IPFS content identifier of the file to pin |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/pin_ipfs_cid_for_apikey?cid=QmExampleCID123" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

---

### POST `/remove_ipfs_cid_from_account`

Unpin and remove the individual IPFS entity identified by its CID from the teckel IPFS node and account inventory.

> **Note:** This only removes content from the teckel node and inventory. The content may still exist on the decentralised IPFS network.

**MCP tool:** `remove_ipfs_cid_from_account` — Unpin and remove the individual entity identified by its IPFS CID from the teckel IPFS node and from the wallet account, for the account corresponding to the teckel API key used in the call. Note: this only removes the content from the teckel node and inventory. It may still exist on the IPFS de-centralized network.

**Parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `cid` | string | No | `""` | IPFS content identifier of the file to remove |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/remove_ipfs_cid_from_account?cid=QmExampleCID123" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

---

### POST `/publish_ipfs_file_to_webserver`

Retrieve an IPFS file by CID from the teckel node and publish it on the teckel web server, applying decryption if necessary. Returns a browseable public URL.

**MCP tool:** `publish_ipfs_file_to_webserver` — Retrieve the IPFS file, identified by its IPFS CID, from the teckel IPFS node, and publish it on the teckel web server, applying decryption if necessary, for the account corresponding to the teckel API key used in the call, returning the browseable URL.

**Parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `cid` | string | No | `""` | IPFS content identifier of the file to publish |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/publish_ipfs_file_to_webserver?cid=QmExampleCID123" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

---

### POST `/upload_sender_file_to_ipfs_for_apikey`

Upload a file to the teckel IPFS node for the account associated with the API key. The content is pre-scanned for compliance with teckel terms before being accepted.

**MCP tool:** `upload_file_to_ipfs` — Upload file contents to the teckel IPFS node for the account corresponding to the teckel API key used in the call. The content will be pre-scanned for compatibility with the teckel terms and will be rejected if not compliant.

**Parameters**

| Name | In | Type | Required | Default | Description |
|------|----|------|----------|---------|-------------|
| `file` | multipart form body | file | Yes | — | File to upload |
| `nicknameOnIPFS` | query | string | No | `""` | Nickname for the IPFS listing. Auto-generated if blank. |
| `doEncryptOnIPFS` | query | string | No | `"false"` | Apply encryption before uploading. `true` or `false`. |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/upload_sender_file_to_ipfs_for_apikey?nicknameOnIPFS=my_document&doEncryptOnIPFS=false" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -F "file=@/path/to/your/file.pdf"
```

---

### POST `/upload_sender_base64_to_ipfs_for_apikey`

Upload base64-encoded file data to the teckel IPFS node for the account associated with the API key. The content is pre-scanned for compliance with teckel terms before being accepted.

**MCP tool:** `upload_base64_file_to_ipfs` — Upload base64-encoded file contents to the teckel IPFS node for the account corresponding to the teckel API key used in the call. The content will be pre-scanned for compatibility with the teckel terms and will be rejected if not compliant.

**Parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `base64_data` | string | No | `""` | Base64-encoded file data |
| `nicknameOnIPFS` | string | No | `""` | Nickname for the IPFS listing. Auto-generated if blank. |
| `doEncryptOnIPFS` | string | No | `"false"` | Apply encryption before uploading. `true` or `false`. |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/upload_sender_base64_to_ipfs_for_apikey?base64_data=SGVsbG8gV29ybGQ%3D&nicknameOnIPFS=my_file&doEncryptOnIPFS=false" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

---

### POST `/retrieve_ipfs_file_for_apikey`

Retrieve the contents of an IPFS file by CID from the account inventory. Automatically decrypts if the file is encrypted. Only files originally posted to IPFS via teckel are supported.

**MCP tool:** `retrieve_ipfs_file` — Retrieve the contents of the IPFS file identified by the provided CID, from the inventory of the account corresponding to the teckel API key used in the call. Will automatically decrypt if encrypted. Only files originally posted to IPFS via teckel are supported i.e., not generic (non-teckel origin) IPFS content.

**Parameters**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `cid` | string | No | `""` | IPFS content identifier of the file to retrieve |

```bash
curl -X POST "https://mcp-servers.bh.tkllabs.io:9780/retrieve_ipfs_file_for_apikey?cid=QmExampleCID123" \
  -H "Authorization: Bearer YOUR_API_KEY"
```
