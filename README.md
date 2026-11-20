# Privacy Policy for Smart Resume AI

**Last Updated:** November 18, 2026  
**Effective Date:** November 18, 2026  
**Policy Version:** 1.2.0  

Smart Resume AI ("the Extension", "we", "us", or "our") is dedicated to protecting your personal privacy and safeguarding your professional information. This Privacy Policy details our data-handling practices, permissions architecture, and network communications for the Smart Resume AI Chrome Extension (Manifest V3).

---

## 1. Affirmative Statement on Chrome Web Store Limited Use

**Smart Resume AI's use and transfer to any other app of information received from Google APIs will adhere to the [Chrome Web Store User Data Policy](https://developer.chrome.com/docs/webstore/program-policies/user-data-policy/), including the Limited Use requirements:**

- **Single Purpose**: All user data accessed or processed by the extension is strictly used to provide or improve the core user-facing features prominently displayed in the extension's user interface—specifically, extracting job descriptions from active job postings, tailoring resumes and cover letters using the built-in Free Trial proxy or user-provided API credentials, and assisting in attaching generated application materials to application forms.
- **No Advertising or Retargeting**: User data is **never** used, transferred, or disclosed for serving personalized advertisements, retargeting, interest-based advertising, or creditworthiness evaluations.
- **No Data Brokers or Sale of Data**: User data is **never** sold, rented, monetized, or transferred to third-party data brokers, information resellers, or marketing aggregators.
- **Strict Prohibition on Human Access**: No humans (including developers or contractors) have access to or read your candidate profile, resumes, or vault narratives, except under narrow, legally mandated conditions:
  1. You provide explicit, affirmative consent for troubleshooting a specific issue;
  2. Necessary for cybersecurity, fraud prevention, or technical bug resolution;
  3. Required to comply with a valid legal obligation or court order; or
  4. The information is aggregated and completely anonymized for statistical system metrics.

---

## 2. Core Architectural Privacy Principles

- **100% Local-First Personal Storage**: Your resumes, candidate profiles, Experience Vault stories, and generation history reside exclusively within your local browser's storage (`chrome.storage.local` and `chrome.storage.session`). We never store personal documents, resumes, API keys, or job descriptions in any Smart Resume AI server database, and we never load remote scripts or third-party tracking SDKs.
- **Built-In Free Trial Proxy (Default) & Bring Your Own Key (BYOK)**: To let new users try Smart Resume AI immediately with zero API setup, the default **Free Trial** mode routes AI inference requests through a stateless Cloudflare Worker proxy to OpenRouter using a developer-managed key, enforcing a daily quota (35 requests/day) via Cloudflare Durable Objects (`RateLimiterDO`) without logging or retaining resume/JD payloads. At any time, you can switch to **BYOK** mode using your personal API keys (OpenRouter, DeepSeek, or Requesty), which are stored locally in your browser and transmitted solely in the HTTPS `Authorization` header directly to the AI endpoint you configure.
- **Anonymous, Zero-PII Diagnostics (1-Click Opt-Out)**: To diagnose onboarding drop-offs, retention/lifecycle trends, parsing bugs, and provider errors without identifying users, the extension records strictly non-personalized operational events using a persistent pseudonymous local install ID (`HMAC-SHA256(local_seed, "smart_resume_install_v1")`). Never collects personal data, text content, URLs, or credentials; can be disabled in one click in Settings → Experience Vault → Generation Preferences (which immediately purges stored diagnostic logs and resets the local seed).
- **Narrowest Permissions Architecture**: The extension requests only the minimum permissions required for its documented functionality. We do **not** declare `<all_urls>` in `host_permissions`. General web browsing is never passively monitored, recorded, or intercepted.

---

## 3. Verified Data-Handling & Storage Claims (Audited Against Code)

All claims below have been audited and verified directly against the extension codebase:

