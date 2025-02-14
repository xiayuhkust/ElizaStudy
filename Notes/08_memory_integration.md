# Memory Integration in Eliza

## 1. Memory and Agent Response Flow

### Response Generation Process
```typescript
interface ResponseContext {
  message: string;
  state: State;
  memories: {
    recent: Memory[];
    user: UserProfile;
    lore: LoreContent[];
  };
}

// Complete flow of how memory affects responses
async function generateResponse(input: string, userId: string): Promise<string> {
  // 1. Gather Memory Context
  const messageContext = await messageManager.getRecentMemories({
    userId,
    limit: 5
  });
  
  const userProfile = await descriptionManager.getMemory(userId);
  
  const relevantLore = await loreManager.searchMemories({
    query: input,
    limit: 3
  });

  // 2. Compose State
  const state = await runtime.composeState(input, {
    recentMessages: messageContext,
    userProfile: userProfile,
    relevantLore: relevantLore
  });

  // 3. Process State
  const processedState = await runtime.processState(state);

  // 4. Generate Response
  const response = await runtime.generateResponseFromState(processedState);

  return response;
}
```

## 2. State Composition Process

### State Structure
```typescript
interface State {
  // Core Identification
  userId?: UUID;
  agentId?: UUID;
  roomId: UUID;
  
  // Character Information
  bio: string;
  lore: string;
  agentName?: string;
  senderName?: string;
  
  // Interaction Context
  actors: string;
  actorsData?: Actor[];
  recentMessages: string;
  recentMessagesData: Memory[];
  
  // Goals and Actions
  goals?: string;
  goalsData?: Goal[];
  actions?: string;
  actionNames?: string;
  providers?: string;
}
```

### State Composition Flow
```typescript
async function composeState(message: string, context: Context): Promise<State> {
  // 1. Initialize Base State
  const baseState = {
    userId: context.userId,
    agentId: runtime.agentId,
    roomId: context.roomId,
    timestamp: Date.now()
  };

  // 2. Add Character Information
  const characterInfo = await getCharacterInfo();
  
  // 3. Add Recent Messages
  const recentMessages = await getRecentMessages(context);
  
  // 4. Add User Profile
  const userProfile = await getUserProfile(context.userId);
  
  // 5. Add Goals and Actions
  const goals = await getActiveGoals();
  const actions = await getAvailableActions();

  // 6. Compose Final State
  return {
    ...baseState,
    ...characterInfo,
    recentMessages,
    recentMessagesData: recentMessages,
    userProfile,
    goals,
    actions
  };
}
```

## 3. Memory Cleanup and Maintenance

### Cleanup Strategies
1. **Time-based Cleanup**
```typescript
async function cleanupOldMemories() {
  const cutoffDate = Date.now() - (30 * 24 * 60 * 60 * 1000); // 30 days
  
  await Promise.all([
    messageManager.deleteMemories({ olderThan: cutoffDate }),
    descriptionManager.archiveInactiveUsers(cutoffDate),
    loreManager.cleanupUnusedLore(cutoffDate)
  ]);
}
```

2. **Usage-based Cleanup**
```typescript
async function cleanupUnusedMemories() {
  const unusedThreshold = Date.now() - (90 * 24 * 60 * 60 * 1000); // 90 days
  
  const unusedMemories = await messageManager.findUnusedMemories(unusedThreshold);
  await messageManager.archiveMemories(unusedMemories);
}
```

3. **Size-based Cleanup**
```typescript
async function enforceMemoryLimits() {
  const maxMemories = 1000;
  const currentCount = await messageManager.getMemoryCount();
  
  if (currentCount > maxMemories) {
    const excessCount = currentCount - maxMemories;
    await messageManager.removeOldestMemories(excessCount);
  }
}
```

## 4. Memory Integration Points

### 1. Message Processing
```typescript
async function processMessage(message: string, userId: string) {
  // 1. Store Message
  await messageManager.createMemory({
    id: generateUniqueId(),
    content: { text: message },
    userId
  });

  // 2. Update User Profile
  await updateUserProfile(userId, message);

  // 3. Process Response
  const response = await generateResponse(message, userId);

  // 4. Store Response
  await messageManager.createMemory({
    id: generateUniqueId(),
    content: { text: response },
    userId: runtime.agentId
  });

  return response;
}
```

### 2. State Updates
```typescript
async function updateState(state: State, newData: any) {
  // 1. Validate Update
  validateStateUpdate(newData);

  // 2. Merge Changes
  const updatedState = {
    ...state,
    ...newData,
    timestamp: Date.now()
  };

  // 3. Process State
  return runtime.processState(updatedState);
}
```

### 3. Context Management
```typescript
async function manageContext(userId: string) {
  // 1. Get Recent Context
  const recentContext = await messageManager.getRecentMemories({
    userId,
    limit: 10
  });

  // 2. Get User Profile
  const userProfile = await descriptionManager.getMemory(userId);

  // 3. Get Relevant Knowledge
  const relevantKnowledge = await loreManager.getRelevantLore(recentContext);

  // 4. Compose Context
  return {
    messages: recentContext,
    profile: userProfile,
    knowledge: relevantKnowledge
  };
}
```

## 5. Best Practices

### Memory Management
1. Regular cleanup of old memories
2. Archive instead of delete when possible
3. Maintain memory size limits
4. Index frequently accessed memories

### State Management
1. Keep states immutable
2. Validate state updates
3. Maintain state history
4. Clean up old states

### Performance Optimization
1. Cache frequently accessed data
2. Use batch operations for cleanup
3. Implement proper indexing
4. Monitor memory usage

### Error Handling
1. Validate all memory operations
2. Implement retry mechanisms
3. Log errors appropriately
4. Provide fallback responses
