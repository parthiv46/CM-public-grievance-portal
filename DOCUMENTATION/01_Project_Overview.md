# Project overview

## Problem statement

Citizens need a clear route to submit concerns about public services, while the Chief Minister’s Office (CMO) needs a reliable way to review, route and monitor those concerns. This prototype demonstrates those roles and the citizen-visible record of action.

## Objectives

- Provide public information, department and FAQ pages.
- Let citizens register, sign in, submit and track grievances, and give feedback on outcomes.
- Let the CMO review, assign and monitor cases.
- Let department officers investigate and submit an action-taken report for CMO review.
- Preserve a timestamped status history, serve supporting attachments and show stored-data statistics.
- Offer AI-assisted classification, prioritization and summaries with a human in control.

## Scope and limits

This is a runnable recruitment prototype, not a government service. It uses fictional seed data, MongoDB persistence and signed eight-hour bearer tokens. It does not send notifications, connect to government systems, or provide production-grade identity verification, durable session management, backups or operational monitoring. The app keeps a single in-memory view of records, so deploy one server instance. When `GEMINI_API_KEY` is configured, grievance triage uses Gemini; otherwise, a clearly labeled local classifier provides fallback suggestions. Suggestions require human review.
