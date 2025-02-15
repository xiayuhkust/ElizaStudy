# Eliza Client Types and Integration

## Supported Client Types

1. DirectClient (@elizaos/client-direct)
   - Core client for direct interaction
   - Used for local development and testing
   - Handles agent registration and initialization

2. AutoClient (@elizaos/client-auto)
   - Automated client interface
   - Supports automated interactions
   - Used for testing and automation

3. DiscordClient (@elizaos/client-discord)
   - Discord platform integration
   - Enables bot functionality on Discord
   - Handles Discord-specific message formats

4. TelegramClient (@elizaos/client-telegram)
   - Telegram platform integration
   - Enables bot functionality on Telegram
   - Handles Telegram-specific message formats

5. TwitterClient (@elizaos/client-twitter)
   - Twitter platform integration
   - Enables bot functionality on Twitter
   - Handles Twitter-specific message formats

## Client Configuration

### Character Configuration
```typescript
// In character definition
{
    clients: ["auto", "discord", "telegram", "twitter"],
    // Other character settings...
}
```

### Client Initialization
```typescript
// In clients/index.ts
export async function initializeClients(
  character: Character,
  runtime: IAgentRuntime
) {
  const clients = [];
  const clientTypes = character.clients?.map((str) => str.toLowerCase()) || [];

  // Initialize configured clients
  if (clientTypes.includes("auto")) {
    const autoClient = await AutoClientInterface.start(runtime);
    if (autoClient) clients.push(autoClient);
  }

  // Add other client types...
}
```

## Custom Client Implementation

To implement a custom client:

1. Create Client Interface
```typescript
interface CustomClient {
  start(runtime: IAgentRuntime): Promise<void>;
  handleMessage(message: Message): Promise<void>;
  sendResponse(response: Response): Promise<void>;
}
```

2. Implement Client Class
```typescript
class MyCustomClient implements CustomClient {
  private runtime: IAgentRuntime;

  async start(runtime: IAgentRuntime): Promise<void> {
    this.runtime = runtime;
    // Initialize client
  }

  async handleMessage(message: Message): Promise<void> {
    // Process incoming message
  }

  async sendResponse(response: Response): Promise<void> {
    // Send response
  }
}
```

3. Register Client
```typescript
// Add to character configuration
{
    clients: ["custom"],
    plugins: [{
        clients: [MyCustomClient]
    }]
}
```

## Client Integration Process

1. Agent Startup
```typescript
async function startAgent(character: Character, directClient: DirectClient) {
  // Initialize runtime
  const runtime = createAgent(character, db, cache, token);
  await runtime.initialize();

  // Initialize clients
  runtime.clients = await initializeClients(character, runtime);

  // Register with direct client
  directClient.registerAgent(runtime);
}
```

2. Message Flow
- Client receives message
- Message processed by runtime
- Response generated
- Response sent through client

## Best Practices

1. Client Implementation
   - Follow interface contracts
   - Handle errors gracefully
   - Implement proper cleanup
   - Document client capabilities

2. Message Handling
   - Validate messages
   - Handle rate limits
   - Process responses properly
   - Maintain state appropriately

3. Error Handling
   - Handle connection issues
   - Implement retry logic
   - Log errors properly
   - Clean up resources

## Implementation Notes
1. Each client type has its own package
2. Clients are initialized based on character configuration
3. Custom clients can be added through plugins
4. Direct client is always available for development
