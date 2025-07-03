# Testing Strategies and Test-Driven Development

## Purpose
Establish comprehensive testing practices, promote test-driven development (TDD), and ensure code reliability through systematic testing approaches.

## Core Testing Principles

### 1. Test-First Development
- **Write tests before code**: Define expected behavior first
- **Red-Green-Refactor cycle**: Fail, pass, improve
- **One test at a time**: Focus on single functionality
- **Minimal implementation**: Write just enough code to pass

### 2. Test Pyramid Strategy

The test pyramid represents the ideal distribution of different types of tests:

**Unit Tests (Foundation - 60-70%)**:
- Fast, isolated tests of individual components
- High coverage of business logic and edge cases
- Quick feedback during development
- Low maintenance cost

**Integration Tests (Middle - 20-30%)**:
- Test interactions between components
- Validate data flow and system boundaries
- Balance between speed and realistic testing
- Medium maintenance cost

**End-to-End Tests (Top - 10-20%)**:
- Test complete user journeys and workflows
- Validate system behavior from user perspective
- Slower execution but high confidence
- Higher maintenance cost

## Testing Workflow Protocol

### Before Writing Any Code
1. **Identify test scenarios**: List all expected behaviors
2. **Write failing test**: Start with the simplest case
3. **Run test to see failure**: Ensure test actually fails
4. **Implement minimal code**: Just enough to pass
5. **Refactor if needed**: Improve code while keeping tests green

### Test Organization
```
project/
├── src/
│   ├── components/
│   │   └── Button.js
│   └── utils/
│       └── calculations.js
└── tests/
    ├── unit/
    │   ├── components/
    │   │   └── Button.test.js
    │   └── utils/
    │       └── calculations.test.js
    ├── integration/
    └── e2e/
```

## Unit Testing Best Practices

### Test Structure (AAA Pattern)
```javascript
describe('ComponentName', () => {
  it('should do something specific', () => {
    // Arrange - Set up test data and dependencies
    const input = { value: 5 };
    
    // Act - Execute the functionality
    const result = calculateTax(input);
    
    // Assert - Verify the outcome
    expect(result).toBe(5.5);
  });
});
```

### What to Test
- **Public APIs**: All exported functions/methods
- **Edge cases**: Boundary values, null/undefined
- **Error scenarios**: Invalid inputs, exceptions
- **State changes**: Mutations, side effects
- **Integration points**: Module interactions

### What NOT to Test
- **Implementation details**: Private methods
- **Framework code**: Library functionality
- **Trivial code**: Simple getters/setters
- **External services**: Mock these instead

## Test Writing Guidelines

### Descriptive Test Names
```javascript
// Bad
it('test user', () => {});

// Good
it('should return user data when valid ID is provided', () => {});
it('should throw error when user ID is invalid', () => {});
```

### Independent Tests
- **No shared state**: Each test runs in isolation
- **No test ordering**: Tests can run in any order
- **Clean setup/teardown**: Use beforeEach/afterEach
- **Avoid global variables**: Prevent test pollution

### Effective Assertions
```javascript
// Specific assertions
expect(user.age).toBe(25);
expect(users).toHaveLength(3);
expect(response.status).toBe(200);
expect(fn).toThrow(ValidationError);

// Avoid generic assertions
expect(result).toBeTruthy(); // Too vague
```

## Integration Testing

### Database Tests
```javascript
describe('User Repository', () => {
  beforeEach(async () => {
    await db.migrate.latest();
    await db.seed.run();
  });
  
  afterEach(async () => {
    await db.migrate.rollback();
  });
  
  it('should create user in database', async () => {
    const user = await createUser({ name: 'John' });
    const found = await db('users').where({ id: user.id }).first();
    expect(found.name).toBe('John');
  });
});
```

### API Tests
```javascript
describe('POST /api/users', () => {
  it('should create new user with valid data', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'Jane', email: 'jane@example.com' })
      .expect(201);
    
    expect(response.body).toMatchObject({
      id: expect.any(Number),
      name: 'Jane',
      email: 'jane@example.com'
    });
  });
});
```

## Mocking and Stubbing

### When to Mock
- **External services**: APIs, databases, file systems
- **Time-dependent code**: Dates, timers, random values
- **Complex dependencies**: Heavy computations
- **Unreliable services**: Network requests

