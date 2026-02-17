# Security Guidelines

## Mandatory Checks (Before ANY Commit)

- No hardcoded secrets (API keys, passwords, tokens) -- use env vars
- All user inputs validated (use Zod or similar)
- SQL injection prevention (parameterized queries only)
- XSS prevention (sanitize user-provided HTML)

For detailed patterns and code examples, see the `code-review` skill.

## Security Response Protocol

If security issue found:
1. STOP immediately
2. Use **security-reviewer** agent
3. Fix CRITICAL issues before continuing
4. Rotate any exposed secrets
5. Review codebase for similar issues
