# Security Best Practices and Secure Coding

## Purpose
Establish comprehensive security practices to prevent vulnerabilities, protect sensitive data, and ensure secure code development throughout the software lifecycle across all programming languages and platforms.

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

### Universal Secret Principles
- **Never hardcode secrets**: API keys, passwords, tokens should never be in source code
- **Environment variables**: Use OS environment variables for configuration
- **Secret rotation**: Regularly rotate secrets and credentials
- **Least privilege**: Secrets should have minimal necessary permissions
- **Secret scanning**: Automatically scan for accidentally committed secrets

### Secret Storage Strategies
1. **Environment variables**: For simple applications
2. **Secret management services**: AWS Secrets Manager, Azure Key Vault, HashiCorp Vault
3. **Configuration files**: Encrypted and excluded from version control
4. **Container secrets**: Docker secrets, Kubernetes secrets

### Secret Detection and Prevention
- **Pre-commit hooks**: Scan commits for secret patterns
- **Repository scanning**: Regular scans of entire codebase
- **CI/CD integration**: Prevent secrets in build artifacts
- **Secret patterns**: Detect API key formats, connection strings, tokens

### Environment Configuration Best Practices
- Create example/template files with placeholder values
- Document required environment variables
- Use consistent naming conventions
- Validate required secrets at application startup
- Never log secret values

## Input Validation and Sanitization

### Universal Input Validation Principles
- **Never trust external input**: All user input, API calls, file contents are potentially malicious
- **Validate early**: Check input at system boundaries
- **Whitelist approach**: Define what is allowed, reject everything else
- **Fail securely**: Invalid input should result in secure failure states
- **Consistent validation**: Use the same validation logic across the application

### Input Validation Categories
1. **Data type validation**: Ensure correct types (string, number, boolean)
2. **Format validation**: Email addresses, phone numbers, URLs
3. **Range validation**: Minimum/maximum values, string lengths
4. **Business logic validation**: Valid combinations, referential integrity
5. **Encoding validation**: Character sets, escape sequences

### Common Input Attack Vectors
- **SQL Injection**: Malicious SQL in input parameters
- **Command Injection**: Operating system commands in input
- **Cross-Site Scripting (XSS)**: Malicious scripts in web input
- **Path Traversal**: Directory navigation in file paths
- **LDAP Injection**: LDAP queries in authentication input
- **XML/JSON Injection**: Malformed data structures

### Validation Implementation Strategies
- **Input validation libraries**: Use well-tested validation frameworks
- **Parameterized queries**: For database operations
- **Output encoding**: Escape data for specific contexts (HTML, SQL, etc.)
- **Content Security Policy**: Browser-level protection for web applications
- **Input length limits**: Prevent buffer overflow and DoS attacks

## Authentication and Authorization

### Password Security Fundamentals
- **Never store plain text passwords**: Always use cryptographic hashing
- **Strong hashing algorithms**: Use bcrypt, scrypt, Argon2, or PBKDF2
- **Salt generation**: Use unique, random salts for each password
- **Adequate work factors**: Configure sufficient computational cost
- **Password policies**: Enforce strong password requirements

### Password Policy Guidelines
- **Minimum length**: 8-12 characters minimum
- **Character complexity**: Mix of uppercase, lowercase, numbers, symbols
- **Common password prevention**: Block dictionary words and common passwords
- **Password history**: Prevent reuse of recent passwords
- **Account lockout**: Limit failed authentication attempts

### Multi-Factor Authentication (MFA)
- **Something you know**: Password or PIN
- **Something you have**: Phone, hardware token, smart card
- **Something you are**: Biometrics (fingerprint, face, voice)
- **Risk-based authentication**: Additional factors for suspicious activity

### Session Management Principles
- **Secure session generation**: Use cryptographically secure random session IDs
- **Session expiration**: Implement both idle and absolute timeouts
- **Session invalidation**: Clear sessions on logout and security events
- **Session storage**: Secure server-side session storage
- **Transport security**: Always use HTTPS for session cookies

### Authorization Patterns
- **Principle of least privilege**: Grant minimum necessary permissions
- **Role-based access control (RBAC)**: Group permissions by roles
- **Attribute-based access control (ABAC)**: Dynamic permissions based on attributes
- **Resource-level permissions**: Fine-grained access control
- **Regular access reviews**: Periodic review and cleanup of permissions

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

