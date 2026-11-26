# 05 — Security & Privacy

Guiding principle: **collect the minimum, keep it private by default, make deletion real.** MOMents stores data about families with young children, so every field has to justify itself.

## 1. Data inventory (what we collect and why)

| Data | Why | Visible to | Retention |
|---|---|---|---|
| Email (auth) | Sign-in, account recovery, legal notices | User; admin (support) | Until account deletion |
| Google account ID (auth) | Sign-in | System only | Until deletion |
| First name | Greeting | User only (MVP) | Until deletion |
| Profile photo (optional) | Personalization (future community) | User only (MVP) | Until deletion/replacement |
| General area (neighborhood) | Default search area, group suggestions | User only | Until deletion |
| Children: birth month/year, optional nickname | Age filters, checklist suggestions | **User only, never admins, never other users** | Until removed or account deletion |
| Preferred categories | Personalization | User only | Until deletion |
| Saved listings, reminders | Feature | User only; admin sees aggregate counts | Until deletion |
| Checklists & templates | Feature | User only | Until deletion |
| Tips, reports | Content quality | User (own) + admin | Kept, de-identified on account deletion |
| Consent records | Legal proof | User + admin | Duration of account + legally required period (confirm with lawyer) |
| Analytics events | Measure product use | Admin (aggregates) | 13 months raw, then aggregate; user link removed on deletion |
| Crash reports | Stability | Developer | Per Sentry retention; PII scrubbing on |
| **Precise GPS location** | Find nearby listings | **Not stored.** Used on device, sent rounded (~100 m) only as a query parameter | Not retained |

**Not collected:** exact birth dates, children's full names or photos, phone numbers (MVP), gender, home address, background location, contacts, calendar contents.

## 2. Threat model (main risks)

| Threat | Example | Mitigation |
|---|---|---|
| Data leak via API | Attacker uses the public anon key to read other users' data | RLS default deny on every table; pgTAP tests per policy; no `anon` table access |
| Privilege escalation | User sets themselves as admin | Roles in `user_roles`, writable only by admins; `has_role()` checks the table, not client claims |
| Secret exposure | Server key in app bundle or repo | Secrets only in Edge Function / CI secrets; secret scanning in CI; Android Maps key restricted to app signature |
| Location exposure | Inferring a mother's home from data | No stored GPS; only neighborhood; future community shows neighborhood name only; distance buckets, never exact |
| Malicious uploads | Non-image files, huge files, EXIF GPS in photos | Bucket MIME allow-list (jpeg/png/webp/heic) + size limit; **strip EXIF on device before upload** (avatars and admin uploads) |
| Spam/abuse via tips & reports | Flooding the queue | Per-user rate limits in DB; length limits; admin can disable a user |
| Harmful external groups | A listed WhatsApp group is a scam or predatory | Only listed with group-admin consent; admin review; report button; quick archive; explainer before leaving the app |
| Account takeover | Stolen email OTP | Short OTP expiry, rate limits; sessions revocable; sign-out-everywhere on deletion |
| Fake/inaccurate listings causing harm | Unsafe venue | Admin verification, last-verified badge, report wrong info, terms disclaimer |
| Lost device | Someone opens the app | Tokens encrypted at rest; OS lock screen; data is low-sensitivity on device |
| Admin account compromise | Full content access | Strong password manager + MFA on Supabase dashboard, Google Cloud, Play Console, admin account (Supabase Auth MFA/TOTP for admin role); audit log |

## 3. Security requirements

### Authentication
- Google sign-in and email OTP only; no passwords stored.
- MFA (TOTP) **required** for admin accounts (enforce: admin RLS policies check `auth.jwt()->>'aal' = 'aal2'`).
- Refresh-token rotation enabled; sessions revoked on account deletion.

### Data access
- RLS on 100% of tables (CI check: a query on `pg_tables` fails the build if any public table lacks RLS).
- `SECURITY DEFINER` functions only where necessary, with fixed `search_path` and explicit permission checks.
- Admin app uses the anon key + admin JWT, never the service-role key.
- Least-privilege Google Cloud keys: separate keys for Android Maps SDK and server Geocoding, each restricted.

### Transport & storage
- HTTPS only (Supabase default). Database encryption at rest (Supabase default).
- Daily backups on Pro (7-day retention per current plan); test a restore before launch.

### Images
- Buckets: `activity-images` (public read, admin write), `avatars` (private, owner folder only).
- Client-side: resize (max 1600 px listings, 512 px avatars), convert to JPEG/WebP, **strip metadata**.
- Server-side: bucket file-size limit and MIME allow-list.

