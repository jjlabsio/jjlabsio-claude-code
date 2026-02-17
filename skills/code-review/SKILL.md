---
name: code-review
description: Comprehensive code review covering security, quality, and best practices. Use after writing code, before commits, or when reviewing PRs. Invokes the security-reviewer agent for deep analysis.
---

# Code Review & Security Skill

Single source of truth for security patterns, vulnerability detection, and code review workflows.

## When to Activate

- After writing or modifying code
- Before committing changes
- Reviewing pull requests
- Implementing authentication, authorization, or user input handling
- Creating API endpoints or working with secrets
- Implementing payment/financial features
- Storing or transmitting sensitive data

## Code Review Workflow

### 1. Get Changed Files
```bash
git diff --name-only HEAD
```

### 2. Review Each File

**Security Issues (CRITICAL):**
- Hardcoded credentials, API keys, tokens
- SQL injection vulnerabilities
- XSS vulnerabilities
- Missing input validation
- Insecure dependencies
- Path traversal risks
- CSRF vulnerabilities
- Authentication bypasses

**Code Quality (HIGH):**
- Functions > 50 lines
- Files > 800 lines
- Nesting depth > 4 levels
- Missing error handling
- console.log statements
- Mutation patterns
- Missing tests for new code

**Best Practices (MEDIUM):**
- TODO/FIXME without tickets
- Accessibility issues
- Poor variable naming
- Magic numbers
- Inconsistent formatting

### 3. Generate Report

For each issue:
```
[SEVERITY] Issue Title
File: path/to/file.ts:42
Issue: Description
Fix: Suggested remediation
```

### 4. Approval Criteria

- **Approve**: No CRITICAL or HIGH issues
- **Warning**: MEDIUM issues only (can merge with caution)
- **Block**: CRITICAL or HIGH issues found -- never approve code with security vulnerabilities

## Security Checklist

### 1. Secrets Management

```typescript
// NEVER: Hardcoded secrets
const apiKey = "sk-proj-xxxxx"

// ALWAYS: Environment variables
const apiKey = process.env.OPENAI_API_KEY
if (!apiKey) {
  throw new Error('OPENAI_API_KEY not configured')
}
```

- [ ] No hardcoded API keys, tokens, or passwords
- [ ] All secrets in environment variables
- [ ] `.env.local` in .gitignore
- [ ] No secrets in git history
- [ ] Production secrets in hosting platform

### 2. Input Validation

```typescript
import { z } from 'zod'

const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
  age: z.number().int().min(0).max(150)
})

export async function createUser(input: unknown) {
  const validated = CreateUserSchema.parse(input)
  return await db.users.create(validated)
}
```

- [ ] All user inputs validated with schemas
- [ ] File uploads restricted (size, type, extension)
- [ ] No direct use of user input in queries
- [ ] Whitelist validation (not blacklist)
- [ ] Error messages don't leak sensitive info

### 3. SQL Injection Prevention

```typescript
// NEVER: String concatenation
const query = `SELECT * FROM users WHERE email = '${userEmail}'`

// ALWAYS: Parameterized queries
const { data } = await supabase
  .from('users')
  .select('*')
  .eq('email', userEmail)
```

- [ ] All database queries use parameterized queries
- [ ] No string concatenation in SQL
- [ ] ORM/query builder used correctly

### 4. Authentication & Authorization

```typescript
// Tokens: httpOnly cookies, not localStorage
res.setHeader('Set-Cookie',
  `token=${token}; HttpOnly; Secure; SameSite=Strict; Max-Age=3600`)
```

- [ ] Tokens stored in httpOnly cookies (not localStorage)
- [ ] Authorization checks before sensitive operations
- [ ] Row Level Security enabled in Supabase
- [ ] Role-based access control implemented

### 5. XSS Prevention

```typescript
import DOMPurify from 'isomorphic-dompurify'

const clean = DOMPurify.sanitize(html, {
  ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p'],
  ALLOWED_ATTR: []
})
```

- [ ] User-provided HTML sanitized
- [ ] CSP headers configured
- [ ] React's built-in XSS protection used

