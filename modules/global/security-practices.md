# Security Best Practices and Secure Coding

## Purpose
Establish comprehensive security practices to prevent vulnerabilities, protect sensitive data, and ensure secure code development throughout the software lifecycle.

## Core Security Principles

### 1. Defense in Depth
- **Multiple layers**: Never rely on single security measure
- **Fail securely**: Default to secure state on errors
- **Least privilege**: Minimal permissions required
- **Zero trust**: Verify everything, trust nothing

### 2. Security by Design
- **Threat modeling**: Identify risks early
- **Secure defaults**: Safe configuration out of the box
- **Privacy first**: Data protection built-in
- **Regular updates**: Keep dependencies current

## Secrets Management

### NEVER Commit Secrets
```bash
# Bad - Hardcoded secrets
API_KEY = "sk_live_abcd1234"
DATABASE_URL = "postgres://user:password@host/db"

# Good - Environment variables
API_KEY = os.environ.get('API_KEY')
DATABASE_URL = os.environ.get('DATABASE_URL')
```

### Secret Detection
```bash
# Pre-commit hook for secret scanning
# .gitleaks.toml
[allowlist]
description = "Allowlist for known safe patterns"
regexes = [
  '''example\.com''',
  '''EXAMPLE_[A-Z_]+'''
]

# Run gitleaks
gitleaks detect --source . -v
```

### Environment Configuration
```javascript
// .env.example (commit this)
API_KEY=your_api_key_here
DATABASE_URL=postgres://user:password@localhost/dbname
JWT_SECRET=your_secret_here

// .env (never commit - add to .gitignore)
API_KEY=sk_live_actual_key
DATABASE_URL=postgres://produser:prodpass@prod.db.com/proddb
JWT_SECRET=actual_secret_key
```

## Input Validation and Sanitization

### Never Trust User Input
```javascript
// Bad - Direct use of user input
const query = `SELECT * FROM users WHERE id = ${req.params.id}`;

// Good - Parameterized queries
const query = 'SELECT * FROM users WHERE id = $1';
const result = await db.query(query, [req.params.id]);
```

### Validation Patterns
```typescript
// Input validation schema
const userSchema = z.object({
  email: z.string().email().max(255),
  password: z.string().min(8).max(128),
  age: z.number().int().min(0).max(150),
  username: z.string().regex(/^[a-zA-Z0-9_-]{3,20}$/)
});

// Validate before processing
try {
  const validatedData = userSchema.parse(req.body);
  // Process validated data
} catch (error) {
  return res.status(400).json({ error: 'Invalid input' });
}
```

### XSS Prevention
```javascript
// Bad - Direct HTML injection
element.innerHTML = userInput;

// Good - Text content or sanitization
element.textContent = userInput;
// Or use DOMPurify for HTML
element.innerHTML = DOMPurify.sanitize(userInput);
```

## Authentication and Authorization

### Password Security
```javascript
// Never store plain text passwords
// Bad
const user = { password: req.body.password };

// Good - Use bcrypt or argon2
const hashedPassword = await bcrypt.hash(password, 12);
const user = { password: hashedPassword };

// Password requirements
const passwordPolicy = {
  minLength: 8,
  requireUppercase: true,
  requireLowercase: true,
  requireNumbers: true,
  requireSpecialChars: true,
  preventCommonPasswords: true
};
```

### JWT Best Practices
```javascript
// Secure JWT implementation
const token = jwt.sign(
  { userId: user.id, role: user.role },
  process.env.JWT_SECRET,
  { 
    expiresIn: '15m',  // Short expiration
    issuer: 'myapp.com',
    audience: 'myapp.com',
    algorithm: 'HS256'
  }
);

// Verify with all claims
jwt.verify(token, process.env.JWT_SECRET, {
  issuer: 'myapp.com',
  audience: 'myapp.com',
  algorithms: ['HS256']
});
```

### Session Management
```javascript
// Secure session configuration
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    secure: true,  // HTTPS only
    httpOnly: true,  // No JS access
    maxAge: 1000 * 60 * 15,  // 15 minutes
    sameSite: 'strict'  // CSRF protection
  }
}));
```

## HTTPS and Transport Security

