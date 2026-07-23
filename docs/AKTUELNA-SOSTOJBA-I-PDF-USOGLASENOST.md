# DigitMak Portal - актуелна состојба и усогласеност со V1 спецификацијата

Датум на проверка: 22.07.2026  
Извор: `digitmak-portal-v1-technical-spec.pdf`, 27 страници, нацрт од 15.06.2026  
Проверена верзија: тековната работна копија по последните промени во состаноци, организациски пристап и барања за промена на корисничка сметка

## Краток заклучок

Функционалниот V1 опфат од PDF-от е имплементиран во кодот. Проектот е модуларен монолит со .NET 10 backend, React/TypeScript frontend, EF Core/PostgreSQL production конфигурација, Identity/JWT, SignalR, background workers, disk storage, audit, reporting и deployment assets.

Ова не значи дека системот е веќе production-пуштен. Реалниот домен, TLS, Brevo, GitLab deployment, production PostgreSQL, правното одобрување и официјалниот branding се надворешни acceptance чекори.

## Што точно постои

### Јавен портал

- Почетна, услуги, обуки, AI поддршка и контакт.
- Македонски, англиски и албански јазик.
- DMA контакт-форма со полињата од секција 7 на PDF-от.
- Шест експлицитни internal DMA категории.
- Согласност за контакт и прифаќање политика за приватност.
- Confirmation екран и database-queued confirmation e-mail.
- Privacy, Terms и AI help-desk disclaimer.
- Секција со осум DigitMak партнери, додадена според јавниот идентитет на DigitMak; ова не е точно дефинирано во PDF-от.

### Корисници и пристап

- Регистрација со е-пошта и лозинка.
- Потврда на е-пошта, заборавена лозинка и reset.
- ASP.NET Identity со силно hash-ирање на лозинки.
- JWT access token, refresh rotation, logout/revocation и проверка на активен корисник.
- `CreatedAt`, `UpdatedAt`, `EmailVerifiedAt`, `LastLoginAt`, status и preferred language.
- Улоги: Client, HelpDeskAgent, Expert и Admin.
- Subscriber е entitlement од активна лична претплата, не статична улога.
- Server-side resource authorization; frontend-от не е единствената заштита.

### Организации

- Креирање или приклучување кон организација.
- PendingApproval, Approved, Rejected и Suspended состојби.
- Admin одобрување, одбивање, суспендирање и повторно активирање.
- Членови и примарен контакт.
- Клиентот ја гледа својата организација.
- Admin ги гледа сите организации.
- Help-desk може да ја отвори организацијата поврзана со контакт-барање што има право да го обработува; ова ја отстрануваше последната 403 недоследност.

### Лични претплати

- Admin покана за точно определен корисник и неговата одобрена организација.
- Прифаќање покана од клиент.
- PendingPayment за offline плаќање.
- Банкарски инструкции внесени од admin и видливи само кога клиентот чека уплата.
- Рачна потврда на уплата и активација.
- Важност 12 месеци, expiry worker, cancellation и renewal.
- Историја на покани и статуси.
- Online card payment не е додаден, бидејќи PDF-от експлицитно заклучува offline payment.

### Нов workflow: промена на организација или претплата

Ова не е посебно барање во PDF-от, но е додадено за интерфејсот да има целосна смисла:

1. Клиентот избира Organization или Subscription и внесува објаснување.
2. Системот создава `AccountChangeRequest`, а не обичен тикет.
3. Не дозволува две паралелни pending барања од ист тип.
4. Admin го гледа барањето во секцијата Претплати.
5. Admin избира Одобри или Одбиј и може да остави објаснување.
6. Одлуката се audit-ира и се додава notification во редот за e-mail.
7. Клиентот гледа Pending, Approved или Declined и admin објаснување.
8. За одобрена промена на организација клиентот избира друга одобрена организација директно на истата страница. Системот го префрла активното членство и ја означува промената како применета.
9. За одобрена промена на претплата клиентот гледа дека admin може да испрати нова покана.

### Тикети и разговор

