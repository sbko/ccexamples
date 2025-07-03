# Debugging Protocols and Troubleshooting

## Purpose
Establish systematic debugging approaches and troubleshooting methodologies to efficiently identify and resolve issues.

## Core Debugging Principles

### 1. Systematic Approach
- **Reproduce consistently**: Ensure the issue can be reproduced
- **Isolate the problem**: Narrow down to smallest failing case
- **Form hypothesis**: Based on evidence, not assumptions
- **Test hypothesis**: Verify with targeted experiments
- **Document findings**: Record what works and what doesn't

### 2. Debugging Hierarchy
1. **Check the obvious**: Typos, missing imports, syntax errors
2. **Verify assumptions**: Question everything assumed to work
3. **Binary search**: Divide problem space in half repeatedly
4. **Trace execution**: Follow code path step by step
5. **Examine state**: Inspect variables at key points

## Debugging Tools and Techniques

### Console Debugging
```javascript
// Strategic console logging
console.log('=== ENTERING FUNCTION ===', functionName);
console.log('Input params:', { param1, param2 });
console.log('Current state:', state);

// Group related logs
console.group('User Authentication Flow');
console.log('1. Checking credentials');
console.log('2. Validating token');
console.log('3. Loading user data');
console.groupEnd();

// Conditional debugging
const DEBUG = process.env.NODE_ENV === 'development';
DEBUG && console.log('Debug info:', data);

// Stack traces
console.trace('Execution path to this point');
```

### Debugger Statements
```javascript
// Browser/Node.js debugger
function problematicFunction(data) {
  debugger; // Execution will pause here
  const result = processData(data);
  return result;
}

// Conditional breakpoints
if (user.id === problematicUserId) {
  debugger;
}
```

### Error Tracking
```javascript
// Enhanced error information
class DetailedError extends Error {
  constructor(message, code, context) {
    super(message);
    this.code = code;
    this.context = context;
    this.timestamp = new Date().toISOString();
    
    // Capture stack trace
    Error.captureStackTrace(this, this.constructor);
  }
}

// Usage
throw new DetailedError(
  'Failed to process payment',
  'PAYMENT_FAILED',
  { userId, amount, paymentMethod }
);
```

## Development Environment Debugging

### Source Maps
```javascript
// webpack.config.js
module.exports = {
  devtool: 'source-map', // Full source maps for development
  // or 'cheap-module-source-map' for faster builds
};

// tsconfig.json
{
  "compilerOptions": {
    "sourceMap": true,
    "inlineSources": true
  }
}
```

### Hot Module Replacement
```javascript
// Preserve state during debugging
if (module.hot) {
  module.hot.accept('./module', () => {
    // Re-apply module while preserving state
    const NextModule = require('./module');
    updateModule(NextModule);
  });
}
```

## Network Debugging

### Request Inspection
```javascript
// Axios interceptors for debugging
axios.interceptors.request.use(request => {
  console.log('Starting Request:', request);
  request.metadata = { startTime: new Date() };
  return request;
});

axios.interceptors.response.use(response => {
  const duration = new Date() - response.config.metadata.startTime;
  console.log(`Request to ${response.config.url} took ${duration}ms`);
  return response;
}, error => {
  console.error('Request failed:', error.config);
  return Promise.reject(error);
});
```

### Proxy for API Debugging
```javascript
// setupProxy.js (Create React App)
module.exports = function(app) {
  app.use(
    '/api',
    createProxyMiddleware({
      target: 'http://localhost:5000',
      changeOrigin: true,
      onProxyReq: (proxyReq, req, res) => {
        console.log('Proxying:', req.method, req.url);
      },
      onProxyRes: (proxyRes, req, res) => {
        console.log('Response:', proxyRes.statusCode);
      }
    })
  );
};
```

## Performance Debugging

### Performance Profiling
```javascript
// Measure execution time
console.time('expensive-operation');
performExpensiveOperation();
console.timeEnd('expensive-operation');

// Performance marks
performance.mark('myFunction-start');
myFunction();
performance.mark('myFunction-end');
performance.measure('myFunction', 'myFunction-start', 'myFunction-end');

// Get measurements
const measures = performance.getEntriesByType('measure');
console.table(measures);
```