### 6. CSRF Protection

- [ ] CSRF tokens on state-changing operations
- [ ] SameSite=Strict on all cookies

### 7. Rate Limiting

- [ ] Rate limiting on all API endpoints
- [ ] Stricter limits on expensive operations
- [ ] IP-based and user-based rate limiting

### 8. Sensitive Data Exposure

```typescript
// NEVER: Log sensitive data
console.log('User login:', { email, password })

// ALWAYS: Redact
console.log('User login:', { email, userId })
```

- [ ] No passwords, tokens, or secrets in logs
- [ ] Error messages generic for users
- [ ] No stack traces exposed to users

### 9. Blockchain Security (Solana)

- [ ] Wallet signatures verified
- [ ] Transaction details validated
- [ ] Balance checks before transactions
- [ ] No blind transaction signing

### 10. Dependency Security

```bash
npm audit
npm audit fix
```

- [ ] Dependencies up to date
- [ ] No known vulnerabilities
- [ ] Lock files committed

## Security Review Report Format

```markdown
# Security Review Report

**File/Component:** [path/to/file.ts]
**Reviewed:** YYYY-MM-DD
**Reviewer:** security-reviewer agent

## Summary

- **Critical Issues:** X
- **High Issues:** Y
- **Medium Issues:** Z
- **Risk Level:** HIGH / MEDIUM / LOW

## Critical Issues (Fix Immediately)

### 1. [Issue Title]
**Severity:** CRITICAL
**Category:** SQL Injection / XSS / Authentication / etc.
**Location:** `file.ts:123`

**Issue:** [Description]
**Impact:** [What could happen if exploited]

**Remediation:**
[Secure code example]

**References:**
- OWASP: [link]
- CWE: [number]

## Security Checklist

[Use checklist from above]

## Recommendations

[Security improvements and tooling suggestions]
```

## PR Security Review Template

```markdown
## Security Review

**Reviewer:** security-reviewer agent
**Risk Level:** HIGH / MEDIUM / LOW

### Blocking Issues
- [ ] **CRITICAL**: [Description] @ `file:line`
- [ ] **HIGH**: [Description] @ `file:line`

### Non-Blocking Issues
- [ ] **MEDIUM**: [Description] @ `file:line`

**Recommendation:** BLOCK / APPROVE WITH CHANGES / APPROVE
```

## Common False Positives

Not every finding is a vulnerability:
- Environment variables in .env.example (not actual secrets)
- Test credentials in test files (if clearly marked)
- Public API keys (if actually meant to be public)
- SHA256/MD5 used for checksums (not passwords)

Always verify context before flagging.

## Emergency Response

If a CRITICAL vulnerability is found:
1. **Document** - Create detailed report
2. **Notify** - Alert project owner immediately
3. **Recommend Fix** - Provide secure code example
4. **Test Fix** - Verify remediation works
5. **Rotate Secrets** - If credentials exposed

## Pre-Deployment Security Checklist

Before ANY production deployment:

- [ ] No hardcoded secrets, all in env vars
- [ ] All user inputs validated
- [ ] SQL queries parameterized
- [ ] XSS prevention in place
- [ ] CSRF protection enabled
- [ ] Authentication/authorization verified
- [ ] Rate limiting enabled
- [ ] HTTPS enforced
- [ ] Security headers configured
- [ ] Dependencies up to date
- [ ] Logging sanitized

## Security Testing

```typescript
test('requires authentication', async () => {
  const response = await fetch('/api/protected')
  expect(response.status).toBe(401)
})

test('requires admin role', async () => {
  const response = await fetch('/api/admin', {
    headers: { Authorization: `Bearer ${userToken}` }
  })
  expect(response.status).toBe(403)
})

test('rejects invalid input', async () => {
  const response = await fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify({ email: 'not-an-email' })
  })
  expect(response.status).toBe(400)
})
```

## Related Agent

This skill invokes the **security-reviewer** agent for deep vulnerability analysis.

## Resources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Next.js Security](https://nextjs.org/docs/security)
- [Supabase Security](https://supabase.com/docs/guides/auth)
