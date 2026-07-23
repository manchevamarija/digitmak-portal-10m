# DigitMak Portal V1 - PDF compliance verification

Verification date: 2026-07-22  
Specification: `digitmak-portal-v1-technical-spec.pdf`, 27 pages, dated 2026-06-15  
Project version: V19 final candidate

## Final classification

- Functional V1 implementation: COMPLETE
- Automated source verification: PASSED
- Production deployment readiness: PENDING EXTERNAL CONFIGURATION AND ACCEPTANCE

The repository implements the functional V1 scope described by the specification. It must not be labelled as a live production system until the external production checks listed below are completed.

## Compliance matrix

| PDF section | Result | Project implementation |
|---|---|---|
| Purpose and one-stop-shop scope | Complete | Public portal, contact intake, subscriptions, ticket help desk, meetings, staff and admin workspaces |
| Modular monolith architecture | Complete | .NET 10 modular API, application abstractions/services, EF Core and hosted workers |
| Frontend architecture | Complete | React, TypeScript, Vite, React Router, i18next and SignalR client |
| PostgreSQL and migrations | Complete in production configuration | Npgsql provider, EF migrations and PostgreSQL Docker service |
| File storage | Complete | Disk storage abstraction, metadata, checksum, MIME/signature/size validation, visibility and ClamAV production scanner |
| Roles and access | Complete | Client, subscriber entitlement, HelpDeskAgent, Expert and Admin with server-side authorization |
| Public DMA contact workflow | Complete | Full short DMA intake, six explicit DMA categories, consent, confirmation and admin handling |
| Registration and organizations | Complete | Registration, email verification, create/join organization, member and organization approval workflows |
| Offline subscriptions | Complete | Invitation, acceptance, pending payment, activation, 12-month expiry, cancellation and expiry worker |
| Tickets | Complete | Categories, priorities, statuses, assignment, recommendations, referral and attachments |
| Real-time ticket chat | Complete | SignalR hub, persisted messages, presence tracking, client/staff replies, internal notes and system events |
| Meetings | Complete | Requests, ticket linking, confirmation/rejection/completion/cancellation, internal calendar and ICS export |
| Data model | Complete | V12 user lifecycle fields plus all core entities, TicketAttachment and KPI documentation entities |
| API surface | Complete | PDF routes are implemented; compatibility aliases are included where needed |
| Notifications | Complete in code | Database queue, localized templates, Brevo SMTP sender, retry/backoff and scheduled expiry notices |
| Audit and compliance | Complete in code | Audit events, actor IP, old/new values, privacy/terms consent, disclaimer, retention and RBAC |
| Reporting and KPI foundation | Complete | KPI totals, detailed breakdowns, UTF-8 CSV and formatted Excel exports, plus manual KPI documentation upload/download |
| Internationalization | Complete | MK, EN and SQ public/auth/workspace text, enum labels, translated content and email rendering |
| Required frontend screens | Complete | Public, client, staff and admin areas from section 14 |
| Repository and CI/CD structure | Complete | Backend/frontend/tests/deploy/docs, GitLab stages and protected manual production deployment |
| VM deployment assets | Complete in repository | Docker Compose, Nginx, TLS initialization, systemd units, backup and restore scripts |
| Security baseline | Complete in code/configuration | HTTPS/HSTS configuration, headers, rate limits, strong passwords, refresh rotation, revocation and upload controls |

## Verification results

- Backend Release tests: 40 passed, 0 failed, including CSV/Excel compatibility, complete client-to-admin reply flow, ticket attachments, authorization and token revocation.
- Frontend tests: 5 passed, 0 failed.
- Frontend lint: passed.
- TypeScript and Vite production build: passed.
- Local backend health endpoint: HTTP 200.
- Client/admin demo flows were verified in the browser, including direct opening of a selected client ticket from the portal overview.
- The admin ticket area is the role-aware primary ticket destination; staff/admin see client tickets while clients see only their own tickets.
- The public request confirmation, partner area and ticket controls were visually checked after the final responsive fixes.

## Mandatory production acceptance items

These items require infrastructure or owner decisions and cannot be truthfully completed inside the source archive:

1. Create a protected `.env.production` on the target VM with real PostgreSQL, JWT, Brevo, domain, admin and ClamAV values.
2. Provision the Linux VM and configure the final DNS name.
3. Issue and verify the Let's Encrypt TLS certificate.
4. Send real MK, EN and SQ messages through the production Brevo account.
5. Run `deploy/rehearse-backup-restore.sh` against a non-production PostgreSQL instance and record the result.
6. Execute the GitLab staging deployment and complete UAT before the protected production deployment.
7. Obtain legal approval for Privacy Policy and Terms.
8. Obtain DigitMak approval for final branding and visual design.

## PDF open items

The following are explicitly deferred by section 20 and are not V1 completion defects: exact VM sizing, staging domain, final GitLab naming, final Brevo settings, final retention decisions, Moodle SSO protocol, direct Microsoft/Google calendar integration and future structured KPI-document workflows.

## Local demo limitation

`START-BACKEND.cmd` uses a persistent local SQLite database in `data/digitmak-dev.db` when no PostgreSQL connection is supplied. `START-FULL-SYSTEM.cmd` provides the PostgreSQL-based Docker environment. Production continues to use PostgreSQL.

The SQLite development path was also verified with a real restart: a client ticket was created, the backend was stopped, restarted, and the same ticket was read back successfully. SQLite data, generated output and local uploads are excluded from the sanitized delivery archive.
