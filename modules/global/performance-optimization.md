# Performance Optimization Guidelines

## Purpose
Establish performance best practices and optimization strategies to ensure fast, efficient, and scalable applications.

## Core Performance Principles

### 1. Measure First
- **Profile before optimizing**: Never guess performance bottlenecks
- **Establish baselines**: Know your starting point
- **Set performance budgets**: Define acceptable thresholds
- **Monitor continuously**: Track performance over time

### 2. Optimization Priority
1. **Algorithm complexity**: O(n²) to O(n log n)
2. **Database queries**: N+1 problems, missing indexes
3. **Network requests**: Reduce round trips
4. **Memory usage**: Prevent leaks, optimize allocation
5. **Rendering performance**: Minimize repaints/reflows

## Frontend Performance

### Bundle Optimization
```javascript
// Webpack configuration for code splitting
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          priority: 10
        },
        common: {
          minChunks: 2,
          priority: 5,
          reuseExistingChunk: true
        }
      }
    }
  }
};

// Dynamic imports for lazy loading
const HeavyComponent = lazy(() => import('./HeavyComponent'));
```

### React Performance
```javascript
// Memoization for expensive computations
const expensiveValue = useMemo(
  () => computeExpensiveValue(a, b),
  [a, b]
);

// Prevent unnecessary renders
const MemoizedComponent = memo(Component, (prevProps, nextProps) => {
  return prevProps.id === nextProps.id;
});

// Virtualization for large lists
<VirtualList
  height={600}
  itemCount={10000}
  itemSize={50}
  width='100%'
>
  {Row}
</VirtualList>
```

### Asset Optimization
```bash
# Image optimization
imagemin src/images/* --out-dir=dist/images

# WebP conversion with fallback
<picture>
  <source srcset="image.webp" type="image/webp">
  <img src="image.jpg" alt="Description">
</picture>

# Critical CSS inlining
<style>/* Critical CSS inline */</style>
<link rel="preload" href="styles.css" as="style">
```

## Backend Performance

### Database Optimization
```sql
-- Add indexes for frequent queries
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_created ON orders(user_id, created_at);

-- Use EXPLAIN to analyze queries
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 123;

-- Batch operations
INSERT INTO users (name, email) VALUES
  ('User1', 'user1@example.com'),
  ('User2', 'user2@example.com'),
  ('User3', 'user3@example.com');
```

### Query Optimization
```javascript
// Bad - N+1 query problem
const users = await User.findAll();
for (const user of users) {
  user.posts = await Post.findAll({ where: { userId: user.id } });
}

// Good - Eager loading
const users = await User.findAll({
  include: [{
    model: Post,
    as: 'posts'
  }]
});

// Use projection to fetch only needed fields
const users = await User.findAll({
  attributes: ['id', 'name', 'email'],
  raw: true
});
```

### Caching Strategies
```javascript
// Redis caching layer
const getCachedData = async (key) => {
  const cached = await redis.get(key);
  if (cached) return JSON.parse(cached);
  
  const data = await fetchFromDatabase();
  await redis.setex(key, 3600, JSON.stringify(data)); // 1 hour TTL
  return data;
};

// In-memory caching with LRU
const LRU = require('lru-cache');
const cache = new LRU({
  max: 500,
  maxAge: 1000 * 60 * 60 // 1 hour
});

// HTTP caching headers
app.use((req, res, next) => {
  res.set({
    'Cache-Control': 'public, max-age=86400', // 1 day
    'ETag': generateETag(content)
  });
  next();
});
```

## Async Performance

### Concurrent Operations
```javascript
// Bad - Sequential execution
const user = await fetchUser(id);
const posts = await fetchPosts(id);
const comments = await fetchComments(id);

// Good - Parallel execution
const [user, posts, comments] = await Promise.all([
  fetchUser(id),
  fetchPosts(id),
  fetchComments(id)
]);

// Batch processing with concurrency limit
const pLimit = require('p-limit');
const limit = pLimit(5); // Max 5 concurrent

const results = await Promise.all(
  items.map(item => limit(() => processItem(item)))
);
```