### App hardening
- No sensitive data in logs; Sentry `beforeSend` scrubs emails/tokens/coordinates.
- Force-update mechanism (minimum supported version from a config row) to retire vulnerable builds.
- Dependency updates monthly; `npm audit`/Dependabot in CI.

## 4. Content moderation (MVP scope)

MVP has **no user-to-user content**, which keeps moderation small:

| Content | Who creates | Moderation |
|---|---|---|
| Listings | Admin | Admin verification; reports queue |
| Tips | Users | Private until an admin turns them into listings; never published automatically |
| Group directory | Admin (with group-admin consent) | Reports; periodic link check |
| Reports | Users | Admin queue; target reviewed within 72 h (MVP target) |

**v1.2 in-app community requirements (designed now):** report on every post/comment/profile; block user (hides both ways everywhere); keyword filter on posts; image moderation (manual approval or automated service — check pricing); moderator role and queue; ban/suspend; response to reports within 24 h (App Store guideline 1.2); published community guidelines; playdates at public venues only; no direct messages at first; no display of children's details beyond age buckets.

## 5. Children's information
- MOMents is for **parents**, not children: users must be 18+ (stated in terms, confirmed at sign-up). The app is not listed in Google Play's "Designed for Families" programme or in a kids category.
- Child data is limited to birth month/year and an optional nickname, used only for age-based filtering and checklist suggestions.
- Child data is **excluded from admin views** and analytics. Analytics receive at most an age bucket where needed (e.g. `age_bucket: "6-12m"` on `search_performed`), never per-child identifiers.
- No child photos are collected anywhere in MVP. Listing photos showing children must come from organizers with permission, or be avoided.

## 6. Consent management
- At sign-up: required acceptance of Terms + Privacy Policy (version recorded in `consents`).
- Separate, optional, unticked consent for product update emails.
- Location: OS permission with in-app explainer; app works without it (area picker).
- Notifications: OS permission requested only when the user first sets a reminder.
- Policy changes: bump version → prompt existing users to accept on next launch for material changes.

## 7. Data rights & deletion
- **In-app deletion** (Settings › Account › Delete account): calls `delete-account` Edge Function →
  1. delete avatar files from storage;
  2. delete `profiles` row (cascades to children, preferences, saved, checklists, templates, notification prefs, consents* (*or keep minimal proof if the lawyer advises));
  3. set `user_id = null` on analytics events, tips and reports;
  4. delete the `auth.users` record (revokes all sessions);
  5. cancel local reminders on device and clear local storage.
- **Web deletion request page** (required by Google Play for apps with accounts) on the marketing/legal site.
- **Access/export requests:** MVP via email to the support address, fulfilled manually within the legal deadline (confirm with lawyer); automated export in a later version.
- **Correction:** users can edit all their data in-app.

## 8. Legal & store compliance checklist

> Not legal advice. Have a privacy lawyer review before public launch.

**Israel — Privacy Protection Law (incl. Amendment 13, in force since August 2025)**
- [ ] Privacy notice explains: what data, purpose, whether providing it is mandatory, who receives it (Supabase, Google, Sentry, email provider), user rights.
- [ ] Data minimization documented (this doc §1).
- [ ] Database definition document / registration requirements checked with lawyer (Amendment 13 changed registration thresholds — confirm whether MOMents needs to register or only keep a definition document).
- [ ] Information security measures per the Privacy Protection (Data Security) Regulations — likely the basic tier for this dataset; confirm.
- [ ] Data processing agreements / terms accepted with processors (Supabase DPA, Google, Sentry).
- [ ] Cross-border transfer: data stored in the EU (Supabase region) — EU adequacy status generally permits transfer; confirm.
- [ ] Breach response: written plan (who to notify, how fast).

**GDPR** (if EU-based users are expected, e.g. expats): lawful basis, rights handling, DPA — largely covered by the above.

**Google Play**
- [ ] Privacy policy URL in listing and in-app.
- [ ] Data Safety form matches §1 exactly (including Sentry and analytics).
- [ ] Account deletion in-app **and** via web link.
- [ ] Location permission: foreground only, prominent disclosure before the system prompt.
- [ ] Target the required Android API level for the release year.
- [ ] New personal developer accounts: closed testing with the required minimum number of testers for the required consecutive days before production access (currently 12 testers / 14 days — verify).

**Apple App Store (v1.1)**
- [ ] Sign in with Apple (if Google sign-in offered).
- [ ] Privacy nutrition labels; App Tracking Transparency not needed (no tracking).
- [ ] In-app account deletion.
- [ ] Guideline 1.2 compliance before launching in-app community.

**Terms of Use must cover:** 18+ requirement, listings are informational and provided by third parties (no guarantee of accuracy or safety), external groups are governed by their platforms, acceptable use for tips/reports, termination, Israeli law and jurisdiction.
