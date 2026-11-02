# Contributing

Thanks for your interest in improving Build Your Own Calendly!

## Ways to contribute

- Improve explanations and architecture documentation.
- Report bugs or unclear behavior.
- Add unit, integration, or end-to-end tests.
- Implement roadmap features.
- Improve accessibility, security, and developer experience.

## Before opening an issue

Search existing issues first. Include:

- What you expected.
- What happened instead.
- Steps to reproduce.
- Relevant logs with secrets and personal data removed.
- Environment details when relevant.

## Pull request workflow

1. Fork the repository.
2. Create a focused branch, for example `feat/availability-rules`.
3. Make a small, reviewable change.
4. Add or update tests and documentation.
5. Run lint, typecheck, and tests.
6. Open a pull request describing the problem and solution.

## Engineering expectations

- Keep domain logic out of UI components.
- Validate untrusted input on the server.
- Enforce authorization for host-owned resources.
- Use IANA timezone identifiers and test daylight-saving boundaries.
- Never rely only on client-side availability checks.
- Consider concurrent requests and idempotent retries.
- Do not commit secrets, tokens, private user data, or generated build artifacts.
- Use clear names and explain non-obvious decisions.

## Scope and originality

This is an independent educational project. Contributions must be original or compatible with their source licenses. Do not submit copied proprietary code, private API details, or Calendly branding/assets.

## Code of conduct

Be respectful, constructive, and welcoming. Critique ideas and code, not people.
