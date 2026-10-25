# smart-resume-ai-privacy-policy

# Privacy Policy for Smart Resume AI

**Last Updated:** October 25, 2026  
**Effective Date:** October 25, 2026  

Smart Resume AI ("the Extension", "we", "us", or "our") is dedicated to protecting your personal privacy and safeguarding your professional information. This Privacy Policy details our data-handling practices, permissions architecture, and network communications for the Smart Resume AI Chrome Extension (Manifest V3).

---

## 1. Affirmative Statement on Chrome Web Store Limited Use

**Smart Resume AI's use and transfer to any other app of information received from Google APIs will adhere to the [Chrome Web Store User Data Policy](https://developer.chrome.com/docs/webstore/program-policies/user-data-policy/), including the Limited Use requirements:**

- **Single Purpose**: All user data accessed or processed by the extension is strictly used to provide or improve the core user-facing features prominently displayed in the extension's user interface—specifically, extracting job descriptions from active job postings, tailoring resumes and cover letters using user-provided API credentials, and assisting in attaching generated application materials to application forms.
- **No Advertising or Retargeting**: User data is **never** used, transferred, or disclosed for serving personalized advertisements, retargeting, interest-based advertising, or creditworthiness evaluations.
- **No Data Brokers or Sale of Data**: User data is **never** sold, rented, monetized, or transferred to third-party data brokers, information resellers, or marketing aggregators.
- **Strict Prohibition on Human Access**: No humans (including developers or contractors) have access to or read your candidate profile, resumes, or vault narratives, except under narrow, legally mandated conditions:
  1. You provide explicit, affirmative consent for troubleshooting a specific issue;
  2. Necessary for cybersecurity, fraud prevention, or technical bug resolution;
  3. Required to comply with a valid legal obligation or court order; or
  4. The information is aggregated and completely anonymized for statistical system metrics.

---

## 2. Core Architectural Privacy Principles

- **100% Local-First Storage**: Your resumes, candidate profiles, Experience Vault stories, and generation history reside exclusively within your local browser's storage (`chrome.storage.local` and `chrome.storage.session`). We do not operate any central servers, remote user databases, tracking pixels, or telemetry pipelines.
- **Bring Your Own Key (BYOK)**: All AI inference operations use your personal API keys (OpenRouter, DeepSeek, or Requesty). Your credentials are stored locally in your browser and are transmitted solely in the HTTPS `Authorization` header directly to the AI endpoint you configure.
- **Narrowest Permissions Architecture**: The extension requests only the minimum permissions required for its documented functionality. We do **not** declare `<all_urls>` in `host_permissions`. General web browsing is never passively monitored, recorded, or intercepted.

---

## 3. Verified Data-Handling & Storage Claims (Audited Against Code)

All claims below have been audited and verified directly against the extension codebase:

| Category | Data Items | Storage Mechanism | Transmission Destination | Third-Party Sharing |
|---|---|---|---|---|
| **Candidate Profiles** | Name, contact info (email, phone, city), LinkedIn/GitHub URLs, employment history, education, skills | `chrome.storage.local` (`rt_profiles`) | Sent directly to the configured AI provider when user clicks *Generate* | Shared only with user's chosen AI provider |
| **Experience Vault** | Strategic career narratives, flagship stories, quantifiable metrics | `chrome.storage.local` (`rt_experience_vault`) | Sent directly to the configured AI provider when user clicks *Generate* | Shared only with user's chosen AI provider |
| **API Credentials** | Provider API key (OpenRouter, DeepSeek, Requesty), model name, token limits | `chrome.storage.local` (`rt_api_config`) | Transmitted in `Authorization: Bearer <key>` header on inference calls | Only to the designated API endpoint |
| **Usage & Cost Data** | Prompt tokens, completion tokens, call count, estimated spend | `chrome.storage.local` (`rt_cost_tracker`) | Stays strictly on device; never transmitted | None |
| **Generation History** | Tailored resume text, cover letters, fit scorecard radar metrics | `chrome.storage.local` (`rt_history`) | Stays strictly on device; never transmitted | None |
| **Job Description Data** | Job title, company name, location, job description text | In-memory session / `chrome.storage.session` | Sent to configured AI provider to analyze fit and synthesize documents | Only to the designated AI provider |
| **Company Research** | Company name only (e.g., "Stripe", "Ramp") | Not persisted (ephemeral memory) | Queries public search snippets via DuckDuckGo (`https://html.duckduckgo.com`) | Public search query only; zero personal data |

### Outbound Network Requests Verification
The extension initiates network requests **exclusively** to the following endpoints upon explicit user action:
1. `https://openrouter.ai/api/v1/chat/completions` (OpenRouter AI inference gateway)
2. `https://api.deepseek.com/chat/completions` (DeepSeek chat completions API)
3. `https://router.requesty.ai/v1/chat/completions` or `https://api.requesty.ai/*` (Requesty AI router)
4. `https://html.duckduckgo.com/html/?q=...` (DuckDuckGo search snippets for public company intelligence)

**Zero Telemetry & Zero Analytics:**  
Smart Resume AI contains **no** third-party analytics libraries (such as Google Analytics, Mixpanel, Amplitude, or Sentry), **no** external tracking cookies, **no** web beacons, and **no** remote script loading.

---

## 4. Permissions Usage & Scope Justification

Smart Resume AI follows the principle of least privilege:

- **`storage`**: Persists candidate profiles, experience vaults, API keys, and application history locally within Chrome's isolated storage sandboxes (`chrome.storage.local` and `chrome.storage.session`).
- **`activeTab`**: Grants temporary, on-demand access to the current active browser tab **only when you explicitly invoke the extension** (by clicking the extension toolbar icon or opening the popup). This allows extracting the job description or attaching a PDF without requiring broad access to all websites.
- **`scripting`**: Executes isolated helper routines on the active job application tab to read visible role descriptions and dispatch file-attach events to `<input type="file">` elements upon your command.
- **`downloads`**: Allows saving synthesized PDF resumes and cover letters directly into your computer's local Downloads folder.
- **No Browsing History Access**: Smart Resume AI intentionally omits the general `tabs` permission. The extension **never** reads, tracks, or collects your browsing history or background tab URLs.
- **`host_permissions`**: Strictly restricted to our 4 approved AI service providers and DuckDuckGo (`https://openrouter.ai/*`, `https://api.deepseek.com/*`, `https://api.requesty.ai/*`, `https://router.requesty.ai/*`, `https://html.duckduckgo.com/*`). **`<all_urls>` is not requested.**
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
4. **API Key & Cost Reset**: In **Settings → API Settings & Cost Tracker**, you can clear your stored API keys or reset accumulated token counters to zero.
5. **Complete Uninstallation Purge**: When you uninstall or remove the Smart Resume AI extension from Chrome (`chrome://extensions`), Google Chrome automatically and permanently deletes all `chrome.storage.local` and session data associated with the extension.

---

## 6. Children's Privacy

Smart Resume AI is intended for adults seeking career employment and job opportunities. We do not knowingly collect, process, or store information from children under the age of 16.

---

## 7. Policy Updates

If we make material changes to how the extension handles data or introduces new network-enabled capabilities, we will update the "Last Updated" date at the top of this document, describe the changes in our repository release notes, and display an in-extension notification.

---

## 8. Concrete Support & Contact Information

For any inquiries, requests, please contact me through:
- **Policy URL:** [https://sanket-k.github.io/smart-resume/PRIVACY_POLICY.html](https://sanket-k.github.io/smart-resume/PRIVACY_POLICY.html)
- **Email:** [sanket0249@gmail.com](mailto:sanket0249@gmail.com)