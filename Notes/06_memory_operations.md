# Memory Operations in Eliza

## 1. Basic Memory Operations

### Create Memory
```typescript
// Basic structure of memory creation
interface MemoryContent {
  text?: string;
  metadata?: any;
  timestamp?: number;
}

interface MemoryEntry {
  id: string;
  content: MemoryContent;
  userId: string;
  roomId?: string;
}

// Example: Creating a new memory
await memoryManager.createMemory({
  id: generateUniqueId(),
  content: {
    text: "User preference: prefers technical discussions",
    timestamp: Date.now()
  },
  userId: "user123"
});
```

### Retrieve Memory
```typescript
// Single memory retrieval
const memory = await memoryManager.getMemory(memoryId);

// Multiple memories retrieval
const memories = await memoryManager.getMemories({
  userId: "user123",
  limit: 10,
  offset: 0
});

// Recent memories
const recentMemories = await memoryManager.getRecentMemories({
  userId: "user123",
  roomId: "room456",
  limit: 5
});
```

### Update Memory
```typescript
// Update existing memory
await memoryManager.updateMemory(memoryId, {
  text: "Updated preference: prefers casual discussions",
  timestamp: Date.now()
});

// Partial update
await memoryManager.updateMemoryContent(memoryId, {
  metadata: { lastInteraction: Date.now() }
});
```

### Delete Memory
```typescript
// Remove single memory
await memoryManager.deleteMemory(memoryId);

// Bulk deletion
await memoryManager.deleteMemories({
  userId: "user123",
  olderThan: timestamp
});
```

## 2. State Management

### State Composition
```typescript
// Compose initial state
const state = await runtime.composeState(message, {
  additionalContext: "Custom context"
});

// State structure
interface State {
  userId?: string;
  agentId?: string;
  roomId: string;
  bio: string;
  lore: string;
  recentMessages: string;
  recentMessagesData: Memory[];
  goals?: string;
  goalsData?: Goal[];
}
```

### State Updates
```typescript
// Update message state
const updatedState = await runtime.updateRecentMessageState(state);

// Update with new context
const enrichedState = await runtime.enrichState(state, {
  additionalContext: newContext
});
```

## 3. Context Handling

### Message Context
```typescript
// Get conversation context
const context = await messageManager.getConversationContext({
  userId: "user123",
  roomId: "room456",
  messageLimit: 10
});

// Process context
const processedContext = await runtime.processContext(context, {
  includeMetadata: true,
  filterSystem: false
});
```

### User Context
```typescript
// Get user profile context
const userContext = await descriptionManager.getUserContext(userId);

// Update user context
await descriptionManager.updateUserContext(userId, {
  preferences: newPreferences,
  history: interactionHistory
});
```

### Character Context
```typescript
// Get character knowledge
const characterContext = await loreManager.getCharacterContext(characterId);

// Update character knowledge
await loreManager.updateCharacterContext(characterId, {
  facts: updatedFacts,
  responses: updatedResponses
});
```

## 4. RAG (Retrieval-Augmented Generation)

### Vector Search
```typescript
// Search relevant memories
const relevantMemories = await memoryManager.searchMemories({
  query: "user question about technical topics",
  limit: 5,
  threshold: 0.7
});

// Search with filters
const filteredMemories = await memoryManager.searchMemories({
  query: searchQuery,
  filters: {
    userId: "user123",
    type: "technical"
  }
});
```

### Context Enhancement
```typescript
// Enhance context with relevant memories
const enhancedContext = await runtime.enhanceContextWithRAG(
  baseContext,
  searchQuery,
  {
    maxResults: 3,
    minRelevance: 0.5
  }
);

// Apply RAG to response generation
const response = await runtime.generateResponseWithRAG(
  message,
  enhancedContext
);
```

## 5. Memory Operation Flow

### Conversation Flow
```typescript
// 1. Store incoming message
await messageManager.createMemory({
  id: messageId,
  content: { text: userMessage },
  userId: userId
});

// 2. Get conversation context
const context = await messageManager.getConversationContext({
  userId: userId,
  limit: 10
});

// 3. Enhance with user information
const userInfo = await descriptionManager.getMemory(userId);
const enhancedContext = {
  ...context,
  userProfile: userInfo
};

// 4. Get relevant character knowledge
const relevantLore = await loreManager.searchMemories({
  query: userMessage,
  limit: 3
});

// 5. Compose final state
const state = await runtime.composeState(message, {
  context: enhancedContext,
  lore: relevantLore
});
```

### Memory Lifecycle
1. Creation
2. Access
3. Updates
4. Context Integration
5. RAG Enhancement
6. Cleanup

## 6. Best Practices

1. Memory Operations
   - Generate unique IDs for memories
   - Include timestamps
   - Handle concurrent operations
   - Implement proper error handling

2. State Management
   - Keep states immutable
   - Update atomically
   - Cache frequently accessed states
   - Clean up old states

3. Context Handling
   - Limit context size
   - Prioritize recent context
   - Filter irrelevant information
   - Optimize for performance

4. RAG Implementation
   - Set appropriate thresholds
   - Optimize search parameters
   - Cache search results
   - Monitor performance
