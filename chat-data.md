Your instinct is correct: trying to “force everything into one server” will likely increase complexity more than it saves cost—especially with your current workflow (Vercel + Supabase). OJS is the outlier here because it is stateful, PHP-based, and not serverless-friendly.

Below is a grounded breakdown of realistic setups, ordered from **lowest complexity → more unified but higher effort**.

---

# 1) Recommended: Split stack (keep what already works)

### Architecture

* **Frontend (Vue.js site)** → Vercel
* **Database / backend (if needed)** → Supabase
* **Journal system (OJS)** → VPS (Hostinger / Lightsail)

### Why this works best

* Vercel = zero DevOps, instant deploys (you already know it)
* OJS = requires PHP + MySQL + file storage → needs VPS anyway
* No conflict between modern stack and legacy stack

### Cost (approx, India-friendly)

* Vercel: free or low cost
* Supabase: free → ₹2–3k/month when scaling
* VPS for OJS:

  * Hostinger: ₹600–₹1,200/month
  * Amazon Lightsail: ~$5–10/month (~₹400–₹900)

### Complexity

Very low. Clean separation.

### Verdict

Best fit for a non-technical builder.

---

# 2) Semi-unified: VPS for OJS + Static deploy on same server

### Architecture

* VPS hosts:

  * OJS (PHP + MySQL)
  * Built Vue app (static files via Nginx)

### What changes

* You stop using Vercel
* You manually:

  * build Vue (`npm run build`)
  * upload files to server
  * configure Nginx

### Problems

* You lose:

  * instant deploys
  * previews
  * easy rollback
* You gain:

  * server maintenance burden
  * debugging headaches

### Cost

Same VPS cost (~₹600–₹1,200/month)

### Complexity

Moderate → high (for your current skill level)

### Verdict

Not worth it unless you want to learn DevOps.

---

# 3) Fully unified modern stack (Not recommended for OJS)

Trying to run:

* Vue app
* Supabase alternative
* OJS

…on one machine or platform.

### Issue

OJS is:

* PHP-based
* File-heavy (uploads, journals, PDFs)
* Needs persistent storage + cron jobs

This clashes with:

* serverless infra (Vercel)
* modern JS workflows

### Verdict

Architecturally messy. Avoid.

---

# 4) Cleaner upgrade option (if you want simplicity + future scaling)

Instead of Hostinger, consider:

### Option A: Amazon Lightsail

* Slightly more “serious” than Hostinger
* Fixed pricing, predictable
* Easier scaling later if journals grow

### Option B: DigitalOcean

* Better docs for OJS installs
* Huge community support

### Option C: Managed OJS hosting (if budget allows)

* Offered by Public Knowledge Project partners
* Costs more, but removes all technical burden

---

# What you should actually do

### Final recommendation (optimized for you)

Keep it simple and stable:

* **Main site**

  * Vercel (Vue)
  * Supabase (if needed)

* **Journal system**

  * Hostinger VPS (cheapest)
  * or Lightsail (cleaner long-term)

* **Structure**

  * `iamt.co.in` → Vercel
  * `iamt.co.in/ijmt` → Vercel page
  * `journal.iamt.co.in` → VPS (OJS)

---

# Key insight (important)

Trying to “optimize cost by merging servers” here will:

* save maybe ₹300–₹500/month
* cost you hours of debugging + fragility

Your current hybrid approach is already **industry-standard**:

* serverless frontend
* VPS for legacy systems

---

# If you want, next step

I can:

* recommend exact VPS specs for OJS (RAM, CPU, storage)
* give a **step-by-step OJS install plan on Hostinger/Lightsail**
* or design a **zero-maintenance deployment workflow** for your setup

Just specify.

## What Open Journal Systems (OJS) can do for IJMT

**Open Journal Systems (OJS)**, developed by the Public Knowledge Project, is a full-stack journal management and publishing platform specifically designed for scholarly journals. It is widely used by universities, research bodies, and independent journals globally.

