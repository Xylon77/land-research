# Land Research



# Gap Analysis: Government-Funded Land Management Apps in Bangladesh
## Objective

To evaluate the current state of Bangladesh's government-funded land management apps and document:
- Missing or incomplete service coverage
- Weaknesses in app architecture / UX flow
- Performance issues
- Recommendations for improvement

## Scope
This review covers the govt-funded land management apps available on the Play Store, including:
| App Name | Publisher | Primary Function 
| নামজারি (branded in-app as "মিউটেশন") | Ministry of Land (developed by Business Automation Ltd.) | Land mutation (name transfer) tracking |
| ভূমি (Bhumi) | Ministry of Land | Consolidated land services (mutation, land tax, e-Porcha, mouza maps) |
| ভূমি উন্নয়ন কর | Ministry of Land | Land development tax payment |
| ভূমি পিডিয়া | Ministry of Land | Land-related information repository |

### App Details (from in-app "About" page)

- **App name (branding):** নামজারি / মিউটেশন
- **Version reviewed:** 3.3.17
- **Owner:** Ministry of Land, Government of Bangladesh
- **Developed by:** Business Automation Ltd. (on behalf of Ministry of Land)
- **Stated purpose:** "Developed in addition to land portal (land.gov.bd) to ease the Mutation service of Bangladesh government."

## Architecture / Infrastructure Description
### High-level architecture

```mermaid
flowchart TD
    A[User] --> B[নামজারি / মিউটেশন Android App]
    B --> C[Home: প্রচ্ছদ]
    C --> D[Native Mutation Features]
    D --> D1[আবেদন অনুসন্ধান - Application Search]
    D --> D2[ধাপসমূহ - Process Steps]
    D --> D3[কর্মকর্তাগণ - Officials Directory]
    D --> D4[নির্দেশিকা - Guidelines]
    D --> D5[জিজ্ঞাসা - FAQ]
    D --> D6[প্রজ্ঞাপন ও বিধি - Notices and Rules]
    D --> D7[অভিযোগ - Complaints]

    B --> E[সকল সেবা - All Services tab]
    E --> F1[Icon 1: Unlabeled]
    E --> F2[Icon 2: Unlabeled]
    E --> F3[Icon 3: Unlabeled]
    E --> F4[Icon 4: Unlabeled]

    F1 -->|External Browser| G1[(portal.ldtax.gov.bd\nLand Development Tax)]
    F2 -->|External Browser| G2[(portal-citizen.land.gov.bd\nCitizen Land Portal)]
    F3 -->|External Browser| G3[(lsg-land-owner.land.gov.bd\nLSG Land Owner Portal)]
    F4 -->|External Browser| G4[(dlrms.land.gov.bd\nDLRMS - Digital Land Records)]

    style D fill:#d4f7d4,stroke:#2d862d
    style G1 fill:#fde2e2,stroke:#c0392b
    style G2 fill:#fde2e2,stroke:#c0392b
    style G3 fill:#fde2e2,stroke:#c0392b
    style G4 fill:#fde2e2,stroke:#c0392b
```

**Observed components:**

- **Client Layer:** Native Android application, functioning mainly as a shell for the mutation workflow (application tracking, officials directory, guidelines, complaints).
- **Core Service Handling:** Only mutation-specific features are handled natively inside the app.
- **External Dependency Layer:** The "সকল সেবা" (All Services) tab exposes **4 unlabeled icons** that, on tap, hand off to **4 separate government web domains** via the external browser rather than an in-app view:
  - `portal.ldtax.gov.bd` : Land Development Tax
  - `portal-citizen.land.gov.bd` : Citizen Land Portal
  - `lsg-land-owner.land.gov.bd` : LSG Land Owner Portal
  - `dlrms.land.gov.bd` : DLRMS (Digital Land Records Management System)
- **No shared session layer:** Each portal appears to run its own independent login/session, separate from the app and from each other.

This confirms the backend is a **collection of siloed government web portals**, each serving one service domain, with the mobile app acting as a thin, single-purpose wrapper (mutation) plus a set of bare links to the rest.

---

## Findings — Gaps & Limitations

### 1. Narrow Service Scope (By Design)
The app's own "About" page states it exists specifically to "ease the Mutation service" , confirming this is not an oversight but a deliberate scoping decision. Other essential land management services (land tax, citizen land records, LSG land ownership, DLRMS) are **not integrated** as native features; they exist only as external links.

### 2. Slow App Loading / Startup Performance
On launch, the app exhibits noticeably slow loading before the home screen becomes usable. For a citizen service app used for time-sensitive submissions (mutation deadlines, fee payments), this is a meaningful usability barrier, especially on lower-end devices or weaker network connections, which are common among the app's target users.

### 3. Unlabeled Navigation Icons ("সকল সেবা")
The "All Services" tab presents **4 icons with no text labels**. Users cannot know what each icon leads to without tapping it first : a significant discoverability and trust problem, especially for a government app where users need confidence before entering sensitive personal/land data.

### 4. Broken In-App Experience via External Browser Redirects
Each of the 4 "সকল সেবা" icons redirects to a **separate external domain in the device's default browser** instead of:
- Opening an in-app WebView, or
- Providing a native in-app module for that service

## Recommendations

