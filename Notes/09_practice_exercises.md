# Eliza Memory System Practice Exercises

## Exercise 1: Basic Memory Operations

### Task: Implement a Basic Conversation Memory System

**Requirements:**
1. Store user messages
2. Retrieve recent conversation history
3. Update user preferences based on interactions
4. Clean up old messages

**Template Code:**
```typescript
// 1. Initialize Memory Managers
const messageManager = runtime.getMemoryManager("messages");
const descriptionManager = runtime.getMemoryManager("descriptions");

// 2. Implement these functions:
async function storeUserMessage(userId: string, message: string) {
  // TODO: Store the message with appropriate metadata
}

async function getConversationHistory(userId: string, limit: number) {
  // TODO: Retrieve recent messages for the user
}

async function updateUserPreferences(userId: string, interaction: Interaction) {
  // TODO: Update user preferences based on interaction
}

async function cleanupOldMessages(olderThan: number) {
  // TODO: Remove messages older than the specified timestamp
}
```

**Expected Output:**
- Stored messages should include: timestamp, user ID, content
- Conversation history should be ordered by timestamp
- User preferences should accumulate over time
- Old messages should be properly cleaned up

## Exercise 2: State Management

### Task: Implement State Composition and Updates

**Requirements:**
1. Create initial state from user input
2. Update state with new messages
3. Handle state transitions
4. Maintain state consistency

**Template Code:**
```typescript
interface State {
  userId: string;
  messages: Message[];
  preferences: UserPreferences;
  context: Context;
}

// Implement these functions:
async function createInitialState(userId: string) {
  // TODO: Create and return initial state
}

async function updateStateWithMessage(state: State, message: Message) {
  // TODO: Update state with new message
}

async function handleStateTransition(state: State, event: StateEvent) {
  // TODO: Handle state transition based on event
}

async function validateState(state: State) {
  // TODO: Validate state consistency
}
```

**Expected Output:**
- Initial state should contain all required fields
- State updates should be immutable
- State transitions should be atomic
- Invalid states should be rejected

## Exercise 3: Context Retrieval

### Task: Implement Context-Aware Response System

**Requirements:**
1. Retrieve relevant conversation context
2. Get user profile information
3. Find applicable character knowledge
4. Combine contexts for response generation

**Template Code:**
```typescript
// Implement these functions:
async function getRelevantContext(message: string, userId: string) {
  // TODO: Get conversation context relevant to message
}

async function getUserContext(userId: string) {
  // TODO: Get user profile and preferences
}

async function getCharacterKnowledge(context: Context) {
  // TODO: Find relevant character knowledge
}

async function combineContexts(
  messageContext: Context,
  userContext: UserContext,
  characterContext: CharacterContext
) {
  // TODO: Combine contexts for response generation
}
```

**Expected Output:**
- Context should be relevant to current conversation
- User context should reflect preferences
- Character knowledge should be appropriate
- Combined context should be well-structured

## Exercise 4: RAG Implementation

### Task: Implement Retrieval-Augmented Generation System

**Requirements:**
1. Implement vector search
2. Score and rank results
3. Enhance context with retrieved information
4. Generate response using enhanced context

**Template Code:**
```typescript
// Implement these functions:
async function performVectorSearch(query: string, limit: number) {
  // TODO: Search for relevant memories
}

async function rankSearchResults(results: SearchResult[]) {
  // TODO: Score and rank search results
}

async function enhanceContext(
  baseContext: Context,
  searchResults: RankedResult[]
) {
  // TODO: Enhance context with search results
}

async function generateEnhancedResponse(
  query: string,
  enhancedContext: Context
) {
  // TODO: Generate response using enhanced context
}
```

**Expected Output:**
- Search results should be relevant to query
- Results should be properly ranked
- Context enhancement should be meaningful
- Generated responses should use enhanced context

## Exercise 5: Integration Challenge

### Task: Build Complete Memory-Aware Chat System

**Requirements:**
1. Handle incoming messages
2. Maintain conversation context
3. Update user profiles
4. Generate contextual responses
5. Manage memory cleanup

**Template Code:**
```typescript
class MemoryAwareChatSystem {
  private messageManager: IMemoryManager;
  private descriptionManager: IMemoryManager;
  private loreManager: IMemoryManager;

  constructor(runtime: AgentRuntime) {
    // TODO: Initialize managers
  }

  async handleMessage(message: string, userId: string) {
    // TODO: Implement message handling
  }

  async updateUserProfile(userId: string, interaction: Interaction) {
    // TODO: Implement profile updates
  }

  async generateResponse(context: Context) {
    // TODO: Implement response generation
  }

  async performMaintenance() {
    // TODO: Implement memory maintenance
  }
}
```

**Expected Output:**
- System should handle messages properly
- Context should be maintained accurately
- User profiles should be updated
- Responses should be contextual
- Memory should be properly maintained

## Evaluation Criteria

For each exercise, evaluate:
1. **Functionality**
   - All requirements implemented
   - Proper error handling
   - Expected outputs achieved

2. **Code Quality**
   - Clear structure
   - Proper typing
   - Error handling
   - Documentation

3. **Memory Management**
   - Efficient use of memory
   - Proper cleanup
   - Resource management

4. **Integration**
   - Components work together
   - State consistency
   - Error propagation

## Notes for Practice

1. **Environment Setup**
   - Use TypeScript for better type safety
   - Initialize proper memory managers
   - Set up error handling

2. **Testing Approach**
   - Test each component individually
   - Verify integration points
   - Check error cases
   - Validate memory usage

3. **Common Pitfalls**
   - Not handling errors properly
   - Forgetting to clean up
   - Ignoring state consistency
   - Missing context validation