---

## 1) End-to-End Journal Workflow (Core Benefit)

OJS manages the **entire publishing lifecycle in one system**:

* Submission (authors upload manuscripts)
* Editorial screening
* Peer review (single-blind, double-blind, or open)
* Copyediting & proofreading
* Production & publication

**Impact for IJMT:**
Removes reliance on email/Google Drive workflows and replaces them with a structured, trackable system. This is critical as you scale submissions internationally.

---

## 2) Role-Based Access & Editorial Structure

OJS allows multiple user roles with controlled permissions:

* Journal Manager
* Editor / Section Editor
* Reviewer
* Author
* Copyeditor / Proofreader

**Impact:**
You can replicate a formal editorial board structure digitally—important for credibility and indexing readiness.

---

## 3) Built-in Peer Review System

Key features:

* Reviewer database and assignment
* Automated review requests and reminders
* Blind review support
* Reviewer comments and file uploads

**Impact:**
Standardizes peer review, improves turnaround time, and creates audit trails—essential for Scopus / UGC / indexing applications.

---

## 4) Publishing & Issue Management

OJS supports:

* Volume/Issue creation
* Article scheduling
* DOI integration (e.g., Crossref)
* PDF/HTML/XML publishing formats

**Impact:**
You can publish IJMT as a **fully structured international journal** with consistent metadata—required for discoverability.

---

## 5) Indexing & Metadata Standards

OJS is built for academic indexing:

* Dublin Core metadata
* OAI-PMH protocol (for harvesting by databases)
* Google Scholar compatibility
* Crossref DOI support

**Impact:**
Directly improves visibility and eligibility for indexing platforms (Scopus, DOAJ, etc.).

---

## 6) Author & Reviewer Experience

* Authors can track submission status in real-time
* Reviewers get a structured dashboard
* Automated emails reduce manual communication

**Impact:**
Professional experience comparable to established journals.

---

## 7) Website + Journal Platform in One

OJS also functions as a **journal website**:

* Journal homepage
* Archives (past issues)
* Editorial board pages
* Submission guidelines
* Announcements

**Impact:**
You do not need a separate CMS (like WordPress) unless you want advanced marketing pages.

---

## 8) Cost Efficiency (Major Advantage)

* Open-source (no license fee)
* Only hosting + setup cost required
* Large global community support

**Impact:**
High-quality publishing infrastructure at very low cost—ideal for associations like IAMT.

---

## 9) Customization & Integration

OJS can be extended with plugins:

* Payment gateways (for APCs)
* DOI registration
* Plagiarism tools integration
* ORCID integration

**Impact:**
You can evolve IJMT into a **modern, revenue-generating journal system** if needed.

---

## 10) Multi-Journal & Scalability

* One installation can host multiple journals
* Supports growth in submissions and editorial teams

**Impact:**
Future-proof if IAMT launches additional journals or conference proceedings.

---

## Practical Example for IJMT

With OJS, your workflow becomes:

1. Author submits paper via IJMT website
2. Editor assigns reviewers
3. Reviewers submit feedback inside system
4. Editor makes decision
5. Copyediting & proofreading tracked
6. Article published with DOI in an issue
7. Automatically indexed and discoverable

---

## Strategic Value for IAMT

Adopting OJS positions IJMT as:

* A **serious academic journal** (not informal publishing)
* Ready for **indexing and accreditation**
* Capable of **international submissions**
* Operationally efficient with minimal manpower

---

## When OJS is Especially Worth It

Use OJS if you plan to:

* Publish regularly (quarterly / biannual)
* Apply for indexing (Scopus, DOAJ, UGC CARE)
* Handle increasing submission volume
* Build long-term academic credibility

---

## When You Might Need Additional Tools

* Marketing website → WordPress / Wix (optional)
* DOI registration → Crossref membership
* Hosting → VPS or managed OJS hosting provider

---

## Bottom Line