### Force HTTPS
```javascript
// Redirect HTTP to HTTPS
app.use((req, res, next) => {
  if (req.header('x-forwarded-proto') !== 'https') {
    res.redirect(`https://${req.header('host')}${req.url}`);
  } else {
    next();
  }
});

// HSTS header
app.use(helmet.hsts({
  maxAge: 31536000,
  includeSubDomains: true,
  preload: true
}));
```

### Certificate Pinning
```javascript
// Pin certificates for critical APIs
const agent = new https.Agent({
  ca: fs.readFileSync('path/to/ca-cert.pem'),
  checkServerIdentity: (host, cert) => {
    // Verify certificate fingerprint
    const fingerprint = cert.fingerprint256;
    if (fingerprint !== expectedFingerprint) {
      throw new Error('Certificate fingerprint mismatch');
    }
  }
});
```

## SQL Injection Prevention

### Parameterized Queries
```javascript
// Bad - String concatenation
const query = `SELECT * FROM users WHERE email = '${email}'`;

// Good - Parameterized query (PostgreSQL)
const query = 'SELECT * FROM users WHERE email = $1';
const result = await client.query(query, [email]);

// Good - Parameterized query (MySQL)
const query = 'SELECT * FROM users WHERE email = ?';
const [rows] = await connection.execute(query, [email]);
```

### ORM Security
```javascript
// Sequelize with proper escaping
const users = await User.findAll({
  where: {
    email: req.body.email,  // Automatically escaped
    [Op.or]: [
      { status: 'active' },
      { status: 'pending' }
    ]
  }
});

// Avoid raw queries unless necessary
// If needed, use bind parameters
const results = await sequelize.query(
  'SELECT * FROM users WHERE email = :email',
  {
    replacements: { email: userEmail },
    type: QueryTypes.SELECT
  }
);
```

## CSRF Protection

### Token Implementation
```javascript
// Generate CSRF token
app.use(csrf());

app.use((req, res, next) => {
  res.locals.csrfToken = req.csrfToken();
  next();
});

// Include in forms
<form method="POST">
  <input type="hidden" name="_csrf" value="{{csrfToken}}">
  <!-- form fields -->
</form>

// Include in AJAX requests
fetch('/api/data', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-CSRF-Token': csrfToken
  },
  body: JSON.stringify(data)
});
```

## File Upload Security

### Validation and Restrictions
```javascript
const upload = multer({
  storage: multer.diskStorage({
    destination: './uploads/',
    filename: (req, file, cb) => {
      // Generate safe filename
      const uniqueName = `${Date.now()}-${crypto.randomBytes(6).toString('hex')}`;
      const ext = path.extname(file.originalname).toLowerCase();
      cb(null, uniqueName + ext);
    }
  }),
  fileFilter: (req, file, cb) => {
    // Whitelist allowed extensions
    const allowedExts = ['.jpg', '.jpeg', '.png', '.gif'];
    const ext = path.extname(file.originalname).toLowerCase();
    
    if (!allowedExts.includes(ext)) {
      return cb(new Error('Invalid file type'));
    }
    
    // Check MIME type
    const allowedMimes = ['image/jpeg', 'image/png', 'image/gif'];
    if (!allowedMimes.includes(file.mimetype)) {
      return cb(new Error('Invalid file type'));
    }
    
    cb(null, true);
  },
  limits: {
    fileSize: 5 * 1024 * 1024,  // 5MB limit
    files: 1
  }
});

// Scan uploaded files
const scanFile = async (filePath) => {
  // Implement virus scanning
  // Check file content matches extension
  // Verify magic numbers
};
```

## API Security

### Rate Limiting
```javascript
const rateLimit = require('express-rate-limit');

// General rate limit
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 100,  // 100 requests per window
  message: 'Too many requests, please try again later',
  standardHeaders: true,
  legacyHeaders: false
});

// Strict limit for auth endpoints
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,  // 5 attempts per window
  skipSuccessfulRequests: true
});