| Category | Data Items | Storage Mechanism | Transmission Destination | Third-Party Sharing |
|---|---|---|---|---|
| **Candidate Profiles** | Name, contact info (email, phone, city), LinkedIn/GitHub URLs, employment history, education, skills | `chrome.storage.local` (`rt_profiles`) | Sent to the configured AI provider (directly in BYOK mode, or relayed via the stateless Free Trial proxy to OpenRouter) when user clicks *Generate* — with **PII Guard (default ON)** redacting recognized names, emails, phones, and personal profile links into opaque tokens first (see disclosure below); originals are restored locally | Shared only with user's chosen AI provider (and Cloudflare Worker relay in Free Trial mode) |
| **Experience Vault** | Strategic career narratives, flagship stories, quantifiable metrics | `chrome.storage.local` (`rt_experience_vaults`, `rt_experience_vault`) | Sent to the configured AI provider (or stateless Free Trial proxy → OpenRouter) when user clicks *Generate* (per selected Evidence Scope) — PII Guard's generic regex layers redact emails/phones/social URLs in the narrative; unknown free-text names are a documented limitation | Shared only with user's chosen AI provider (and Cloudflare Worker relay in Free Trial mode) |
| **API Credentials & Trial Quota** | Provider API key (OpenRouter, DeepSeek, Requesty), model name, token limits, optional Beta Invite Code, and daily Free Trial quota counters | `chrome.storage.local` (`rt_api_config`, `rt_trial_quota`); ephemeral daily rate-limit counter (`used`, `limit_val`, `reset_at`) keyed by client IP (`CF-Connecting-IP`) or optional Beta Code inside Cloudflare Durable Object (`RateLimiterDO`) | BYOK: `Authorization: Bearer <key>` sent directly to configured AI endpoint. Free Trial: `X-Client-Mode: trial` (+ optional `X-Beta-Code`) sent to stateless Cloudflare Worker proxy to forward sanitized prompts to OpenRouter | BYOK: Only the designated API endpoint. Free Trial: Cloudflare Workers/Durable Objects (ephemeral daily rate-limit counter only; zero prompt/resume storage) & OpenRouter |
| **Usage & Cost Data** | Prompt tokens, completion tokens, call count, estimated spend | `chrome.storage.local` (`rt_cost_tracker`) | Stays strictly on device; never transmitted | None |
| **Generation History** | Tailored resume text, cover letters, fit scorecard radar metrics | `chrome.storage.local` (`rt_history`) | Stays strictly on device; never transmitted | None |
| **Job Description Data** | Job title, company name, location, job description text | In-memory session / `chrome.storage.session` | Sent to configured AI provider (or stateless Free Trial proxy → OpenRouter) to analyze fit and synthesize documents | Only to the designated AI provider (and Cloudflare Worker relay in Free Trial mode) |
| **Company Research** | Company name only (e.g., "Stripe", "Ramp") | Not persisted (ephemeral memory) | Queries public search snippets via DuckDuckGo (`https://html.duckduckgo.com`), or in BYOK mode via DeepSeek Search / OpenRouter / Requesty web search tools (disabled in Free Trial mode, which uses DuckDuckGo only) | Public search query only; zero personal data |
| **Anonymous Funnel & Error Diagnostics** | Setup/generation status enums (`success`, HTTP error category `401`/`402`/`429`), length/role/match/token buckets (`500_1999`, `high_75_plus`), inferred role seniority (`practitioner`/`staff_principal`/`executive`), site adapter category (`linkedin`, `workday`), OS platform, locale, timezone, currency code, UI/PDF theme & paper size, local edit-delta ratio, sanitized stack frames, and persistent pseudonymous install UUID (`HMAC-SHA256`) | `chrome.storage.local` (`rt_diagnostic_log`, capped at 100 events; `rt_telemetry_queue`, capped at 50 events) | Batched over HTTPS to PostHog Cloud (`us.i.posthog.com/batch/`), where GeoIP resolves coarse location (Country, Region, City, Timezone) and links events to a persistent pseudonymous person profile (`$process_person_profile: true`) for unique-user, retention, and lifecycle insights; non-PII stage/error summary params appended to Tally uninstall survey (`https://tally.so/r/zExPrg`) | Aggregate anonymous analytics only (PostHog Cloud); zero PII, names, text, URLs, or API keys |