- **Label every icon** in "সকল সেবা" clearly : a one-line UX fix with an outsized trust/usability benefit.
- **Replace external browser redirects with in-app WebViews** at minimum, to preserve navigation context and branding.
- **Introduce a shared authentication/session layer** (e.g. SSO) across `land.gov.bd`, `ldtax.gov.bd`, and related subdomains so users log in once.
- **Optimize app startup performance** : investigate whether slow loading is due to unnecessary network calls on launch, unoptimized assets, or backend latency.
- **Consolidate services long-term:** the existing **ভূমি (Bhumi)** app appears to already attempt integrating mutation + tax + records : worth a direct comparison in a future version of this report.
- **API-first backend design** so any single app can pull all land services through one unified interface instead of four separate portals.
.
.
.
.
.
.
.
.
.
.
.
# Gap Analysis: ভূমি (Bhumi) App — Beta Version Review
## Objective

To evaluate ভূমি's current functionality and document:
- UI/UX accessibility issues for its target user base
- Release readiness (beta status, broken features)
- Actual vs. advertised service coverage
- Recommendations for improvement

## App Details

- **Status:** Explicitly labeled **"(বেটা ভার্সন)"** (Beta Version) in the app header.
- **Tagline:** "এক ঠিকানায় ভূমিসেবা" : positions itself as the unified land-service destination.
- **Menu structure:** A hamburger menu with **16 items** : Home, Services, Offices, Laws & Rules, Notices, Notifications, Nearest LSFC Office, Calculator, QR Code, Accessibility, Settings, Complaints, FAQ, Feedback, About Us, Login . all displayed **in English**.
- **Core services grid:** 8 tiles on the home screen : Mutation, Land Development Tax, Khatian and Map, Land Acquisition, Lease Management, Land ADM Mgmt, Land Revenue Case, Land Information Bank.


## Architecture / Infrastructure Description
### Service & menu structure

```mermaid
flowchart TD
    A[User] --> B[ভূমি App - Beta]
    B --> C[Hamburger Menu - 16 items, English only]
    B --> D[Home Screen: 8 Core Service Tiles]

    D --> S1[✅ Mutation - Working]
    D --> S2[✅ Land Development Tax - Working]
    D --> S3[✅ Khatian and Map - Working]
    D --> S4[❌ Land Acquisition - No data found]
    D --> S5[❌ Lease Management - Under construction]
    D --> S6[❌ Land ADM Mgmt - Under construction]
    D --> S7[❌ Land Revenue Case - Under construction]
    D --> S8[❌ Land Information Bank - Under construction]

    C --> M1[✅ Offices - Division/District/Upazila locator - Working]
    C --> M2[✅ QR Code - Document verification - Working]
    C --> M3[✅ FAQ - Working]
    C --> M4[❌ Calculator - Under construction popup]
    C --> M5[❓ Notices / Notifications / Complaints / Settings / etc. - Untested]

    style S1 fill:#d4f7d4,stroke:#2d862d
    style S2 fill:#d4f7d4,stroke:#2d862d
    style S3 fill:#d4f7d4,stroke:#2d862d
    style S4 fill:#fde2e2,stroke:#c0392b
    style S5 fill:#fde2e2,stroke:#c0392b
    style S6 fill:#fde2e2,stroke:#c0392b
    style S7 fill:#fde2e2,stroke:#c0392b
    style S8 fill:#fde2e2,stroke:#c0392b
    style M1 fill:#d4f7d4,stroke:#2d862d
    style M2 fill:#d4f7d4,stroke:#2d862d
    style M3 fill:#d4f7d4,stroke:#2d862d
    style M4 fill:#fde2e2,stroke:#c0392b
```

### Service completion status

```mermaid
pie showData title Bhumi App — 8 Core Services: Working vs Non-Functional
    "Working (Mutation, Tax, Khatian & Map)" : 3
    "Not Working (Acquisition, Lease, ADM Mgmt, Revenue Case, Info Bank)" : 5
```

**Observed components:**

- **Client Layer:** Native app intended to be the unified land-services hub, avoiding the external-redirect pattern seen in other Ministry apps.
- **Menu Layer:** 16 English-only menu entries; at least one (Calculator) returns a **"This service is under construction"** popup on tap.
- **Service Layer:** Of the 8 headline service tiles, only **3 are functional** : Mutation, Land Development Tax, and Khatian and Map. The remaining 5 show either an empty **"No data found"** state or a **"দুঃখিত, সেবাটি নির্মাণাধীন"** ("Sorry, this service is under construction") screen.
- **Working auxiliary features:** Office locator (Division → District → Upazila/Circle, with a Land Office / Citizen Land Service Center toggle), QR code document verification (branded "VumiSheba"), and FAQ all work independently of the main 8 services.
- **Localization / QA defect:** The home banner reads **"THE MINISTRY OF LAND IS V 30 August 2026"**, and the under-construction screen reads **"WORKING TO DELIVER CITIZE[N] ... 30 August 2026."** These read like two halves of one sentence that a template/placeholder failed to render . a sign the beta shipped without full QA on its dynamic text strings.

## Findings — Gaps & Limitations

