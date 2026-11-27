Ask the user for their preferred programming language.

You are helping with Sinch Conversation API number/sender operations.

**IMPORTANT: Follow this priority order:**

1. **First Priority**: Check if Sinch MCP (Model Context Protocol) tools are available. If yes, use MCP tools to get real data and generate code based on actual API responses.
2. **Second Priority**: If MCP tools are not available, use the Sinch Coding Assistant extension's bundled code snippets (available via "Sinch: Insert Code Snippet" command or snippets.json in the extension). Look for snippets matching the operation (e.g., "getNumber", "listNumbers", "createNumber", "deleteNumber").
3. **Third Priority**: If neither MCP tools nor matching snippets exist, generate code using standard HTTP client libraries with the API documentation below.

Based on the user's request, implement the appropriate operation:

## Available Operations:

### GET Number (e.g., "/sinch-number get {numberId}")

Create a class method that retrieves virtual number/sender information.

**Priority 1 - MCP Tool**: If available, use the Sinch MCP tool to fetch number details, then generate code based on the actual response structure.

**Priority 2 - Extension Snippet**: Use the "getNumber" snippet from the Sinch Coding Assistant extension's bundled snippets.

**Priority 3 - Standard Implementation**:
Include:

- Method: getNumber(numberId)
- HTTP GET to `https://{region}.conversation.api.sinch.com/v1/projects/{projectId}/numbers/{numberId}`
- Authorization: Basic auth (base64 encoded key:secret)
- Parse response fields: id, display_name, capabilities, channel_identities
- Handle errors: 404 not found, 401 unauthorized, network errors

### LIST Numbers (e.g., "/sinch-number list")

Create a class method that lists all virtual numbers.

**Priority 1 - MCP Tool**: If available, use the Sinch MCP tool to list numbers, then generate pagination code based on actual results.

**Priority 2 - Extension Snippet**: Use the "listNumbers" or similar snippet from the extension.

**Priority 3 - Standard Implementation**:
Include:

- Method: listNumbers(pageSize, pageToken)
- HTTP GET to `https://{region}.conversation.api.sinch.com/v1/projects/{projectId}/numbers`
- Query params: page_size (max 1000), page_token
- Handle pagination with next_page_token
- Return array of number objects

### CREATE Number (e.g., "/sinch-number create")

Create a class method that provisions a new virtual number.

**Priority 1 - MCP Tool**: If available, use the Sinch MCP tool to create a number and show the user the actual request/response.

**Priority 2 - Extension Snippet**: Use the "createNumber" or similar snippet from the extension.

**Priority 3 - Standard Implementation**:
Include:

- Method: createNumber(displayName, capabilities, channelId)
- HTTP POST to `https://{region}.conversation.api.sinch.com/v1/projects/{projectId}/numbers`
- Request body: display_name, capabilities array, channel configurations
- Handle response with new number ID

### DELETE Number (e.g., "/sinch-number delete {numberId}")

Create a class method that removes a virtual number.

**Priority 1 - MCP Tool**: If available, use the Sinch MCP tool to delete the number.

**Priority 2 - Extension Snippet**: Use the "deleteNumber" or similar snippet from the extension.

**Priority 3 - Standard Implementation**:
Include:

- Method: deleteNumber(numberId)
- HTTP DELETE to `https://{region}.conversation.api.sinch.com/v1/projects/{projectId}/numbers/{numberId}`
- Return success boolean
- Handle 404 if number doesn't exist

## Common Requirements for All Operations:

- Use environment variables for credentials (SINCH_KEY_ID, SINCH_KEY_SECRET, SINCH_PROJECT_ID, SINCH_REGION)
- Add Authorization header: Basic base64(key_id:key_secret)
- Include error handling with try-catch
- Add structured logging with correlation IDs
- Implement exponential backoff retry for 429/5xx errors
- Use strong typing (interfaces/classes)

Ask the user which operation they want to implement.
