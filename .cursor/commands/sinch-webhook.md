Ask the user for their preferred programming language and web framework (Express, Flask, Spring Boot, FastAPI, etc.).

You are helping with Sinch Conversation API webhook operations.

**IMPORTANT: Follow this priority order:**

1. **First Priority**: Check if Sinch MCP (Model Context Protocol) tools are available. If yes, use MCP tools to create/update/list webhooks and get actual webhook configurations, then generate code based on real data.
2. **Second Priority**: If MCP tools are not available, use the Sinch Coding Assistant extension's bundled code snippets (available via "Sinch: Insert Code Snippet" command or snippets.json in the extension). Look for snippets matching the operation (e.g., "createWebhook", "updateWebhook", "deleteWebhook", "listWebhooks").
3. **Third Priority**: If neither MCP tools nor matching snippets exist, generate code using standard HTTP client libraries with the API documentation below.

Based on the user's request, implement the appropriate operation:

## Available Operations:

### CREATE Webhook (e.g., "/sinch-webhook create")

Create a class method that registers a new webhook.

**Priority 1 - MCP Tool**: If available, use the Sinch MCP "create webhook" tool to register a webhook, then generate code based on the actual request/response.

**Priority 2 - Extension Snippet**: Use the "createWebhook" snippet from the extension.

**Priority 3 - Standard Implementation**:
Include:

- Method: createWebhook(appId, targetUrl, triggers, secret)
- HTTP POST to `https://{region}.conversation.api.sinch.com/v1/projects/{projectId}/apps/{appId}/webhooks`
- Request body: { target, target_type: "HTTP", triggers: [], secret }
- Validate targetUrl is HTTPS
- Return webhook id and configuration

### UPDATE Webhook (e.g., "/sinch-webhook update")

Create a class method that updates webhook configuration.

**Priority 1 - MCP Tool**: If available, use the Sinch MCP "update webhook" tool.

**Priority 2 - Extension Snippet**: Use the "updateWebhook" snippet from the extension.

**Priority 3 - Standard Implementation**:
Include:

- Method: updateWebhook(appId, webhookId, targetUrl, triggers, secret)
- HTTP PATCH to `https://{region}.conversation.api.sinch.com/v1/projects/{projectId}/apps/{appId}/webhooks/{webhookId}`
- Partial update: only send fields to change
- Handle 404 if webhook not found

### DELETE Webhook (e.g., "/sinch-webhook delete")

Create a class method that removes a webhook.

**Priority 1 - MCP Tool**: If available, use the Sinch MCP "delete webhook" tool.

**Priority 2 - Extension Snippet**: Use the "deleteWebhook" snippet from the extension.

**Priority 3 - Standard Implementation**:
Include:

- Method: deleteWebhook(appId, webhookId)
- HTTP DELETE to `https://{region}.conversation.api.sinch.com/v1/projects/{projectId}/apps/{appId}/webhooks/{webhookId}`
- Handle 200 OK (empty body)
- Return success boolean

### LIST Webhooks (e.g., "/sinch-webhook list")

Create a class method that retrieves all webhooks for an app.

**Priority 1 - MCP Tool**: If available, use the Sinch MCP "list webhooks" tool to get actual webhook configurations.

**Priority 2 - Extension Snippet**: Use the "listWebhooks" snippet from the extension.

**Priority 3 - Standard Implementation**:
Include:

- Method: listWebhooks(appId)
- HTTP GET to `https://{region}.conversation.api.sinch.com/v1/projects/{projectId}/apps/{appId}/webhooks`
- Return array of webhook configurations

### HANDLE Webhook Event (e.g., "/sinch-webhook handler MESSAGE_DELIVERY")

Create a webhook handler endpoint that receives and processes webhook events.

**Priority 1 - MCP Tool**: If available, use the Sinch MCP tools to inspect actual webhook payload examples from your app's history.

**Priority 2 - Extension Snippet**: Use webhook handler snippets from the extension (check for "webhookHandler", "handleWebhook", or event-specific handlers).

**Priority 3 - Standard Implementation**:
Include:

- POST endpoint route (e.g., /webhooks/sinch)
- Validate webhook signature using HMAC-SHA256:
  1. Get 'x-sinch-webhook-signature' header
  2. Compute HMAC-SHA256 of raw request body using webhook secret
  3. Compare with header value (constant-time comparison)
  4. Return 401 Unauthorized if mismatch
- Parse JSON request body
- Extract event type and route to appropriate handler
- Return 200 OK within 10 seconds
- Process asynchronously if needed (queue/worker pattern)
- Implement idempotency using event_time or unique IDs
- Add structured logging

Common webhook payload structure:

```json
{
  "app_id": "string",
  "accepted_time": "ISO-8601",
  "event_time": "ISO-8601",
  "project_id": "string",
  "message_metadata": "optional",
  "message_delivery_report": {
    /* event data */
  }
  // or other event types...
}
```

Available webhook triggers:
- MESSAGE_DELIVERY: Handle message delivery status updates (delivery receipts)
- MESSAGE_INBOUND: Handle incoming messages from users
- MESSAGE_SUBMIT: Handle message submission events
- EVENT_DELIVERY: Handle event delivery status updates
- EVENT_INBOUND: Handle incoming events from channels
- CONVERSATION_START: Handle new conversation creation
- CONVERSATION_STOP: Handle conversation end events
- CONVERSATION_DELETE: Handle conversation deletion events
- CONTACT_CREATE: Handle new contact creation
- CONTACT_DELETE: Handle contact deletion events
- CONTACT_MERGE: Handle contact merge events when duplicates are combined
- CONTACT_UPDATE: Handle contact information updates
- CONTACT_IDENTITIES_DUPLICATION: Handle duplicate contact identity detection
- OPT_IN: Handle user opt-in events for messaging
- OPT_OUT: Handle user opt-out events (unsubscribe)
- CAPABILITY: Handle channel capability updates
- CHANNEL_EVENT: Handle channel-specific events
- UNSUPPORTED: Handle unsupported message types
- SMART_CONVERSATION: Handle smart conversation events
- MESSAGE_INBOUND_SMART_CONVERSATION_REDACTION: Handle message redaction for privacy compliance
- RECORD_NOTIFICATION: Handle recording notification events

**Note:** Channel-specific callbacks (e.g., WhatsApp payment status, KakaoTalk commerce events) may exist for certain channels. Check the [Sinch Conversation API webhooks documentation](https://developers.sinch.com/docs/conversation/webhooks/) for the latest trigger list.

## Common Requirements for All Operations:

- Use environment variables for credentials
- Add Authorization header: Basic base64(key_id:key_secret)
- Include comprehensive error handling
- For webhook handlers:
  - Always return 200 OK (even on processing errors)
  - Store webhook secret securely
  - Log all events with correlation IDs
  - Handle duplicate deliveries gracefully
  - Sanitize PII before logging
  - Process asynchronously for long operations

Ask the user:

1. Which webhook operation they want to implement
2. For handlers: which event types they want to handle
3. What business logic they want to execute on webhook events