### 1. UI/UX Overload for the Target Audience
A 16-item, **English-only** hamburger menu : including technically-named entries like "Land ADM Mgmt" and "Nearest LSFC Office" . is a heavy cognitive load for an app whose actual end users are the general Bengali-speaking public, many with limited English proficiency or technical familiarity. There is no visible language toggle to switch the interface to Bengali.

### 2. App Still Officially in Beta
The **"(বেটা ভার্সন)"** tag is shown directly in the app header. A beta-labeled build being distributed as the Ministry's primary "all land services in one place" app sets citizen expectations poorly and signals the developers themselves don't consider it production-ready.

### 3. Majority of Advertised Services Are Non-Functional
Of the 8 core services advertised on the home screen, only 3 actually work. The other 5 : **Land Acquisition, Lease Management, Land ADM Mgmt, Land Revenue Case, and Land Information Bank** that return either an empty "No data found" state or an explicit "under construction" message. In practice, the app currently delivers roughly **37.5%** of its own advertised core functionality.

### 4. Broken / Incomplete UI Strings
Malformed banner and popup text (e.g. "THE MINISTRY OF LAND IS V 30 August 2026", "WORKING TO DELIVER CITIZE...") indicate unfinished localization or template logic shipped to production : a quality-assurance gap rather than a content or design decision.


## Recommendations

- **Add a Bengali-language toggle** and simplify/regroup the 16-item menu for non-technical users : group related items (Notices/Notifications, Complaints/Feedback) instead of listing all 16 flat.
- **Communicate beta limitations honestly:** replace silent "No data found" / generic "under construction" dead-ends with a clear "Coming Soon" state that sets expectations rather than looking broken.
- **Prioritize finishing the 5 non-functional core services** (Land Acquisition, Lease Management, Land ADM Mgmt, Land Revenue Case, Land Information Bank) before promoting the app further as a complete solution.
- **Fix the malformed template strings** : indicates a need for a stronger QA/localization review step before release.
- **Consider a phased public rollout:** hide or clearly gate not-yet-working tiles instead of shipping all 8 visibly, which currently makes ~62% of the home screen a dead end for users.
.
.
.
.
.
.
.
.
.
.
.
# Gap Analysis: ভূমি উন্নয়ন কর (Land Development Tax) App
## Objective

To evaluate the app's onboarding/authentication experience and document:
- Session persistence issues
- Identity verification burden on the user
- Recommendations for improvement

## App Details

- **Full name:** ভূমি উন্নয়ন কর ব্যবস্থাপনা সিস্টেম (Land Development Tax Management System)
- **Login options:** নাগরিক লগইন (Citizen Login) and সংস্থা লগইন (Organization Login)
- **Language toggle:** English/Bengali (বাং) switch present on the welcome screen

## Architecture / Infrastructure Description
### Authentication & onboarding flow

```mermaid
flowchart TD
    A[User Opens App] --> B[Welcome Screen]
    B --> C{Login Type}
    C -->|Citizen| D[নাগরিক লগইন]
    C -->|Organization| E[সংস্থা লগইন]

    D --> F[Login Form]
    F --> F1[Mobile Number +880]
    F --> F2[Password]
    F --> F3[CAPTCHA - image text entry]
    F1 & F2 & F3 --> G[লগইন করুন - Submit]

    G --> H[Profile Data Verification Gate]
    H --> I1[✅ Mobile Number - auto-verified]
    H --> I2[⬜ NID - জাতীয় পরিচয়পত্র - manual verify]
    H --> I3[⬜ Birth Registration - জন্ম নিবন্ধন - manual verify]
    H --> I4[⬜ Passport - manual verify]

    I1 & I2 & I3 & I4 --> J[Full Access to Tax Services]

    style F3 fill:#fde2e2,stroke:#c0392b
    style H fill:#fff3cd,stroke:#c99a2e
    style I2 fill:#fde2e2,stroke:#c0392b
    style I3 fill:#fde2e2,stroke:#c0392b
    style I4 fill:#fde2e2,stroke:#c0392b
```

**Observed components:**

- **Login layer:** Mobile number + password + a manually-typed CAPTCHA, required on **every** login . there is no persistent session, "remember me," biometric unlock, or refresh-token mechanism observed. Logging out means repeating the entire flow, CAPTCHA included, next time.
- **Identity verification gate:** After login, before the app is usable, the user is asked to verify **four separate identifiers simultaneously**: mobile number (auto-verified from login), National ID (NID), Birth Registration, and Passport . each requiring a separate "যাচাই করুন" (Verify) action, likely against different government identity systems (NID database, birth registration database, passport database).
- **No apparent fallback/partial-access path:** The screen frames this as required "to complete the ভূমি উন্নয়ন কর profile," suggesting a citizen without all four documents readily available/verifiable digitally could be blocked from progressing.

## Findings — Gaps & Limitations

### 1. No Persistent Session — Full Re-Login Required Every Time
Once a user logs out, there is no lighter-weight way back in. Every session start requires re-entering the mobile number, password, and a freshly generated CAPTCHA. For a service citizens may need to check periodically (e.g. tax due dates, payment confirmations), this repeated friction discourages regular use and increases the chance of failed login attempts (mistyped CAPTCHA, forgotten password prompting resets).

