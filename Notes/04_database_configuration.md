# Database Configuration in Eliza

## 1. Database Initialization

The database system in Eliza is designed to be flexible, supporting both PostgreSQL and SQLite. The initialization is handled automatically through database adapters.

### Core Components

1. **Database Adapters**
   - PostgresDatabaseAdapter from @elizaos/adapter-postgres
   - SqliteDatabaseAdapter from @elizaos/adapter-sqlite

2. **Configuration Files**
   - .env file for environment variables
   - Default configuration in database/index.ts

### Environment Variables

```env
# PostgreSQL Configuration
POSTGRES_URL=postgresql://user:password@localhost:5432/dbname

# SQLite Configuration (Optional)
SQLITE_FILE=/path/to/db.sqlite
```

### Initialization Process

```typescript
export function initializeDatabase(dataDir: string) {
  if (process.env.POSTGRES_URL) {
    // PostgreSQL initialization
    const db = new PostgresDatabaseAdapter({
      connectionString: process.env.POSTGRES_URL,
    });
    return db;
  } else {
    // SQLite fallback
    const filePath = 
      process.env.SQLITE_FILE ?? path.resolve(dataDir, "db.sqlite");
    const db = new SqliteDatabaseAdapter(new Database(filePath));
    return db;
  }
}
```

## 2. Database Selection Logic

1. **Priority Order**
   - First checks for POSTGRES_URL environment variable
   - Falls back to SQLite if PostgreSQL is not configured
   - Uses default SQLite file path if SQLITE_FILE is not set

2. **Automatic Configuration**
   - No manual database setup required
   - Adapters handle table creation and schema management
   - Database connection is managed by the runtime

## 3. Database Integration

### Runtime Integration
```typescript
// In startAgent function
const db = initializeDatabase(dataDir);
await db.init();

const cache = initializeDbCache(character, db);
const runtime = createAgent(character, db, cache, token);
```

### Cache Management
```typescript
// Cache initialization with database adapter
export function initializeDbCache(
  character: Character,
  db: IDatabaseCacheAdapter
) {
  const cache = new CacheManager(
    new DbCacheAdapter(db, character.id)
  );
  return cache;
}
```

## 4. Key Features

1. **Automatic Setup**
   - No manual database creation needed
   - Schema management handled by adapters
   - Automatic table creation and updates

2. **Flexible Storage**
   - PostgreSQL for production environments
   - SQLite for development and testing
   - Easy switching between databases

3. **Cache Integration**
   - Database-backed caching system
   - Character-specific cache isolation
   - Automatic cache management

## 5. Best Practices

1. **Environment Configuration**
   - Always use environment variables for database credentials
   - Keep sensitive information in .env file
   - Use .env.example as a template

2. **Database Selection**
   - Use PostgreSQL for production deployments
   - Use SQLite for development and testing
   - Always test with the same database type as production

3. **Error Handling**
   - Database errors are logged through elizaLogger
   - Connection issues trigger automatic retries
   - Failed database operations are reported to runtime

## 6. Implementation Notes

1. **PostgreSQL Adapter**
   - Handles connection pooling
   - Manages concurrent connections
   - Provides transaction support

2. **SQLite Adapter**
   - File-based storage
   - Automatic file creation
   - Suitable for single-instance deployments

3. **Common Features**
   - ACID compliance
   - Automatic schema management
   - Query optimization
