# Custom Client Implementation Guide

## Overview
This guide explains how to implement custom clients for the Eliza framework.

## Client Interface
```typescript
interface ElizaClient {
  // Required methods
  start(runtime: IAgentRuntime): Promise<void>;
  handleMessage(message: Message): Promise<void>;
  sendResponse(response: Response): Promise<void>;
  
  // Optional methods
  onError?(error: Error): void;
  cleanup?(): Promise<void>;
}
```

## Basic Implementation
```typescript
class CustomClient implements ElizaClient {
  private runtime: IAgentRuntime;
  private connection: any;
  private isConnected: boolean = false;

  async start(runtime: IAgentRuntime): Promise<void> {
    this.runtime = runtime;
    await this.initialize();
  }

  private async initialize(): Promise<void> {
    // Set up connection
    // Initialize resources
    // Configure event handlers
    this.isConnected = true;
  }

  async handleMessage(message: Message): Promise<void> {
    try {
      // Validate message
      this.validateMessage(message);

      // Process message
      const context = await this.buildContext(message);
      
      // Generate response
      const response = await this.runtime.generateResponse(context);
      
      // Send response
      await this.sendResponse(response);
    } catch (error) {
      this.handleError(error);
    }
  }

  async sendResponse(response: Response): Promise<void> {
    if (!this.isConnected) {
      throw new Error('Client not connected');
    }
    
    // Format response
    const formattedResponse = this.formatResponse(response);
    
    // Send response through connection
    await this.connection.send(formattedResponse);
  }

  private handleError(error: Error): void {
    console.error('Client error:', error);
    // Implement error handling logic
  }

  async cleanup(): Promise<void> {
    // Clean up resources
    // Close connections
    this.isConnected = false;
  }
}
```

## State Management
```typescript
interface ClientState {
  isConnected: boolean;
  lastMessageId: string;
  messageQueue: Message[];
  retryCount: number;
}

class CustomClient implements ElizaClient {
  private state: ClientState = {
    isConnected: false,
    lastMessageId: '',
    messageQueue: [],
    retryCount: 0
  };

  private async updateState(updates: Partial<ClientState>): Promise<void> {
    this.state = {
      ...this.state,
      ...updates
    };
  }

  private async handleStateChange(): Promise<void> {
    // React to state changes
    if (!this.state.isConnected && this.state.retryCount < 3) {
      await this.reconnect();
    }
  }
}
```

## Connection Management
```typescript
interface Connection {
  connect(): Promise<void>;
  disconnect(): Promise<void>;
  send(message: any): Promise<void>;
  onMessage(handler: (message: any) => void): void;
}

class CustomConnection implements Connection {
  private url: string;
  private ws: WebSocket;

  constructor(url: string) {
    this.url = url;
  }

  async connect(): Promise<void> {
    // Implement connection logic
  }

  async disconnect(): Promise<void> {
    // Implement disconnection logic
  }

  async send(message: any): Promise<void> {
    // Implement send logic
  }

  onMessage(handler: (message: any) => void): void {
    // Implement message handling
  }
}
```

## Message Processing
```typescript
interface MessageProcessor {
  validateMessage(message: Message): boolean;
  buildContext(message: Message): Promise<Context>;
  formatResponse(response: Response): FormattedResponse;
}

class CustomMessageProcessor implements MessageProcessor {
  validateMessage(message: Message): boolean {
    // Implement validation logic
    return true;
  }

  async buildContext(message: Message): Promise<Context> {
    // Build message context
    return {
      message,
      timestamp: Date.now(),
      metadata: {}
    };
  }

  formatResponse(response: Response): FormattedResponse {
    // Format response for client
    return {
      content: response.content,
      timestamp: Date.now()
    };
  }
}
```

## Plugin Integration
```typescript
interface ClientPlugin {
  name: string;
  initialize(client: ElizaClient): Promise<void>;
  processMessage?(message: Message): Promise<Message>;
  processResponse?(response: Response): Promise<Response>;
}

class CustomClientPlugin implements ClientPlugin {
  name = 'custom-plugin';

  async initialize(client: ElizaClient): Promise<void> {
    // Initialize plugin
  }

  async processMessage(message: Message): Promise<Message> {
    // Process message
    return message;
  }

  async processResponse(response: Response): Promise<Response> {
    // Process response
    return response;
  }
}
```

## Best Practices

### 1. Error Handling
- Implement proper error handling
- Use retry mechanisms
- Log errors appropriately
- Clean up resources on error

### 2. State Management
- Maintain client state
- Handle state transitions
- Implement reconnection logic
- Monitor connection status

### 3. Message Processing
- Validate messages
- Build proper context
- Format responses correctly
- Handle message queuing

### 4. Resource Management
- Clean up resources properly
- Handle connection lifecycle
- Manage memory usage
- Monitor performance

## Implementation Notes
1. Follow interface contracts strictly
2. Implement proper error handling
3. Clean up resources appropriately
4. Document client capabilities
5. Test thoroughly before deployment
6. Monitor client performance
7. Handle rate limiting
8. Implement security measures