### 2. Four Simultaneous Identity Verifications Required
To complete the profile, the user must verify **NID, Birth Registration, and Passport** all at once (mobile is auto-verified via login). This is a heavy burden:
- Not every citizen holds a passport, so requiring it alongside NID and birth registration risks excluding a segment of legitimate taxpayers who only have one or two of these documents.
- Presenting all four as needed **simultaneously**, rather than accepting any one valid national identifier, adds unnecessary friction to a process that fundamentally just needs to confirm "who is this taxpayer."
- No visible explanation for *why* three separate document types are needed for a tax-payment app, which can come across as disproportionate data collection for the stated purpose.


## Recommendations

- **Introduce persistent sessions:** support "remember this device," refresh tokens, or biometric/PIN unlock so returning users aren't forced through full CAPTCHA-based login every time.
- **Reduce CAPTCHA friction:** consider CAPTCHA only after repeated failed attempts (adaptive/risk-based) rather than on every single login.
- **Make identity verification flexible, not simultaneous:** allow the user to complete their profile with **any one strong identifier** (NID is typically sufficient for Bangladeshi citizens) rather than requiring NID, Birth Registration, and Passport all at once. Reserve additional verification for edge cases (e.g. no NID available).
- **Explain the "why":** a short note on why each document is requested (e.g. NID for identity, Passport only if NID unavailable) would reduce user confusion and hesitation around sharing sensitive documents.
- **Allow partial/staged access:** let users view basic tax information before full identity verification is complete, reserving full verification for the payment step where it's actually necessary.
.
.
.
.
.
.
.
.
.
.
# Gap Analysis: ভূমি পিডিয়া (Bhumi Pedia) App
## 🎯 Objective

To evaluate ভূমি পিডিয়া's information architecture and account system, and document:
- Redundant/repetitive navigation structures
- Unclear content organization (documents vs. news)
- Unlabeled navigation elements
- Account registration and authentication reliability

---

## App Details

- **Purpose:** Central repository for land-related legal/regulatory documents plus land service shortcuts and Ministry news.
- **Home page document categories (12):** Acts, Ordinances, Presidentorders, Rules, Policies, Guidelines, Circulars, Notifications, Mous, Manuals, Gazettes, Others.
- **Land Service shortcuts (6):** Mutation, Bhumi Unnoyon Tax, Bhumi Record Map, Land Acquisition and Occupation, Land Revenue Case, Land Information Bank.
- **Bottom navigation:** 5 icon-only tabs : Home, and four further tabs the reviewer had to guess at by icon alone (E-book, Blog/News, a chat-style icon, and an AI bot icon).

---

## Architecture / Infrastructure Description

The app's home page surfaces the same 12 document categories through **three separate, redundant UI paths**, mixes legal-document content with news/blog content in the same feed without clear visual separation, and gates some bottom-nav features behind a login wall that isn't consistently or clearly communicated.

### Home page navigation structure

```mermaid
flowchart TD
    A[User Opens App] --> B[Home Page]

    B --> C1[Home Grid: 12 Document Category Tiles]
    B --> C2[Hamburger Menu ≡ - same 12 categories]
    B --> C3["Key... Dropdown - same 12 categories again"]

    C2 --> BUG[⚠️ Extra 'Null' entry appears at top of hamburger list]

    B --> D[Interleaved Feed: Gazettes / Notices / Guidelines mixed with News Articles]
    B --> E[Land Service Section - 6 Tiles]
    E --> E1[Mutation]
    E --> E2[Bhumi Unnoyon Tax]
    E --> E3[Bhumi Record Map]
    E --> E4[Land Acquisition and Occupation]
    E --> E5[Land Revenue Case]
    E --> E6[Land Information Bank]

    B --> F[Bottom Nav - 5 unlabeled icons]
    F --> F1[🏠 Home]
    F --> F2["📖 icon - guessed: E-book"]
    F --> F3["📰 icon - guessed: Blog/News"]
    F --> F4["💬 icon - guessed: Notifications/Chat"]
    F --> F5["🤖 icon - AI bot"]

    F3 -->|Tapped| G1[⚠️ Blank white screen - no content, no error, no loader]
    F5 -->|Tapped| G2[⚠️ 'Sign in Required' - plain text, no login button]

    style BUG fill:#fde2e2,stroke:#c0392b
    style C1 fill:#fff3cd,stroke:#c99a2e
    style C2 fill:#fff3cd,stroke:#c99a2e
    style C3 fill:#fff3cd,stroke:#c99a2e
    style G1 fill:#fde2e2,stroke:#c0392b
    style G2 fill:#fde2e2,stroke:#c0392b
```

### The critical issue: authentication dead-end

Testing registration and login with the same phone number produced two **contradictory** server responses, and the recovery path failed outright:

```mermaid
sequenceDiagram
    participant U as User
    participant App as Bhumi Pedia App
    participant Auth as Auth Backend

    U->>App: Fill registration form (name, phone, email, password)
    U->>App: Tap "Register"
    App->>Auth: Submit registration
    Auth-->>App: Error — "Server error or Phone number already exist"
    Note over U,App: Ambiguous: is it a server failure, or a duplicate account?

    U->>App: Try "Login" with same phone number instead
    App->>Auth: Submit login credentials
    Auth-->>App: Error — "This phone number is not registered! Please Sign Up"
    Note over U,App: Directly contradicts the registration error above.

    U->>App: Try "Forgot Password" with same phone number
    App->>Auth: Request OTP
    Auth-->>App: Error — "Server error"
    Note over U,App: All three paths (Register, Login, Reset) now fail.<br/>User is completely locked out with no way forward.
```

