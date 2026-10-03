<!-- SHOWCASE_START --><div align="center">[![Typing](https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=22&duration=2800&pause=900&color=58A6FF&center=true&vCenter=true&width=900&lines=production%20employee%20onboarding%20automation;AI%20%7C%20Automation%20%7C%20Engineering;Explore%20the%20project%20%F0%9F%9A%80)](https://github.com/shaikshahid777/production-employee-onboarding-automation)<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0D1117,50:161B22,100:58A6FF&height=110&section=header&text=production-employee-onboarding-automation&fontSize=26&fontColor=FFFFFF&animation=twinkling&fontAlignY=65" width="100%" alt="Animated project banner"/>

[![Repository](https://img.shields.io/badge/Repository-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/shaikshahid777/production-employee-onboarding-automation) [![Issues](https://img.shields.io/badge/Report-Issue-red?style=for-the-badge&logo=github)](https://github.com/shaikshahid777/production-employee-onboarding-automation/issues/new) [![Stars](https://img.shields.io/github/stars/shaikshahid777/production-employee-onboarding-automation?style=for-the-badge&logo=github)](https://github.com/shaikshahid777/production-employee-onboarding-automation/stargazers) [![Fork](https://img.shields.io/github/forks/shaikshahid777/production-employee-onboarding-automation?style=for-the-badge&logo=github)](https://github.com/shaikshahid777/production-employee-onboarding-automation/fork) [![Profile](https://img.shields.io/badge/Profile-Visit-0A66C2?style=for-the-badge&logo=github)](https://github.com/shaikshahid777)</div>

> ✨ **Project Showcase Mode:** animated banner • interactive navigation • live repository actions

[🚀 Repository](https://github.com/shaikshahid777/production-employee-onboarding-automation) · [🐞 Report Issue](https://github.com/shaikshahid777/production-employee-onboarding-automation/issues/new) · [⭐ Star](https://github.com/shaikshahid777/production-employee-onboarding-automation/stargazers) · [🔱 Fork](https://github.com/shaikshahid777/production-employee-onboarding-automation/fork) · [👤 Profile](https://github.com/shaikshahid777)

<!-- SHOWCASE_END -->

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
