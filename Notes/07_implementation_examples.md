# Eliza Memory System Implementation Examples

## 1. Basic Conversation Flow Example

This example demonstrates how the three memory managers work together in a typical conversation:

```typescript
// Initialize memory managers
const messageManager = runtime.getMemoryManager("messages");
const descriptionManager = runtime.getMemoryManager("descriptions");
const loreManager = runtime.getMemoryManager("lore");

async function handleConversation(userMessage: string, userId: string) {
  // 1. Store the incoming message
  const messageId = generateUniqueId();
  await messageManager.createMemory({
    id: messageId,
    content: {
      text: userMessage,
      timestamp: Date.now()
    },
    userId: userId
  });

  // 2. Get or create user profile
  let userProfile = await descriptionManager.getMemory(userId);
  if (!userProfile) {
    await descriptionManager.createMemory({
      id: userId,
      content: {
        profile: {
          firstInteraction: Date.now(),
          preferences: {}
        }
      },
      userId: userId
    });
  }

  // 3. Get conversation context
  const recentMessages = await messageManager.getRecentMemories({
    userId: userId,
    limit: 5
  });

  // 4. Use RAG to find relevant character knowledge
  const relevantLore = await loreManager.searchMemories({
    query: userMessage,
    limit: 3
  });

  // 5. Compose state for response generation
  const state = await runtime.composeState(userMessage, {
    recentMessages,
    userProfile,
    relevantLore
  });

  return state;
}
```

## 2. User Preference Learning Example

This example shows how to track and update user preferences over time:

```typescript
async function updateUserPreferences(userId: string, interaction: Interaction) {
  const descriptionManager = runtime.getMemoryManager("descriptions");
  
  // Get current user profile
  const userProfile = await descriptionManager.getMemory(userId);
  
  // Extract preferences from interaction
  const newPreferences = analyzeInteraction(interaction);
  
  // Update user profile with new preferences
  await descriptionManager.updateMemory(userId, {
    ...userProfile,
    content: {
      ...userProfile.content,
      preferences: {
        ...userProfile.content.preferences,
        ...newPreferences
      },
      lastInteraction: Date.now()
    }
  });
}

async function analyzeInteraction(interaction: Interaction) {
  // Example preference extraction
  return {
    topicPreferences: interaction.topics,
    responseStyle: interaction.style,
    technicalLevel: interaction.complexity
  };
}
```

## 3. Context-Aware Response Generation

This example demonstrates how to use all memory types for generating contextual responses:

```typescript
async function generateContextualResponse(
  message: string,
  userId: string,
  roomId: string
) {
  // 1. Get memory managers
  const messageManager = runtime.getMemoryManager("messages");
  const descriptionManager = runtime.getMemoryManager("descriptions");
  const loreManager = runtime.getMemoryManager("lore");

  // 2. Gather context from all sources
  const [recentMessages, userProfile, relevantLore] = await Promise.all([
    messageManager.getRecentMemories({
      userId,
      roomId,
      limit: 10
    }),
    descriptionManager.getMemory(userId),
    loreManager.searchMemories({
      query: message,
      limit: 5
    })
  ]);

  // 3. Compose enhanced context
  const context = {
    currentMessage: message,
    conversationHistory: recentMessages,
    userPreferences: userProfile?.content?.preferences,
    relevantKnowledge: relevantLore
  };

  // 4. Generate response using context
  const response = await runtime.generateResponse(context);

  // 5. Store the response
  await messageManager.createMemory({
    id: generateUniqueId(),
    content: {
      text: response,
      timestamp: Date.now()
    },
    userId,
    roomId
  });

  return response;
}
```

## 4. Memory Cleanup and Maintenance

Example of managing memory lifecycle and cleanup:

```typescript
async function performMemoryMaintenance() {
  const messageManager = runtime.getMemoryManager("messages");
  const descriptionManager = runtime.getMemoryManager("descriptions");

  // 1. Clean up old messages
  const oldMessagesCutoff = Date.now() - (30 * 24 * 60 * 60 * 1000); // 30 days
  await messageManager.deleteMemories({
    olderThan: oldMessagesCutoff
  });

  // 2. Archive inactive user profiles
  const inactivityCutoff = Date.now() - (90 * 24 * 60 * 60 * 1000); // 90 days
  const inactiveUsers = await descriptionManager.findMemories({
    filter: {
      'content.lastInteraction': {
        $lt: inactivityCutoff
      }
    }
  });

  // 3. Archive or update inactive profiles
  for (const user of inactiveUsers) {
    await descriptionManager.updateMemory(user.id, {
      ...user,
      content: {
        ...user.content,
        status: 'archived'
      }
    });
  }
}
```

## 5. RAG Implementation Example

Demonstrates how to implement Retrieval-Augmented Generation:

```typescript
async function enhanceResponseWithRAG(
  query: string,
  baseContext: Context
) {
  const loreManager = runtime.getMemoryManager("lore");
  
  // 1. Perform vector search
  const relevantMemories = await loreManager.searchMemories({
    query,
    limit: 5,
    threshold: 0.7
  });

  // 2. Process and rank results
  const rankedResults = relevantMemories.map(memory => ({
    content: memory.content,
    relevance: calculateRelevance(query, memory.content)
  })).sort((a, b) => b.relevance - a.relevance);

  // 3. Enhance context with relevant information
  const enhancedContext = {
    ...baseContext,
    relevantKnowledge: rankedResults.map(r => r.content)
  };

  // 4. Generate response with enhanced context
  return runtime.generateResponse(enhancedContext);
}

function calculateRelevance(query: string, content: any): number {
  // Example relevance calculation
  // In practice, this would use more sophisticated matching
  return 0.5 + (Math.random() * 0.5); // Simplified example
}
```

## Best Practices from Examples

1. **Memory Management**
   - Always include timestamps
   - Use unique IDs
   - Handle errors gracefully
   - Clean up old data regularly

2. **Context Handling**
   - Gather context from all relevant sources
   - Prioritize recent and relevant information
   - Consider user preferences
   - Maintain conversation flow

3. **Performance**
   - Use Promise.all for parallel operations
   - Implement proper caching
   - Limit result sets
   - Clean up unused data

4. **Error Handling**
   - Validate input data
   - Handle missing profiles
   - Provide fallback responses
   - Log errors appropriately