**Observed components:**
- **Redundant navigation layer:** The same 12 document categories are exposed through the home grid, the hamburger drawer, and a separate "Key..." filter dropdown , three different UI surfaces doing the same job, with no apparent reason for the duplication.
- **Data/rendering bug:** The hamburger menu additionally shows a stray **"Null"** entry above the real categories , a classic symptom of an unhandled empty/undefined value being rendered directly into the UI list.
- **Mixed content model:** Legal documents (Gazettes, Guidelines, Orders) and Ministry news/blog posts (with view/like/comment/share counts) appear in the same interleaved feed and grid layout, with no clear visual distinction between "this is an official document" and "this is a news post."
- **Unlabeled, inconsistent bottom navigation:** None of the 5 bottom tabs carry text labels. Two of them lead to broken or unclear states , one to a completely blank screen, another to a bare "Sign in Required" message with no visible way to actually sign in from that screen.
- **Broken authentication loop:** Registration, login, and password-reset each fail in ways that contradict one another, leaving no working path into an account.


## Findings — Gaps & Limitations

### 1. Triple-Redundant Document Category Navigation
The same 12 categories (Acts, Ordinances, Rules, Policies, etc.) are shown on the home grid, again in the hamburger menu, and again in the "Key..." dropdown , three ways to reach the identical list. This adds visual clutter and cognitive overhead without adding any distinct functionality.

### 2. "Null" Entry in Hamburger Menu (Rendering Bug)
The slide-out menu shows a broken **"Null"** item above the real category list , a visible software defect that undermines confidence in a government information portal.

### 3. Legal Documents and News Content Interleaved Without Separation
Between the document categories and the "Land Service" section, the feed mixes official gazettes/notices with Ministry news articles in the same visual format. A citizen looking for a specific regulation has to sift through unrelated news posts to find it, and vice versa.

### 4. Unlabeled Bottom Navigation Icons
All 5 bottom tabs are icon-only. The reviewer could only guess at three of them (E-book, Blog, Notifications) by shape alone , a discoverability problem consistent with the other Ministry of Land apps reviewed in this series.

### 5. Broken/Unclear Gated States
Tapping the icon guessed to be "Blog/News" produces a **completely blank screen** : no content, no loading indicator, no error message. Tapping the AI bot icon shows **"Sign in Required"** as plain unstyled text, with no visible button or link to actually go log in from that screen.

### 6. Critical: Contradictory, Fully Broken Authentication Flow
This is the most severe issue found:
- **Registering** a new account with a phone number returns: *"Server error or Phone number already exist."*
- **Logging in** with that same phone number returns: *"This phone number is not registered! Please click Sign Up button for Registration!"*
- **Resetting the password** for that same phone number via OTP returns: *"Server error."*

All three paths contradict each other and all three fail. A user hitting this cannot register, cannot log in, and cannot recover their account , a complete dead end that blocks access to any feature requiring sign-in (including the AI bot noted above).

## Recommendations

- **Consolidate document navigation to a single source of truth** : pick one of the home grid, hamburger menu, or dropdown filter as the primary category browser and remove (or clearly differentiate the purpose of) the others.
- **Fix the "Null" rendering bug** in the hamburger menu : indicates a missing null/empty check in the menu data source.
- **Visually separate legal documents from news content** : distinct card styles, section headers, or separate tabs so citizens can tell an official gazette apart from a news post at a glance.
- **Label every bottom navigation icon** with text, consistent with the recurring pattern seen across all four Ministry of Land apps reviewed in this series.
- **Replace blank/bare error states with real UI:** a proper empty state or loading indicator instead of a blank screen, and a functional "Login" button directly on the "Sign in Required" screen.
- **Fix the authentication backend immediately** : this is a launch-blocking defect. Registration, login, and password reset must return **consistent, accurate** states: if a number is already registered, login should work; if it doesn't exist, registration should succeed; and OTP delivery must not fail outright. This should be treated as the highest-priority fix in this report, since it fully locks users out of the app's account-gated features.


## Conclusion

1.ভূমি is architecturally the more ambitious of the Ministry's land apps — a genuine attempt at a single, unified service hub rather than a thin wrapper pointing elsewhere. But its beta status is not just a label: 5 of its 8 headline services are non-functional, its menu is inaccessible to much of its target audience due to English-only labeling, and visible template/localization bugs suggest the release shipped ahead of adequate QA. The auxiliary features that do work (office locator, QR verification, FAQ) show the underlying capability is there — the gap is in completion and polish, not concept.
 
---

2. ভূমি পিডিয়া is conceptually a reasonable idea , a single home for land-related legal documents . but its execution has real structural and technical problems. The home page repeats itself three times over for no functional benefit, mixes legal documents with news content in a way that undermines its purpose as a reference repository, and leaves navigation icons unlabeled just like its sibling apps in this ecosystem. Most seriously, its authentication system is fully broken: registration, login, and password recovery each fail with mutually contradictory error messages, meaning no new user can currently create and access an account at all. Of all four apps reviewed in this series, this is the one with the most severe, launch-blocking defect.
.
.
.
.
.
.
.
.
.
.
# Information Architecture Research: dubailand.gov.ae