### PII Guard — Outbound Redaction Disclosure (Default Enabled)

PII Guard helps keep your contact details out of AI requests. When enabled, Smart Resume AI removes or replaces recognized names, email addresses, phone numbers, and personal profile links before sending prompts to your selected AI provider. Your original contact details are restored locally for your finished documents. Professional history and location may still be sent to the AI provider, and some identifiers in unstructured text may not be detected.

Verified mechanics (audited and enforced by automated tests):

- **What is redacted**: your full name (whole-name matching only), email addresses, phone numbers (digit-normalized matching, including differently formatted variants of your number), and personal/social profile links (LinkedIn, GitHub, X/Twitter, personal website, portfolio, other links) — anywhere they appear in the outgoing prompt, including inside pasted job descriptions.
- **Fail-closed gate**: before any request is transmitted, the serialized request body is scanned for every known protected identifier; if a known identifier cannot be fully redacted, the request is blocked rather than sent.
- **Local restoration**: redacted values are replaced by per-request placeholder tokens and re-inserted locally after the AI responds. The token mapping exists only in memory for the duration of a single request — it is not deliberately persisted to storage, written to logs (only token counts are logged), or shared between requests.
- **What still may be sent**: location, employers, job titles, education, work history dates, metrics, non-personal/business URLs, and unknown person names in unstructured text (their emails/phones/social URLs are still redacted). Distinctive employment history can still identify a candidate; this feature reduces direct-identifier exposure only and is not a guarantee of anonymity.
- **User control**: PII Guard can be disabled at any time in Settings → Generation Preferences; when disabled, prompts are sent unredacted.

### Outbound Network Requests Verification
The extension initiates network requests **exclusively** to the following endpoints:
1. `https://*.workers.dev/*` — Stateless Free Trial Cloudflare Worker proxy & Durable Object rate limiter when using default Free Trial mode)
2. `https://openrouter.ai/api/v1/chat/completions` & `/models` (OpenRouter AI inference gateway & dynamic model catalog)
3. `https://api.deepseek.com/chat/completions`, `/models`, & `/v1/tools/search` (DeepSeek chat completions, model discovery, and optional search API in BYOK mode)
4. `https://router.requesty.ai/v1/chat/completions`, `/models`, or `https://api.requesty.ai/*` (Requesty AI router)
5. `https://html.duckduckgo.com/html/?q=...` (DuckDuckGo search snippets for optional public company intelligence)
6. `https://boards-api.greenhouse.io/v1/boards/*` (Public Greenhouse job board API when resolving cross-origin embedded job postings)
7. Optional Zero-PII diagnostic endpoint (`https://us.i.posthog.com/batch/` or user-configured diagnostics endpoint, strictly disabled when opted out) — PostHog resolves coarse GeoIP location (Country, Region/State, City, Timezone) from the HTTPS request and associates anonymous non-PII environment/funnel traits (`$process_person_profile: true`, `$set`, `$set_once`) strictly with the persistent pseudonymous install UUID (`distinct_id`) for retention & lifecycle analytics — plus Chrome's native `setUninstallURL` survey redirect (`https://tally.so/r/zExPrg?v=...&stage=...`, passing `?telemetry=off` when opted out).

