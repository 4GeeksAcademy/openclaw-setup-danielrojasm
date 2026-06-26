# 4Geeks Token Verification Skill

Verify a 4Geeks Academy student token against the BreatheCode API. Returns profile data and confirms whether the session is active.

## Token Source

All tokens are read from `/root/.openclaw/workspace/.env`.

| Variable | Description |
|---|---|
| `4GEEKS_TOKEN` | Student token for `breathecode.herokuapp.com` |

Every skill that needs an authentication token **must** source it from `.env`. No hardcoded tokens, no passing in chat.

## Steps

### 1. Load the token

```bash
source /root/.openclaw/workspace/.env
TOKEN="${4GEEKS_TOKEN}"
```

If `4GEEKS_TOKEN` is unset or empty, stop and tell the user to add it to `.env`.

### 2. Verify

```bash
curl -s -o /tmp/bc_verify.json -w "%{http_code}" \
  -H "Authorization: Token ${TOKEN}" \
  "https://breathecode.herokuapp.com/v1/auth/user/me"
```

### 3. Interpret the status code

| Code | Meaning |
|---|---|
| 200 | ✅ Valid |
| 401 | ❌ Invalid / expired |
| 403 | ❌ No access |
| 404 | ❌ Wrong endpoint |

### 4. Parse profile (on 200)

Fields:
- `email`, `first_name`, `last_name`, `username`
- `roles[].academy.name` — academy (e.g. "4Geeks Madrid")
- `roles[].role` — student / admin / teacher
- `date_joined` — account creation date
- `github.avatar_url`, `github.username` — linked GitHub
- `permissions[].name` — granted capabilities
- `settings.lang` — language preference

### 5. Document

Write timestamp, result, profile summary to `Skill_log.md`.

### 6. Next steps

Ask user what to build next: progress, tasks, submissions, mentorship, certificates.

## API Details

- **Base URL**: `https://breathecode.herokuapp.com/v1/`
- **Auth**: `Authorization: Token <token>`
- **Verified endpoint**: `GET /v1/auth/user/me`
- **Platform API** (separate): `https://api.4geeks.io/v1/` with `Authorization: Api-Key <key>`

## Security

- Never log the raw token in chat output — use masked form (`${TOKEN:0:4}…${TOKEN: -2}`)
- Store only in `.env`, never in code or chat
- `.env` is gitignored by convention
- Student tokens != platform API Keys (those start with `sk_`)