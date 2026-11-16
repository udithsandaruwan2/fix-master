# ADR-006: Authentication — Phone OTP + JWT

**Status:** Accepted
**Date:** 2026-11-16
**Deciders:** human

## Context

AutoLanka needs authentication for:
- Guest users (stranded drivers) — frictionless, no password
- Registered users — saved vehicles, history, receipts
- Providers — portal access, job management
- Admins — dashboard access

Sri Lankan context: phone numbers are universal; email less so.

## Decision

**Phone OTP for entry + JWT for sessions.**

### Flow

1. **Guest/Registered entry**: Enter phone → receive 6-digit OTP via SMS → verify
2. **First time**: Creates `User` with `role: 'guest'`; optional account upgrade later
3. **Returning**: OTP → JWT in HttpOnly cookie + CSRF token
4. **Role escalation**: `guest` → `user` (on profile complete) → `provider` (on onboarding) → `admin` (manual)

### Token design

| Token | Storage | Expiry | Purpose |
|---|---|---|---|
| Access JWT | HttpOnly cookie (Secure, SameSite=Lax) | 15 min | API auth |
| Refresh JWT | HttpOnly cookie (Secure, SameSite=Strict) | 30 days | Silent refresh |
| CSRF token | Separate cookie (non-HttpOnly) | Session | Mutating requests |

### OTP provider

- **Interface**: `SmsProvider.send(phone, code)` + `verify(phone, code)`
- **V1**: Twilio-compatible stub (logs to console)
- **Production**: Local SMS gateway (Dialog, Mobitel, Hutch APIs) or Twilio

### Security

- Rate limit: 3 OTP requests / 15 min / phone
- OTP expiry: 5 minutes
- Brute force: 5 failed verifies → 15 min lockout
- Device fingerprinting for suspicious activity
- Audit log: all auth events (login, role change, failed attempts)

## Consequences

**Positive:**
- Phone-first matches Sri Lanka (near 100% mobile penetration)
- No password management for users
- JWT in HttpOnly cookies = XSS resistant
- Role-based access guards on all endpoints
- Easy provider/admin escalation

**Negative:**
- SMS delivery dependency (mitigated by stub + local gateway)
- Phone number recycling risk (verify on each login)
- CSRF cookie complexity (mitigated by NestJS CsrfGuard)
- Session revocation needs token blocklist (Redis)

## Alternatives considered

| Option | Rejected because |
|---|---|
| Email + password | Low email usage in target demographic |
| Social login (Google/Facebook) | Not universal; privacy concerns; extra dependency |
| Magic links (email) | Same as email + password |
| Passkeys / WebAuthn | Browser support gaps; overkill for v1 |

## References

- NestJS JWT + Passport + CsrfGuard
- Twilio Verify API (reference)
- Sri Lanka SMS gateway APIs
- OWASP Authentication Cheat Sheet