OJS is not just software—it is a **complete digital infrastructure for running a professional academic journal**. For restarting IJMT, it provides structure, credibility, and scalability from day one without high costs.

---

If needed, a next step can be:

* Architecture setup (hosting + domain + OJS install)
* Cost breakdown for India
* Workflow design specific to IJMT
* Integration with your existing systems (Wix, DOI, etc.)

## 1) Simple Architecture (How everything fits together)

### Recommended Setup (clean + scalable)

```
[Your Main Website]
   ├── WordPress (current)
   └── React (future via Codex)
          ↓
   journal.yourdomain.com  →  OJS (Journal System)
          ↓
   VPS Server (Hostinger / DigitalOcean / AWS Lightsail)
          ↓
   Database (MySQL) + Storage (articles, PDFs)
```

### In plain terms

* Your **main website (WordPress / React)** = branding, marketing, information
* **OJS** = actual journal engine (submissions, peer review, publishing)
* Users move from website → “Submit Paper” → OJS

---

## 2) Hosting Architecture (Best options for India)

### Option A — Best for You (Recommended)

**Managed VPS (simple + affordable)**

* Hostinger (India-friendly pricing)
* OR DigitalOcean

**Why this works for you:**

* No DevOps complexity
* Stable performance
* Easy scaling later

---

### Server Specs (Start here)

| Component | Recommended                     |
| --------- | ------------------------------- |
| RAM       | 4 GB                            |
| CPU       | 2 vCPU                          |
| Storage   | 80–120 GB SSD                   |
| OS        | Ubuntu                          |
| Panel     | Optional (CyberPanel / aaPanel) |

---

## 3) Cost Breakdown (India realistic)

### One-Time Setup (Year 1)

| Item                                    | Cost (₹)             |
| --------------------------------------- | -------------------- |
| Domain (if new)                         | 800 – 1,200          |
| VPS Hosting (annual)                    | 6,000 – 12,000       |
| OJS Installation (freelancer or agency) | 10,000 – 25,000      |
| SSL Certificate                         | Free (Let’s Encrypt) |
| Initial Design/Theme                    | 5,000 – 15,000       |

**Total Year 1:**
👉 ₹20,000 – ₹50,000 (lean setup)
👉 ₹50,000 – ₹80,000 (polished setup)

---

### Recurring Yearly Cost

| Item                      | Cost (₹)                     |
| ------------------------- | ---------------------------- |
| Hosting renewal           | 6,000 – 12,000               |
| Maintenance (optional)    | 10,000 – 30,000              |
| DOI (Crossref membership) | ~₹25,000/year + per DOI cost |

---

## 4) Workflow Design for IJMT (Non-technical explanation)

### Step-by-step journal flow

#### Step 1: Submission

* Author clicks **“Submit Paper”**
* Uploads manuscript on OJS

#### Step 2: Editorial Check

* You or editor checks:

  * Scope fit
  * Basic quality

#### Step 3: Peer Review

* Assign 2 reviewers
* They review inside OJS
* Submit comments

#### Step 4: Decision

* Accept / Revise / Reject

#### Step 5: Copyediting

* Grammar, formatting, citations

#### Step 6: Publication

* Assign DOI
* Publish in issue (Volume/Number)

---

### Your Role (as Secretary)

You mainly:

* Oversee editors
* Manage policies
* Monitor progress dashboard

You do **NOT** need to manage technical backend daily.

---

## 5) Integration with WordPress (Current Website)

### Goal

Keep WordPress as main site, OJS as journal system

### How it works

#### Add buttons like:

* “Submit Article”
* “Current Issue”
* “Archives”

👉 These link to:

```
journal.yourdomain.com
```

---

### Example structure

| Page             | Action       |
| ---------------- | ------------ |
| ijmt.org         | Main website |
| ijmt.org/journal | Info page    |
| journal.ijmt.org | OJS system   |

---

### Optional Enhancements

* Match colors/fonts (branding consistency)
* Add OJS iframe preview (optional, not necessary)

---