### Anonymous Zero-PII Telemetry & Diagnostic Control
Smart Resume AI contains **no** third-party advertising SDKs, **no** tracking cookies, **no** web beacons, and **no** remote script loading (`Manifest V3` compliant first-party `fetch()` only):
- **Persistent Pseudonymous Install ID**: Derived locally via Web Crypto `HMAC-SHA256(local_seed, "smart_resume_install_v1")` formatted as UUID v4 so multi-month retention and lifecycle insights work without personal identifiers. Disabling telemetry immediately purges the seed, breaking continuity with any prior diagnostic sessions.
- **Strict Zero-PII Runtime Guard (`assertZeroPii`)**: Enforces a strict event allowlist and blocks/redacts any key or string matching names, emails, phone numbers, resume/vault/cover-letter/JD text, company names, job titles, full page URLs, or API keys before persistence or dispatch.
- **1-Click Opt-Out & Instant Purge**: Enabled by default (`#pref-telemetry-enabled` in **Settings → Experience Vault → Generation Preferences**) and disclosed during first-run onboarding. Unchecking immediately stops recording and purges `rt_diagnostic_log`, `rt_telemetry_queue`, `rt_telemetry_seed`, and `rt_telemetry_milestones`.
- **1-Click Zero-PII Diagnostic Export**: Users can inspect and copy their sanitized local diagnostic JSON (`#copy-diagnostic-report-btn`) before sharing bug reports.

---

## 4. Permissions Usage & Scope Justification

Smart Resume AI follows the principle of least privilege:

- **`storage`**: Persists candidate profiles, experience vaults, API keys, and application history locally within Chrome's isolated storage sandboxes (`chrome.storage.local` and `chrome.storage.session`).
- **`activeTab`**: Grants temporary, on-demand access to the current active browser tab **only when you explicitly invoke the extension** (by clicking the extension toolbar icon or opening the popup). This allows extracting the job description or attaching a PDF without requiring broad access to all websites.
- **`scripting`**: Executes isolated helper routines on the active job application tab to read visible role descriptions and dispatch file-attach events to `<input type="file">` elements upon your command.
- **`downloads`**: Allows saving synthesized PDF resumes and cover letters directly into your computer's local Downloads folder.
- **No Browsing History Access**: Smart Resume AI intentionally omits the general `tabs` permission. The extension **never** reads, tracks, or collects your browsing history or background tab URLs.
- **`host_permissions`**: Strictly restricted to our approved Free Trial proxy, AI service/search providers, and public job board API (`https://*.workers.dev/*`, `https://openrouter.ai/*`, `https://api.deepseek.com/*`, `https://api.requesty.ai/*`, `https://router.requesty.ai/*`, `https://html.duckduckgo.com/*`, `https://boards-api.greenhouse.io/*`). **`<all_urls>` is not requested.**
- **`content_scripts`**: Scoped strictly to recognized job boards and Applicant Tracking Systems (ATS) to display the quick "Tailor Resume" button:
  - Ashby (`*.ashbyhq.com`)
  - Greenhouse (`*.greenhouse.io`, `boards.greenhouse.io`)
  - Lever (`*.lever.co`, `jobs.lever.co`)
  - Workday (`*.myworkdayjobs.com`, `*.workday.com`)
  - LinkedIn Jobs (`*.linkedin.com/jobs/*`)
  - Indeed (`*.indeed.com`)
  - Glassdoor (`*.glassdoor.com`)
  - Wellfound / AngelList (`*.wellfound.com`)
  - ZipRecruiter (`*.ziprecruiter.com`)
  - SmartRecruiters (`*.smartrecruiters.com`)
  - Jobvite (`*.jobvite.com`)
  - iCIMS (`*.icims.com`)
  - Breezy HR (`*.breezy.hr`)
  - Workable (`*.workable.com`)
  - RemoteOK (`*.remoteok.com`, `*.remoteok.io`)
  - Y Combinator Work at a Startup (`*.workatastartup.com`)

*For company-hosted career pages not listed above, the extension does not passively run in the background; script execution occurs only on-demand when you click the extension icon via `activeTab`.*

---

## 5. User Control, Data Access & Deletion Rights

You have complete and sovereign control over all data stored within the extension:

1. **Profile Editing & Deletion**: In **Settings → Profile & Work History**, you can inspect, edit, or permanently delete any profile at any time.
2. **Experience Vault Wipe**: In **Settings → Experience Vault**, you can delete individual stories, clear positioning narratives, or reset the entire vault with one click.
3. **History Deletion**: In the **History** tab, you can delete individual past generation records or click "Clear All History" to wipe the database.