A structural research report on the public-facing information architecture of the Dubai Land Department (DLD) website, produced for the purpose of designing an original government/public-service portal inspired by its patterns.

> **Scope note:** This report documents *publicly observable structure* only :— navigation, page hierarchy, and reusable UI/content patterns gathered via manual browsing, the site's own published sitemap, and public search indexes. It does not include backend code, infrastructure, or any non-public system detail, and none of DLD's original content, branding, or code is reproduced here.

---

## 1. Methodology

| Step | Source | What it provided |
|---|---|---|
| 1 | Homepage crawl | Section layout, component patterns, service names |
| 2 | Official `/en/sitemap/` page | Top-level page tree (~35 curated entries) |
| 3 | Web search across service sub-pages | Individual service URLs not listed in the curated sitemap |
| 4 | Public SEO index (Ahrefs) | Scale check : site has **~2,500 indexed pages** total |

**Key limitation:** DLD's published sitemap is a curated summary, not exhaustive. The site's true page count (~2,500) is far larger, driven mainly by individual service detail pages, dynamic listing pages (`?id=`), news archives, and several linked subdomains (MyDLD, Trakheesi, eMart, RVS, REES, NBP) that each have their own internal structure. A full page-level crawl requires a dedicated crawler (e.g. Screaming Frog) . this report reflects everything discoverable through manual research without that tooling.

---

## 2. Site Architecture Diagram

Top-level navigation, page hierarchy, and cross-links to subdomains/external services, as published in DLD's own sitemap.

```mermaid
flowchart TD
    Home["Home\n/en/"]

    Home --> AboutUs["About Us"]
    Home --> Services["Services"]
    Home --> Info["Information"]
    Home --> OpenData["Open Data"]
    Home --> NewsSec["News & Media"]
    Home --> Footer["Footer Pages"]

    AboutUs --> AU1["About DLD"]
    AboutUs --> AU2["Customer Happiness Charter"]
    AboutUs --> AU3["Cyber Security Awareness"]
    AboutUs --> AU4["Annual Report"]
    AboutUs --> AU5["Organization Chart"]
    AboutUs --> AU6["Regional & Int'l Relations"]
    AboutUs --> AU7["Strategic Map"]
    AboutUs --> AU8["Management's Message"]
    AboutUs --> AU9["Partnership"]
    AboutUs --> AU10["Policies"]
    AboutUs --> AU11["Rules & Regulations"]

    Services --> SV1["All Services (dynamic listing)"]
    Services --> SV2["Information / Inquiries"]

    Info --> IN1["Owner"]
    Info --> IN2["Tenant"]
    Info --> IN3["Developer"]
    Info --> IN4["Real Estate Companies"]
    Info --> IN5["Financial Institutions"]

    OpenData --> OD1["Development Handbook"]
    OpenData --> OD2["Research"]
    OpenData --> OD3["Indexes"]
    OpenData --> OD4["Real Estate Data"]

    NewsSec --> NM1["News & Media Archive"]
    NewsSec --> NM2["User Guide"]
    NewsSec --> NM3["Downloads"]

    Footer --> FT1["Terms & Conditions"]
    Footer --> FT2["Privacy Policy"]
    Footer --> FT3["Accessibility"]
    Footer --> FT4["Contact Us"]
    Footer --> FT5["FAQ"]
    Footer --> FT6["RE Companies Incubator"]

    Home -.->|external| EXT1["Dubai Careers"]
    Home -.->|external| EXT2["Dubai Pulse"]
    Home -.->|external| EXT3["Dubai.ae Portal"]
    Home -.->|subdomain| SUB1["MyDLD Login"]
    Home -.->|subdomain| SUB2["Payment Portal"]
    Home -.->|subdomain| SUB3["Trakheesi / Dubai Brokers"]
    Home -.->|subdomain| SUB4["eMart Auctions"]
    Home -.->|subdomain| SUB5["RVS Complaints System"]
    Home -.->|subdomain| SUB6["REES Platform"]
    Home -.->|subdomain| SUB7["Broker Program (NBP)"]
```

---

## 3. Homepage Component Stack

DLD's homepage is built almost entirely from **one repeating card component** (icon + title + one-line description + CTA button), re-skinned across multiple sections rather than custom-built per section. This is the most reusable takeaway for a rebuild.

```mermaid
flowchart TD
    HP["Homepage"] --> HP1["1. Hero Banner — flagship CTA"]
    HP --> HP2["2. Quick-Access Shortcuts Row"]
    HP --> HP3["3. Most Popular Services (card grid)"]
    HP --> HP4["4. Partner Services (card grid)"]
    HP --> HP5["5. Rules & Regulations (card grid)"]
    HP --> HP6["6. Initiatives / Programs (image cards)"]
    HP --> HP7["7. Tailored Services (text cards)"]
    HP --> HP8["8. Live Data Widget (transaction stats)"]
    HP --> HP9["9. Latest News (3-card teaser)"]
    HP --> HP10["10. Customer Support Block"]
    HP --> HP11["11. Footer"]

    Card["Reusable Card Component"] --> C1["Icon / Image"]
    Card --> C2["Title"]
    Card --> C3["One-line description"]
    Card --> C4["Single CTA button"]

    HP3 -.uses.-> Card
    HP4 -.uses.-> Card
    HP5 -.uses.-> Card
    HP6 -.uses.-> Card
    HP7 -.uses.-> Card
```