## 6) Integration with React Website (Your Codex Project)

### Same concept, cleaner execution

In your React site:

#### Add navigation:

* Journal
* Submit Paper
* Archives

#### These link to:

```
https://journal.yourdomain.com
```

---

### Advanced (optional later)

If you want deeper integration:

* Fetch latest articles via OJS API
* Show them on homepage
* Sync announcements

But for now:
👉 **Just link it. Keep simple.**

---

## 7) Design Strategy (Important for credibility)

OJS default UI is basic. Improve it:

* Add logo + journal branding
* Clean homepage layout
* Proper sections:

  * About IJMT
  * Editorial Board
  * Author Guidelines
  * Ethics Policy

---

## 8) What You Should NOT Do (Common mistakes)

Avoid:

* Hosting OJS on cheap shared hosting ❌
* Mixing WordPress + OJS in same install ❌
* Over-customizing early ❌
* Trying to manage server yourself ❌

---

## 9) Suggested Setup Plan (Step-by-step for you)

### Phase 1 (Week 1–2)

* Buy domain (if needed)
* Buy VPS
* Install OJS

### Phase 2 (Week 2–3)

* Configure:

  * Journal settings
  * Roles (editor, reviewer)
  * Email templates

### Phase 3 (Week 3–4)

* Integrate with WordPress
* Add submission links

### Phase 4

* Soft launch with test submissions

---

## 10) Future Scaling (Important for your vision)

Later you can add:

* Payment system (APCs)
* Reviewer recognition certificates
* AI-assisted screening
* Automated formatting tools

---

## Final Recommendation

For your situation:

* Use **OJS as backend journal engine**
* Keep **WordPress / React as frontend**
* Start simple → scale later

---

## If you want next step

Can provide:

* Exact VPS + plan link (best value in India)
* Freelancer hiring checklist
* Ready-made OJS configuration template for IJMT
* Editorial policy templates (Scopus-ready)

Specify what to proceed with.

Your structure is correct and aligns with how most journals deploy OJS:

* **iamt.co.in** → main site
* **iamt.co.in/ijmt** → journal info page
* **journal.iamt.co.in** → OJS system

No issues there.

---

## 1) Do you actually need a freelancer or agency?

**Short answer: No.**

You can absolutely set this up yourself using AI tools like Codex / Claude Code.

### What a freelancer normally does

They typically:

* Set up VPS
* Install OJS
* Configure email + database
* Fix permissions issues
* Do basic branding

### Why people hire them

Because OJS setup involves:

* Linux commands
* Database setup
* PHP dependencies
* File permissions (this is where most people get stuck)

---

### Reality for you (important)

Given your profile (non-technical but using AI tools):

👉 You are **fully capable of doing this yourself**
👉 BUT expect **1–2 days of friction/debugging**

---

### When you *might* need help

Only if you hit:

* Email not sending (SMTP issues)
* File upload errors
* Permissions breaking OJS

Even then, a **₹2–5k one-time fix** is enough—not a full freelancer engagement.

---

## 2) Can Codex / Claude Code handle OJS installation?

Yes—with the right prompts.

You’ll basically ask them to:

* Set up Ubuntu server
* Install:

  * PHP (8.x)
  * MySQL
  * Apache or Nginx
* Download OJS
* Configure database
* Set file permissions

They can generate exact commands.

---

### What AI is especially good at here

* Debugging install errors
* Fixing config files
* Explaining logs

---

### What AI is NOT good at

* Your server UI (you’ll still click things)
* DNS setup (you must do this in your domain panel)

---

## 3) Why was “Design / Theme” listed as a cost?

Because:

### Default OJS UI is functional—but basic

It looks like:

* University journal (not startup-level polished)
* Slightly outdated UX
* Not mobile-optimized perfectly

---

### But your case is different

You said:

> “I’m happy with the base UI”

👉 Then **you do NOT need to spend anything on design**

---

### What you get out of the box

With Open Journal Systems default theme:

