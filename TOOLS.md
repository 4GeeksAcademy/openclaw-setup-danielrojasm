# TOOLS.md - Luna's Local Notes & Composio Services

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## Composio Services Configuration

**Google Workspace:**
- **Calendar:** `primary` (Daniel's main Google Calendar)
  - Default visibility: Show free/busy only for new events
  - Default duration: 30 minutes for unspecified meetings
  - Timezone: Europe/Madrid (CET/CEST)
  
- **Gmail:**
  - Default signature: "Saludos,\nDaniel"
  - Send as: Primary email address
  - Reply-to: Same as sender
  
- **Drive:**
  - Default folder: `OpenClaw/` (create if missing)
  - Sharing: Private by default
  - Naming: `YYYY-MM-DD-description.ext`

**GitHub:**
- **Default account:** Daniel's personal account
- **API token:** Use configured Composio token
- **Repo conventions:**
  - Issues: Use labels `bug`, `enhancement`, `question`
  - PRs: Require at least one review before merge
  - Branch: `feature/short-description` for new features

**Notion:**
- **Default workspace:** Personal workspace
- **Database IDs:** (to be filled as used)
  - Tasks database: ``
  - Notes database: ``
  - Projects database: ``

**Slack:**
- **Default workspace:** Personal/primary workspace
- **Default channel:** `#general` or DM based on context
- **Notification settings:** Respect channel notification preferences

**Discord:**
- **Default server:** Primary personal server
- **Default channel:** `#general` or based on task
- **Webhook usage:** For automated notifications

## Service Usage Guidelines

### When to Use Each Service

**Google Calendar:**
- Scheduling meetings/events
- Checking availability
- Setting reminders
- Daily agenda review

**Gmail:**
- Sending important notifications
- Email triage and organization
- Automated follow-ups
- **Ask before:** Bulk emails, sensitive content

**GitHub:**
- Repository management
- Issue/PR creation and updates
- Code reviews automation
- Release management

**Notion:**
- Knowledge base updates
- Task management
- Project documentation
- Meeting notes

**Slack/Discord:**
- Team communications
- Status updates
- Notification delivery
- Quick coordination

### Default Behaviors

**Time-sensitive actions:**
- All calendar events in Europe/Madrid timezone
- Email scheduling: Send immediately unless specified
- Reminders: 10 minutes before by default

**File handling:**
- Temporary files: Save to `/tmp/` or workspace `temp/`
- Important outputs: Save to workspace `outputs/YYYY-MM-DD/`
- Backups: Always suggest backup before major changes

**Error handling:**
- Retry once on network errors
- Log errors to workspace `logs/errors.log`
- Notify Daniel on persistent failures

## Authentication Notes

- **Composio tokens:** Managed via OpenClaw configuration
- **Token rotation:** Alert if token appears expired
- **Scopes:** Use minimal necessary scopes
- **Security:** Never log tokens or credentials

## Environment-Specific Settings

**Development:**
- API endpoints: Use staging if available
- Dry-run mode: Enable for testing
- Logging: Verbose in dev, concise in production

**Production:**
- Confirm destructive actions
- Validate inputs thoroughly
- Maintain audit trail

## Service Limits & Quotas

**Google:**
- Daily email send limit: Monitor to avoid throttling
- Calendar API: 1,000,000 queries per day
- Drive: 750 GB storage

**GitHub:**
- API: 5,000 requests per hour
- File size: 100 MB max per file

**Notion:**
- API: 3 requests per second
- Page size: Limit large operations

## Emergency Contacts

- **Primary:** Daniel (via OpenClaw chat)
- **Backup:** (To be configured if needed)
- **System alerts:** OpenClaw notification system

## Changelog

- **2024-01-01:** Initial configuration
- **2024-01-15:** Added Google Workspace defaults
- **2024-02-01:** Updated service usage guidelines

---

**Note:** This file is Luna's personal cheat sheet. Update it as services change or new patterns emerge.