## Database Security

### SQL Injection Prevention
- **Parameterized queries**: Use prepared statements with parameter binding
- **Stored procedures**: When properly implemented, can prevent injection
- **Input validation**: Validate all user input before database operations
- **Least privilege**: Database users should have minimal necessary permissions
- **Escape user input**: When parameterization isn't possible (rare cases)

### Database Security Principles
1. **Connection security**: Use encrypted connections (TLS/SSL)
2. **Authentication**: Strong database user authentication
3. **Authorization**: Principle of least privilege for database access
4. **Auditing**: Log database access and operations
5. **Backup security**: Encrypt and secure database backups

### ORM/Query Builder Security
- **Use ORM safely**: Even ORMs can be vulnerable if misused
- **Avoid raw queries**: Use ORM query builders when possible
- **Parameter binding**: Ensure ORM uses parameterized queries
- **Query review**: Review complex queries for injection vulnerabilities
- **Schema validation**: Validate data against database schema

### NoSQL Injection Prevention
- **Input validation**: NoSQL databases are also vulnerable to injection
- **Query sanitization**: Sanitize queries in MongoDB, CouchDB, etc.
- **Schema enforcement**: Use strict schemas when available
- **Authentication**: Proper authentication for NoSQL databases
- **Access controls**: Implement proper authorization patterns

## Cross-Site Request Forgery (CSRF) Protection

### CSRF Attack Understanding
- **Malicious requests**: Attacker tricks authenticated user into making unintended requests
- **State-changing operations**: Particularly dangerous for actions that modify data
- **Browser behavior**: Browsers automatically include cookies with requests
- **Same-origin policy limitations**: CSRF bypasses many browser protections

### CSRF Prevention Techniques
1. **CSRF tokens**: Unique, unpredictable tokens for each request
2. **SameSite cookies**: Browser-level protection against cross-site requests
3. **Referer checking**: Validate request origin (with limitations)
4. **Custom headers**: AJAX requests with custom headers
5. **Double submit cookies**: Compare cookie value with request parameter

### Token-Based CSRF Protection
- **Unique tokens**: Generate cryptographically secure random tokens
- **Token validation**: Verify token on every state-changing request
- **Token lifecycle**: Rotate tokens periodically or per session
- **Token storage**: Secure storage and transmission of tokens

### Additional CSRF Mitigations
- **SameSite cookie attribute**: Set cookies to SameSite=Strict or Lax
- **Origin validation**: Check Origin and Referer headers
- **Re-authentication**: Require password for sensitive operations
- **User confirmation**: Additional confirmation for critical actions

## File Upload Security

### File Upload Attack Vectors
- **Malicious file execution**: Uploading executable files that get executed
- **Path traversal**: Using file names to access unauthorized directories
- **Denial of service**: Large files consuming server resources
- **Content type spoofing**: Disguising malicious files as safe formats
- **Virus/malware uploads**: Infected files spreading to other systems

### File Upload Security Controls
1. **File type validation**: Whitelist allowed file extensions and MIME types
2. **File size limits**: Prevent resource exhaustion attacks
3. **File name sanitization**: Remove dangerous characters and path elements
4. **Content scanning**: Scan files for malware and malicious content
5. **Storage isolation**: Store uploads outside web root and execution paths

### Secure File Handling
- **Rename uploaded files**: Generate secure, unique filenames
- **Validate file headers**: Check magic numbers, not just extensions
- **Virus scanning**: Integrate with antivirus solutions
- **Content inspection**: Verify file contents match claimed type
- **Access controls**: Implement proper permissions on uploaded files

### Upload Storage Security
- **Separate storage**: Store uploads separate from application code
- **No execution permissions**: Prevent uploaded files from being executed
- **Access restrictions**: Control who can access uploaded files
- **Backup security**: Secure backup and recovery procedures
- **Monitoring**: Log and monitor file upload activities

## API Security

### API Attack Vectors
- **Rate limiting bypass**: Overwhelming APIs with excessive requests
- **Authentication bypass**: Accessing APIs without proper credentials
- **Authorization flaws**: Accessing resources beyond permissions
- **Data exposure**: APIs revealing sensitive information
- **Injection attacks**: SQL, NoSQL, command injection through API parameters