### Memory Debugging
```javascript
// Memory usage monitoring
const used = process.memoryUsage();
console.log('Memory Usage:');
for (let key in used) {
  console.log(`${key}: ${Math.round(used[key] / 1024 / 1024 * 100) / 100} MB`);
}

// Detect memory leaks
let initialMemory = process.memoryUsage().heapUsed;
// ... run suspected code ...
let finalMemory = process.memoryUsage().heapUsed;
console.log('Memory increase:', finalMemory - initialMemory);
```

## React Debugging

### React DevTools
```javascript
// Component debugging helpers
export const DebugComponent = ({ data, children }) => {
  useEffect(() => {
    console.log('Component mounted with data:', data);
    return () => console.log('Component unmounting');
  }, []);

  useEffect(() => {
    console.log('Data changed:', data);
  }, [data]);

  return process.env.NODE_ENV === 'development' ? (
    <div style={{ border: '1px solid red', padding: '10px' }}>
      <pre>{JSON.stringify(data, null, 2)}</pre>
      {children}
    </div>
  ) : children;
};
```

### Why Did You Render
```javascript
// wdyr.js
import React from 'react';

if (process.env.NODE_ENV === 'development') {
  const whyDidYouRender = require('@welldone-software/why-did-you-render');
  whyDidYouRender(React, {
    trackAllPureComponents: true,
    trackHooks: true,
    logOwnerReasons: true
  });
}
```

## Database Debugging

### Query Logging
```sql
-- Enable query logging in PostgreSQL
SET log_statement = 'all';
SET log_duration = on;

-- MySQL slow query log
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;
```

### ORM Debugging
```javascript
// Sequelize logging
const sequelize = new Sequelize({
  logging: (msg) => console.log(`[SQL] ${msg}`),
  benchmark: true
});

// Mongoose debugging
mongoose.set('debug', (collectionName, method, query, doc) => {
  console.log(`${collectionName}.${method}`, JSON.stringify(query), doc);
});
```

## Production Debugging

### Feature Flags for Debugging
```javascript
const debugFeatures = {
  verboseLogging: process.env.FEATURE_VERBOSE_LOGGING === 'true',
  showDebugPanel: process.env.FEATURE_DEBUG_PANEL === 'true',
  slowQueryWarning: process.env.FEATURE_SLOW_QUERY_WARNING === 'true'
};

if (debugFeatures.verboseLogging) {
  // Enable detailed logging
}
```

### Remote Debugging
```javascript
// Conditional remote debugging
if (user.isAdmin && url.searchParams.get('debug') === 'true') {
  window.__DEBUG__ = {
    state: store.getState(),
    config: appConfig,
    version: process.env.REACT_APP_VERSION
  };
}
```

## Debugging Workflows

### Bug Report Template
```markdown
## Bug Description
[Clear description of the issue]

## Steps to Reproduce
1. Go to...
2. Click on...
3. See error...

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Environment
- OS: [e.g., macOS 12.0]
- Browser: [e.g., Chrome 95]
- Version: [e.g., 1.2.3]

## Additional Context
[Any other relevant information]
```

### Debugging Checklist
- [ ] Can reproduce the issue consistently
- [ ] Checked browser console for errors
- [ ] Verified network requests/responses
- [ ] Checked application state
- [ ] Reviewed recent code changes
- [ ] Tested in different environments
- [ ] Searched for similar issues
- [ ] Created minimal reproduction

## Integration with Claude Code

### Intelligent Debugging Assistance
1. **Error analysis**: Parse error messages and suggest fixes
2. **Log interpretation**: Analyze console logs for patterns
3. **Stack trace navigation**: Jump to error locations
4. **State inspection**: Examine application state at breakpoints
5. **Performance bottleneck identification**: Find slow operations

### Automated Debugging Steps
1. **Identify error type**: Syntax, runtime, logic, or performance
2. **Locate error source**: Trace through stack trace
3. **Suggest fixes**: Based on error patterns
4. **Verify resolution**: Run tests after fixes
5. **Document solution**: Update knowledge base

This module ensures efficient, systematic debugging practices that reduce time to resolution and improve code quality.