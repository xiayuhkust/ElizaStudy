# Discord Client Integration in Eliza

## Overview
The Discord client integration in Eliza is handled through the @elizaos/client-discord package, which provides a seamless interface between Discord and the Eliza agent system.

## Architecture

### 1. Discord Client Interface
```typescript
interface DiscordClientInterface {
  start(runtime: IAgentRuntime): Promise<void>;
  handleMessage(message: DiscordMessage): Promise<void>;
  sendResponse(response: Response, channel: string): Promise<void>;
}
```

### 2. Initialization Process
```typescript
// In clients/index.ts
if (clientTypes.includes("discord")) {
  const discordClient = await DiscordClientInterface.start(runtime);
  clients.push(discordClient);
}
```

## Message Flow

### 1. Receiving Messages
```typescript
class DiscordClient implements DiscordClientInterface {
  private client: Discord.Client;

  private async setupMessageHandler() {
    this.client.on('messageCreate', async (message) => {
      // Ignore bot messages
      if (message.author.bot) return;

      // Convert Discord message to system format
      const systemMessage = this.convertToSystemMessage(message);

      // Process message
      await this.handleMessage(systemMessage);
    });
  }

  private convertToSystemMessage(discordMessage: Discord.Message): Message {
    return {
      id: discordMessage.id,
      content: {
        text: discordMessage.content
      },
      userId: discordMessage.author.id,
      channelId: discordMessage.channelId,
      timestamp: discordMessage.createdTimestamp
    };
  }
}
```

### 2. Processing Messages
```typescript
async handleMessage(message: Message): Promise<void> {
  try {
    // Build context
    const context = await this.runtime.buildContext(message);

    // Generate response
    const response = await this.runtime.generateResponse(context);

    // Send response
    await this.sendResponse(response, message.channelId);
  } catch (error) {
    console.error('Error processing Discord message:', error);
  }
}
```

### 3. Sending Responses
```typescript
async sendResponse(response: Response, channelId: string): Promise<void> {
  try {
    const channel = await this.client.channels.fetch(channelId);
    if (channel?.isText()) {
      await channel.send(response.content.text);
    }
  } catch (error) {
    console.error('Error sending Discord response:', error);
  }
}
```

## Configuration

### 1. Discord Bot Setup
1. Create Discord application in Discord Developer Portal
2. Create bot user
3. Get bot token
4. Set required permissions
5. Generate invite link

### 2. Client Configuration
```typescript
// In character configuration
{
    "name": "Eliza",
    "clients": ["discord"],
    "settings": {
        "secrets": {
            "DISCORD_TOKEN": "your-bot-token"
        }
    }
}
```

### 3. Environment Variables
```bash
# Required environment variables
DISCORD_TOKEN=your-bot-token
DISCORD_CLIENT_ID=your-client-id
DISCORD_GUILD_ID=your-guild-id
```

## Event Handling

### 1. Connection Events
```typescript
private async setupConnectionHandlers() {
  this.client.on('ready', () => {
    console.log(`Logged in as ${this.client.user?.tag}`);
  });

  this.client.on('error', (error) => {
    console.error('Discord client error:', error);
  });
}
```

### 2. Message Events
```typescript
private async setupMessageHandlers() {
  // Message creation
  this.client.on('messageCreate', this.handleIncomingMessage);

  // Message update
  this.client.on('messageUpdate', this.handleMessageUpdate);

  // Message deletion
  this.client.on('messageDelete', this.handleMessageDelete);
}
```

### 3. Guild Events
```typescript
private async setupGuildHandlers() {
  // Join guild
  this.client.on('guildCreate', this.handleGuildJoin);

  // Leave guild
  this.client.on('guildDelete', this.handleGuildLeave);
}
```

## Error Handling

### 1. Connection Errors
```typescript
private async handleConnectionError(error: Error) {
  console.error('Discord connection error:', error);
  
  // Attempt reconnection
  if (this.retryCount < this.maxRetries) {
    this.retryCount++;
    await this.reconnect();
  }
}
```

### 2. Message Errors
```typescript
private async handleMessageError(error: Error, message: Message) {
  console.error('Message processing error:', error);
  
  // Notify channel of error
  if (message.channelId) {
    const channel = await this.client.channels.fetch(message.channelId);
    if (channel?.isText()) {
      await channel.send('Sorry, I encountered an error processing your message.');
    }
  }
}
```

## Best Practices

### 1. Rate Limiting
- Respect Discord API rate limits
- Implement message queuing
- Handle rate limit errors

### 2. Error Handling
- Handle connection issues
- Implement retry logic
- Log errors appropriately

### 3. Message Processing
- Validate messages
- Handle different message types
- Process attachments properly

### 4. Security
- Validate permissions
- Sanitize input
- Protect sensitive data

## Implementation Notes
1. Follow Discord API guidelines
2. Handle rate limits properly
3. Implement proper error handling
4. Clean up resources appropriately
5. Monitor bot performance
6. Log important events
7. Handle reconnection gracefully
8. Maintain state consistency