### Rate Limiting Strategies
1. **Request rate limits**: Limit requests per time window
2. **Burst protection**: Handle short-term request spikes
3. **Per-user limits**: Individual user rate limiting
4. **Endpoint-specific limits**: Different limits for different operations
5. **Distributed rate limiting**: Coordination across multiple servers

### API Authentication Methods
- **API keys**: Simple tokens for service identification
- **JWT tokens**: Self-contained authentication tokens
- **OAuth 2.0**: Industry standard for authorization
- **Basic authentication**: Username/password over HTTPS
- **Mutual TLS**: Certificate-based authentication

### API Authorization Patterns
- **Scope-based access**: Granular permissions for different operations
- **Resource-level authorization**: Per-resource access control
- **Time-based access**: Temporary or scheduled access
- **IP-based restrictions**: Geographic or network-based access control
- **Usage quotas**: Limits on API consumption

### API Security Best Practices
- **HTTPS everywhere**: Encrypt all API communications
- **Input validation**: Validate all API parameters
- **Output filtering**: Only return necessary data
- **Error handling**: Don't leak sensitive information in errors
- **Logging and monitoring**: Track API usage and security events

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

## Security Headers and Transport Security

### Essential Security Headers
- **Content Security Policy (CSP)**: Prevent XSS attacks by controlling resource loading
- **Strict-Transport-Security (HSTS)**: Enforce HTTPS connections
- **X-Frame-Options**: Prevent clickjacking attacks
- **X-Content-Type-Options**: Prevent MIME type sniffing
- **Referrer-Policy**: Control referrer information leakage
- **Permissions-Policy**: Control browser feature access

### Transport Layer Security
1. **HTTPS everywhere**: Encrypt all communications
2. **TLS version**: Use TLS 1.2 or higher
3. **Certificate management**: Proper certificate validation and renewal
4. **HSTS implementation**: Force HTTPS connections
5. **Certificate pinning**: Pin certificates for critical services

### Cross-Origin Security
- **CORS configuration**: Properly configure cross-origin resource sharing
- **Origin validation**: Verify request origins
- **Credential handling**: Secure handling of cross-origin credentials
- **Preflight requests**: Handle OPTIONS requests securely

### Browser Security Features
- **Subresource Integrity (SRI)**: Verify external resource integrity
- **Feature Policy**: Control browser feature access
- **Cross-Origin isolation**: Isolate sensitive operations
- **Trusted Types**: Prevent DOM-based XSS attacks

## Integration with Claude Code

### Automated Security Analysis
1. **Secret detection**: Automatically scan code for hardcoded credentials
2. **Dependency analysis**: Check for known vulnerabilities in dependencies
3. **Code pattern analysis**: Identify insecure coding patterns
4. **Configuration review**: Verify secure configuration practices
5. **Architecture assessment**: Evaluate overall security design

### Security-First Development
- **Threat modeling**: Consider security implications of new features
- **Secure defaults**: Implement secure configurations by default
- **Defense in depth**: Layer multiple security controls
- **Security testing**: Include security tests in development process
- **Incident response**: Prepare for security incidents

### Universal Security Principles
1. **Never trust external input**: All external data is potentially malicious
2. **Implement least privilege**: Grant minimum necessary permissions
3. **Use defense in depth**: Multiple layers of security controls
4. **Fail securely**: Failures should not compromise security
5. **Keep security simple**: Complexity introduces vulnerabilities
6. **Stay updated**: Regularly update dependencies and security practices
7. **Monitor and log**: Track security events and anomalies
8. **Plan for incidents**: Prepare response procedures for security breaches

### Language-Agnostic Security Checklist
- Input validation and sanitization implemented
- Authentication and authorization properly configured
- Secrets management system in place
- Database security measures applied
- File upload restrictions implemented
- Rate limiting and API security configured
- HTTPS/TLS encryption enabled
- Security headers configured
- Dependency vulnerabilities addressed
- Security logging and monitoring active

This module provides universal security guidance applicable to all programming languages and platforms, ensuring comprehensive protection against common vulnerabilities.