- Сите категории, приоритети и статуси од PDF-от.
- Креирање само со verified/active корисник, одобрена организација и активна лична претплата.
- „Мои тикети“ за клиент; „Клиентски тикети“ за staff/admin.
- Admin може да креира тикет во име на клиент.
- Assignment на help-desk и expert.
- New, Assigned, InProgress, Resolved и Closed workflow.
- Persisted SignalR разговор во реално време.
- Client message, staff reply, internal note и system event.
- Internal notes не се видливи за клиент.
- Final recommendation и referral recommendation.
- Email notification кога примачот не го гледа тикетот.
- Прилози поврзани со тикет и опционално со порака преку `TicketAttachment`.
- Клиентот може да отстрани сопствен дозволен прилог; admin може да ги прегледува документите.

### Состаноци

- Барање од dashboard или од тикет.
- Online и во живо.
- Organization, requested-by, assigned staff, linked ticket, preferred window, start/end, location/link, notes и status.
- Requested, Confirmed, Rejected, Completed и Cancelled.
- Assignee потврдува, одбива или предлага друг термин.
- Клиентот може да побара промена/презакажување и да откаже.
- Admin може да создаде состанок за конкретен клиент/организација.
- Staff календарот сега користи компактни картички; целата измена се отвора во посебен dialog.
- ICS export за цел календар и поединечен состанок, и за online и за состанок во живо.
- ICS може да се отвори во Outlook или да се import-ира во Google Calendar.

### Документи, KPI и извештаи

- FileObject metadata: име, патека, MIME, големина, checksum, uploader, entity, visibility и timestamps.
- Extension/MIME/signature/size проверки и root-path containment.
- ClamAV интеграција кога е конфигурирана; health check покажува degraded кога локално нема ClamAV.
- EvidenceFile и evidence templates.
- Рачно прикачување и поврзување со ticket, meeting, subscription, contact request или KPI period.
- KPI totals и детални групирања: контакти, тикети, состаноци и referrals.
- UTF-8 CSV export како што бара PDF-от.
- Форматиран XLSX export како дополнување над PDF-от.

### Известувања, audit и безбедност

- Database notification queue, background worker, retry/backoff и SMTP sender.
- Локализирани MK/EN/SQ e-mail templates.
- Audit за identity, organizations, subscriptions, contact requests, tickets, files, meetings, content и settings.
- Actor user, IP, entity, old/new/metadata JSON и timestamp.
- Audit записите не се editable преку UI.
- Rate limiting, HTTPS/HSTS production поставки и Nginx security headers.
- Secrets од environment/secret files.
- Health checks за application, database, storage, SMTP и antivirus.

## Архитектура

Backend-от е модуларен монолит: еден deployable API процес, но кодот е поделен по business modules. Minimal API endpoint класите ја имаат controller улогата. EF Core `DbContext` е unit-of-work; намерно нема генерички repository wrapper кој само би го дуплирал EF Core. Повторливите business workflows се во фокусирани application services.

```text
React SPA
   |
   v
.NET 10 modular monolith
   |- Identity
   |- Clients / Organizations / Subscriptions
   |- Public intake
   |- Staff / Tickets / Meetings
   |- Administration / Reports / Content
   |- Files / Notifications / Audit
   |
   v
EF Core -> PostgreSQL (production)
        -> SQLite (локална Windows демонстрација)
```

## Усогласеност по PDF секција

