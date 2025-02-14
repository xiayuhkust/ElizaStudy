# Eliza Agents and Memory System Study

## Memory System Overview

### Core Components
1. **Database Layer**
   - Supports PostgreSQL and SQLite
   - Configurable through environment variables
   - Provides persistent storage for all memory types

2. **Memory Managers**
   - `messageManager`: Handles conversation history
   - `descriptionManager`: Manages user profiles and descriptions
   - `loreManager`: Maintains static character knowledge

### Memory Types
1. **Message History**
   - Purpose: Maintains conversation continuity
   - Storage: Recent messages for context
   - Implementation: Through messageManager

2. **Factual Memory**
   - Purpose: Stores user-specific information
   - Storage: Long-term user preferences and details
   - Implementation: Through descriptionManager

3. **Knowledge Base**
   - Purpose: Static character knowledge
   - Storage: Pre-defined responses and character lore
   - Implementation: Through loreManager

4. **Relationship Tracking**
   - Purpose: User-agent interaction history
   - Storage: Interaction frequency and sentiment
   - Implementation: Combined through database layer

5. **RAG Integration**
   - Purpose: Contextual memory retrieval
   - Method: Vector-based similarity search
   - Usage: Retrieves relevant memory based on conversation context

## Implementation Details
- The system uses @elizaos/core for core functionality
- Memory persistence is handled through database adapters
- Flexible storage options (PostgreSQL/SQLite) for different deployment scenarios
- Stateless chat interface with state management handled by core package
