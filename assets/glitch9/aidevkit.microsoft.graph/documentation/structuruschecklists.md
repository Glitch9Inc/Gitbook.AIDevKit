# Structurus Work Checklist

## Objectives

* Ensure a stable streaming text pipeline in Unity
* Control text output rate with deterministic throttling
* Establish a Microsoft SharePoint connection via the OpenAI MCP connector
* Integrate Credentials Flow authentication for secure access
* Ensure Microsoft account data is not shared with the OpenAI model and remains secure
* Save and reload AI conversations on a per-user basis

***

## Tasks

### Streaming Chat Pipeline

* [x] Build a minimal streaming text throttler for Unity runtime (MonoBehaviour)
* [x] Implement conversation reload integrity checks
* [x] Implement 'Tool Use' related UI to existing chat UI

***

### Requests to Structurus

* [ ] Obtain required information from Structurus
  * [ ] Tenant ID: {?}
  * [ ] Tenant domain: {?}.onmicrosoft.com (for quick testing purposes)
  * [x] Client ID: 9ea405d7-70fc-4d51-9ba0-45fac0661f8b
  * [x] Client secret (testing only; should not be stored in Unity builds; invalidate after testing)
* [ ] Confirm minimum required permission scopes for SharePoint access => User.Read, User.Read.All, Files.Read.All, Sites.Read.All
* [ ] Confirm conversation storage format: Local or OpenAI Conversation API (recommended)

### OAuth Integration (Credentials Flow)

* [x] Add Credentials Flow implementation to the existing AIDevKit OAuth system
* [x] Create SharePoint OAuth settings (ScriptableObject)
* [x] Test access token retrieval via Credentials Flow
  * [x] Create a personal Azure AD App Registration for testing
    * [x] Get Tenant ID: 93535a7f-5b2c-4d2a-bf33-c458dfb5ef10 (amaiichigopuringmail.onmicrosoft.com) => contoso.onmicrosoft.com → https://contoso.sharepoint.com => amaiichigopuringmail.onmicrosoft.com → https://amaiichigopuringmail.sharepoint.com => Joined trial => munchkin@glitch9.onmicrosoft.com => https://glitch9.sharepoint.com => tenant ID: 38a80755-f44a-4482-8631-085e5cd2d0b3 => client ID: 86f0e669-ec9f-4e27-b168-53ac69bb0a46
    * [x] Get Client ID: 5c995717-a6f0-4faa-b310-c917a81c92f8 (called "Application ID" in Azure portal)
    * [x] Get Client Secret (for testing only)
  * [x] Validate tenant / site / drive scoping behavior => Turns out that OpenAI SharePoint MCP connector does not support Client Credentials Flow with app permissions. => Which means I have to build an entire Graph API SharePoint connector from scratch and use it as a 'Function' not 'Mcp'.

***

### Graph API SharePoint Connector

* [x] Verify SharePoint API references and official documentation => Tester: https://developer.microsoft.com/en-us/graph/graph-explorer => Official Docs: https://learn.microsoft.com/en-us/graph/api/resources/sharepoint?view=graph-rest-1.0
* [x] Implement authentication & token management
  * [x] Acquire token (client credentials)
  * [x] Token caching + refresh
  * [x] Retry on 401/429 with backoff
* [x] Implement minimal models for SharePoint entities for AI model citation (Estimated time: 1 day) => Official doc says: The SharePoint API exposes three major resource types: Site, List, ListItem => However, for file access, we need to use Drive and DriveItem resources. => Ignore List and ListItem forever for less headache.
  * [x] Site
  * [x] Drive (document library 1:1 mapping)
  * [x] DriveItem (file/folder)
* [x] Implement SharePoint file read operations via Microsoft Graph API (Estimated time: 2-3 days)
  * [x] List files in a document library
  * [x] List files in a folder by path (`root:/path:/children`)
  * [x] Pagination support (`@odata.nextLink`)
  * [x] Read file metadata (name, id, size, lastModifiedDateTime, webUrl, file hash if available)
  * [x] Download file contents (`/content`) with large-file handling
    * [x] Stream download (avoid loading whole file into memory)
    * [x] Handle 302 redirect download URLs
  * [x] Delta sync for incremental updates (`/delta`) (strongly recommended)
* [x] Robustness & edge cases => Use IUnityWebRequestExceptionParser from CoreLib.IO.Networking (Estimated time: 2 days)
  * [x] Rate limiting handling (HTTP 429 + Retry-After)
  * [x] Transient errors (5xx) retry policy
  * [x] 404 vs permission errors distinction
  * [x] MIME type detection + unsupported type handling
  * [x] Filename/path encoding (spaces, unicode)
* [x] Content extraction pipeline (for RAG) (Estimated time: 3-4 days)
  * [x] Text extraction per format + normalization (list excludes ones that requires OpenXML SDK or other 3rd-party heavy libs)
    * [x] Plain text(TXT) extractor
    * [x] Markdown(MD) extractor
    * [x] HTML(or HTM) extractor
    * [x] CSV extractor (optional)
    * [x] JSON extractor (optional)
    * [x] XML extractor (optional)
    * [x] YAML extractor (optional)
    * [x] PDF extractor (optional) -> uses Azure Document Intelligence via AIDevKit.Microsoft.Azure -> also covers DOCX, PPTX, XLSX indirectly, and other image-based formats
  * [x] Chunking strategy (size, overlap) + stable chunk IDs
  * [x] Metadata schema for citations (site, drive, path, file id, version, modified time)