---

## 4. Individual Services Identified

Sampled from the homepage and linked service pages (not exhaustive . the "All Services" listing contains many more behind dynamic IDs):

- Project Status Enquiry
- Verify Title Deed
- Register / Renew Tenancy Contract (Ejari)
- Cancel Tenancy Contract
- Download Rental Certificate (Ejari)
- Rental Index
- Verify License and Permits (via Trakheesi)
- Pay Fees and Deposits (RDC)
- Real Estate Brokers Application (Dubai Brokers)
- Golden Visa Investor
- Issuing Map Application
- Trade License Search
- Live Property Auction (eMart)
- Manage Co-occupants (Ejari)
- Dubai REST (unified mobile app for owners/tenants/brokers/developers/valuators/investors)

## 5. Cross-cutting / Global UI Patterns

- Language toggle (EN/AR)
- Accessibility toggle
- Notification bell
- Persistent live-chat / virtual assistant widget
- Multi-channel support tray (phone, email, complaints, social)
- Site search

## 7. Sources

- DLD official site: https://dubailand.gov.ae/en/
- DLD official sitemap: https://dubailand.gov.ae/en/sitemap/
.
.
.
.
.
.
.
.
.
.
.
# Benchmark Reference: Dubai Land Department (DLD) Website

## 📌 Overview

