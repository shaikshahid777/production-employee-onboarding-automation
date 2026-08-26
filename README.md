# Production Employee Onboarding Automation — Lesson 5 Assessment

Production-oriented n8n employee onboarding capstone using PostgreSQL, reusable sub-workflows, department routing, Slack approval, asynchronous webhook resumption, and centralized error logging.

## Architecture

```text
Pipeline A — Master Onboarding Orchestrator
POST /capstone-onboarding
        ↓
SQL_CheckDuplicates
        ↓
Duplicate?
   ├── Yes → Duplicate - Stop
   └── No → SQL_CreateProfile → SQL_CreateChecklist
                       ↓
                Route_Department
                  ├── Engineering → Pipeline B — IT Worker
                  └── HR/Marketing → Pipeline C — HR Worker
                                           ↓
                                  Slack Approval Request
                                           ↓
                                  Wait / Webhook Resume
                                           ↓
                                  SQL_UpdateApproval

Pipeline D — Global Error Logger
Error Trigger → SQL_LogError → capstone_error_log
```

## Database

The one-off database setup creates:
- `capstone_employees`
- `capstone_onboarding_tasks`
- `capstone_error_log`

The employee table includes duplicate protection through a unique email constraint and stores provisioning and approval state.

## Workers

### Pipeline B — IT Provision Worker
Receives `employee_id`, `department`, and `role`, simulates GitHub/IT provisioning, and updates `it_provisioned = TRUE`.

### Pipeline C — HR Provision Worker
Receives `employee_id`, `department`, and `role`, simulates payroll/benefits setup, and updates `hr_payroll_setup = TRUE`.

## Approval

Pipeline A sends a Slack approval request and pauses with a Wait node configured for webhook resumption using the `capstone-approval-response` suffix. The approval decision is persisted in PostgreSQL using `employee_id`.

## Error Handling

Pipeline D is intended to be configured as the Error Workflow for Pipelines A, B, and C. It captures workflow and execution metadata, failed node information, the error message, and stack trace in `capstone_error_log`.

## Testing

Use cURL against the deployed onboarding webhook. Validate:
1. New employee creation.
2. Duplicate email prevention.
3. Engineering → IT routing.
4. HR/Marketing → HR routing.
5. Worker status updates.
6. Approval callback and database status update.
7. Global error logging.

## Security / Deployment

Do not commit database passwords, Slack tokens, webhook secrets, or other credentials. Configure credentials through n8n Credential Manager and validate production webhook URLs and retention settings before deployment.

## Submission

This repository is intended to contain the exported Pipeline A/B/C workflow JSON files, SQL initialization script, documentation, screenshots/evidence, cURL validation commands, and deployment instructions.

Loom demonstration:
https://www.loom.com/share/8ce6f2f7be9b49e48f5f306b8be5847d