### Stream Processing
```javascript
// Process large files with streams
const readStream = fs.createReadStream('large-file.csv');
const writeStream = fs.createWriteStream('output.json');

readStream
  .pipe(csv())
  .pipe(transform(row => processRow(row)))
  .pipe(writeStream);

// Database streaming
const stream = db.query('SELECT * FROM large_table').stream();
stream.on('data', row => {
  // Process row without loading all into memory
});
```

## Memory Management

### Prevent Memory Leaks
```javascript
// Clear references when done
class Component {
  constructor() {
    this.largeData = new Array(1000000);
    this.interval = setInterval(() => this.update(), 1000);
  }
  
  destroy() {
    // Clean up to prevent leaks
    clearInterval(this.interval);
    this.largeData = null;
  }
}

// Remove event listeners
element.addEventListener('click', handler);
// Later...
element.removeEventListener('click', handler);
```

### Efficient Data Structures
```javascript
// Use appropriate data structures
// Map for frequent lookups
const userMap = new Map(users.map(u => [u.id, u]));
const user = userMap.get(userId); // O(1)

// Set for uniqueness checks
const uniqueIds = new Set(items.map(item => item.id));
const isDuplicate = uniqueIds.has(newId); // O(1)

// WeakMap for metadata without preventing GC
const metadata = new WeakMap();
metadata.set(object, { created: Date.now() });
```

## Network Optimization

### API Design
```javascript
// GraphQL for precise data fetching
const query = `
  query GetUser($id: ID!) {
    user(id: $id) {
      id
      name
      posts(limit: 5) {
        title
      }
    }
  }
`;

// Pagination for large datasets
app.get('/api/items', async (req, res) => {
  const { page = 1, limit = 20 } = req.query;
  const offset = (page - 1) * limit;
  
  const items = await Item.findAll({
    limit: parseInt(limit),
    offset: parseInt(offset)
  });
  
  res.json({ items, page, limit });
});
```

### Compression
```javascript
// Enable gzip compression
const compression = require('compression');
app.use(compression());

// Brotli for better compression
app.use(compression({
  brotli: { enabled: true, zlib: {} }
}));
```

## Build Optimization

### Production Builds
```javascript
// Webpack production config
module.exports = {
  mode: 'production',
  optimization: {
    minimize: true,
    sideEffects: false,
    usedExports: true,
    concatenateModules: true
  }
};

// Tree shaking imports
// Bad
import * as utils from './utils';

// Good
import { specificFunction } from './utils';
```

### Preloading and Prefetching
```html
<!-- Preload critical resources -->
<link rel="preload" href="font.woff2" as="font" crossorigin>
<link rel="preload" href="critical.css" as="style">

<!-- Prefetch future navigation -->
<link rel="prefetch" href="/next-page.js">

<!-- DNS prefetch for external domains -->
<link rel="dns-prefetch" href="//api.example.com">
```

## Performance Monitoring

### Web Vitals
```javascript
// Monitor Core Web Vitals
import { getCLS, getFID, getLCP, getFCP, getTTFB } from 'web-vitals';

const sendMetric = ({ name, value }) => {
  // Send to analytics
  analytics.track('web-vital', { metric: name, value });
};

getCLS(sendMetric);
getFID(sendMetric);
getLCP(sendMetric);
getFCP(sendMetric);
getTTFB(sendMetric);
```

### Server Monitoring
```javascript
// Request timing middleware
app.use((req, res, next) => {
  const start = process.hrtime.bigint();
  
  res.on('finish', () => {
    const end = process.hrtime.bigint();
    const duration = Number(end - start) / 1e6; // Convert to ms
    
    logger.info({
      method: req.method,
      url: req.url,
      status: res.statusCode,
      duration
    });
    
    // Alert on slow requests
    if (duration > 1000) {
      logger.warn(`Slow request: ${req.url} took ${duration}ms`);
    }
  });
  
  next();
});
```

## Integration with Claude Code

### Performance Analysis Workflow
1. **Identify slow operations**: Profile and measure
2. **Suggest optimizations**: Based on patterns
3. **Implement improvements**: Apply best practices
4. **Verify results**: Measure impact

### Automated Optimizations
- Suggest code splitting points
- Identify N+1 query problems
- Recommend caching opportunities
- Find unnecessary re-renders
- Detect memory leak patterns

This module ensures applications are built with performance in mind, providing fast and efficient user experiences.