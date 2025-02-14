# Eliza Memory System Implementation Guide

## 1. System Architecture

### Database Layer
```typescript
// Database initialization (handled automatically)
const db = initializeDatabase(dataDir);
await db.init();

// Cache initialization
const cache = initializeDbCache(character, db);
```

### Memory Managers
The system uses three specialized memory managers:

1. **Message Manager** (`messageManager`)
   - Purpose: Stores conversation history
   - Usage: Short-term context and conversation flow
   ```typescript
   const messageManager = runtime.getMemoryManager("messages");
   await messageManager.createMemory({
     id: messageId,
     content: { text: "Message content" },
     userId: userId,
     roomId: roomId,
   });
   ```

2. **Description Manager** (`descriptionManager`)
   - Purpose: User profiles and preferences
   - Usage: Long-term user information
   ```typescript
   const descriptionManager = runtime.getMemoryManager("descriptions");
   await descriptionManager.createMemory({
     id: userId,
     content: { 
       preferences: userPreferences,
       profile: userProfile
     },
     userId: userId
   });
   ```

3. **Lore Manager** (`loreManager`)
   - Purpose: Static character knowledge
   - Usage: Character personality and responses
   ```typescript
   const loreManager = runtime.getMemoryManager("lore");
   await loreManager.createMemory({
     id: "character_knowledge",
     content: { 
       facts: characterFacts,
       responses: commonResponses
     },
     characterId: agentId
   });
   ```

## 2. Memory Operations

### Basic Operations
1. **Create Memory**
```typescript
await memoryManager.createMemory({
  id: uniqueId,
  content: memoryContent,
  userId: userId
});
```

2. **Retrieve Memory**
```typescript
const memory = await memoryManager.getMemory(memoryId);
```

3. **Update Memory**
```typescript
await memoryManager.updateMemory(memoryId, newContent);
```

4. **Delete Memory**
```typescript
await memoryManager.deleteMemory(memoryId);
```

### Advanced Features

1. **RAG (Retrieval-Augmented Generation)**
```typescript
// Retrieve relevant memories based on context
const relevantMemories = await memoryManager.searchMemories({
  query: currentContext,
  limit: 5
});
```

2. **Relationship Tracking**
```typescript
// Store interaction data
await descriptionManager.createMemory({
  id: `interaction_${userId}_${timestamp}`,
  content: {
    type: "interaction",
    sentiment: interactionSentiment,
    frequency: interactionCount
  },
  userId: userId
});
```

## 3. Best Practices

1. **Memory Management**
   - Clean up old memories periodically
   - Use appropriate managers for different data types
   - Consider memory limits when storing data

2. **State Management**
   - Keep state immutable where possible
   - Use `composeState` for initial state
   - Use `updateRecentMessageState` for updates

3. **Database Configuration**
   - PostgreSQL: Set `POSTGRES_URL` in .env
   - SQLite: Set `SQLITE_FILE` or use default
   - Both adapters handle setup automatically

## 4. Example Usage Flow

```typescript
// 1. Initialize runtime with memory system
const runtime = new AgentRuntime({
  databaseAdapter: db,
  cacheManager: cache,
  // ... other config
});

// 2. Use memory managers
const messageManager = runtime.getMemoryManager("messages");
const descriptionManager = runtime.getMemoryManager("descriptions");
const loreManager = runtime.getMemoryManager("lore");

// 3. Store conversation context
await messageManager.createMemory({
  id: messageId,
  content: { text: userMessage },
  userId: userId
});

// 4. Retrieve user context
const userProfile = await descriptionManager.getMemory(userId);

// 5. Get relevant character knowledge
const relevantLore = await loreManager.searchMemories({
  query: userMessage,
  limit: 3
});

// 6. Compose response using context
const state = await runtime.composeState(message, {
  additionalContext: relevantLore
});
```