* Homepage
* Article pages
* Submission system
* Issue archives
* Editorial board pages

---

### When design becomes necessary (later)

Only if you want:

* Premium journal feel (Springer/Elsevier style)
* Better branding consistency with React site
* Higher trust for international authors

---

## 4) Minimal Setup Plan (DIY with AI — tailored for you)

### Step 1 — Buy VPS

Use:

* Hostinger (simplest UI)

---

### Step 2 — Point subdomain

In your domain panel:

```
journal.iamt.co.in → VPS IP
```

---

### Step 3 — Use AI to install OJS

Prompt Codex / Claude:

```
Set up Open Journal Systems (OJS 3.x) on Ubuntu server with:
- Apache
- MySQL
- PHP 8.1+

Include:
- All dependencies
- File permissions
- Cron setup
- SSL with Let's Encrypt
```

---

### Step 4 — Run OJS web installer

Open:

```
journal.iamt.co.in
```

Fill:

* Journal name: IJMT
* Admin account
* Database credentials

---

### Step 5 — Basic configuration inside OJS

* Add logo
* Set email
* Create roles:

  * Editor
  * Reviewer
* Add sections (Articles, Reviews, etc.)

---

## 5) Integration with your sites (no-code approach)

### WordPress

On **iamt.co.in/ijmt**:

Add buttons:

* Submit Paper → journal.iamt.co.in/index.php/ijmt/about/submissions
* Current Issue → journal.iamt.co.in
* Archives → journal.iamt.co.in/index.php/ijmt/issue/archive

---

### React (Codex site)

Just add links:

```jsx
<a href="https://journal.iamt.co.in">Journal</a>
```

That’s it.

---

## 6) Hidden technical things to not ignore

These matter more than design:

### 1. Email (VERY important)

OJS sends:

* Reviewer requests
* Author notifications

Set up SMTP using:

* Gmail SMTP or Zoho Mail

---

### 2. Backups

Minimum:

* Weekly database backup
* Article files backup

---

### 3. Storage planning

PDFs will grow fast.

Start with:

* 80–100 GB

---

### 4. Security

* Enable SSL (free)
* Keep OJS updated

---

## 7) Honest Bottom Line

* You **do NOT need a freelancer**
* You **do NOT need paid design**
* You **CAN fully DIY with AI tools**

But:

👉 Expect **setup friction, not complexity**
👉 Once installed, OJS becomes very easy to operate

---

## If you want next

Can give you:

* Exact copy-paste install commands (zero thinking)
* SMTP setup guide (critical)
* OJS configuration checklist for IJMT
* Editorial workflow template ready for your team

Specify what you want next.
## Hostinger VPS — which plan you should pick