app.use('/api/', limiter);
app.use('/api/auth/', authLimiter);
```

### API Key Management
```javascript
// Secure API key validation
const validateApiKey = (req, res, next) => {
  const apiKey = req.headers['x-api-key'];
  
  if (!apiKey) {
    return res.status(401).json({ error: 'API key required' });
  }
  
  // Hash comparison to prevent timing attacks
  const hashedKey = crypto
    .createHash('sha256')
    .update(apiKey)
    .digest('hex');
    
  const validKeyHash = process.env.API_KEY_HASH;
  
  if (!crypto.timingSafeEqual(
    Buffer.from(hashedKey),
    Buffer.from(validKeyHash)
  )) {
    return res.status(401).json({ error: 'Invalid API key' });
  }
  
  next();
};
```

## Dependency Security

### Regular Updates
```bash
# Check for vulnerabilities
npm audit
yarn audit
pip-audit

# Update dependencies
npm update
npm audit fix

# Check for outdated packages
npm outdated
```

### Lock File Integrity
```bash
# Verify lock file integrity
npm ci  # Clean install from lock file
yarn install --frozen-lockfile

# Never manually edit lock files
```

## Error Handling

### Secure Error Messages
```javascript
// Bad - Exposes internal details
app.use((err, req, res, next) => {
  res.status(500).json({
    error: err.message,
    stack: err.stack,
    query: req.query
  });
});

// Good - Generic messages for production
app.use((err, req, res, next) => {
  // Log full error internally
  logger.error(err);
  
  // Send generic message to client
  const message = process.env.NODE_ENV === 'production'
    ? 'An error occurred'
    : err.message;
    
  res.status(500).json({ error: message });
});
```

## Logging and Monitoring

### Security Event Logging
```javascript
// Log security events
const logSecurityEvent = (event, req) => {
  logger.warn({
    type: 'SECURITY',
    event,
    ip: req.ip,
    userAgent: req.headers['user-agent'],
    userId: req.user?.id,
    timestamp: new Date().toISOString()
  });
};

// Usage
logSecurityEvent('FAILED_LOGIN_ATTEMPT', req);
logSecurityEvent('SUSPICIOUS_FILE_UPLOAD', req);
logSecurityEvent('RATE_LIMIT_EXCEEDED', req);
```

### Sensitive Data in Logs
```javascript
// Never log sensitive data
// Bad
logger.info(`User login: ${email}, password: ${password}`);

// Good
logger.info(`User login attempt for email: ${email}`);

// Sanitize log data
const sanitizeForLog = (obj) => {
  const sensitive = ['password', 'token', 'apiKey', 'ssn'];
  const sanitized = { ...obj };
  
  sensitive.forEach(key => {
    if (sanitized[key]) {
      sanitized[key] = '[REDACTED]';
    }
  });
  
  return sanitized;
};
```

## Security Headers

### Comprehensive Headers
```javascript
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "'nonce-{{nonce}}'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      imgSrc: ["'self'", "data:", "https:"],
      connectSrc: ["'self'"],
      fontSrc: ["'self'"],
      objectSrc: ["'none'"],
      mediaSrc: ["'self'"],
      frameSrc: ["'none'"]
    }
  },
  crossOriginEmbedderPolicy: true,
  crossOriginOpenerPolicy: true,
  crossOriginResourcePolicy: { policy: "cross-origin" },
  dnsPrefetchControl: true,
  frameguard: { action: 'deny' },
  hidePoweredBy: true,
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  },
  ieNoOpen: true,
  noSniff: true,
  originAgentCluster: true,
  permittedCrossDomainPolicies: false,
  referrerPolicy: { policy: "no-referrer" },
  xssFilter: true
}));
```

## Integration with Claude Code

### Security Checks Before Execution
1. **Scan for secrets**: Check for hardcoded credentials
2. **Validate dependencies**: Check for known vulnerabilities
3. **Review permissions**: Ensure least privilege
4. **Audit logs**: Check for sensitive data exposure

### Automated Security Fixes
1. **Replace hardcoded secrets**: Use environment variables
2. **Update vulnerable dependencies**: Apply security patches
3. **Add missing security headers**: Implement comprehensive protection
4. **Fix insecure patterns**: Replace with secure alternatives

### Security Reminders
- Never commit secrets or credentials
- Always validate and sanitize user input
- Use parameterized queries for database access
- Implement proper authentication and authorization
- Keep dependencies up to date
- Log security events for monitoring
- Use HTTPS for all communications
- Implement rate limiting for APIs

This module ensures secure coding practices are followed throughout development, preventing common vulnerabilities and protecting sensitive data.