| PDF секција | Состојба | Забелешка |
|---|---|---|
| 1-3 Purpose/Architecture | Готово | Full portal, React, .NET 10, modular monolith, EF Core, SignalR |
| 4 Roles and access | Готово | Personal entitlement и server-side isolation |
| 5 Workflows | Готово | Contact, organization, offline subscription, ticket/chat, meeting |
| 6 Ticket categories | Готово | Сите 11 категории и 4 приоритети |
| 7 Public contact form | Готово | Сите наведени intake полиња и DMA mapping |
| 8 Data model | Готово | Core entities, lifecycle fields, attachments и evidence |
| 9 API surface | Готово | Предложените routes и compatibility aliases; има и дополнителни routes |
| 10 Notifications | Готово во код | Реално Brevo испраќање бара production credentials |
| 11 Audit/compliance | Готово во код | Legal текстовите бараат одобрување |
| 12 Reporting/KPI | Готово | CSV + дополнителен XLSX |
| 13 Internationalization | Готово | MK/EN/SQ, enum codes во база |
| 14 Frontend screens | Готово | Public, client, staff и admin области |
| 15-17 Repo/CI/deploy | Подготвено | Реален staging/production run бара VM/GitLab |
| 18 Security baseline | Подготвено во код/config | Реален TLS/ClamAV/secret acceptance бара инфраструктура |
| 19 Backlog | Имплементирано | Сите V1 milestones се присутни |
| 20 Open items | Надворешно/одложено | Не се V1 defects |

## Дополнувања што не ги бара PDF-от

- Account change approval workflow.
- Excel exports покрај CSV.
- Evidence template generator, иако template-driven workflow е deferred во PDF.
- Поединечен ICS download и reschedule flow.
- Admin meeting/ticket on behalf of client.
- Organization reactivation и subscription renewal.
- Attachment preview/delete UX.
- OAuth calendar configuration hooks и signed Moodle launch основа.
- Partner showcase и DigitMak-inspired јавен изглед.

## Што навистина не е завршено

Ова не може чесно да се означи како завршено само со локален код:

1. Финален Linux VM, sizing и disk allocation.
2. Финален domain/DNS и staging domain.
3. Реален Let's Encrypt сертификат и TLS acceptance.
4. Реални Brevo credentials и испраќање тестови на MK/EN/SQ.
5. Реално GitLab staging и protected production deployment.
6. Production backup/restore rehearsal со одобрен retention период.
7. Финален file-retention период.
8. Правно одобрени Privacy/Terms текстови.
9. Официјално одобрен visual design и branding assets.
10. Moodle-side SSO протокол и credentials.
11. Native Google/Microsoft two-way calendar sync. V1 има ICS; PDF ја остава native интеграцијата за V2.

## Код-хигиена и „човечки“ код

- C# е форматиран со CSharpier; TypeScript/CSS со Prettier.
- `.editorconfig` ги стандардизира UTF-8, завршните линии, indentation и whitespace правилата за различни уредувачи.
- GitLab frontend lint фазата задолжително извршува и `format:check` пред lint.
- Frontend lint се извршува со Oxlint.
- Nullable reference types и TypeScript strict build се активни.
- Именувањата се business-oriented: `SubscriptionInvitation`, `TicketAttachment`, `MeetingDecisionRequest`, `AccountChangeRequest`.
- Нема `TODO`, `FIXME`, `NotImplementedException` или скриени mock endpoints во активниот source.
- `Program.cs` е composition root, а business endpoints се по модули.
- Не е додаден бескорисен generic repository само за шаблонска „слоевитост“.
- Големата admin React страница сè уште е кандидат за понатамошно делење во feature components. Тоа е maintainability подобрување, не функционален или PDF defect.

## Последна автоматска проверка

- Backend Release: 42/42 тестови поминаа, вклучувајќи целосен client -> admin decision -> client account-change flow.
- Frontend: 6/6 тестови поминаа.
- Frontend lint: помина.
- TypeScript + Vite production build: помина.
- CSharpier и Prettier: применети.

## Како рачно да се провери новиот workflow

1. Најави се како client.
2. Отвори `Организација и претплата`.
3. Притисни `Побарај промена`, избери тип и внеси најмалку 10 знаци.
4. Најави се како admin и отвори `Претплати`.
5. Во `Барања за промена` внеси објаснување и избери `Одобри` или `Одбиј`.
6. Повторно отвори ја client страницата.
7. Провери го статусот и admin објаснувањето.
8. Ако е одобрена Organization промена, избери друга одобрена организација и употреби `Промени организација`. Потоа статусот станува `Применето`.
9. Ако е одобрена Subscription промена, admin испраќа нова покана преку постојниот subscription workflow.