* [x] Indexing & search integration (Estimated time: 2-3 days)
  * [x] Embedding generation
  * [x] Vector store upsert/update/delete mapping
  * [x] Deletion detection (delta removed items)
  * [x] Re-index policy (on hash/version change)
* [x] Observability & ops (Estimated time: 1-2 days)
  * [x] Structured logs (site, fileId, requestId)
  * [x] Metrics: sync duration, files processed, failures, throttles
  * [x] Dry-run mode + verbose debug toggle
  * [x] Config management (tenant, site\_path, include/exclude folders, file type allowlist)
* [x] Implement Function (SharePoint Action) connectors (Added new, Estimated time: 2-3 days)
  * [x] Finalize the function list
  * [x] Implement each function connector with proper parameterization and error handling
  * [x] Test with actual GPT models
  * [x] Adjust function list based on testing feedback
  * [x] Implement adjusted functions if necessary
* [x] Security review (Estimated time: 1 day)
  * [x] Secret storage (max security for client-embedded secrets) // Note: Client-embedded secrets are inherently insecure. Try limiting the scope as much as possible.
  * [x] Least-privilege permission review: User.Read, User.Read.All, Files.Read.All, Sites.Read.All
  * [x] Optional: restrict to specific site/drive via app access policies (advanced)
* [x] Tests (Estimated time: 2 days)
  * [x] Unit tests for URL building + pagination + delta parsing
  * [x] Integration test against a test tenant/site
  * [x] Regression fixtures for tricky filenames and large files
* [ ] Total estimated time: 14-18 working days (2-3 weeks / may vary based on complexity and edge cases)
* [ ] Estimated cost: £2800–£3600 (based on £200 per working day)
* [ ] Offering cost: £1400 (50%-70% discount for initial implementation and long-term partnership)

***

### SharePoint MCP Connector

* [x] Create SharePoint MCP tool definition (ScriptableObject)
* [x] Verify SharePoint minimum permission scopes required by the MCP connector => User.Read, User.Read.All, Files.Read.All, Sites.Read.All
* [x] Add the necessary permission scopes to the Azure AD App Registration to my personal testing app => I don't have sharepoint license. So.. join this => https://developer.microsoft.com/microsoft-365/dev-program
* [x] Join Microsoft 365 Developer Program to get a free SharePoint license for testing => I got: You don't current qualify for a Microsoft 365 Developer Program sandbox subscription. => Can't proceed testing for now.

***

### Conversation Persistence

* [x] Implement conversation save/load via confirmed storage method
  * [x] Test save/load integrity
  * [x] Handle save/load failure cases (network issues, permission errors, etc.)

***

### Security Considerations

At the moment, OpenAI API keys and Azure credentials (client secret / certificate) are embedded directly in the Unity client. This creates an inherent security risk, as these secrets can be extracted from the client build.

During the process of validating the authentication and integration flows, it became clear that this client-side approach has unavoidable security limitations when using credentials-based authentication.

If required, I can implement a secure server-side architecture on AWS that handles OpenAI and Microsoft (Azure / SharePoint) authentication and requests on behalf of the client. In this setup, all secrets are stored and used only on the server, and are never exposed to the Unity application.

This would involve additional development work (AWS Lambda / API Gateway, secret management, and API proxying), so this would be handled as a separate, paid task outside the current scope. The expected price range would be approximately £2000–£3200, depending on the exact requirements and complexity. Since this does not utilize most of the existing AIDevKit codes (it's like partially porting the AIDevKit to a server-side architecture), the price is higher than typical feature additions.

Please let me know if you would like to proceed with this approach.

***

### Microsoft Developer Program Signup Issue

Current Eligibility Criteria:

As of February 2024, Microsoft has limited access to the free Microsoft 365 E5 developer subscriptions. To qualify, you must be:

* A developer or part of an organization with an active Visual Studio Enterprise subscription.
* A participant in specific Microsoft AI Cloud Partner Programs.

For detailed information, refer to the Microsoft 365 Developer Program FAQ.

Alternative Learning Resources: If you don't meet the current eligibility criteria, consider the following options to continue your learning journey:

1. Microsoft 365 Training Center:
   * Access a wide range of free training modules and tutorials covering various Microsoft 365 services.
   * Visit the Microsoft 365 Training Center to explore available resources.
2. Microsoft 365 Trial Subscription:
   * Sign up for a free trial of Microsoft 365, which typically lasts 30 days.
   * This trial provides temporary access to Microsoft 365 services, allowing you to practice and familiarize yourself with the platform.
   * Be aware that a subscription fee is required for continued access after the trial period.

Note: Microsoft is piloting a new verification process to expand eligibility for the developer subscription. While this process is underway, access is primarily limited to the groups mentioned above.

***
