# Client Integration with Eliza

## Overview
Eliza supports multiple client types through a standardized client interface. This allows for integration with various platforms and custom client implementations.

## Supported Clients

### 1. HTTP/REST Client
```typescript
interface HttpClient {
  // Basic HTTP endpoints
  POST /message    // Send message to agent
  GET  /messages   // Get message history
  POST /initialize // Initialize session
}
```

### 2. WebSocket Client
```typescript
interface WebSocketClient {
  // WebSocket events
  connect()       // Connect to server
  sendMessage()   // Send message
  onMessage()     // Receive message
  disconnect()    // Disconnect from server
}
```

### 3. CLI Client
```typescript
interface CliClient {
  // Command line interface
  start()         // Start CLI session
  processInput()  // Process user input
  displayOutput() // Display agent response
}
```

## Custom Client Implementation

### 1. Basic Client Interface
```typescript
interface ElizaClient {
  // Required methods
  initialize(config: ClientConfig): Promise<void>;
  sendMessage(message: string): Promise<Response>;
  getHistory(): Promise<Message[]>;
  
  // Optional methods
  onResponse?(response: Response): void;
  onError?(error: Error): void;
}
```

### 2. Implementation Example
```typescript
class CustomElizaClient implements ElizaClient {
  private config: ClientConfig;
  private connection: Connection;

  async initialize(config: ClientConfig): Promise<void> {
    this.config = config;
    this.connection = await this.setupConnection();
  }

  async sendMessage(message: string): Promise<Response> {
    // Format message
    const formattedMessage = this.formatMessage(message);
    
    // Send to server
    const response = await this.connection.send(formattedMessage);
    
    // Process response
    return this.processResponse(response);
  }

  async getHistory(): Promise<Message[]> {
    return await this.connection.getMessages();
  }

  private formatMessage(message: string): FormattedMessage {
    return {
      content: message,
      timestamp: Date.now(),
      metadata: this.config.metadata
    };
  }

  private processResponse(response: any): Response {
    // Process and validate response
    return {
      content: response.text,
      timestamp: response.timestamp
    };
  }
}
```

### 3. Connection Setup
```typescript
interface Connection {
  send(message: FormattedMessage): Promise<any>;
  getMessages(): Promise<Message[]>;
  close(): Promise<void>;
}

class HttpConnection implements Connection {
  private baseUrl: string;
  private headers: Headers;

  constructor(config: ConnectionConfig) {
    this.baseUrl = config.url;
    this.headers = this.setupHeaders(config);
  }

  async send(message: FormattedMessage): Promise<any> {
    const response = await fetch(`${this.baseUrl}/message`, {
      method: 'POST',
      headers: this.headers,
      body: JSON.stringify(message)
    });
    return await response.json();
  }

  async getMessages(): Promise<Message[]> {
    const response = await fetch(`${this.baseUrl}/messages`, {
      headers: this.headers
    });
    return await response.json();
  }

  async close(): Promise<void> {
    // Cleanup connection
  }
}
```

## Integration Steps

1. Choose Client Type
   - HTTP/REST for simple integrations
   - WebSocket for real-time communication
   - Custom implementation for specific needs

2. Configure Client
```typescript
const config: ClientConfig = {
  url: 'https://your-eliza-server.com',
  apiKey: 'your-api-key',
  metadata: {
    clientId: 'custom-client-1',
    version: '1.0.0'
  }
};
```

3. Initialize Client
```typescript
const client = new CustomElizaClient();
await client.initialize(config);
```

4. Handle Communication
```typescript
// Send message
const response = await client.sendMessage('Hello');

// Get history
const history = await client.getHistory();
```

## Best Practices

### 1. Error Handling
- Implement proper error handling
- Use retry mechanisms
- Log errors appropriately

### 2. Connection Management
- Handle connection timeouts
- Implement reconnection logic
- Clean up resources properly

### 3. Message Processing
- Validate messages
- Handle different message types
- Process responses correctly

### 4. Security
- Use secure connections
- Implement authentication
- Protect sensitive data

## Implementation Notes
1. Choose appropriate client type based on needs
2. Follow security best practices
3. Implement proper error handling
4. Document client implementation
5. Test thoroughly before deployment