### Mocking Examples
```javascript
// Mock external API
jest.mock('./api');
api.fetchUser.mockResolvedValue({ id: 1, name: 'Test' });

// Mock timers
jest.useFakeTimers();
setTimeout(() => callback(), 1000);
jest.runAllTimers();
expect(callback).toHaveBeenCalled();

// Mock modules
jest.mock('fs', () => ({
  readFileSync: jest.fn(() => 'mocked content')
}));
```

## Test Coverage

### Coverage Goals
- **Unit tests**: 80-90% coverage
- **Critical paths**: 100% coverage
- **Edge cases**: Comprehensive coverage
- **Error handling**: All error paths tested

### Coverage Commands
```bash
# Run tests with coverage
npm test -- --coverage
jest --coverage
pytest --cov=src

# Coverage thresholds in config
{
  "coverageThreshold": {
    "global": {
      "branches": 80,
      "functions": 80,
      "lines": 80,
      "statements": 80
    }
  }
}
```

## E2E Testing Strategy

### Scenario Selection
- **Critical user journeys**: Registration, checkout
- **Complex workflows**: Multi-step processes
- **Integration points**: Third-party services
- **Cross-browser compatibility**: Major browsers

### E2E Best Practices
```javascript
describe('User Registration Flow', () => {
  it('should complete registration process', async () => {
    // Navigate to registration
    await page.goto('/register');
    
    // Fill form
    await page.fill('#email', 'test@example.com');
    await page.fill('#password', 'SecurePass123!');
    
    // Submit and verify
    await page.click('button[type="submit"]');
    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('h1')).toContainText('Welcome');
  });
});
```

## Performance Testing

### Load Testing
```javascript
describe('API Performance', () => {
  it('should handle 100 concurrent requests', async () => {
    const requests = Array(100).fill().map(() => 
      fetch('/api/users')
    );
    
    const start = Date.now();
    await Promise.all(requests);
    const duration = Date.now() - start;
    
    expect(duration).toBeLessThan(5000); // 5 seconds
  });
});
```

## Test Maintenance

### Refactoring Tests
- **DRY principle**: Extract common test utilities
- **Test helpers**: Create reusable test functions
- **Fixtures**: Centralize test data
- **Page objects**: For E2E tests

### Test Utilities
```javascript
// Test helpers
export const createTestUser = (overrides = {}) => ({
  id: 1,
  name: 'Test User',
  email: 'test@example.com',
  ...overrides
});

// Custom matchers
expect.extend({
  toBeValidEmail(received) {
    const pass = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(received);
    return { pass, message: () => `Expected ${received} to be valid email` };
  }
});
```

## Continuous Testing

### Pre-commit Hooks
```bash
# Run affected tests before commit
npm test -- --onlyChanged
```

### CI/CD Integration
```yaml
# GitHub Actions example
test:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v2
    - run: npm install
    - run: npm test -- --coverage
    - run: npm run test:e2e
```

## Testing Anti-patterns to Avoid

### DON'T
- Test implementation details
- Write tests after debugging
- Ignore flaky tests
- Use production data in tests
- Share state between tests
- Mock everything
- Write overly complex tests
- Skip error scenarios

### DO
- Test behavior, not implementation
- Write tests first
- Fix flaky tests immediately
- Use test-specific data
- Isolate each test
- Mock only external dependencies
- Keep tests simple and focused
- Test both success and failure paths

## Framework-Specific Guidelines

### React Testing
```javascript
// Test user interactions
fireEvent.click(button);
await waitFor(() => expect(mockFn).toHaveBeenCalled());

// Test hooks
const { result } = renderHook(() => useCounter());
act(() => result.current.increment());
expect(result.current.count).toBe(1);
```

### Node.js Testing
```javascript
// Test async code
it('should handle async operations', async () => {
  const data = await fetchData();
  expect(data).toEqual({ success: true });
});

// Test streams
const stream = createReadStream();
const chunks = [];
stream.on('data', chunk => chunks.push(chunk));
await finished(stream);
```

## Integration with Claude Code

### Automated Test Generation
1. **Suggest test cases**: Based on function signatures
2. **Generate test stubs**: For new functions
3. **Identify missing tests**: Coverage gaps
4. **Update tests**: When code changes

### Test-Driven Workflow
1. **Parse requirements**: Extract test scenarios
2. **Generate failing tests**: Based on requirements
3. **Implement incrementally**: One test at a time
4. **Refactor safely**: With test protection

This module ensures robust, maintainable code through comprehensive testing practices and promotes a test-first development culture.