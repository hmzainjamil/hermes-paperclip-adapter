# Security notes

This adapter starts Hermes Agent as a child process inside the Paperclip host environment. Its execution code adds Hermes `--yolo`, which bypasses dangerous-command approval prompts. This is a material trust boundary: the process may use the host account's filesystem, network, and configured tools.

## Before connecting an agent

- Run Paperclip and Hermes under a dedicated account with limited file and network access.
- Restrict toolsets, workspace paths, and provider credentials to the agent's actual needs.
- Review prompt templates and assigned tasks; an agent may act on instructions received from Paperclip.
- Keep sessions/worktrees isolated where appropriate and inspect outputs and logs.
- Do not expose the Paperclip API key or provider credentials through prompts, issue content, output, or logs.
- Test with non-sensitive data in an isolated environment before production use.

The adapter also merges configured environment values into the child process environment. Treat adapter configuration and secret references as trusted administrative input.

## Reporting

Do not post credentials, private task content, or exploit details in public issues. Use GitHub private vulnerability reporting if enabled for this repository; otherwise contact the maintainer through the private method listed on the maintainer's GitHub profile.

This document describes source-visible behavior. It is not a security audit or guarantee.