Across the four Bangladesh government land-management apps reviewed in this series (নামজারি, ভূমি, ভূমি উন্নয়ন কর, and ভূমি পিডিয়া), a recurring set of gaps emerged: unlabeled navigation, redundant menus, fragmented services, no accessibility support, and broken account flows. The **Dubai Land Department (DLD) website** — [dubailand.gov.ae](https://dubailand.gov.ae/en/) — is offered here as a **positive benchmark**: a comparable government land-services platform that solves nearly every category of problem identified earlier in this report series.

This is not a gap analysis but a **reference/solution report** — documenting what DLD does well, mapped directly against the specific issues found in the four apps.

## Objective

- Document DLD's information architecture and UI patterns as a working example of best practice.
- Explicitly map each DLD strength to the corresponding gap it resolves from the earlier reports.
- Provide a concrete reference point for recommendations going forward.

---

## Architecture / Infrastructure Description

Unlike the fragmented, multi-app, multi-domain model seen in Bangladesh's land services, DLD operates as a **single, unified website** with one consistent navigation system, persona-based routing, and a shared design language across every section.

### Top-level site structure

```mermaid
flowchart TD
    A[User] --> B[dubailand.gov.ae - Single Unified Site]

    B --> N[Primary Nav Bar - all text-labeled]
    N --> N1[Home]
    N --> N2[About Us ▾]
    N --> N3[Services ▾]
    N --> N4[Information ▾ - persona based]
    N --> N5[Open Data ▾]
    N --> N6[User Guide]
    N --> N7[Downloads]

    B --> U[Utility Bar - all icons labeled or self-explanatory]
    U --> U1[🔍 Search]
    U --> U2[🌐 العربية - Language Toggle]
    U --> U3[🔔 Notifications]
    U --> U4[♿ Accessibility Menu]
    U --> U5[👤 Login]
    U --> U6[🤖 AI Assistant Avatar]

    B --> S[Home Page Service Shortcuts - 6 labeled tiles]
    S --> S1[Rental Index]
    S --> S2[Service Charge Index]
    S --> S3[Property Valuation]
    S --> S4[Download Rental Certificate - Ejari]
    S --> S5[To Whom It May Concern Certificate]
    S --> S6[Property Status Enquiry]

    style N fill:#d4f7d4,stroke:#2d862d
    style U fill:#d4f7d4,stroke:#2d862d
    style S fill:#d4f7d4,stroke:#2d862d
```

### Persona-based "Information" routing

Rather than presenting one undifferentiated service list, DLD segments its **Information** menu by *who the visitor is* — directly solving the "one-size-fits-all navigation" problem seen across the Bangladeshi apps:

```mermaid
flowchart LR
    A[Information Menu] --> B1[Owner]
    A --> B2[Tenant]
    A --> B3[Developer]
    A --> B4[Real Estate Companies]
    A --> B5[Financial Institutions]

    style A fill:#d4f7d4,stroke:#2d862d
```

### Services catalog structure

```mermaid
flowchart TD
    A[Services Section] --> B[Most Popular Tab]
    A --> C[Our Partners Tab]
    A --> D[Rules and Regulations Tab]

    B --> B1[Project Status Enquiry]
    B --> B2[Verify Title Deed]
    B --> B3[Register/Renew Tenancy Contract]
    B --> B4[Verify License and Permits]
    B --> B5[Pay Fees and Deposits]
    B --> B6[Real Estate Brokers Application]

    A --> E[Tailored Services Carousel]
    E --> E1[Integrate with our APIs]
    E --> E2[Golden Visa Investor]
    E --> E3[Issuing Map Application]
    E --> E4[Trade License Search]

    A --> F[DLD Initiatives - Accordion]
    F --> F1[Your First Home in Dubai]
    F --> F2[Real Estate Tokenization]
    F --> F3[Emirati Real Estate Companies Incubator]
    F --> F4[Real Estate Evolution Space - REES]
    F --> F5["برنامج البذرة Program"]
```

Every single tile in every one of these sections — service shortcuts, popular services, tailored services, initiatives — carries a clear **name, description, and "Proceed" action**. Nothing is presented as a bare, unlabeled icon.

---

## 🔍 How DLD Solves the Gaps Found Earlier in This Series

| Gap Identified Earlier | App(s) Affected | How DLD Resolves It |
|---|---|---|
| Icons with no text labels, forcing users to guess or tap blindly | নামজারি ("সকল সেবা"), ভূমি পিডিয়া (bottom nav) | Every icon across the entire site . nav items, utility bar, service tiles , has a visible text label or caption. Nothing is icon-only. |
| Same content repeated across 3 separate navigation surfaces | ভূমি পিডিয়া (home grid + hamburger + dropdown, all showing the same 12 categories) | A single primary nav bar with organized dropdown submenus (About Us, Services, Information, Open Data). Each menu path is distinct . no duplicated content shown three different ways. |
| Services scattered across 4 separate government domains, requiring external browser handoffs | নামজারি (ldtax.gov.bd, portal-citizen, lsg-land-owner, dlrms , all external) | All services live under one domain (`dubailand.gov.ae`), accessed within the same site and design system , no jarring browser handoffs or session loss. |
| No language accessibility beyond the app's default | ভূমি (English-only 16-item menu with no Bengali toggle) | A one-click Arabic/English toggle (`العربية`) is present directly in the utility bar on every page. |
| No accessibility support for users with disabilities | All four Bangladeshi apps (none observed) | A dedicated **Accessibility Menu** (Ctrl+U, powered by UserWay/Level Access) offers Screen Reader, Contrast+, Smart Contrast, Bigger Text, Text Spacing, Dyslexia-Friendly mode, Cursor adjustment, Line Height, Text Align, and more. a full toolkit, not a token gesture. |
| App still in Beta, with 5 of 8 core services showing "under construction" | ভূমি | Every section observed on DLD is fully functional and populated with real data , no placeholder or "coming soon" states encountered. |
| Legal/reference documents mixed with unrelated content, no clear structure | ভূমি পিডিয়া (gazettes and news interleaved in one feed) | **User Guide** and **Downloads** are dedicated, searchable, sortable data tables (Name / Date / Size / Extension / Action) , clearly separated from services and news content elsewhere on the site. |
| Broken/contradictory registration, login, and password-reset flows | ভূমি পিডিয়া | *(Not directly tested in this review , login flow screenshots weren't captured , but the surrounding platform's overall polish and consistency suggest a materially more mature engineering standard than the failure states observed in ভূমি পিডিয়া.)* |

---

## Additional Strengths Worth Noting

- **AI assistant avatar** is present persistently in the corner of every page (not gated behind a separate unlabeled bottom-nav icon as in ভূমি পিডিয়া) , visible, human-illustrated, and clearly framed as a support feature.
- **Persona-based "Information" menu** (Owner / Tenant / Developer / Real Estate Companies / Financial Institutions) helps different types of users find relevant content immediately, instead of forcing everyone through the same generic list.
- **Consistent card design language**: every service, initiative, and tailored-service tile follows the same visual pattern (icon + title + short description + "Proceed" link), making the whole site feel like one product rather than a patchwork of features.
- **Searchable, sortable data tables** for User Guide and Downloads make it trivial to find a specific file by name, date, or type — a pattern none of the four Bangladeshi apps implemented for their document repositories.


## Recommendations for Bangladesh's Land Management Apps

Based on this benchmark, the clearest path forward for the reviewed apps is:

1. **Consolidate onto a single, unified platform** (following ভূমি's original intent) rather than maintaining four separate apps/domains , mirroring DLD's one-site model.
2. **Label every icon, everywhere** : this single change would resolve the most frequently repeated finding across all four Bangladeshi apps reviewed.
3. **Adopt persona-based navigation** (e.g. Citizen / Office / Developer / Financial Institution) instead of one flat service list, to help different user types find relevant services faster.
4. **Build a real accessibility layer** : even a basic widget (text resize, contrast, screen-reader support) would be a meaningful improvement over the current lack of any accessibility features.
5. **Separate reference/document repositories from news feeds**, using structured, searchable tables (as DLD does for User Guide and Downloads) instead of an interleaved content feed.
6. **Add a genuine language toggle** directly in the main navigation, not buried in settings , critical given the target user base is majority Bengali-speaking.

---

## Conclusion

DLD demonstrates that the problems found across নামজারি, ভূমি, ভূমি উন্নয়ন কর, and ভূমি পিডিয়া are not inherent to government land-service platforms , they are solvable, and other national land authorities have already solved them. A single unified site, consistent labeling, persona-based navigation, a real accessibility toolkit, and structured document repositories together produce a platform that feels coherent and trustworthy, in contrast to the fragmented, partially-broken experience found across Bangladesh's current app ecosystem. This benchmark can serve as a concrete reference point for future redesign or consolidation efforts.
.
.
.
.
.
.
.
.
.
.
.
.
.