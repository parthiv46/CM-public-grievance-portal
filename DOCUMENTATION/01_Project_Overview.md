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

This is a runnable local recruitment prototype, not a government service. It uses fictional data, a file-backed JSON database and in-memory bearer sessions. It does not send notifications, connect to government systems, or provide production-grade identity verification, durable session management, backups or operational monitoring. The local classifier used when no model key is configured is a labeled fallback, not completed AI integration.
