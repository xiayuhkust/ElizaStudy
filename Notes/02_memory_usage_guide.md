# Eliza Memory System Usage Guide

## Database Setup
The database setup is handled automatically through the database adapters:

```typescript
// Database initialization is automatic based on environment variables
if (process.env.POSTGRES_URL) {
  // PostgreSQL will be used if POSTGRES_URL is provided
  const db = new PostgresDatabaseAdapter({
    connectionString: process.env.POSTGRES_URL,
  });
} else {
  // SQLite is used as fallback
  const db = new SqliteDatabaseAdapter(new Database(filePath));
}
```

## Memory System Usage

### 1. Basic Setup
To use the memory system, you need to ensure your .env file includes either:
- `POSTGRES_URL` for PostgreSQL
- `SQLITE_FILE` (optional) for SQLite file location

The system will automatically choose the appropriate database adapter.

### 2. Memory Operations
Memory operations are handled through the @elizaos/core package. The main interfaces are:

```typescript
// Message History
messageManager.store(message)
messageManager.retrieve(context)

// User Descriptions
descriptionManager.store(userProfile)
descriptionManager.retrieve(userId)

// Character Knowledge
loreManager.store(knowledge)
loreManager.retrieve(context)
```

### 3. Implementation Example
To implement memory operations in your agent:

1. First, ensure your agent has the necessary managers:
```typescript
import { AgentRuntime, ModelProviderName } from "@elizaos/core";

const runtime = new AgentRuntime({
  // ... other config
  messageManager: new MemoryManager(),
  descriptionManager: new MemoryManager(),
  loreManager: new MemoryManager(),
});
```

2. Use the managers in your agent's logic:
```typescript
// Store a message
await runtime.messageManager.store({
  userId: "user123",
  content: "Hello!",
  timestamp: Date.now(),
});

// Retrieve context
const context = await runtime.messageManager.retrieve({
  userId: "user123",
  limit: 10,
});
```

## Important Notes
1. The memory system is already configured in the starter project
2. No additional setup is required for basic usage
3. The core package handles all memory operations internally
4. Custom memory operations can be implemented by extending the base managers
