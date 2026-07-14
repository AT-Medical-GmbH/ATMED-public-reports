---
title: "Hardening a Restic backup workflow against transient exclusive locks"
date: "2026-07-13"
category: monitoring
tags: [restic, backup, ssh, systemd, incident-response]
source_internal_report: "private reference withheld"
sanitation_status: "sanitized-draft"
approval_status: "not-approved"
public_release: false
reddit:
  draft_available: true
  approved: false
  suggested_subreddits: ["r/selfhosted", "r/devops", "r/sysadmin"]
  selected_subreddit: null
  posted_url: null
---

# Hardening a Restic backup workflow against transient exclusive locks

## Summary

One operation in a multi-host Restic backup run failed because another process briefly held an exclusive repository lock. Repository checks and restore tests remained successful. The workflow was hardened to wait for transient locks and to make its SSH retry and error-reporting paths reliable.

## Problem and root cause

The backup command failed with `repository is already locked exclusively`. A concurrent Restic operation was the immediate cause. The impact was amplified because the backup did not wait for lock release, an SSH retry reused already-consumed standard input, and a handled non-zero aggregate result triggered a misleading generic error trap.

## Resolution

- Add a bounded `--retry-lock` duration to backup commands.
- Buffer scripts supplied through standard input so every SSH retry receives the complete payload.
- Separate expected aggregate exit codes from unexpected shell errors.
- Do not automatically remove locks without first proving that no legitimate process owns them.

## Verification

A complete follow-up run created every expected snapshot. Repository integrity checks and small restore tests succeeded for both storage targets, and the service exited successfully.

## Prevention and lessons

Distributed backup workflows must treat repository locking as normal coordination, not automatically as corruption. Retries must replay both the command and its input. Alerts should preserve the primary backend error instead of replacing it with a wrapper-level line number. A lock that exceeds the bounded wait should still fail visibly and require operator investigation.

## Disclaimer

This report is a sanitized technical troubleshooting note. Adapt commands and configuration to your own environment and never run an unlock command blindly against an active repository.
