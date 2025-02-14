# Memory Managers in Eliza

## 1. Overview of Memory Manager Types

Eliza uses three specialized memory managers to handle different types of data:

### Message Manager
Purpose: Handles conversation history and context
```typescript
interface MessageMemory {
  id: string;
  content: {
    text: string;
    timestamp?: number;
    metadata?: any;
  };
  userId: string;
  roomId?: string;
}
```

Key Features:
- Stores recent conversation messages
- Maintains conversation context
- Enables natural dialogue flow
- Prevents repetitive responses

### Description Manager
Purpose: Manages user profiles and preferences
```typescript
interface DescriptionMemory {
  id: string;
  content: {
    profile?: UserProfile;
    preferences?: UserPreferences;
    interactions?: InteractionHistory;
  };
  userId: string;
}
```

Key Features:
- Stores user-specific information
- Tracks user preferences
- Maintains relationship data
- Enables personalized interactions

### Lore Manager
Purpose: Handles static character knowledge
```typescript
interface LoreMemory {
  id: string;
  content: {
    facts: string[];
    responses: Record<string, string>;
    personality?: CharacterTraits;
  };
  characterId: string;
}
```

Key Features:
- Maintains character consistency
- Stores predefined responses
- Manages character personality
- Provides knowledge base access

## 2. Memory Manager Initialization

```typescript
// In AgentRuntime initialization
const runtime = new AgentRuntime({
  messageManager: new MemoryManager({
    type: "messages",
    databaseAdapter: db,
    cacheManager: cache
  }),
  descriptionManager: new MemoryManager({
    type: "descriptions",
    databaseAdapter: db,
    cacheManager: cache
  }),
  loreManager: new MemoryManager({
    type: "lore",
    databaseAdapter: db,
    cacheManager: cache
  })
});
```

## 3. Common Operations

### Basic Memory Operations
```typescript
// Create memory
await memoryManager.createMemory({
  id: uniqueId,
  content: memoryContent,
  userId: userId
});

// Retrieve memory
const memory = await memoryManager.getMemory(memoryId);

// Update memory
await memoryManager.updateMemory(memoryId, newContent);

// Delete memory
await memoryManager.deleteMemory(memoryId);
```

### Specialized Operations

1. Message Manager
```typescript
// Store conversation message
await messageManager.createMemory({
  id: messageId,
  content: {
    text: messageText,
    timestamp: Date.now()
  },
  userId: userId,
  roomId: roomId
});

// Get recent conversation
const recentMessages = await messageManager.getRecentMemories({
  userId: userId,
  roomId: roomId,
  limit: 10
});
```

2. Description Manager
```typescript
// Update user profile
await descriptionManager.updateMemory(userId, {
  profile: updatedProfile,
  preferences: userPreferences
});

// Get user information
const userInfo = await descriptionManager.getMemory(userId);
```

3. Lore Manager
```typescript
// Access character knowledge
const characterKnowledge = await loreManager.getMemory(characterId);

// Search relevant responses
const relevantLore = await loreManager.searchMemories({
  query: context,
  limit: 5
});
```

## 4. Memory Integration

### State Composition
```typescript
const state = await runtime.composeState(message, {
  recentMessages: await messageManager.getRecentMemories(),
  userProfile: await descriptionManager.getMemory(userId),
  characterLore: await loreManager.getRelevantLore(context)
});
```

### Context Management
```typescript
// Update conversation context
await runtime.updateRecentMessageState(state);

// Get contextual memory
const contextualMemory = await runtime.getContextualMemory(message);
```

## 5. Best Practices

1. Memory Usage
   - Use appropriate manager for data type
   - Clean up old memories regularly
   - Cache frequently accessed data
   - Handle memory limits appropriately

2. State Management
   - Keep state immutable
   - Use proper state composition
   - Update state consistently
   - Handle state transitions carefully

3. Performance Optimization
   - Use memory limits
   - Implement proper cleanup
   - Cache effectively
   - Handle concurrent access

## 6. Implementation Notes

1. Data Persistence
   - All memories are stored in database
   - Cache layer improves performance
   - Automatic cleanup of old data
   - Transaction support for consistency

2. Memory Lifecycle
   - Creation: Through manager.createMemory()
   - Access: Through manager.getMemory()
   - Update: Through manager.updateMemory()
   - Deletion: Through manager.deleteMemory()
   - Cleanup: Automatic based on configuration

3. Integration Points
   - Database layer for persistence
   - Cache layer for performance
   - State management for context
   - Runtime for coordination