![Image](https://images.openai.com/static-rsc-4/YM6bATjaJA3RtotusE_NYWfldaQS7XoIt3tpbwCcVUOO9OWN8muOe-VyjLRJFROoKOEtBp7Y7yDXst96rCLx0wE7TJHiobaHZIq3uMC1AGMDE2XCyefqpd1BqvFwIuewtOrXFn4XPabct3YOuhfbmzk9cTC75IQrWx_XqlJp4nxTw6mZ8Cen8liUcldT4VSQ?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/TxN6V8-ImQKbu0HSqK8EhvIE5TERhE_g3uaoGz3zbktGQfIkqFTrSn2rkmMrcsgH5Qd65WtKfLfPDJG4EJ9LW8jfCtrs49yugP1g57_lbGEKDGTnOrAVmK_lPHY0a19ReRs-wTCQRsGJA7HyNQNPxqzq7PB8aa_Tkg143hpnw6N79xbMCUUcG4Be6ZVWdNYb?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/PiKLZKnreM0_1AtWJDqor4rs71mHvwOQogZkyaVkJmU2NTuJnF4XM-zzOkyQ7NYw6GZ1T_YtGRuFCmoqaDmYgB_yWGr0oi2bh-3h3HOMyUxJFNawVmIlOKX1XbqnEyxvEtvVXuGdBkyIjRVnpX6sUMffncDs8Ugi0fF1JQlQmbq12D56LlKyafbwJ-Lo--mW?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/QXZrFmwC5oeL4KwhavlPl3hxfojbBalVNnwRweowe9e9dzi7SUrxIXFbV8DOyTHvxl48w69UrJOg7_t_wq9xRkTrJCDlgI4G5Iy6vmiJkUBJ-oqFOcvdq7wfmBioj8D5tF-D1rA2t48Ee1ZleKzOiRxp0SL8wcaOZ_Z1h4pUe9hgxM63G2S33YStPjA4RjW6?purpose=fullsize)

### Direct answer (based on your use case: IJMT + OJS)

👉 **Pick: KVM 2 (₹929/month)**

---

## Why KVM 2 is the correct choice

| Factor          | KVM 1  | KVM 2 ✅                     |
| --------------- | ------ | --------------------------- |
| RAM             | 4 GB   | **8 GB (important)**        |
| CPU             | 1 core | **2 cores**                 |
| Storage         | 50 GB  | **100 GB (PDFs grow fast)** |
| OJS performance | Risky  | **Smooth + safe**           |

### Key reasoning

* OJS is **not heavy**, but:

  * PDF uploads
  * Multiple users (editors + reviewers)
  * Email processing
    👉 These need **stable RAM**

* 4 GB (KVM 1) will work initially, but:

  * Slows down with 10–20 submissions
  * Can crash during uploads or cron jobs

👉 KVM 2 gives **headroom + no stress**

---

## When should you NOT choose KVM 2?

Only if:

* You want **absolute lowest cost**
* You are okay upgrading in 2–3 months

Then:
👉 Start with **KVM 1 (₹699/month)**

But expect:

* Possible lag
* Upgrade soon anyway

---

## When to choose KVM 4?

Only if:

* You expect **100+ submissions/month**
* Multiple journals later

👉 Not needed for IJMT right now

---

# Cheaper Alternatives (important)

## 1) Contabo (Best cheap performance)

* ₹500–700/month equivalent
* Much higher specs for price

### Downsides:

* Slower support
* Slightly more technical setup

👉 Best if you’re comfortable with AI-guided setup

---

## 2) Amazon Lightsail

* ~$5–10/month (₹400–800)

### Pros:

* Very stable
* Clean UI

### Cons:

* Slight learning curve
* Bandwidth limits

---

## 3) DigitalOcean

* ~$6/month (₹500)

### Pros:

* Developer-friendly
* Clean and reliable

### Cons:

* No hand-holding

---

# Honest Recommendation (based on YOU)

You said:

* Non-technical
* Using Codex
* Want smooth setup

### Best decision:

👉 **Take Hostinger KVM 2**

Because:

* Easiest UI
* Lowest friction
* Indian support ecosystem
* Enough power for 2–3 years

---

# Cost Optimization Strategy

### Smart move:

1. Start with:
   👉 KVM 2 (₹929/month)

2. After 6–12 months:

   * If usage low → downgrade
   * If growing → upgrade

---

# Final Decision Summary

* **Best overall:** KVM 2 ✅
* **Cheapest possible:** KVM 1 ⚠️
* **Best alt (cheaper but harder):** Contabo
* **Best alt (balanced):** DigitalOcean

---

## If you want next step

I can now give you:

* Exact **“copy-paste” Codex prompt** to fully install OJS on Hostinger
* Or a **click-by-click setup guide using hPanel (zero coding)**

Specify which route you want.

## Amazon Lightsail vs Hostinger — for IJMT (OJS) with your skill level

---

## 1) What matters for *your* use case

You are:

* Non-technical (but using AI tools)
* Setting up **OJS (PHP + MySQL app)**
* Want **low friction + reliability**
* Not building a complex distributed system

So evaluation criteria:

* Setup difficulty
* Debugging difficulty
* UI friendliness
* Stability
* Cost vs value
* Long-term maintainability

---

## 2) Side-by-side comparison (practical, not theoretical)

| Factor                 | Hostinger VPS             | Amazon Lightsail          |
| ---------------------- | ------------------------- | ------------------------- |
| Setup ease             | **Very easy (UI-driven)** | Medium (AWS-style UI)     |
| Learning curve         | **Low**                   | Moderate                  |
| Control panel          | Built-in (hPanel)         | Minimal                   |
| Server access          | SSH + UI tools            | SSH only (mostly)         |
| OJS install difficulty | **Low (AI + UI combo)**   | Medium (more manual)      |
| Debugging              | Easier (logs + UI help)   | Harder (pure server logs) |
| Performance            | Good                      | Very good                 |
| Reliability            | Good                      | **Excellent (AWS infra)** |
| Bandwidth              | Generous                  | Limited per plan          |
| Scaling                | Simple                    | Slightly more complex     |
| Cost (India)           | ₹700–₹1200/mo             | ₹400–₹800/mo              |

---

## 3) Real-world experience difference

### Hostinger experience (what you’ll feel)

* “Click → configure → done”
* Many things visible in UI:

  * DNS
  * Files
  * Backups
* Easier to recover from mistakes

👉 Feels like: **“semi-managed VPS”**

---

### Lightsail experience

* More raw VPS feel
* You will:

  * Use terminal more
  * Configure things manually
* Fewer guardrails

👉 Feels like: **“entry-level DevOps”**

---

## 4) OJS-specific comparison

### Installing OJS

**Hostinger:**

* AI gives commands → you run → works
* Panel helps with:

  * PHP config
  * Domain linking
* Fewer unknowns

**Lightsail:**

* Same commands, but:

  * More manual config
  * Firewall + networking confusion possible

---

### Email (critical for OJS)

**Hostinger:**

* Easier SMTP setup (often bundled options)

**Lightsail:**

* You MUST configure external SMTP properly
* AWS email (SES) adds complexity

---

### File uploads (PDFs)

**Hostinger:**

* Preconfigured limits easier to tweak

**Lightsail:**

* Requires manual PHP config edits

---

## 5) Risk factor (very important)

### Hostinger risks

* Slightly less “pure” infrastructure
* Performance not hyperscale-level

👉 But stable enough for journals

---

### Lightsail risks

* Misconfiguration (firewall, ports)
* Email issues
* Permissions errors harder to debug

👉 These are the exact areas beginners struggle with

---

## 6) Cost reality (not just pricing page)

| Scenario     | Hostinger       | Lightsail                     |
| ------------ | --------------- | ----------------------------- |
| Base cost    | Slightly higher | Slightly cheaper              |
| Hidden costs | None            | Bandwidth overages possible   |
| Time cost    | **Low**         | **Higher (your time = cost)** |

---

## 7) Best fit for YOU (clear answer)

### Choose Hostinger if:

* You want **fast setup**
* You don’t want to learn DevOps deeply
* You want **minimum friction**
* You want to rely on AI + UI together

👉 **This matches you exactly**

---

### Choose Lightsail if:

* You want to **learn infrastructure**
* You’re okay debugging Linux issues
* You want AWS ecosystem later

👉 Not aligned with your current goal (launch IJMT quickly)

---

## 8) Strategic recommendation

### Phase 1 (now)

👉 Use **Hostinger KVM 2**

* Launch fast
* Focus on journal operations, not tech

---

### Phase 2 (future, optional)

If IJMT grows:

* Move to:

  * AWS (Lightsail → EC2)
  * Or DigitalOcean

---

## Final Verdict

* **Best practical choice:** Hostinger VPS ✅
* **Best technical choice (not for you right now):** Lightsail

---

## If you want next step

Can provide:

* Exact **Hostinger setup checklist (click-by-click + commands)**
* OR **Codex mega-prompt to fully automate OJS install**
* OR **OJS config tailored for IJMT (roles, workflow, policies)**

Specify next action.
