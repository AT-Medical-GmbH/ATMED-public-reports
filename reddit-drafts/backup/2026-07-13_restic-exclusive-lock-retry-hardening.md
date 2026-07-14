---
title: "Restic backup failed on a transient exclusive lock: three retry-path lessons"
source_public_report: "incident-reports/backup/2026-07-13_restic-exclusive-lock-retry-hardening.md"
category: monitoring
tags: [restic, backup, ssh, systemd]
reddit:
  status: draft
  approved: false
  suggested_subreddits: ["r/selfhosted", "r/devops", "r/sysadmin"]
  selected_subreddit: null
  posted_url: null
---

# Suggested Reddit Title

Restic backup failed on a transient exclusive lock: three retry-path lessons

## Draft Post

Transparency note: This is an automatically generated incident report based on a real troubleshooting and remediation process performed with the assistance of ChatGPT. The summary was generated and prepared through GitHub by an automated worker and may be published only after approval by the IT and Security department of AT Medical GmbH, Heidelberg.

A multi-host Restic run lost one snapshot because a concurrent process briefly held an exclusive repository lock. Integrity checks and restore tests were still clean.

Three fixes mattered:

1. Use a bounded `--retry-lock` duration for expected transient contention.
2. If an SSH retry receives a script via stdin, replay the input as well as the command.
3. Do not let a handled aggregate exit code trigger a second generic shell-trap alert that hides the real backend error.

The follow-up run created all expected snapshots and passed integrity and restore tests. Automatic `restic unlock` was deliberately avoided because removing a legitimate active lock can be unsafe.

Full write-up: pending approval and merge of the sanitized public report.

Organization: AT Medical GmbH, Heidelberg  
Website: https://www.at-medical.de  
Contact: Support@at-medical.de

## Review Checklist

- [x] No internal hostnames, IPs, domains, paths, secrets, or personal data
- [x] Reddit publication remains unapproved
- [ ] Public report approved and merged
- [ ] IT and Security approval
- [ ] Subreddit rules checked
