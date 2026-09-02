---
title: The Madrid Open (Vol.1) and the Reversa Real-Time Parliamentary Record Challenge
description: A full dossier on the 3 October 2026 Madrid Open competition, the three startups that own its challenges, and a deep technical landscape of the Reversa democracy track — real-time parliamentary transcription and named speaker attribution
author: Claude Code
---
# Research: The Madrid Open (Vol.1) and the Reversa Real-Time Parliamentary Record Challenge

**Date:** 2026-09-02 (last updated 2026-09-02; second pass: Track 02 technical deep
dive; third pass: fact-check corrections and companion-report cross-links, fourth pass
2026-09-02 (inventory corrections); fifth pass 2026-09-02: §12 team strategy)

**Author:** Claude Code

**Status:** Complete

## Overview

This report assembles everything publicly available about the **Madrid Open (Vol.1)**, a
one-day, forty-person, invitation-screened build competition held on **Saturday 3
October 2026** at Mad Tech Campus in Madrid, jointly presented by three Madrid-connected
startups: **Talky** (accounting), **Reversa** (regulation and democracy), and **Tunen**
(agriculture).

It covers six things:

1. The competition itself: format, rules, selection, schedule, scoring rubric, prizes,
   and logistics.
2. The three startups that own the challenges, with the deepest treatment of Reversa,
   whose site hosts the event.
3. All nine identifiable founders and principals across the three companies, with what
   is and is not on the public record for each.
4. Every public GitHub repository belonging to the three companies and their founders,
   cloned locally and read — including a complete starter kit from a hackathon Talky ran
   six months earlier.
5. All three challenge tracks as stated, with the discrepancies between the two official
   descriptions surfaced rather than smoothed over.
6. The problem domain behind **Track 02 (Reversa / Democracy)**—real-time parliamentary
   transcription and speaker attribution—in depth (§11): the three problems that get
   conflated, what the published accuracy numbers actually say and under which
   protocols, the open-set identification failure mode at 350-member scale, the audio
   channel that makes the multilingual case unwinnable if you take the public feed, the
   corpora that exist and the one that does not, where the five-minute latency budget
   actually goes, and the eleven parliamentary chambers that have already deployed some
   version of this.

The motivating decision context is participation: registration closes **25 September
2026**, which is **23 days from the date of this report**, and the briefs are released
only the week before the event.
Anything that can be designed in advance has to be designed against the public
information collected here.

### A note on sourcing and confidence

Following this repository’s convention, claims are separated by evidential status.
Tags used throughout:

- **[Primary]** — the organizers’ own published materials (reversa.ai, the Luma listing,
  company sites). Authoritative as to intent, but marketing copy.
- **[Press]** — journalism and third-party startup profiles.
  Useful for numbers the companies do not publish; not independently audited.
- **[Technical]** — peer-reviewed papers, published corpora, model cards, and public
  benchmarks.
- **[Analysis]** — inference and judgment by the author of this report.
  Not sourced, and should be argued with rather than cited.

Where two official sources disagree, both readings are given.
Where a number appears only in vendor or aggregator copy, it is labeled as such.

## Questions to Answer

1. What exactly is the Madrid Open, who runs it, and what are the rules and the scoring
   function?
2. Who are Talky, Reversa, and Tunen, and what does each already run in production?
3. Who founded each company, what are their backgrounds, and what does the composition
   of the teams reveal?
4. What code have these companies published, and what does it reveal about their systems
   and about how they run a hackathon?
5. What precisely is each challenge asking for, and what is the measured metric?
6. For the Reversa track: what is the real-world problem, why does the official record
   lag, what is the current state of the art in the relevant technologies, and—the
   question the track’s own metric turns on—how accurately can a system **name** the
   speaker, on what data, under what protocol, and against which incumbent systems?
7. What does the scoring rubric imply about how a twelve-hour build should be
   prioritized?
8. What remains unknown until the briefs are released?

## Scope

**Included:** the public record on the event, its organizers, and their founders as of
2026-09-02; the technical literature bearing on multilingual ASR, speaker diarization,
speaker identification and speaker-attributed ASR in parliamentary and broadcast
settings; the public corpora and the deployed parliamentary systems; the Spanish
Congress’s own procurement record for transcription; and analysis of the stated rubric.

Public source code published by the companies and their founders was cloned and read;
see §9.

**Excluded:** the challenge briefs and datasets themselves, which are not public and are
distributed only to registered participants the week before the event.
No private, paywalled, or credentialed source was accessed.
Spanish-language sources were searched and read alongside English ones, and everything
is reported here in English; the Spanish and English versions of the event site are
faithful translations of each other, and where the Spanish wording is sharper it is
quoted and translated.

## Findings

### 1. The competition at a glance

| Field | Value |
| --- | --- |
| Name | Madrid Open · Vol.1 |
| Date | Saturday 3 October 2026, 09:00–21:00 (12 hours) |
| Venue | Mad Tech Campus, P.º de la Chopera 14, Arganzuela, 28045 Madrid |
| Capacity | 40 seats |
| Team size | 3, formed by the organizers as people register |
| Tracks | 3 (Talky / accounting, Reversa / democracy, Tunen / agriculture); pick one at registration |
| Registration | Luma, host-approved (private event); closes 25 September 2026 |
| Languages | English and Spanish both accepted for registration, the room, and the demo |
| Cost to enter | None stated |
| Prizes | Set independently by each company for the top three teams on its track; announced with the brief |
| Presented by | Talky, Reversa, Tunen |
| Hosted by | Mad Tech Campus |
| Event pages | [reversa.ai/madrid-open](https://reversa.ai/madrid-open) (EN), [reversa.ai/es/madrid-open](https://reversa.ai/es/madrid-open) (ES) |
| Registration page | [luma.com/event/evt-Ooqpabo6jDOft5s](https://luma.com/event/evt-Ooqpabo6jDOft5s) |

**[Primary]** The framing sentence is: “Three companies open the challenge nobody has
cracked yet.” The organizers are explicit that each company already runs its system in
production and that what they bring to the event is “the frontier: the part nobody has
cracked, and the number they would trust it against.”

### 2. Format and the three guarantees

**[Primary]** The event site organizes its pitch around three promises, each of which is
also a constraint on how the day works:

- **Real data.** “You get their real data.
  Not a demo file. The same data they work with every day.”
  Qualified in the FAQ: the data is “real in structure and difficulty, prepared so no
  customer information is exposed.
  Public sources are used as they are.”
- **Real people.** The challenge owners present at 09:00 and “stay in the room until the
  demos.” The FAQ states no domain knowledge is required precisely because the owners are
  available all day.
- **Real score.** “You are scored on one number.
  Agreed before you start, measured on data your system has never seen.”

**[Analysis]** The third promise is the load-bearing one.
A held-out evaluation set run live at 21:00 means the deliverable is a *system that
runs*, not a deck and not a notebook that only works on the data you tuned against.
It also means the failure mode of the day is a pipeline that breaks on unseen input
formats, not a model that scores a few points lower.

### 3. Who is selected

**[Primary]** Forty seats, teams of three.
“We read every registration and select for evidence that you finish things, not for the
best CV in Madrid.” The four stated selection signals:

1. **You handle ambiguity** — “Nobody will tell you where the answer is, or whether
   there is one.”
2. **You like tough challenges** — “The harder the question, the more you want it.”
3. **You deliver under pressure** — “Twelve hours, one clock, something that runs at the
   end of it.”
4. **You build for the love of it** — “Building things is what you would be doing on a
   Saturday anyway.”

**[Primary]** Registration is a private Luma event requiring host approval.
Hosts listed on Luma are **Tomas Burgaleta**, **Maximilian Letón**, and **Julius** — one
per company (Reversa, Talky, Tunen respectively).
The organizers commit to replying to everyone.

**[Analysis]** “Evidence that you finish things” plus host approval means the
registration free-text is the actual application.
Shipped artifacts with links—repos, live systems, prior competition results—are the
currency; credentials are explicitly discounted.
Registering as a pre-formed team of three is permitted and removes the organizers’
matching risk, which is plausibly a mild selection advantage.

### 4. Schedule

**[Primary]**

| Time | Event |
| --- | --- |
| Week before | Briefs and reading material released, “so you can design as much as you want before the day” |
| 09:00 | Doors, coffee, 30-minute presentation of the three challenges by their owners, objectives set out in full. “The test starts here.” |
| 09:30 | Full datasets released. Build starts. Owners remain in the room. |
| 14:00 | Lunch, “without stopping the clock” |
| 17:00 | Public checkpoint; every team shows where it stands |
| 20:45 | Code freeze |
| 21:00 | Demos: every system runs live against data it has never seen; results read out in the room |

**[Primary]** Data credits from technology providers are handed out **during** the
event, not in advance.
The FAQ confirms: you may design ahead, but “the data credits are not sent in advance,
and the test itself starts at 09:00 on the day.”

**[Analysis]** The effective build window is **11.25 hours** (09:30 to 20:45), minus
whatever the 17:00 public checkpoint costs.
Because credits arrive on the day, any design that assumes a specific paid API should
have a local or free-tier fallback path.
The 17:00 checkpoint is a hard commitment to having something demonstrable roughly 7.5
hours in, which argues for an end-to-end thin slice early rather than a
component-perfect approach.

### 5. The scoring rubric

**[Primary]** Presented under the heading “Knowing when not to answer is part of the
score”:

| Weight | Criterion |
| --- | --- |
| 45% | Result against the track metric |
| 20% | Safe autonomy and restraint |
| 15% | Auditability of every answer |
| 10% | Would it ship: cost, latency, security |
| 10% | Ambition and live demo |

**[Analysis]** This is the most strategically informative artifact on the site, and it
is deliberately not a pure leaderboard:

- **The track metric is a minority of the score (45%).** A team that wins the raw number
  but cannot explain or defend its outputs loses to a team that is second on the number
  with a calibrated, auditable system.
- **Restraint is worth 20%, nearly half as much as accuracy.** Combined with the Talky
  metric’s explicit note that “approving something wrong costs more than flagging it.
  Escalating everything does not win either,” this is a request for **calibrated
  abstention**: a system that knows its own confidence and declines selectively.
  Both over-answering and blanket-escalating are named as losing strategies.
- **Auditability at 15% means provenance is a first-class deliverable**, not
  documentation. Every output should trace to its input—a timestamp, an audio span, a
  source document, a source URL. This aligns exactly with what all three companies sell:
  Talky’s pitch is “every output tied back to the document it came from,” Reversa’s is a
  traceable regulatory chain, and Tunen’s is “datasets others can trust.”
- **The 55% that is not the metric is largely engineering judgment**, and it is the part
  a strong team can bank in advance, before ever seeing the data.

### 6. Prizes and the real incentive

**[Primary]** Three components:

- **Prizes.** Each of Talky, Reversa, and Tunen sets its own prize for the top three
  teams on its own challenge, announced with the brief.
  Amounts are not public as of this report.
- **Credits.** From technology providers, handed out during the event.
  Providers are not named.
- **Working together.** “Three founding teams in the room, hiring.
  The best work turns into a conversation about doing it for real.”
  The organizers’ own section says: “Some of them will end up building with us.
  That would be a good day.”

**[Analysis]** The event is, transparently and by the organizers’ own account, a
combination of an R&D probe on unsolved production problems and a hiring funnel.
That is not a criticism—it is stated openly—but it does mean the audience for your demo
is three founding teams evaluating you as a collaborator, which raises the return on
communicating clearly at 21:00 relative to squeezing the last point out of the metric.

### 7. The organizers

#### 7.1 Reversa — the operating system for regulation

**[Primary]** Self-description on the event page: “The operating system for regulation:
a live digital twin of legislative activity, from the moment a text moves to the
obligation it lands on a team.”

**[Primary/Press]** Company facts:

| Field | Value |
| --- | --- |
| Founded | 2025 |
| Legal entity | Disruptive Labs SL |
| HQ | Calle Valle de la Fuenfría 10, 28034 Madrid |
| Contact | info@reversa.ai, +34 653 553 999 |
| Sector | RegTech / LegalTech |
| Programs | NVIDIA Inception, Google for Startups, Fuse by A&O Shearman (joined 14 Jul 2026) |

**Founders** — all three hold law degrees alongside technical or business degrees, which
is unusual and is the company’s stated edge on domain knowledge (full profiles in §8.1):

- **Santiago Aliaga Estella**, Co-Founder & CTO. LLB in Law and BSc in Business
  Analytics, ICADE (Comillas).
  Previously a software engineer at Amazon and Accenture; described elsewhere as an
  ex-Amazon AI lead, with experience in AI, data engineering, and software architecture.
- **Íñigo Martín Llorente**, Co-Founder.
  Listed as **CPO** on the company’s own about page and as **COO** in a press
  profile—the discrepancy is unresolved.
  LLB in Law and BSc in Business Analytics, ICADE (Comillas).
  Previously Monitor Deloitte, Mundi Ventures, and Job&Talent.
- **Tomás Burgaleta Alonso**, Co-Founder & CRO. BBA and LLB, UC3M; LL.M in International
  Law, Tulane Law School.
  Previously Sellside Group and **Tetuan Valley**—a detail that matters, because Tetuan
  Valley is a founding partner of the venue.
  He is one of the three Luma hosts.

**Product.** **[Primary]** Reversa positions itself as automating the whole regulatory
workflow rather than sending monitoring alerts.
Named components:

- **Regulatory Map** — a personalized regulatory landscape derived from industry,
  jurisdictions, and business activities.
- **Agentic Radar** — agents monitoring documents daily and issuing contextual alerts
  with explanations rather than keyword matches.
- **Legislative Twins** — tracking EU legislative procedures from Commission proposal
  through to enforcement, following rapporteurs, votes, and amendments in real time.
  **[Analysis]** This is the component the Madrid Open democracy track most directly
  extends: the parliamentary record is the raw material a legislative twin is built
  from.

The stated technical differentiators are contextual rather than lexical matching,
sector-specialized agents, full lifecycle coverage from proposal to enforcement, and
consolidation of otherwise disconnected tools.
A press profile describes the product across three time horizons: **past** (which
regulations apply), **present** (monitoring 500+ official sources daily), and **future**
(anticipating legislative change).

**Regulations and customers.** **[Primary]** Named regulatory coverage includes the EU
AI Act, DORA, NIS2, CSRD/ESG, MiCA, and GDPR. Named customers and partners include
IUS+AEQUITAS, the **Ilustre Colegio de Abogados de Madrid (ICAM)**—a December 2025
partnership the company describes as reaching over 70,000 lawyers—Santiago Mediano
Abogados, Rud Pedersen, Nitid, and Roman.
The site states the product operates within IBEX 35 and Fortune 500 companies.

**Traction and funding — treat with caution.** **[Press]** The published numbers
conflict and none are audited:

- Time savings are quoted as “approximately 90%” on the company site and in one profile,
  and “up to 95%” in another Spanish trade article.
- A showcase claim: a report produced in 44 seconds that had previously taken two weeks
  of law firm work, surfacing 14 gaps and contradictions that had not been detected.
- Revenue is given as “€60k ARR in 5 months … growing at 100% MoM” in one aggregator and
  as “six-figure ARR with approximately 100% monthly growth within five months” in a
  profile. These are not the same claim.
- Funding: one profile reports a **€300K pre-seed** being finalized.
  A separate aggregator states backing by the founders of **Factorial** and
  **Jobandtalent** and roughly **10x revenue growth in 2026**. No primary confirmation
  was found for either.
- Pricing: a Spanish trade article reports a SaaS model with a 15-day free trial and
  professional plans from **€50/month**, with a 30% discount for the first 100 users.
  This is inconsistent in scale with enterprise IBEX 35 deployments and may describe an
  early self-serve tier.

**Market context.** **[Press]** The RegTech market is projected to grow from roughly
€19.6B to €83B by 2032. Roughly 44,000 regulatory acts are in force across Europe with
about 2,800 new acts introduced annually, cited as 28% year-over-year growth.
Spain published over one million pages of new legislation in 2024.

#### 7.2 Talky — AI for accounting

**[Primary]** Event-page self-description: “AI for accounting teams and firms, with
every output tied back to the document it came from.”
Site tagline (Spanish): “IA para contabilidad, asesorías y gestorías” — AI for
accounting, advisory, and administrative firms.

**[Press]** From a January 2026 Telemadrid feature:

- **Jorge Beamonte** is CEO and founder.
  **Maximilian Letón** is Co-Founder & COO and is one of the three Luma hosts.
- The product digitizes and posts invoices regardless of arrival channel—paper, email,
  or messaging apps—then extracts the data, generates the accounting entries, creates
  the necessary accounts, and classifies expenses including identifying which are
  tax-deductible for the business activity.
- Originally built for hospitality and restaurants because of transaction volume, since
  expanded to other sectors.
- Business model is **pay-per-use, priced per processed invoice**, covering reading,
  classification, and posting with no additional charges.
- As of that article, under a year old, growing through partnerships with accounting
  firms, and **not yet supporting invoice issuance**.

**[Analysis]** The “tied back to the document it came from” phrasing is the same
provenance requirement the rubric scores at 15%. For an accounting product it is not a
nicety: an audit trail is the difference between a suggestion and a bookable entry.

#### 7.3 Tunen — AI and data engine for agriculture

**[Primary]** Event-page self-description: “The AI and data engine for agriculture,
turning field operations into datasets others can trust.”

**[Primary]** From tunen.ai, the shipped product is farm record-keeping automation:

- **Automatic Activity Detection** — “autonomously documents your farm operations using
  nothing but smartphones,” producing consistent AI-ready datasets.
- **AI Co-Pilot** — voice, image, and chat capture; logs activities, takes notes,
  updates inventory, and operates the app from natural-language commands.
- **Smart Inventory** — real-time stock tracking.
- **Team & Task Management** — assignment and monitoring with a four-eyes verification
  principle.
- Claims up to **75% reduction in documentation time**.
- Data stored on GDPR-compliant servers **in Germany**, with bank-level encryption.

**[Primary/Press]** The team is **Alexandre Shinebourne** (co-founder & CTO), **Julius
von Davier** (co-founder, and the third Luma host), and **Jacob von der Decken** (CCO);
see §8.3 for full profiles.
The company has German activity: a May 2025 presentation at an Innovators Breakfast at
the German Bundestag, and a lecture by Jacob at the University of Göttingen on AI in
agriculture and data management.
The company is registered in the UK and backed by Rockstart AgriFood Fund II (§8.3). No
customers are named.

**[Analysis]** Note the gap between the shipped product (smartphone-based operational
record-keeping) and the challenge (remote-sensing data fusion and yield prediction).
The challenge is genuinely adjacent frontier work rather than an extension of the
existing app, which is consistent with the event’s framing.

#### 7.4 Mad Tech Campus, and how the three met

**[Press]** Mad Tech Campus occupies Building 14 of the Matadero complex at Paseo de la
Chopera 14, Arganzuela, Madrid.
It opened in March 2026 with more than 50 people and 16 startups, reaching close to 100%
occupancy within its first month and now hosting more than 20 startups.
Founding partners are **Tetuan Valley**, **SeedRocket**, **BackFund**, and **La Nave
Nodriza**; Nazareth Rodrigáñez leads Tetuan Valley there.
Spanish coverage describes it explicitly as taking over from Google—that is, stepping
into the role vacated by Google for Startups Campus Madrid.
The space combines fixed and flexible desks, meeting rooms, and an auditorium.

**[Primary]** The founders’ own origin story closes the loop: “We met at Google for
Startups, working on complicated problems, and the weekends were where the real progress
happened.
Those Saturdays are how we got to know each other, and the relationship between
Talky, Reversa and Tunen came out of them.”

**[Analysis]** The three founders met at Google for Startups Campus Madrid; that
campus’s successor institution, Mad Tech Campus, is hosting their event; and Reversa’s
CRO comes out of Tetuan Valley, one of Mad Tech Campus’s founding partners.
The event is a coherent product of a single, small, well-connected Madrid ecosystem
rather than a sponsored corporate hackathon.

**[Primary]** The site’s identity image is Caspar David Friedrich’s *Wanderer above the
Sea of Fog* (1818), which the organizers gloss: “A problem in front of you, a horizon
full of adventure, and no visible path between the two.
You can see the shape of it, you cannot see the way through, and you go anyway.”

### 8. The founders

**[Analysis]** Nine people are identifiable across the three companies.
Depth varies sharply: Reversa publishes full founder bios, Tunen’s founders are
documented through a university profile and an accelerator announcement, and Talky’s are
the thinnest, visible mainly through a Spanish television feature and
professional-network listings.
LinkedIn returns HTTP 999 to automated fetching, so profile details below come from
search indexes and secondary sources rather than direct reads, and should be treated as
**[Press]** rather than primary.

| Founder | Company | Role | Prior |
| --- | --- | --- | --- |
| Santiago Aliaga Estella | Reversa | Co-founder & CTO | Amazon, Accenture |
| Íñigo Martín Llorente | Reversa | Co-founder, CPO (or COO — sources differ) | Monitor Deloitte, Mundi Ventures, Job&Talent |
| Tomás Burgaleta Alonso | Reversa | Co-founder & CRO | Sellside Group, Tetuan Valley |
| Jorge Beamonte | Talky Labs | CEO & founder | Not public |
| Maximilian Letón | Talky Labs | Co-founder & COO | Google for Startups, Ebury, The Startup Events |
| Alexandre Shinebourne | Tunen | Co-founder & CTO | Google, Cambridge (Darwin College), NIAB |
| Julius von Davier | Tunen | Co-founder | Google; raised on a German farm |
| Jacob von der Decken | Tunen | CCO | Agricultural economics; fintech; family farm business |

#### 8.1 Reversa — three lawyers who can also build

**[Primary]** The company’s own about page gives full bios.
The unifying fact, and the stated competitive edge, is that **all three co-founders hold
law degrees alongside a technical or business degree**. For a product whose whole claim
is that it understands regulation contextually rather than lexically, that is a
deliberate composition rather than a coincidence.

**Santiago Aliaga Estella — Co-Founder & CTO.** LLB in Law and BSc in Business
Analytics, both from ICADE (Universidad Pontificia Comillas).
Software engineer at **Amazon** and **Accenture**; described in secondary coverage as an
ex-Amazon AI lead. Also worked as a freelance technical lead, with one profile reporting
a top-1% Fiverr ranking in blockchain.
**[Press]** He owns AI, data engineering, and software architecture — which makes him
the author of the “live pipeline” that Track 02 participants inherit.

**Íñigo Martín Llorente — Co-Founder.** LLB in Law and BSc in Business Analytics, ICADE
(Universidad Pontificia Comillas).
Previously **Monitor Deloitte**, **Mundi Ventures**, and **Job&Talent**. Drives product
strategy and operations.
**His title is genuinely unsettled across sources:** the company’s about page and one
fundraising aggregator say **CPO**, a press profile says **COO**, and a third
description frames his remit as “Operations & Finance.”
He is the contact address on Reversa’s fundraising listing.

**Tomás Burgaleta Alonso — Co-Founder & CRO.** BBA in Business Administration and LLB in
Law, both from Universidad Carlos III de Madrid (UC3M); **LL.M in International Law from
Tulane Law School**. Previously **Sellside Group** and **Tetuan Valley**, where one
profile places him in US M&A and operations.
He leads revenue and partnerships, connecting the product to enterprises, law firms, and
public affairs consultancies across Europe, and he is one of the three Luma hosts for
the Madrid Open. **[Analysis]** His Tetuan Valley history is the concrete link between
Reversa and the venue: Tetuan Valley is a founding partner of Mad Tech Campus.

**Funding, and a claim worth correcting.** **[Press]** Two aggregators give different
pre-seed sizes for the same round: **€300K** ("finalizing") in one profile and **€400K**
in a fundraising newsletter, alongside “€60k ARR in 5 months of operations, revenue
growing 100% MoM.” Separately, an a16z jobs newsletter states Reversa is **backed by the
founders of Factorial and Jobandtalent** with roughly **10x revenue growth in 2026**,
and lists open roles for a Founding Engineer and a Forward Deployed Engineer.
**[Analysis]** One summary of that newsletter renders the backing claim as “the founding
team includes the previous founders of Factorial and Jobandtalent,” which is almost
certainly a garbling.
Reversa’s three founders are named and documented, and Íñigo Martín *worked at*
Job&Talent rather than founding it.
Read it as investor backing, not as founder identity.
None of these figures has primary confirmation.

#### 8.2 Talky Labs — the thinnest public record, and a name collision

**[Press]** **Talky Labs** is Madrid-based, **founded 2025**, listed at 11–50 employees,
positioning itself as “autonomous finance” delivering audit-ready accounting.
It passed through the Vivero de Villaverde acceleration programme (IX edición), a City
of Madrid incubator.
Named advisors include **Fernando Moreu Saínz** and **Alberto Carbonell**; named staff
include Ivan Fúnez Cruz (Lead Engineer), Ignacio Gonzalez Porras (Forward Deployed
Engineer), and Mert Demirel (AI Financial Engineer).

**Jorge Beamonte — CEO & Founder.** **[Press]** He leads the company and was the subject
of a January 2026 Telemadrid feature on automating restaurant accounting.
Beyond his role and that appearance, essentially nothing about his background is public
in the sources searched — no prior employers, no education.
This is the largest single gap in this report’s founder coverage.

**Maximilian Letón — Co-Founder & COO.** **[Press]** Based in Madrid.
Universidad Complutense de Madrid, 2021–2025. Previously **Google for Startups**,
**Ebury**, and **The Startup Events**. He is one of the three Luma hosts.
**[Analysis]** His Google for Startups history is the second concrete thread back to the
event’s origin story — the founders’ claim that they met at Google for Startups and kept
showing up on Saturdays.

**Name collision — important for anyone researching this company.** **[Analysis]** At
least three unrelated companies trade as “Talky,” and startup databases conflate them:

1. **Talky Labs** — the Madrid accounting-AI company in this report (usetalky.com).
2. **Talky** — a browser video-chat and screen-sharing product founded 2013 by Adam
   Brault in Richland, United States, using WebRTC. This is what Tracxn returns for
   “Talky,” with an investor and competitor set (Skype, Nimbuzz) that has nothing to do
   with accounting.
3. **Talky** — gettalky.ai, led by Evan Gauer in New York, roughly seven employees.

Any funding or headcount figure attributed to “Talky” should be checked against the
domain before it is believed.

#### 8.3 Tunen — a Google engineer and a farmer

**[Press]** **Tunen Limited** is a UK company, registration number **15107539**,
**incorporated August 2023**, with Alexandre Shinebourne as a director and person with
significant control.
Despite the UK registration the company is **predominantly active in Germany**. It
joined **Rockstart AgriFood Fund II** in the Spring 2024 cohort, alongside Decameal and
Hydrogen Refinery, and was a finalist at the Grüne Woche Startup Days in Berlin in
January 2025.

**Alexandre Shinebourne — Co-Founder & CTO.** **[Press]** British-French.
BSc in Computer Science from **Warwick University**, then software engineer at
**Google**. In 2023 he completed a one-year **MRes in AI for the Study of Environmental
Risk** at **Darwin College, Cambridge**, the first year of a 1+3 programme (one-year
master’s followed by a three-year PhD), and left before the doctoral phase as Tunen
gained traction: “I did the first year of the 1+3 and absolutely loved it, but this
other project started gaining traction.”
His Cambridge year included collaboration with **NIAB** (the National Institute of
Agricultural Botany) and gave him machine learning and **remote sensing** skills on top
of a conventional software engineering background.
He describes the company’s method as pragmatic: “We try to take a pragmatic approach,
enabling farmers to collect field data in a frictionless way.”
In November 2024 he spoke at Cambridge and Imperial College on “Aligning Data
Expectations with Practical Realities in Agriculture.”

**[Analysis]** Remote sensing plus environmental-risk ML is precisely the skill set
Track 03 asks participants to reproduce in twelve hours.
The challenge owner has a Cambridge master’s in it.

**Julius von Davier — Co-Founder.** **[Press]** A **former Google colleague** of
Shinebourne who **grew up on a farm in Germany**. His agricultural background is
credited with keeping the product grounded in what farmers can actually do — the Darwin
College profile makes the point that researchers routinely miss constraints such as a
farmer wearing gloves while operating machinery.
He pitched AutoTrack at Grüne Woche in January 2025 and co-delivered a guest lecture at
the University of Hohenheim in November 2024 on “LLMs & Agents: New Rules for Value
Creation in AgTech.”
He is listed as “Julius,” one of the three Luma hosts.

**Jacob von der Decken — CCO.** **[Press]** Studied **agricultural economics** and
worked on renewable-energy projects at a fintech before taking over his family’s farming
business in **northern Germany** around age 30, a transition covered in reporting on
German Mittelstand succession.
He is Tunen’s most publicly visible figure in German agricultural policy:

- Presented at the **Innovators Breakfast at the German Bundestag** on agritech
  obstacles (May 2025).
- Joined a **German Federal Ministry of Food and Agriculture** delegation to Ethiopia
  and Zambia for the World Without Hunger Conference (November 2024).
- Spoke on Green AI at Grüne Woche and at **Bitkom’s** “Agri-Political Breakfast”
  (January 2025), and on the DLG stage at **EuroTier 2024**.
- Lectured at the **University of Göttingen** on data management and AI (November 2024)
  and co-lectured at **Hohenheim** with von Davier.

**[Analysis]** Rockstart named only Shinebourne and von Davier as founders in March
2024, so von der Decken most likely joined after the founding pair.
His title is given as CCO.

**[Press]** Rockstart’s Mark Durno characterized the founding pair as combining
“generative AI excellence, first-hand agricultural experience, and a deep understanding
of Large Language Models, specifically how to apply them to farming.”

#### 8.4 The pattern across all three teams

**[Analysis]** Three observations that only appear when the nine profiles are read
together:

1. **Every team pairs a domain insider with a builder, and the pairing is the company.**
   Reversa is three law degrees attached to an Amazon engineer.
   Tunen is a Google engineer attached to someone who grew up farming, with a working
   farmer as CCO. Talky is an accounting-workflow product with an operator from the
   Madrid startup-support world.
   This is the same structural bet placed in three different regulated industries, and
   it explains why all three challenges are about making a machine **trustworthy inside
   an audited profession** rather than about raw model capability.
   The rubric’s 20% for restraint and 15% for auditability is what that bet looks like
   when written as a scoring function.
2. **The Google for Startups thread is real and load-bearing.** Maximilian Letón worked
   at Google for Startups; both Tunen founders are ex-Google; the three companies’
   founders say they met at Google for Startups; and the event is hosted at Mad Tech
   Campus, which Spanish coverage describes as stepping into the role Google vacated in
   Madrid. Reversa’s own technical lineage is Amazon rather than Google, so the thread is
   ecosystem, not employer.
3. **These are very young companies.** Talky Labs and Reversa were both founded in 2025;
   Tunen was incorporated in August 2023. The founders are correspondingly early-career
   — Letón finished his degree in 2025, Shinebourne left a PhD track in 2023. “The part
   nobody has cracked” is, in each case, a problem the company has been living with for
   one to three years, not a decade.
   That is worth knowing before assuming the frontier is further away than it is.

### 9. Public code: what is on GitHub

**[Analysis]** All three companies were searched on GitHub by organization handle, user
search, and code search against their domains.
The results are lopsided: **Tunen and Talky both have public code, Reversa has none**,
and one of the Talky finds is the most operationally useful artifact discovered in this
entire report — a complete starter kit from a hackathon Talky ran six months before the
Madrid Open.

Everything below is public code in public repositories, verified by commit authorship
and domain references rather than by name similarity.
A scan for exposed credentials (AWS keys, private keys, provider tokens) across all
cloned repositories found **none**.

#### 9.1 Verified accounts

| Handle | Entity | Public repos | Basis for the identification |
| --- | --- | --- | --- |
| [`tunen-org`](https://github.com/tunen-org) | **Tunen Limited** | 6 | Org name is “Tunen Limited”; website field is tunen.ai; created 2023-08-23, matching the UK incorporation month |
| [`a-shine`](https://github.com/a-shine) | **Alexandre Shinebourne** | 20 | Profile name, and bio reading “CTO @ Tunen \| ex-Google \| University of Cambridge \| University of Warwick”; sole member of `tunen-org` |
| [`Talky-SP`](https://github.com/Talky-SP) | **Talky Labs** | 2 | Org display name “Talky”, created 2025-01-04; repos configure `api.usetalky.com` production endpoints |
| [`jorgebp226`](https://github.com/jorgebp226) | **Jorge Beamonte** (high confidence, not certain) | 20 | Sole contributor to both Talky-SP repos; personal repos include `Frontend-Talky-Sender`, `appfacturas`/`appfacturas-frontend` (invoice apps) and `frontend-app-restauracion` (restaurant sector — Talky’s origin market); handle matches the initials |
| [`reversa-ai`](https://github.com/reversa-ai) | **Reversa** (probable) | **0** | Handle and creation date (2026-04-08) fit, but the org has no public repositories and no public members, so it cannot be positively confirmed |

**[Analysis]** **Reversa publishes no code at all.** For the company hosting the event
and owning Track 02, there is nothing on GitHub to read.
Anyone preparing for the democracy track gets no head start from their public
engineering.

#### 9.2 Accounts that look right and are not

**[Analysis]** Name-matching produces several convincing false positives.
Each was checked and rejected:

- **[`talkylabs`](https://github.com/talkylabs)** (7 repos) — client libraries in
  Python, Ruby, Node, Go, PHP, Java, and C# for the “Reach@TalkyLabs API,” documented at
  `reach.talkylabs.com`, all pushed in September 2023. A communications/messaging API
  company, unrelated to Madrid accounting.
  **This is a fourth distinct “Talky”**, on top of the three already catalogued in §8.2.
- **[`talky`](https://github.com/talky)** (1 repo, 2014) — “domen’s discussion
  platform.”
- **[`disruptive-labs`](https://github.com/disruptive-labs)** — a startup studio at
  disruptivelabs.io, created 2018, unrelated to **Disruptive Labs SL**, the Madrid
  entity behind Reversa (founded 2025).
- **`@Santiago-58`** — real name “Santiago Luis Aliaga Choque,” not Santiago Aliaga
  Estella.
- **`@AvdDGit`** — real name “Alv von der Decken,” not Jacob.
- **`@burgaleta`**, **`@Beamonte`**, **`@tunen`** — all empty accounts with no
  repositories, no name, and no bio.
  No evidence connects any of them to these companies.

**No GitHub presence was found** for Julius von Davier, Jacob von der Decken, Íñigo
Martín Llorente, Tomás Burgaleta Alonso, Santiago Aliaga Estella, or Maximilian Letón.

#### 9.3 Talky’s previous hackathon — the most useful find in this report

**[Primary]** `Talky-SP` contains exactly two repositories, both created **6 March
2026** for a prior Talky hackathon:

- **`hackathon-base-2026-03-06`** — “Base code for AI Evals Hackathon.”
  React + TypeScript + Vite + Tailwind.
  One commit, “Base for Hackathon,” by `jorgebp226`.
- **`hackathon-backend-base-2026-03-06`** — “Backend base on AWS for the Hackathon.”
  Python AWS CDK. Commits run to 23 March 2026, ending with “End of hackt.”

**[Analysis]** This is a **precedent for how Talky runs a hackathon**, published in
full. Read it before choosing Track 01.

**The brief, quoted from the scaffold’s own Objectives page** (bilingual ES/EN in the
source): the framing question is “In traditional software, 1 + 1 is always 2. In the era
of Artificial Intelligence, 1 + 1 is 'approximately 2, but sometimes it’s 2.01 and I’ll
explain it in a paragraph'.
How do we ensure quality when the base system changes its mind?”
The context given to participants was: “We have built an AI-powered pipeline that
automates the financial and logistics back-office: extracts data, normalizes products,
reconciles documents and matches bank statements.
It works exceptionally well, but we need advanced instrumentation.”
Notably, the brief also told participants their work would feed “a research paper we are
working on about the automation of prompt engineering.”

**What the scaffold provided.** Pre-built pages for Welcome, Getting Started,
Objectives, Docs with an API explorer, Golden Dataset, Annotation, Analytics, Test, and
Resources; Cognito authentication wired up; an i18n layer with full Spanish and English
translations; and design tokens.
The backend shipped AWS CDK stacks for DynamoDB, Lambda, API Gateway with a Cognito
authorizer, and self-mutating dev/pre/prod pipelines, plus a LocalStack stage.

**What it reveals about Talky’s production system.** The repositories document the real
platform in some detail:

- **Infrastructure**: AWS in **eu-west-3 (Paris)** — Cognito, API Gateway, Lambda,
  DynamoDB, CDK, “following the Talky-App architecture pattern.”
- **Live microservices** on `api.usetalky.com`: `tpv-api` (point of sale),
  `user-expenses-api`, `analytics-v2`, `delivery-notes-api` (albaranes), `analytics-v3`
  (payroll search), and `invoice-learning-api`.
- **The data model**, from a committed DynamoDB reference: `User_Expenses` (described as
  “the main financial table”), `User_Invoice_Incomes`, `Delivery_Notes`,
  `Payroll_Slips`, `Bank_Reconciliations`, `Vendors_AI` and `Clients_AI` (AI-grouped
  counterparty names for reconciliation), `Reconciliation_Suggestions` (for complex 1-N
  and N-1 matches), and `Supplier_Payment_Patterns` (described as “temporal guardrails
  for payments”).
- **The agent architecture**, from a committed design doc for an “AI CFO Agent”: a fast
  classifier (**gpt-5-mini**) splits fast chat from complex tasks, an orchestrator
  (**claude-sonnet-4.5**) decides whether it can answer directly or needs data, and a
  `fetch_financial_data` tool call hands off to a DB query agent.
- The backend repo also carries a `.claude/settings.json`, so the team develops with
  Claude Code.

**[Analysis]** Three things follow for Track 01. First, the “buried problems a good
accountant would catch” almost certainly live in structures resembling these tables, and
`Supplier_Payment_Patterns` — explicitly labelled guardrails — signals that Talky
already thinks about the abstention problem temporally.
Second, Talky’s own framing of the hard part is **not extraction but instrumentation**:
knowing whether a change made the system better.
Third, the March scaffold’s centerpiece pages were **Golden Dataset** and
**Annotation**, which is the same calibration-and-evidence theme that the Madrid Open
rubric scores at 20% and 15%.

#### 9.4 Tunen’s public code — infrastructure, not product

**[Primary]** `tunen-org`’s six repositories contain no product code.
Four are original, all authored solely by `a-shine`, and two are forks:

| Repo | Language | Origin | Purpose |
| --- | --- | --- | --- |
| `gcp_artifact_registry_dart_pub` | Dart | original | Implements the Dart Hosted Pub Repository Specification v2 on top of GCP Artifact Registry |
| `gcp-artifact-registry-dart` | Python | original | Wrapper around the same registry |
| `firebase-tools-dart` | Shell | original | Docker image with Dart and Firebase Tools |
| `github-gcp-repo-sync` | HCL/Terraform | original | GitHub-to-GCP repository sync |
| `dart_firebase_admin` | Dart | fork of `firebase/firebase-admin-dart` | Firebase Admin SDK |
| `flutter_map_geojson` | Dart | fork of `jozes/flutter_map_geojson` | GeoJSON layer for Flutter maps |

**[Analysis]** The stack is unambiguous: **Dart and Flutter on Firebase and GCP**, with
enough friction around private Dart package distribution that they built their own
hosted pub repository.
The `flutter_map_geojson` fork is the only hint of the geospatial side — GeoJSON
rendering on Flutter maps is how field boundaries reach a farmer’s phone.
Nothing here touches remote sensing, yield modelling, or the Track 03 problem.

#### 9.5 The Track 03 challenge owner’s own thesis code

**[Primary]** `a-shine/niab-automated-phenotyping` is Alexandre Shinebourne’s
**Cambridge AI4ER MRes project (2024)**, done with NIAB and carrying UKRI and University
of Cambridge branding.
It is a complete, open-source computer-vision phenotyping pipeline for **teff**
(*Eragrostis tef*), an underutilized East African cereal, tracking Plant Canopy Coverage
ratio as an indicator of weed resilience.

Its contents matter more than its subject:

- **U-Net and DeepLabV3+** semantic segmentation models, compared.
- **Monte Carlo dropout for uncertainty quantification**, with the best model being an
  MC-dropout U-Net.
- An **active learning** strategy that uses that uncertainty to choose what to annotate
  next, shown to improve performance with minimal additional annotation.
- Tooling for semi-automated annotation, mask editing, HSV thresholding, benchmarking,
  and uncertainty visualisation.
- A candid limitations section: automated tracking broke down on instance segmentation
  and shoot-to-sector assignment (phenotype-to-genotype mapping).

**[Analysis]** This is the single most revealing artifact about how the Tunen track will
be judged. The person who wrote the Track 03 challenge spent a Cambridge master’s on
**uncertainty quantification and active learning in agricultural computer vision**, and
published the code. The rubric’s 20% for “safe autonomy and restraint” is not a generic
hackathon platitude when the challenge owner’s own thesis is about calibrated
uncertainty driving what a model refuses to answer alone.
His stated methodological instinct — “we try to take a pragmatic approach” — and his
willingness to publish a limitations section suggest that a team documenting where its
model fails will be rewarded rather than penalized.

#### 9.6 Local checkouts

**[Primary]** Seven repositories were cloned into the gitignored `attic/` directory per
this repository’s `checkout-third-party-repo` convention:

```
attic/hackathon-base-2026-03-06           # Talky evals hackathon frontend
attic/hackathon-backend-base-2026-03-06   # Talky hackathon AWS CDK backend + internal docs
attic/gcp-artifact-registry-dart          # Tunen
attic/gcp_artifact_registry_dart_pub      # Tunen
attic/github-gcp-repo-sync                # Tunen
attic/firebase-tools-dart                 # Tunen
attic/niab-automated-phenotyping          # Shinebourne's Cambridge/NIAB thesis code
```

The two `tunen-org` forks were not cloned, being unmodified upstream code.

### 10. The three challenges as stated

Common rules across tracks: **[Primary]** “the brief states the question, the data and
the metric, and nothing about the method.”
You pick one track at registration.

#### Track 01 — Talky / Accounting: “Close the year, alone.”

**The question.** “Can a machine close a full financial year for a company the size of
an IBEX 35 group, with no accountant in the loop?”

**The data.** “One company’s entire year exactly as it arrived, across formats,
languages and currencies.
Buried in it are the things a good accountant would catch, and nobody will tell you
where they are.”

**The metric.** “How much of the year closes with no human, at 99% precision.”
Explicitly annotated: “Approving something wrong costs more than flagging it.
Escalating everything does not win either.”

**[Analysis]** This is a precision-constrained coverage maximization: maximize
autonomous throughput subject to a 99% precision floor.
The scoring is asymmetric by design—false approvals are penalized more than
abstentions—but a system that abstains on everything scores zero coverage.
That makes a well-calibrated confidence threshold the central engineering problem, not
extraction accuracy per se.
The Luma summary describes the same track as automating year-end closure for a large
Spanish company at 99% precision, consistent with the site.

#### Track 02 — Reversa / Democracy: “Read a democracy in real time.”

**The question.** “Parliament speaks all day and the official record lands weeks later.
Can a country know what was said, and who said it, minutes after a session ends?”

**The stakes, in the organizers’ words.** “The people in that room decide the lives of
millions. A sentence spoken there becomes a tax, a licence, a border, a right.
It should not take weeks to reach the people it applies to.”

**What you inherit.** “You inherit a live pipeline that already works on real sessions
of two parliamentary chambers around the world, and the freedom to take it wherever it
needs to go.”
The Spanish text is slightly more specific: *“dos cámaras de parlamentos de
distintos puntos del mundo”* — two chambers of parliaments **from different parts of the
world**, which reads as two different countries rather than two chambers of one
legislature.

**The goal state.** “Get this right and everything a democracy says is captured and
usable the same day, in every official language.”

**The metric.** “How fast the transcript is ready, and how often the right speaker is
named. Measured on real transcripts.”

**A materially more specific target appears only on Luma.** **[Primary]** The Luma
listing describes the Reversa challenge as **transcribing parliamentary proceedings into
official records within five minutes across five languages**. Neither “five minutes” nor
“five languages” appears on the event site in either language.

**[Analysis]** If the Luma figures hold, this is a two-objective problem—**latency** and
**speaker attribution accuracy**—with the composite weighting unknown until the brief.
“Five languages” most plausibly maps onto Spain’s Congreso de los Diputados, where
Spanish plus the co-official languages (Catalan, Basque, Galician, and Valencian counted
separately) gives five; but “different parts of the world” cuts against a Spain-only
reading, and this should be treated as unresolved.
The phrase “into official records” is also doing work: an official parliamentary record
is not a raw transcript, a point developed in §11.

#### Track 03 — Tunen / Agriculture: “Building the future of farming.”

**The question.** “We give you the plot; you resolve its reality to predict the future.
Can your system reflect the reality at a resolution worth acting on?”

**The problem.** “The environment decides what a field can yield, and a lot of the
necessary data is available online: satellites, weather, soil maps, topography… but
never in one place.”

**The task.** “Reconcile different sources into six layers a farmer can read, including
your own yield potential map, refreshed unattended every 14 days.
Different projections, different resolutions, a different portal in every country, and
field edges that quietly corrupt every statistic you compute — you wanted a challenge!”

**The metric.** “How close does your model come to the reality we hold back?”

**A significant discrepancy.** **[Primary]** The Luma listing describes the Tunen
challenge entirely differently: **providing defensible land valuations with confidence
scores and supporting citations for banking applications**. The event site describes
yield-potential mapping for farmers; Luma describes collateral valuation for banks.
These share an underlying data-fusion engine but have different outputs, different
users, and different metrics.

**[Analysis]** The Tunen site does list “banks” among the parties that will need farm
data, so the two framings are reconcilable as one pipeline with two consumers—but anyone
choosing this track should ask which output is scored.
The site’s own warning about field-edge effects is the real tell: mixed pixels along
plot boundaries silently bias every zonal statistic, and handling them is a known,
unglamorous, decisive detail.

#### Discrepancies between the two official sources, summarized

| Track | Event site (reversa.ai) | Luma listing | Status |
| --- | --- | --- | --- |
| 01 Talky | IBEX 35-scale year-end close, 99% precision | Year-end close for a large Spanish company, 99% precision | Consistent |
| 02 Reversa | Speed of transcript + speaker-naming accuracy | Within **5 minutes**, across **5 languages** | Luma adds targets absent from the site |
| 03 Tunen | Six layers + yield potential map for farmers, refreshed every 14 days | Defensible **land valuations** with confidence scores and citations, **for banking** | Materially different framing |

**[Analysis]** Both pages are official, and neither is dated in a way that establishes
precedence. The most likely explanation is that the Luma copy was drafted at a different
moment in the challenge design.
The briefs, released the week before the event, are the authority.
This is the single most important open question for anyone choosing a track.

### 11. Deep dive: the Reversa problem domain

This section is the technical landscape for Track 02. It is organized as: why the
problem exists (§11.1–11.3), what the state of the art actually measures (§11.4–11.9),
what evidence a system can use beyond the audio (§11.10), what data exists (§11.11),
where the latency budget goes (§11.12), what the official-record editorial layer costs
(§11.13), and who has already deployed this (§11.14–11.16).

A companion report,
[Named Speaker Attribution for Parliamentary Transcription — Critical Review and Research Agenda](research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md),
reviews this section critically, corrects it where its figures came from domains unlike
a chamber, and turns it into a measurement protocol and research plan.
The corrections from its fact-check pass have been applied here.

#### 11.1 Why the official record lags

**[Press]** In Spain, the *Diario de Sesiones* is produced by parliamentary
stenographers and stenotypists who record the session and then draft the record.
The published text is not a raw transcript: the differences between the video and the
publication reflect error correction and “the necessary adaptation of oral language to
written language” performed by editorial staff.

**[Press]** Normal publication is the morning after a plenary or committee session.
Delays are visible when they occur: in 2023, sessions held on Tuesday had still not been
published by Friday, a delay attributed to written texts in the co-official languages
only reaching the stenographers at 22:00 on the Wednesday.

**[Analysis]** This nuance matters for how the challenge is framed.
The organizers’ “weeks later” is a fair description of some jurisdictions and of Spain’s
worst cases, but Spain’s routine cadence is closer to next-morning.
The more precise problem is not that the record takes weeks in the median case—it is
that (a) the tail is bad and correlates exactly with multilingual sessions, and (b) even
next-morning is too slow for downstream consumers who need to act the same day.
For Reversa specifically, a legislative twin that ingests the record a day late is a day
late on every obligation it derives.

**[Primary]** Reversa’s commercial interest is direct: its product tracks legislative
procedures “from the moment a text moves to the obligation it lands on a team.”
Same-day capture of floor speech is upstream of that whole chain.

#### 11.2 The multilingual complication, and what it costs today

**[Press]** Since **19 September 2023**, deputies in the Congreso de los Diputados may
speak in the co-official languages, with simultaneous translation into Spanish.
The reform permits any language with official status in an autonomous community:
**Catalan (Valencian being its legal name in the Valencian Community), Basque, Galician,
and Aranese**—not three.
Asturian, which has protected but not official status, is **not** covered, although some
coverage listed it.
Contemporary reporting noted that the Congress’s stenographers stated
they would not take responsibility for translated texts, and the Regulation requires
deputies’ words to be reproduced **both in the language spoken and in Spanish**.

**[Press]** The economics are public and are the clearest statement of the problem’s
market size:

| Item | Figure | Source |
| --- | --- | --- |
| Congress adaptation of the chamber for simultaneous interpretation | €280,000 | COPE, Sept 2023 |
| Adaptation of 8 committee rooms | €1.6M | OKDiario |
| Framework contract for translation, interpretation, transcription and **live subtitling** (annual ceiling) | **~€3.99M** (“3,9 millones”; the exact figure €3,990,315.14 appears in one summary and is not independently confirmed) | Contract CON-2024/9900775.0, awarded Nov 2024 |
| Reported 2025 spend on those services | **~€920,000** (~€2,500 per session day); reported by one outlet, not independently confirmed | Press Digital, June 2026 |
| **Transcription lot awarded to Pangeanic BI Europa, S.L.** | **€462,835**, at **€2.85/minute** for co-official languages and **€1.65/minute** for Spanish | Pangeanic, contract award |

**[Analysis]** Three things follow.
First, the incumbent for AI transcription in the Congress is already named and already
paid: Pangeanic, from 1 January 2025, extendable two years.
Second, the per-minute unit price is the closest thing to a public valuation of the
task—**€1.65–2.85 per minute of floor speech**, or roughly €600–1,000 for a six-hour
session—which is what an automated pipeline is competing against, not zero.
Third, the price differential between Spanish and the co-official languages (1.7×) is
the market’s own estimate of where the difficulty sits, and it agrees with the technical
picture in §11.4.

**[Analysis]** Two structural difficulties follow from the language regime.
First, **code-switching**: speakers move between languages within a single intervention,
so language identification has to operate at sub-utterance granularity rather than per
session or per speaker.
Second, the co-official languages are **lower-resource** than Spanish, so off-the-shelf
multilingual ASR degrades exactly where the human process also degrades.

#### 11.3 The audio you can get is not the audio you want

**[Press]** This is the most consequential practical finding in this section, and it is
not in any of the model documentation.
When a deputy speaks a co-official language, the Congress’s outbound signal—to
television, to the press booths, and to the streaming feed—carries **the original audio
with the interpreter’s voice mixed over the top** (*“con el audio original y la voz del
traductor por encima”*), plus Spanish subtitles.
Spanish interpretation is Channel 1 on the in-room headset system.
Three further reports (El Debate, El Español, elDiario.es, September 2023) describe the
same design; the interpreters work remotely, which puts the interpretation **2–4
seconds** behind the floor, and their individual feeds exist as separate streams before
the mix. The European Parliament’s stream, by contrast, offers up to 32 selectable audio
channels — the floor plus each interpretation.

**[Analysis]** For a transcription-and-attribution system consuming the public stream,
this is a hostile input:

1. Every co-official-language intervention is **permanently overlapped speech** by
   construction, not incidentally.
   Overlap is the dominant error source in diarization (§11.6), and here it covers 100%
   of the segment rather than the 10–16% typical of debate audio.
2. The **interpreter is a different speaker** with a different voice, so acoustic
   speaker identification on that segment identifies the interpreter, not the deputy—or
   fails on the mixture.
3. **Language identification** sees two languages simultaneously in the same channel.
4. The interpreter lags the speaker by seconds, so **word-level timestamps** from the
   mixed channel do not align to either speaker’s turn boundaries.

**[Analysis]** The consequences for challenge strategy are concrete.
Any evaluation set built from the public stream or the YouTube feed either has to be
restricted to Spanish-only interventions, or it is measuring the interpreter.
The clean fix requires an isolated floor channel, which is exactly the kind of thing a
challenge owner with “a live pipeline that already works on real sessions of two
parliamentary chambers” would already have and a participant would not.
**This is the single highest-value question to ask at 09:00: which audio channel does
the provided pipeline consume, and is the floor feed separable from the interpretation
feed?**

**[Analysis]** The mirror-image opportunity is the chamber’s own conference system.
Parliamentary microphone systems from Bosch (DICENTIS, up to 750 devices, with
“identification at seat” modules and IP integration) and Televic (chip-card-activated
personalized microphone units) know exactly which seat’s microphone is open and which
member is assigned to it.
Both expose it programmatically: Televic’s CoCon API is a publish/subscribe HTTP service
carrying microphone on/off and delegate-identity events, and Bosch DICENTIS exposes
delegate, seat, and active-microphone state over a REST API. Where that metadata is
available, speaker attribution is a **database join, not a machine-learning problem**,
and the accuracy ceiling is 100% minus the rate at which members speak from someone
else’s microphone. A serious production answer to this challenge integrates the
conference system; a hackathon answer will not have it, and should say so explicitly
rather than pretend the acoustic route is the only one.

#### 11.4 State of the art: ASR

**[Technical]** The most relevant independent English benchmark is Artificial Analysis’s
**AA-WER v2.0**, which tested 41 models across three real-world datasets—AgentTalk,
VoxPopuli, and Earnings-22—explicitly covering conversational speech, **parliamentary
speech**, and earnings calls.
Reported leaders: **ElevenLabs Scribe v2 at 2.3% WER**, **Gemini 3 Pro at 2.9%**, and
**Voxtral Small at 3.0%**, and **Gemini 3 Flash at 3.2%**. The architectural trend from
2025 onward is toward **encoder \+ LLM-decoder end-to-end** models.

**[Technical]** For the Iberian languages, the picture is much worse than the English
headline and is highly dataset-dependent.
Vanilla `whisper-large-v3` on Common Voice 13 reports **Spanish 4.38%, Catalan 13.67%,
Galician 12.46%, Basque 38.85%** WER; on FLEURS the same model reports **Spanish 15.01%,
Catalan 5.68%, Galician 10.06%**. The Spanish CV13-versus-FLEURS inversion (4.38 vs
15.01) is a warning in itself: these numbers measure corpora at least as much as they
measure models.
These CV13 and FLEURS figures come from a search summary attributing them
to the Whisper-LM paper and were not verified against it; the Spanish FLEURS figure is
implausibly high for large-v3 and should be treated as unconfirmed.

**[Primary]** In-domain evidence exists, from a production operator.
Parlamento.ai’s `open-source-asr` study (GitHub, August 2026; read directly) runs open
models over 168 parliamentary clips (13.79 h; 24 each of Spanish, Catalan, Galician,
Basque, English, Portuguese, and multilingual European Parliament audio) and measures
word-level **agreement with three paid APIs**, not accuracy — the study has no
human-corrected reference.
`whisper-large-v3-turbo` differs from the paid consensus by **3.25%** on Spanish
(Peninsular and Latin American pooled; the paid APIs differ from each other by 5.10%),
**9.56%** on Catalan, **14.71%** on Galician and, for the best Basque model, **21.29%**;
Galician and Basque fall outside the operator’s acceptance band.
Its 90-day workload shows a median of **3** concurrent sessions per five-minute window
and a maximum of **31**, and its paid transcription cost was **USD 0.71 per
audio-hour**. The companion report (§3.3) reads this in full.

**[Technical]** Fine-tuned open models that matter here:

- **`BSC-LT/whisper-large-v3-LoS`** — a single multilingual model covering **Spanish,
  Catalan, Galician, and Basque**, trained on 8,110 hours, with a punctuated variant
  (`-LoS-punctuated`).
- **`projecte-aina/whisper-large-v3-ca-3catparla`** — Catalan, fine-tuned on the
  710-hour 3CatParla broadcast corpus (IberSPEECH 2024), reporting **WER 0.96** on its
  own in-domain test set; a `faster-whisper` conversion is published alongside.
- **`HiTZ/whisper-large-v3-eu`** — Basque, reporting **10.62% WER** on Common Voice 13.0
  (v2: 11.34%).
- **Whisper-LM** (arXiv 2503.23542, de Zuazo et al.)
  — pairing fine-tuned Whisper with n-gram or LLM language models, with gains up to
  **68% relative** for Basque on AhoMyTTS; evaluated across Basque, Spanish, Catalan and
  Galician.

**[Analysis]** The sub-1% Catalan figure is not transferable.
It is reported on a broadcast-derived in-domain test set; parliamentary floor audio with
crosstalk, interruptions, applause, gavel, and variable microphone discipline is a
different distribution.
`BSC-LT/whisper-large-v3-LoS-punctuated` remains the best single starting point: four of
the likely five languages in one model, with punctuation restoration folded in.

**[Technical]** Open Whisper derivatives are not the only route to the co-official
languages; two commercial services document them directly.
**Speechmatics** lists Catalan, Basque and Galician among 55+ languages, in realtime as
well as batch, and is the one major vendor that pairs that coverage with
enrollment-based speaker identification (§11.7)
([languages](https://www.speechmatics.com/languages),
[realtime speaker ID](https://docs.speechmatics.com/speech-to-text/realtime/speaker-identification)).
**Amazon Transcribe** covers Catalan, Basque and Galician in **streaming** as well as
batch — among 54 streaming languages — with speaker partitioning on both paths and
included in the base price, though its speaker labels are anonymous
([streaming-language announcement](https://aws.amazon.com/about-aws/whats-new/2024/10/amazon-transcribe-streaming-transcription-additional-languages)).
Coverage for the other major APIs is weaker or unconfirmed: Google Chirp 3 has `ca-ES`
generally available but `eu-ES` and `gl-ES` unconfirmed, and AssemblyAI, Deepgram and
OpenAI publish no confirmed ca/eu/gl support.
These are vendor-documented capabilities read from vendor and aggregator pages, not
measured accuracy on parliamentary audio.

**[Technical]** Code-switching has a directly on-point 2025 result.
*Optimizing ASR for Catalan-Spanish Code-Switching* (Interspeech 2025, BSC) compares
three strategies—synthetic CS data, concatenated monolingual audio, and real CS data
with language tokens—and finds that **a modest amount of synthetic CS data combined with
the dominant-language token** performs best.
The evaluation set (867 clips, 4h09m, Catalan-dominant with systematic Spanish segments)
is published as `BSC-LT/BSCs_Code_Switching_CA-ES_ASR_Test`. Separately, Whisper’s
architecture emits **one language token per 30-second window**, which is the wrong
granularity for intra-sentential switching; the literature’s fixes are encoder refiners,
language-prompt tuning, and concatenated tokenizers that emit a language ID per token.

**[Technical]** Two Whisper failure modes matter more in parliamentary audio than in
benchmark audio:

- **Hallucination on non-speech.** Whisper generates fluent text from silence, applause,
  and noise; a widely cited study found roughly **1% of transcriptions contained
  entirely hallucinated phrases**, concentrated in long-form audio with long silences.
  Plenary audio is exactly that: gavel, applause, procedural pauses, votes, empty
  rostrum. The mitigations are VAD gating before the decoder (never send silent chunks),
  and contrastive/decoding-side fixes such as Calm-Whisper and multi-negative
  contrastive decoding.
- **Repetition loops and omissions** in long-form decoding, which is why WhisperX-style
  VAD-chunked pipelines with forced alignment are the practical default rather than
  naive long-form decoding.

**[Technical]** The aligner in that pipeline is itself a four-language problem, and the
usual default does not solve it.
The **Montreal Forced Aligner** model zoo ships acoustic models and dictionaries for
**Spanish and Basque only — there is no Catalan and no Galician acoustic model or
dictionary** in the `mfa-models` repository
([mfa-models](https://github.com/MontrealCorpusTools/mfa-models)). Three things do cover
the gap.
`ctc-forced-aligner` aligns over MMS checkpoints, which include es/ca/eu/gl, but
its default weights are CC-BY-NC and the repository returns no SPDX licence
([ctc-forced-aligner](https://github.com/MahmoudAshraf97/ctc-forced-aligner)).
**WhisperX** ships a default alignment model for each of the four — torchaudio
`VOXPOPULI_ASR_BASE_10K_ES` for Spanish, and the community XLSR checkpoints
`softcatala/wav2vec2-large-xlsr-catala`, `stefan-it/wav2vec2-large-xlsr-53-basque` and
`ifrz/wav2vec2-large-xlsr-galician` for the rest, whose parliamentary-domain quality is
unmeasured ([whisperX](https://github.com/m-bain/whisperX)). `Qwen3-ForcedAligner-0.6B`
(Apache-2.0) is the newest option, but its claim of 11 languages comes without the list,
so ca/eu/gl support is unproven ([Qwen3-ASR](https://github.com/QwenLM/Qwen3-ASR)).

**[Analysis]** For the restraint/abstention weighting in the rubric (20%), hallucination
is the specific behaviour to instrument.
A system that emits a confidence per segment and abstains on low-acoustic-support
segments is both more accurate and directly scoring against the rubric.

#### 11.5 Three different problems, routinely conflated

**[Technical]** The challenge statement—“how often the right speaker is named”—sits at
the third of these, and the published numbers get much worse as you move down the list:

| Problem | Question | Output | Standard metric |
| --- | --- | --- | --- |
| **Diarization** | who spoke when | anonymous labels (`spk_0`, `spk_1`) | DER, JER |
| **Speaker identification / assignment** | which enrolled person is this | a name from a closed or open set | identification accuracy, open-set EER/FA |
| **Speaker-attributed ASR** | which person said which words | named transcript | cpWER, tcpWER, SA-WER |

**[Technical]** Enrollment for identification typically requires 5–30 seconds of
single-speaker audio per person, with multiple clips under different acoustic conditions
improving robustness.

**[Analysis]** The distinction is not pedantic; it decides architecture.
Diarization-then-name is a two-stage pipeline whose errors compound: a clustering error
in stage one is unrecoverable in stage two.
Enrollment-first designs (target-speaker VAD, §11.7) invert that dependency, and a
parliament is one of the few domains where the enrollment set is knowable in advance.

#### 11.6 What the diarization numbers actually say

**[Technical]** Open-source and premium diarization, DER in percent, lower is better.
Left table from the `pyannote.audio` repository (benchmark updated 2025-09); right table
from the DiariZen repository, **scored without a collar**:

| Dataset | pyannote 3.1 | pyannote community-1 | pyannoteAI precision-2 |
| --- | --- | --- | --- |
| AMI (IHM) | 18.8 | 17.0 | **12.9** |
| AMI (SDM) | 22.7 | 19.9 | **15.6** |
| DIHARD 3 (full) | 21.4 | 20.2 | **14.7** |
| VoxConverse (v0.3) | 11.2 | 11.2 | **8.5** |
| CALLHOME (part 2) | 28.5 | 26.7 | **16.6** |
| AliMeeting (ch. 1) | 24.5 | 20.3 | **15.2** |
| AVA-AVD | 49.7 | 44.6 | **37.1** |
| REPERE (phase 2, French broadcast) | 7.9 | 8.9 | **7.4** |

| Dataset (no collar) | pyannote 3.1 | DiariZen-Large-s80 | DiariZen-Large-s80-v2 |
| --- | --- | --- | --- |
| AMI-SDM | 22.4 | 14.0 | **13.9** |
| DIHARD 3 full | 21.7 | 14.5 | **14.5** |
| VoxConverse | 11.3 | 9.2 | **9.1** |
| NOTSOFAR-1 | — | 17.9 | **16.7** |
| AliMeeting far | 24.4 | 12.5 | **10.8** |

**[Technical]** A DER figure without its protocol is not a number.
The two tables above disagree on the same model and dataset because collar and
overlap-scoring conventions differ; independent evaluation (Lanzendörfer et al., arXiv
2509.26177) makes the same point, and reports DiariZen at 5.2% on VoxConverse under a
0.25 s collar with overlap scored.
Any comparison the team makes on the day must fix one protocol—`md-eval` via `dscore` or
`meeteval-der`, collar and overlap stated—and hold it.

**[Technical]** Three properties of these systems bear directly on a plenary chamber:

1. **Speaker-count cliff.** NVIDIA’s Streaming Sortformer is architecturally capped at
   four speakers; reported DER is **13.24% for sessions with one to four speakers versus
   42.56% for five to nine**. Clustering-based systems degrade more gracefully but still
   degrade: the count has to be inferred, and splitting one speaker into two or merging
   two into one cascades into confusion error.
   The cap bounds speakers tracked *concurrently*, so with turn-level windowing from the
   agenda or the chair’s announcements it does not bind a plenary; the limitation that
   does bind is that Sortformer emits anonymous labels and takes no enrollment profiles.
2. **Overlap.** Overlap is measured at **1.2% in news broadcasts, 10.4% in TV debates,
   and ~16% in heated televised debate**; 6–14% of words are overlapped in spontaneous
   multi-party speech. Parliamentary interjection rates are high — in the Bundestag, over
   half of all speeches draw at least one heckle, averaging 3.49 per speech.
   And per §11.3, interpreted interventions are 100% overlapped by construction.
3. **Long-form degradation.** Performance degrades on long recordings, most visibly on
   AMI, from the mismatch between training-segment length and full-session length.
   Practical resource note: `pyannote.audio` 4.0.3 was reported to use ~6× the VRAM of
   3.3.2 on a 72-minute file (9.54 GB against 1.59 GB peak in the issue’s profiling
   table), spiking after clustering during reconstruction — relevant if the plan is one
   pass over a six-hour session on a single consumer GPU.

**[Technical]** Streaming changes the tradeoff less than expected.
Streaming Sortformer (Interspeech 2025) maintains competitive DER at chunk sizes down to
**0.32 s**, reporting **19.32% DER on DIHARD and 11.50% on CALLHOME** at that latency,
using an arrival-order speaker cache (AOSC) plus a FIFO queue to resolve between-chunk
permutation. That is the correct component for a bounded-lag design (§11.12) — with its
anonymous-label output, not the speaker cap, as the binding constraint.

#### 11.7 Speaker identification at parliament scale

**[Technical]** Closed-set verification is close to solved on clean benchmarks:
ECAPA-TDNN reports **0.87% EER on VoxCeleb1** at C=1024, with WavLM-front-end and
ReDimNet variants at or below that.
None of that is the regime this challenge is in.

**[Technical]** The regime is **open-set identification against a large watchlist**, and
it has a known, quantified failure mode.
As the enrolled population grows, out-of-set similarity scores shift upward, overlapping
the target distribution and driving false alarms at any fixed threshold.
Published results show **EER degrading from 1.43% at 100 enrolled speakers to 3.04% at
700**; the VoxWatch and VoxBlink2 benchmarks (the latter with 111,284 speakers) exist
specifically to measure this, and score calibration (e.g. QMF) is the standard partial
remedy.

**[Analysis]** The Congreso has **350 deputies** plus ministers, the presiding officers,
and government witnesses — squarely in the 100-to-700 band where that degradation curve
was measured. So the naive design (enroll every member, cosine-match each diarized
segment) lands on a known-bad operating point, and the threshold that controls it is a
per-deployment calibration decision, not a hyperparameter to leave at default.
This is where the rubric’s 20% restraint weight and the technical reality coincide
exactly: the correct behaviour at low margin is to **abstain and emit “unidentified
speaker”**, not to name the nearest neighbour.

**[Technical]** Two further degradations stack on top:

- **Short segments.** Reported EER rises ~46% relative when test duration falls from
  3.59 s to 2.05 s (8.72% → 12.8%). Parliamentary question time is full of sub-5-second
  interventions and procedural one-liners from the chair.
- **Cross-lingual mismatch.** Language switching induces systematic embedding shifts for
  the *same* speaker, with language mismatch the dominant factor; there is a 2026 study
  specifically on Iberian languages.
  A deputy enrolled on Spanish floor audio and speaking Catalan is a cross-lingual
  trial.

**[Technical]** The architecture that fits this shape is **target-speaker VAD
(TS-VAD)**: condition a frame-level activity model on one or more enrolled speaker
profiles and predict per-speaker activity directly, which represents overlapping speech
natively (multiple targets active in the same frame) and inverts the
cluster-then-identify dependency.
TS-VAD systems are the backbone of most winning challenge submissions;
profile-error-tolerant variants (PET-TSVAD) exist because profiles derived from a first
clustering pass carry errors forward.
In a parliament the profiles do not have to come from a clustering pass at all — they
can come from the archive (§11.11).

**[Technical]** If the enrollment step is bought rather than built, the hyperscaler
options are gone. **Azure AI Speaker Recognition was retired on 2025-09-30** — the
enrollment and identification APIs are no longer accessible, although diarization inside
Azure STT is unaffected
([release notes](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/releasenotes))
— and **Amazon Connect Voice ID** closed to new customers on 2025-05-20 and reached
**end of support on 2026-05-20**
([end-of-support notice](https://docs.aws.amazon.com/connect/latest/adminguide/amazonconnect-voiceid-end-of-support.html)).
Both dates are in the past.
Three enrollment services remain, each with a caveat that matters at 350 deputies:

- **pyannoteAI voiceprints** — voiceprint from up to 30 s of clean audio, but
  identification requires the Precision-2 model; €0.112/hr on the Developer tier and
  €0.096/hr on Starter; the trial allows 10 voiceprints and no paid-tier ceiling is
  published.
- **Picovoice Eagle** — on-device enrollment, free tier 100 min/month, no fixed speaker
  limit claimed, but the model is closed and **no public EER** exists.
- **Speechmatics speaker identification** — enrollment from 5–30 s of single-speaker
  audio, in batch and realtime, capped at **50 speaker identifiers per session**: an
  order of magnitude short of ~350 members, so the candidate set has to be pre-filtered
  per sitting. The separate `max_speakers` parameter (2–20) applies only to non-enrolled
  speakers
  ([speaker identification](https://docs.speechmatics.com/speech-to-text/features/speaker-identification)).

Every figure here is vendor-documented rather than independently measured, and each
service is a lock-in point; self-hosted embeddings with AS-norm remain the only route
with a documented open-set protocol.

#### 11.8 The in-language anchor: Albayzin / IberSPEECH-RTVE

**[Technical]** The closest published evaluation to this challenge’s actual metric, in
Spanish, is the **Albayzin IberSPEECH-RTVE Speaker Diarization and Identity Assignment
Challenge (SDIAC)** — segment broadcast audio by speaker, link segments, and identify a
**closed set** of named people.
The RTVE database provides several hundred hours of Spanish public-broadcaster content
across genres — the 2022 identity-assignment task used **74 enrolled speakers over 54
hours** — with accent diversity, overlapping speech, spontaneous speech and background
noise.

**[Technical]** On the RTVE 2022 evaluation set, the Intelligent Voice system reported
**DER 37.2% for diarization-with-identity-assignment and 44.34% for diarization only**.
The 2020 edition ran a **multimodal** track (audio + video), where ViVoLAB’s system
trained a learnable enrollment model per identity — optimized with an approximate
detection cost function — and beat averaged enrollment embeddings with cosine scoring.

**[Analysis]** This is the number to hold in mind, and it is the strongest single reason
to treat Track 02 as an attribution problem rather than a transcription problem.
In-language, in-domain, on broadcast Spanish with a *closed* identity set and no
real-time constraint, a competition system named the right speaker with a DER in the
high thirties. Set that against 2–3% headline WER. The named-speaker problem is roughly
an order of magnitude harder than the words problem, and the gap is not closing by
picking a better ASR model.

The 2020 multimodal result also validates the approach recommended in §11.10: identity
came from **fusing enrollment models across modalities**, not from acoustics alone.

#### 11.9 What “how often the right speaker is named” could mean

**[Analysis]** The metric is under-specified in both official sources, and the plausible
readings differ by more than an order of magnitude in difficulty.
Ask which one is being computed:

| Candidate metric | What it measures | Rough difficulty |
| --- | --- | --- |
| Per-intervention attribution accuracy | fraction of speech turns whose name is right | most forgiving; one label per turn, and turns are long |
| Speaker-attributed word accuracy (cpWER) | fraction of words carrying the right name | punishing; boundary errors cost words |
| tcpWER (collar-constrained) | right words, right name, right time | most punishing; adds timing |
| DER-with-identity (Albayzin SDIAC style) | fraction of audio *time* wrongly attributed | intermediate; overlap dominates |

**[Technical]** `cpWER` concatenates all utterances per speaker and takes the minimum
over speaker permutations, so it captures lexical accuracy and speaker consistency but
is insensitive to local timing; `tcpWER` adds a temporal collar so words match only
within it, and converges to cpWER as the collar grows.
**MeetEval** (`meeteval-wer cpwer|tcpwer|orcwer|tcorcwer`, `meeteval-der dscore`) is the
reference implementation and computes DER through `md-eval` as well, which makes it the
practical choice for a single scoring harness on the day.

**[Technical]** Published speaker-attributed numbers, for calibration:

- CHiME-8 NOTSOFAR-1 (meetings): the USTC-NERCSLIP system reports **tcpWER 22.99%
  single-channel** and **14.27% multi-channel**; a third-placed single-channel system
  reports **33.5%**.
- Vendor-published cpWER on a voice-agent benchmark (2026): AssemblyAI Universal-3.5 Pro
  at **30.17 average cpWER**, ElevenLabs Scribe v2 at **35.26**.

**[Analysis]** Those cpWER figures sit around 30–35 while headline WER sits at 2–3.
Whatever exact definition the organizers use, the ratio is the finding: **speaker
attribution is where essentially all of the error lives.** A team that spends the twelve
hours improving ASR is optimizing the term that is already small.

#### 11.10 The non-acoustic evidence stack

**[Analysis]** A parliament is an unusually favourable environment for beating the
acoustic limit, because identity is redundantly encoded in several channels that are
cheaper and more reliable than the voice:

1. **The chair names the next speaker aloud**, almost always, by name and often by
   parliamentary group (*“tiene la palabra…”*). This is a textual cue inside the
   transcript itself, upstream of any acoustic model, and it is highly formulaic.
2. **The order of business is published in advance**, constraining the candidate set at
   any moment from 350 to a handful — which moves the open-set problem of §11.7 back
   toward the 100-speaker end of the degradation curve, or below.
3. **Enrollment data is free and abundant.** Every member has hours of prior floor audio
   in the public archive, already attributed by the official record.
4. **The camera cuts to the speaker**, and broadcast parliamentary video carries
   on-screen name-and-group overlays.
   Both are independent identity signals.
5. **The conference-system microphone metadata** (§11.3), where obtainable, dominates
   all of the above.

**[Technical]** Each of these has published support:

- **Text-side attribution** is an established task with its own corpora and results —
  GePaDe_SpkAtt for German parliamentary debates (7,700+ manually annotated speech
  events, with source/addressee/message/topic roles), QLoRA-adapted LLMs for the same,
  and *Out of the Mouths of MPs* (LREC-COLING 2024). Processing whole documents rather
  than pages improves speaker tagging through **session-president identification and
  speaker-continuity propagation** — precisely the two cues above.
- **Vision-language models** were applied to Italian parliamentary records in 2026: a
  specialized OCR model preserving reading order, then a large VLM performing
  transcription refinement, element classification and **speaker identification** by
  jointly reasoning over visual layout and text.
- **LLM post-correction of diarization** is a live subfield — DiarizationLM and
  successors show measurable gains, with performance increasing as context length grows,
  and text-only diarization is now competitive enough to be studied on its own.
  The honest caveat from the same literature: inferring identity from transcript content
  is a **fallback, not a primary method**, when used without the structural cues above.
- **Multimodal enrollment fusion** is validated in-language by the Albayzin 2020
  multimodal track (§11.8).
- **The task itself has a precedent.** The REPERE challenge (2012–2014) scored *named*
  person identification on French television by fusing voice, face, and OCR of on-screen
  name overlays; the PERCOL system reached an estimated global error rate of 24.4%
  (supervised) on the 2013 test and ranked first in 2014. Unsupervised naming from
  overlaid text is the same move as naming from the chair’s announcement.

**[Analysis]** The design that follows is evidence fusion with per-attribution
provenance: each named turn carries the evidence that produced it (chair announcement at
timestamp T, agenda slot, acoustic score, on-screen overlay) and a calibrated
confidence, and abstains below threshold.
This serves the stated metric and the 15% auditability weight with the same mechanism,
and it is the part of the system that can be fully designed and built before the event.

#### 11.11 Datasets: what exists, and what does not

**[Technical]** Public parliamentary speech corpora, with what each does and does not
give you:

| Corpus | Coverage | Size | Speaker labels | Relevance here |
| --- | --- | --- | --- | --- |
| **VoxPopuli** | European Parliament 2009–2020, 23 languages | 400K h unlabelled; 1.8K h transcribed | yes — **Spanish: 166 h transcribed, 305 speakers** | the only large EP set with speaker IDs; no co-official languages |
| **Europarl-ASR** | European Parliament | ~1.3K h | partial | streaming-ASR benchmarking and verbatimization |
| **EuroSpeech** (NeurIPS 2025) | **22 national parliaments** | 61K h aligned, 19 languages >1K h | no | **does not include Spain** — see correction below |
| **ParlaMint** | 17–29 national parliaments, text only; includes **ParlaMint-ES** and the ES-CT, ES-GA and ES-PV regional corpora | ~500M words, 11K speakers | yes (rich metadata) | transcripts and speaker metadata; no audio alignment for any Spanish-state corpus |
| **ParlaSpeech 3.0** | HR, CZ, PL, RS | ~6K h | yes, with role/party/orientation | the model for what a Spanish equivalent would look like |
| **Basque Parliament** (`gttsehu/basque_parliament_1`) | Basque + Spanish, bilingual | **1,445 h** train + 17 h manually supervised | **yes**, plus a per-segment language tag (eu/es/bilingual) | closest thing to a Spanish-state parliamentary corpus with code-switching labels |
| **3CatParla** | Catalan broadcast | 710 h | — | training data behind the best open Catalan ASR |
| **ParlamentParla v2.0** (OpenSLR 59) | Parliament of Catalonia, Catalan | **611 h** (211 clean + 400 other) | **yes**, with gender; speaker-disjoint splits | speaker-labeled Catalan parliamentary audio, CC BY 4.0 |
| **Nos_ParlaSpeech-GL** (Proxecto Nós) | Parliament of Galicia, Galician | 1,600+ h auto-aligned, plus 53 h manual (TranscriSpeech-GL) | unconfirmed | Galician parliamentary audio; released under the Galician Parliament’s own terms of use, not a CC licence (search-sourced, unverified); TranscriSpeech-GL needs an access request |
| **Althingi** (LDC2021S01) | Icelandic parliament | 542 h | yes | the reference deployment corpus |
| **Finnish Parliament ASR** | Eduskunta | **3,130 h**, 449 speakers | yes, with demographics | largest single-parliament ASR corpus with speaker metadata |
| **FT Speech** | Danish parliament | 1,857 h, 434 speakers (search-sourced) | yes | another national precedent |
| **FalAR** ([arXiv 2605.27062](https://arxiv.org/abs/2605.27062)) | Portuguese parliament (Assembleia da República), ~20 years | **5,800 h**, of which **4,850 h with speaker identity** | **yes** — 1,180 speakers with age, gender, party and parliamentary role | the closest published analogue to the speaker-annotated Congreso corpus this challenge lacks; 2026 preprint, **access not confirmed** |
| **SloPal / SloPalSpeech** ([arXiv 2509.19270](https://arxiv.org/abs/2509.19270)) | Slovak parliament, 2001–2024 | 330k speaker-segmented transcripts, 66M words; **SloPalSpeech 2,806 h** aligned into ≤30 s segments | yes, speaker-segmented | a language-agnostic anchor-based alignment pipeline — an alternative to EuroSpeech’s two-stage method; **access not confirmed** |
| **RTVE** (Albayzin) | Spanish broadcast TV | several hundred h; SDIAC 2022: 74 speakers, 54 h | **yes, named, closed set** | the only Spanish corpus scored on *named* attribution |

**[Correction]** An earlier draft of this report listed EuroSpeech as directly on point
for this challenge. Reading the released toolkit shows its parliament set is
**Bosnia-Herzegovina, Bulgaria, Croatia, Denmark, Estonia, Finland, Germany, Greece,
Hungary, Iceland, Italy, Latvia, Lithuania, Malta, Montenegro, Norway, Portugal, Serbia,
Slovakia, Slovenia, Sweden, UK, Ukraine** — **Spain is absent**, and so are Catalan,
Basque and Galician.
EuroSpeech remains valuable, but as **methodology rather than data**: its alignment
pipeline is explicitly built for “multi-hour audio plus multiple, potentially
non-verbatim transcripts in mixed formats (PDF, DOCX, HTML, TXT, SRT)”, segmenting with
VAD and diarization, transcribing with `whisper-large-v3-turbo`, and aligning to human
transcripts by **CER-scored two-stage dynamic alignment**. That is exactly the tool
needed to build the missing Spanish corpus, and it is open source.

**[Primary]** For Spain, the raw materials are all public:

- **Congreso — Datos Abiertos** publishes XML/JSON/CSV, including an **Intervenciones**
  dataset organized by initiative and in chronological order.
- The **Archivo Audiovisual** is catalogued and **segmented per intervention**, so
  individual speeches are downloadable as MP4 — i.e. **audio pre-segmented by speaker,
  with the speaker named**, which is an enrollment set handed over ready-made.
- *Congreso en Directo* streams the plenary and up to five simultaneous committees;
  **Canal Parlamento** mirrors to YouTube.
- The **Senado** publishes its own *Diarios de Sesiones*.

**[Analysis]** The combination of per-intervention MP4s and published official
transcripts means a participant can assemble an aligned, speaker-attributed evaluation
set **and** a speaker enrollment set for Spain before the event, without any provided
data — subject to the audio-channel caveat in §11.3. That remains the single
highest-value piece of legal pre-work, and the EuroSpeech aligner plus MeetEval scoring
is a defensible off-the-shelf stack for building it.

#### 11.12 Where the five minutes go

**[Analysis]** Assuming the Luma target, the post-session budget must cover, for a
multi-hour session:

1. Audio acquisition and segmentation.
2. Language identification at sub-utterance granularity for code-switched speech.
3. ASR in five languages.
4. Diarization plus **named** speaker resolution.
5. Punctuation, casing, and the editorial normalization that turns oral speech into a
   written record.
6. Assembly, ordering, and output formatting.

**[Technical]** The compute numbers say plainly that steps 1–4 are not the constraint if
the work is pipelined:

| Component | Reported throughput | Six-hour session implies |
| --- | --- | --- |
| `whisper-large-v3-turbo`, single H100 | **597× real time** (120 s audio in 0.2 s); ~404× at batch 32 | **~40 s** of GPU time |
| `pyannote` community-1, self-hosted H100 | **31 s per hour of audio** on hour-long AMI files (37 s on five-minute DIHARD3 files) | ~3 min |
| `pyannote` precision-2, self-hosted H100 | **14 s per hour of audio** | ~1.4 min |
| `pyannote` 3.1 on a V100 | RTF ~2.5% (~1.5 min per hour) | ~9 min |
| Whisper-Streaming (LocalAgreement) | **3.3 s average latency**, measured on the ESIC European Parliament test set (A40) | streaming, not batch |
| Streaming Sortformer | **0.32 s** chunk latency | streaming, not batch |

**[Analysis]** Two conclusions.
First, the five-minute target is **not a throughput problem** — a batch pass over a
six-hour session is minutes of GPU time on current hardware, and even the older
`pyannote` 3.1 on a V100 fits.
It is an **architecture problem**: five minutes after a session ends is a bounded-lag
requirement, which is satisfied trivially by processing the session incrementally so
that only the tail remains, and satisfied fragilely by a post-hoc batch job whose
failure has no recovery window.
Second, the components that *do* have irreducible latency are the streaming ones — 3.3 s
for LocalAgreement Whisper, 0.32 s for streaming diarization — and both are far inside
the budget.

**[Analysis]** The real risks in the budget are not compute:

- **Retrospective correction.** Speaker attribution improves with context (§11.10), so a
  streaming system will want to revise earlier labels — AssemblyAI’s streaming
  diarization explicitly revises labels as context accrues.
  Revision has to be designed for, or the transcript published at T+5 disagrees with the
  one published at T+30.
- **Post-hoc identity resolution.** If the chair’s announcement resolves an identity
  three turns later, the pipeline must be able to backfill.
- **Step 5**, below.

#### 11.13 The editorial layer, and why it may dominate the score

**[Press]** An official parliamentary record is an edited artifact, not a verbatim
transcript. UK Hansard is explicitly an “edited verbatim report”: repetitions and
redundancies removed, obvious mistakes corrected, meaning preserved — and the written
form is systematically more conservative than the speech it records.
Spain’s *Diario de Sesiones* performs the same “adaptation of oral language to written
language” (§11.1).

**[Technical]** Corpus builders treat this as a known hazard: because parliamentary
reports are not verbatim, **a large proportion of words in aligned utterances are
misaligned** — speakers’ mistakes, fillers, restarts and repetitions having been edited
out. This is why EuroSpeech needed a two-stage aligner tolerant of non-verbatim
transcripts, and why the Althingi corpus pipeline strips chamber annotations and
labelled references before alignment.

**[Analysis]** The scoring consequence is direct and, if unhandled, decisive.
If the metric is WER (or cpWER) computed against published *Diario de Sesiones* text, a
perfect verbatim transcript is penalized for every disfluency the stenographers removed
and every construction they regularized.
**Error attributable to editorial convention rather than mishearing can exceed the ASR
error itself.** Three ways out, in order of preference: (a) score against a verbatim
reference; (b) apply the same normalization to both sides before scoring; (c) model the
editorial transform explicitly as a post-ASR text-to-text stage.
Option (c) is a real capability — it is what the Portuguese Parliament reports doing
with AI-assisted grammatical correction of plenary reports — but it is a second system,
and building it in twelve hours is not realistic.
**Confirm which reference text is scored, at 09:00.**

#### 11.14 Prior deployments: this is a solved-in-production problem, unevenly

**[Press]** The Inter-Parliamentary Union maintains a public catalogue of AI use cases
in parliaments; over 40 use cases have been contributed, and **eleven parliamentary
chambers** have implemented automatic transcription with verbatim-report generation.
The named deployments most worth studying:

| Chamber | System | What is public |
| --- | --- | --- |
| **European Parliament** | RWS + CEDAT85 + Bertin IT (live STT, awarded after an 18-month evaluation); Translated + FBK + PerVoice (speech translation) | live transcription and translation across **24 official languages**, on-screen, including as a hearing-accessibility provision; the translation system is described as adapting output in **under one second** |
| **Estonia (Riigikogu)** | **HANS**, TalTech speech recognition, built by Finestmedia, live since 2020 | the recording system sends **10-minute clips** to the STT service; drafts are produced and **speakers are recognized** — MPs, PM, ministers, president; trained on **1,500 h**; target ≥93–95% correctness, reported error rate **~5%**, with humans correcting the remainder |
| **Portugal (Assembleia da República)** | **STAAR** — Whisper-based ASR plus diarization (Iscte, IJST 2024) | reported **WER 1.7%–11.3% depending on context and speech style** (from a search summary; not verified against the paper); explicitly reduces time to produce the official journal |
| **Brazil (Câmara dos Deputados)** | **Ulysses** suite, module **U5** | automatic voice-to-text integrated with legislative databases |
| **Italy (Camera and Senato)** | contributed IPU use cases; 2026 VLM pipeline for historical records | OCR + VLM with speaker identification |
| **Spain — Senado** | **Etiqmedia** custom deployment | **real-time cataloguing, indexing and transcription while the session is still live**, generating a **draft for the session diary**, plus live and offline subtitling |
| **Spain — Congreso** | **Pangeanic** (transcription lot, from 1 Jan 2025) | AI transcription of co-official languages into the *Diario de Sesiones*; €2.85/min (co-official), €1.65/min (Spanish) |
| **Parlamento.ai** (private operator: Spain, Chile, Peru, European Parliament, US, Brazil) | paid ASR APIs; evaluating open models | published a reproducible 168-clip parliamentary ASR study with workload and cost data (§11.4); no ground-truth reference and no speaker metric |

**[Analysis]** Three readings of this table.

First, **the accuracy bar set by production systems is 5% error with humans in the
loop** (Estonia), and Portugal’s 1.7–11.3% spread quantifies exactly the thing the
Spanish price differential also encodes: accuracy is a function of speech style, not of
model choice.

Second, **Spain already has both chambers covered** — the Senado with real-time
transcription and draft generation, the Congreso with a named AI transcription
contractor. The “weeks later” framing in the challenge is therefore about *some*
jurisdictions, and about the multilingual tail, not about a vacuum.
A team should not present a working Spanish transcription pipeline as though nothing
existed.

Third, and most usefully: **none of the public descriptions of these deployments claims
robust automatic speaker identification at plenary scale.** Estonia’s system “is set to
recognise almost all speakers” in a 101-member chamber; the European Parliament’s public
material is about transcription and translation, not attribution; Portugal’s paper
reports WER, not attribution accuracy.
That is the gap the challenge is pointed at, and it matches the Albayzin evidence in
§11.8 that named attribution is the unsolved half.

#### 11.15 The honest difficulty ranking

**[Analysis]** For a twelve-hour build, ordered by expected contribution to the score:

1. **Named speaker attribution** — the metric names it, published numbers are bad (DER
   37.2% with identity, cpWER 30–35), and the leverage is non-acoustic evidence fusion
   rather than model selection.
2. **The editorial/reference-text question** — potentially larger than every other error
   source combined, resolvable with one question, and cheap to handle once known.
3. **Audio channel provenance** — if the pipeline consumes an interpretation-mixed feed,
   §11.3 caps attribution accuracy on exactly the interventions the challenge is
   nominally about.
4. **Calibrated abstention** — 20% of the rubric, technically necessary anyway at
   350-speaker watchlist size, and invisible if retrofitted late.
5. **Latency architecture** — incremental processing with a short finalization pass;
   comfortably achievable, but only if designed that way from the start.
6. **Code-switching and low-resource ASR** — real, addressed by
   `BSC-LT/whisper-large-v3-LoS-punctuated` plus the Interspeech 2025 CS recipe.
7. **Raw WER** — already 2–5% with off-the-shelf models.
   Almost certainly not where the score is won.

#### 11.16 A reference architecture, with its failure modes

**[Analysis]** What the above implies, concretely:

- **Ingest** the cleanest available channel; detect and flag interpretation-mixed
  segments rather than transcribing them as if they were floor speech.
- **Segment** with VAD before any decoder, both for hallucination suppression and to
  bound chunk size.
- **Transcribe** incrementally (`whisper-large-v3-LoS-punctuated`, LocalAgreement or
  SimulStreaming policy), with per-segment language identification and per-word
  timestamps via forced alignment.
- **Attribute** by fusing, in this priority order: conference-system metadata (if
  available) → chair announcement parsed from the transcript → agenda-constrained
  candidate set → acoustic identification against archive-derived enrollment profiles
  (TS-VAD style, restricted to the candidate set) → on-screen overlay / active-speaker
  detection. Each attribution carries its evidence and a calibrated score.
- **Abstain** below threshold, emitting “unidentified speaker” with the candidate list
  rather than a guess.
- **Normalize** to record conventions only if the scored reference is the edited record,
  and keep both the verbatim and edited layers so the choice is reversible.
- **Score** with MeetEval under one fixed protocol, reporting DER, cpWER and
  per-intervention attribution accuracy side by side so the organizers’ definition can
  be matched to whichever they use.

**[Analysis]** Failure modes to test for explicitly, because each has produced a real
published failure: hallucinated text over applause and gavel; repetition loops on
long-form decode; speaker-count collapse past four speakers on streaming diarization;
false-alarm inflation as the watchlist grows; identity drift when a deputy switches
language mid-intervention; and timestamp divergence between the interpreted and floor
channels.

### 12. Strategy for a team

**[Analysis]** Sections 1 to 11 describe the competition and the problem.
This section is the layer above them: what a team should actually do on 3 October.
It was produced by four independent analyses working from this dossier and its two
companion reports, the
[research agenda](research-2026-09-02-parliamentary-speaker-attribution-research-agenda.md)
(cited as *agenda §n*) and the
[tooling inventory](research-2026-09-02-parliamentary-transcription-tooling-inventory.md)
(*inventory §n*). All four are published in full in
[Track 02 Team Strategy — Four Analyses in Full](research-2026-09-02-track02-team-strategy-analyses.md),
which carries the hour-by-hour detail this section compresses.
Where a claim rests on something measured in the agenda’s spike results (§10 there), it
is marked measured; the rest is judgment, and the two are kept apart because most of the
numbers below came from synthetic or text-only material and are upper bounds.

The four analyses were run separately and converged, which is the most useful thing
about them.
All four independently put the chair’s announcement, not the waveform, at the
centre of the design, and all four made calibrated abstention with visible provenance
the second pillar. That convergence is the recommendation in §12.8.

#### 12.1 The rubric is the specification, and most of it is bankable in advance

The metric is 45% of the score (§5). Restraint is 20%, auditability 15%, shippability
10%, ambition and demo 10%. The 55% that is not the metric depends barely at all on
which chambers, which languages or which data arrive at 09:00, which means it can be
built before the event.
That is the single most important strategic fact in this document.

Whatever bet a team takes, this floor applies:

- **Ask the questions that change the build in the first thirty minutes** (§12.6), and
  run the inherited pipeline unchanged on one session, scored, before 11:30. That
  baseline is the thin slice; everything after is a paired comparison against it (agenda
  §4.7).
- **Score with fixed labels.** Permutation-invariant cpWER and DER score 0.0000 on a
  transcript where every name is wrong (measured, agenda §10.2). A team optimising them
  is optimising diarization consistency and calling it attribution.
- **Never emit a nearest neighbour below threshold.** Emit “unidentified speaker” with
  the candidate list. Open-set EER roughly doubles from 100 to 700 enrolled speakers
  (§11.7), so a cosine match against a 350-member roster names the nearest neighbour,
  not the speaker.
- **Attach evidence to every name**, and gate the decoder with VAD plus the
  compression-ratio filter: ungated whisper-small emitted 401 hallucinated words per
  hour of non-speech, and VAD alone passes 92.5% of babble (measured, agenda §10.6).
- **Never attribute acoustically on an interpretation-mixed segment.** Flag it
  (measured, agenda §10.7).
- **Do not present Spanish transcription as the contribution.** Both chambers already
  run it in production (§11.14), and turbo differs from the paid consensus by 3.25%,
  less than the paid APIs differ from each other (agenda §3.3).

#### 12.2 Five strategies

Five coherent bets, each optimising a different thing.
The scores are the analysis’s honest estimates against the rubric lines, not
predictions.

| Strategy | The bet | Metric /45 | Restraint /20 | Audit /15 | Ship /10 | Demo /10 | Robustness |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **1 Read the Chair** | The name is in the text; audio breaks ties | 32 | 13 | 12 | 6 | 7 | High; exposed to chamber B’s chair habits |
| **2 Ready Before They Sit Down** | Win the speed clause with bounded-lag streaming | 28 | 10 | 9 | 8 | 9 | Medium; needs a GPU and latency scored as a gradient |
| **3 Defend Every Name** | Calibrated abstention as the product | 25 | 18 | 14 | 8 | 5 | High; exposed only to how abstention is scored |
| **4 The Chamber Knows Its Own** | Same-room acoustic identification from the archive | 20–38 | 11 | 9 | 5 | 8 | Low; needs enrollment audio, a GPU and a clean channel |
| **5 Where the Name Is Lost** | Deliver the instrument, not the system | 18 | 12 | 15 | 4 | 8 | High on delivery, low on score |

**1. Read the Chair.** Keep the inherited pipeline for words; add the chair-announcement
parser, a roster matcher, the agenda candidate set and an LLM fallback.
Every name carries its evidence.
The bet rests on a measured floor: on two independent records of the Congreso seven
years apart, the parser names the right member on 93–95% of rostrum turns and 92–94% of
all regular turns with no audio at all (agenda §10.1).

**2. Ready Before They Sit Down.** Bounded-lag streaming: publish at T+30 s, finalize at
T+2 min, log finalization lag, name revision rate and first-versus-final accuracy.
Five minutes is not a throughput problem — a six-hour session is roughly 40 seconds of
GPU time for ASR (§11.12) — it is a behaviour, and nobody has published a revision
logger.

**3. Defend Every Name.** Concede coverage deliberately and spend the day on the
coverage–accuracy curve, an evidence ledger and the shipping story.
The highest floor and the lowest variance of the five, because it targets the 35% of the
rubric that most teams treat as documentation.

**4. The Chamber Knows Its Own.** Enrollment from the named archive, ReDimNet2-B6
embeddings, adaptive s-norm, a calibrator, agenda-only candidate sets, and an overlay
gate at −6 dB. The highest ceiling and the lowest robustness: it needs enrollment audio
for both chambers, a GPU, and a channel that is not interpreter-mixed.

**5. Where the Name Is Lost.** Do not chase the 45%. Produce a fixed-label, stratified
error decomposition of the inherited pipeline and fix the largest stratum.
This is aimed at the hiring conversation the organizers say the event is for (§6), and
it is the right play only if the metric is out of reach for everyone.

#### 12.3 Five architectures: where the name comes from

The architectures differ in their theory of attribution, not in model choice.

- **A1 Chair-Spine (text-first, conservative).** The name comes from the chair’s
  hand-off parsed out of the ASR transcript, plus the continuation rule, regex first
  with an LLM fallback (measured: 0.94 projected, agenda §10.8). Audio supplies words
  and change points only.
- **A2 Cascade (acoustic-first modular).** pyannote to embeddings to archive enrollment
  to AS-norm and calibration to an agenda-constrained open-set match with explicit
  unknown mass. Sits on the known-bad 100-to-700-speaker operating point (§11.7), and the
  in-domain trial that would validate it has not run.
- **A3 Skeleton (record-structure-first).** The published order of business becomes a
  slot sequence and ASR is aligned into it; attribution is a slot id.
  Candidate windows are measured small (median 2 speakers per numbered item, agenda
  §10.10), but as a primary signal this is proposed, not measured.
- **A4 PERCOL-2026 (multimodal).** Chyron OCR, active-speaker detection and face
  matching fused with the chair parser.
  The precedent is REPERE at EGER 24.4% (agenda §3.6); chyron presence on the live feed
  is unverified.
- **A5 Enrolled-Joint (joint SA-ASR, ambitious).** DiCoW or SE-DiCoW with pyannote masks
  and a roster-naming step.
  The highest ceiling, no Iberian evaluation, and no calibrated identity score.
  The right research direction and the wrong eleven-hour build.

| Situation at 09:00 | Build | Reason |
| --- | --- | --- |
| Mixed public stream; es plus co-official; no GPU | A1, Spanish-only CTC for words, co-official turns flagged unknown | Overlay flips at 0 dB (agenda §10.7); only Spanish CTC fits four cores (agenda §10.4) |
| Public stream; one 8 GB GPU | A1 spine, A2 as a tie-break on rostrum turns gated below −6 dB | turbo runs 35.6 audio-hours per hour; A2 needs a pre-built calibrator |
| Isolated floor feed available | A1 plus A2 in full | Enrollment pays once the overlay is gone |
| Conference-system events exported | A join, then A1 for the residual | A database join, not machine learning (§11.3) |
| Video with chyrons in the pipeline | A4’s OCR as a fuser input to A1 | An hour-1 yield count decides |
| Second chamber non-Spanish | A3 if a machine-readable agenda exists, else A1 with the LLM-only parser | Regex coverage falls to 0.438 without per-language rules (measured on Galician, agenda §10.1) |
| Scored reference is the edited record | Add Spanish ITN, keep both text layers | §11.13; agenda §3.11 |

#### 12.4 What the field will miss

Assume most teams build Whisper plus a diarizer plus cosine similarity.
These are the traps that costs them points without their noticing, all of them measured:

- **DER hides exactly the turns the metric counts.** A two-second interjection inside a
  four-minute speech is under 1% of the time.
  The literal maximal-overlap turn-matching rule scored 0.49 on interjections for a
  *perfect* hypothesis; intersection-over-union matching scores 1.00 (agenda §10.2).
- **VAD is not a hallucination guard.** It removes applause, gavel and silence and then
  passes babble, where decoded units hallucinate at 3,216 words per hour.
  Paired on one stream, gating raised the rate from 289 to 519 words per hour (agenda
  §10.6).
- **The leaderboard fine-tunes are fragile, and two are contaminated.** All three
  co-official fine-tunes lose to generic turbo on the operator’s own sample, and the
  Open ASR Leaderboard correlates at ρ −0.14 with parliamentary agreement (agenda
  §10.5). `BSC-LT/whisper-large-v3-LoS-punctuated` declares ParlamentParla and
  Nos_ParlaSpeech-GL in its training data, and the Basque specialists declare Basque
  Parliament (inventory §11.2). If a held-out chamber is one of those, a contaminated
  model looks good for the wrong reason.
- **Whisper’s language token is not a switch detector**, and at 0 dB it reports the
  floor language on only half of full turns (agenda §10.7).
- **Whisper’s basic normalizer inflates Catalan WER by up to 7 points** by splitting
  `l'home` and `col·legi` (agenda §10.9). One versioned normalizer, applied to both
  sides.
- **The record is not verbatim.** Scored against the published record, a perfect
  transcript loses about 9 points to editing policy (agenda §3.11).

#### 12.5 Three demo moments

The 10% for the live demo and much of the 20% for restraint are won or lost in the last
few minutes.

**“The chair told us.”** A name appears on a rostrum turn with a citation chip; clicking
it plays the four seconds where the chair says *“Tiene la palabra el señor X”* and shows
the agenda slot and the acoustic score as secondary evidence.
*“We did not recognise his voice first.
We heard the chair say his name, and here is the sentence.
The voice agreed two seconds later.”*

**“It declined, then it earned the name.”** On a co-official intervention or a
sub-three-second interjection, the card shows the channel flag, the acoustic margin
below threshold, and *“Unidentified speaker.
Candidates from the order of business: X, Y, Z.”* Three seconds later the chair’s
hand-off resolves it, the card updates with the citation, and the revision counter
ticks. The presenter says nothing about restraint; the judge watches it happen.
Then a threshold slider lets a judge pick the operating point on held-out sessions.
This is restraint made visible rather than claimed, which is the difference between
scoring the 20% and asserting it.

**“Rotate every name, score zero.”** Two scoreboards on the same output with every
speaker rotated to another member.
Left: MeetEval cpWER 0.00, DER 0.00. Right: fixed-label cpWER 1.49, per-turn accuracy
0.00, identification error rate 0.999 (measured, agenda §10.2). *“The default tool
cannot see a wrong name.
Ours can.”*

#### 12.6 Planning under a brief nobody has seen

Three unknowns dominate, ranked by hours of rework if guessed wrong.

1. **What “you inherit a live pipeline” means** — a running modifiable service, a black
   box, code to stand up, or only its outputs.
   It decides whether the team builds ASR and diarization at all.
   Reversa publishes no code (§9.1), so nothing narrows it before 09:00. Three to five
   hours.
2. **The scored reference text** — verbatim or the edited record.
   If the record is scored, editorial error can exceed ASR error and a normalizer joins
   the critical path. Two to four hours.
3. **How latency and accuracy compose** — a five-minute cap or a weighted term.
   The five-minute figure appears only on Luma (§10). Cap versus term decides whether
   the streaming runner or the attribution fuser gets the afternoon.
   Two to three hours.

The remaining unknowns — which chambers, which languages, the audio channel, GPU
availability, the input format — are afternoon adjustments once the invariant core
exists.

**Abort conditions**, each with a fallback that still runs on stage.
At **hour 4**, if no session has gone end to end to a scored number, stop component work
and run the text-only fuser on the inherited pipeline’s raw output.
At **hour 7**, if acoustic identification adds nothing over the text-only floor when
paired by session, demote acoustics to evidence-only and spend the time on the coverage
curve and the abstention threshold.
At **hour 9**, if the input format is unhandled or a session exceeds the cap, freeze
features and build only the adapter; failing that, replay a pre-processed session
labelled as a replay, with the coverage curve and the abstentions visible.
Standing rule: a change that raises accuracy at 100% coverage and lowers it at 90% is
not an improvement under this rubric (agenda §4.4).

#### 12.7 What to build before the day

In descending value per hour spent:

1. **The attribution fuser with abstention and per-turn provenance**, consuming generic
   timestamped anonymous turns and emitting a name with its evidence or an explicit
   unknown with candidates.
   It is independent of pipeline shape, chamber, GPU and metric definition, and it
   serves the 45%, 20% and 15% lines with one mechanism.
2. **The scoring harness** with fixed-label metrics, intersection-over-union turn
   matching and the coverage curve (agenda §10.2). It is what lets the team decide
   anything after 09:30.
3. **The chair parser and roster matcher**, packaged, with the per-language rules and
   the hybrid LLM prompt (agenda §10.1, §10.8).
4. **Run the missing spike now**: 200 announcement sentences through TTS, ASR and the
   parser. Every A1 number is measured on clean record text and is an upper bound until
   this runs; if survival is below 60%, pre-build the phonetic matcher.
5. **The hallucination guard**: VAD plus a compression ratio above 2.4 plus a
   babble-aware threshold, retuned on turbo rather than the small model.
6. **Mirrored weights and a container**, since credits arrive on the day and several
   model hosts are slow or gated.
7. **Enrollment assets** if the acoustic path is in scope: cached centroids, trials,
   calibrator, threshold.
   Without these, A2 cannot run on the day.

#### 12.8 Recommendation

**Build A1, the Chair-Spine, as the system, with Strategy 3’s abstention and evidence
ledger as its floor.** The two combine naturally: the fuser produces the evidence the
ledger displays, and the abstention threshold is set on the fused posterior.

The reasons are measured rather than aesthetic.
The text-side floor is 92–94% of regular turns with no audio at all, which is the number
any acoustic identifier must beat, and beating chance is not the same as beating that.
It needs no GPU, no credits and no enrollment audio, so it survives every branch of
§12.6. And this dossier’s own reference architecture places non-acoustic evidence first,
after microphone metadata (§11.16).

Add the acoustic path as a gated tie-break on rostrum turns only, and only once 09:00
confirms named enrollment audio and a channel that is not interpreter-mixed.
Fold A3’s agenda structure into A1 as its candidate-set prior.
Leave A5 to the research programme.

Two honest caveats.
The headline number for this recommendation is measured on the edited
record, not on ASR output, and the spike that would test whether the chair’s hand-off
survives recognition has not been run — which is why it is item 4 on the pre-build list.
And if the second chamber is not Spanish, the parser’s structure transfers but its rules
do not: precision held at 0.95 on a Galician corpus while coverage fell to 0.44 (agenda
§10.1).

## Key Insights

1. **The rubric, not the metric, is the specification.** 55% of the score is restraint,
   auditability, shippability, and demo quality.
   All of it can be designed before the data arrives.
   A team that shows up with a provenance model, a calibrated abstention mechanism, and
   a rehearsed demo has banked the majority of the available points before 09:30.
2. **“Knowing when not to answer is part of the score” is the thesis of the whole
   event.** It appears as the rubric’s headline, as the explicit annotation on the Talky
   metric, and as the 20% restraint weight.
   All three companies sell into regulated, audited domains—accounting, legal
   compliance, agricultural finance—where a confident wrong answer is worse than a
   flagged uncertainty.
   Calibration is the shared subtext of all three tracks.
3. **Track 02’s difficulty is attribution, not transcription, and the gap is roughly an
   order of magnitude.** Headline WER is 2–3% on parliamentary-style speech and 2–5% for
   Spanish with off-the-shelf models, while speaker-attributed error sits an order of
   magnitude higher: **cpWER 30–35** on published vendor and challenge benchmarks, and —
   the closest in-language anchor there is — **DER 37.2% with identity assignment** for
   a competition system on Spanish broadcast TV with a *closed* identity set and no
   real-time constraint (Albayzin IberSPEECH-RTVE 2022, §11.8). The metric names speaker
   accuracy explicitly.
   The leverage is in fusing non-acoustic evidence—conference-system microphone
   metadata, the chair’s spoken announcements, the published order of business, prior
   attributed archives, the camera feed and its name overlays—rather than in pushing an
   acoustic model (§11.10).
4. **The strongest legal pre-work is building your own evaluation set — and there is no
   Spanish parliamentary corpus to skip that work with.** VoxPopuli carries only **166
   transcribed hours of Spanish across 305 speakers**, all European Parliament;
   **EuroSpeech does not include Spain** at all (its 23 parliaments are listed in
   §11.11, and Spain is not among them); ParlaSpeech covers four Slavic languages.
   What does exist is the raw material: the Congress’s **Archivo Audiovisual is
   segmented per intervention and named**, which is a speaker-enrollment set handed over
   ready-made, and EuroSpeech’s open aligner is purpose-built for aligning multi-hour
   audio to non-verbatim transcripts.
   A participant can arrive with a working pipeline, measured against real parliamentary
   audio, and spend the twelve hours adapting rather than building.
5. **The two official sources disagree about Track 03 and under-specify Track 02.** The
   site and Luma give materially different descriptions of the Tunen challenge, and Luma
   alone supplies the “five minutes, five languages” targets.
   Clarify before choosing a track.
6. **The editorial-normalization gap is the hidden risk in Track 02.** An official
   parliamentary record is an edited document — UK Hansard says so explicitly, and
   Spain’s *Diario de Sesiones* performs the same adaptation of oral to written
   language. If scoring is against published records, the gap between “heard correctly”
   and “matches the official text” may exceed the ASR error itself, and corpus builders
   already treat non-verbatim references as a known alignment hazard (§11.13). One
   question at 09:00 resolves it: is the scored reference verbatim or edited?
7. **The founders explain the rubric.** Every one of the three teams pairs a domain
   insider with a builder — three law degrees plus an Amazon engineer at Reversa, a
   Google engineer plus someone raised on a German farm at Tunen, with a working farmer
   as CCO. That composition is a bet that the hard part of AI in a regulated profession
   is trustworthiness rather than capability, and the scoring function is that bet
   written down: 20% restraint, 15% auditability.
8. **These companies are one to three years old, and so is the frontier.** Talky Labs
   and Reversa were both founded in 2025; Tunen incorporated in August 2023. “The part
   nobody has cracked” is a problem each team has been living with for a short time, not
   a decade — which makes it more tractable in twelve hours than the framing suggests.
9. **Talky published a full starter kit from its previous hackathon, and Reversa
   published nothing.** `Talky-SP` carries the March 2026 “AI Evals Hackathon” frontend
   and AWS CDK backend, including committed internal documentation of Talky’s DynamoDB
   schema and its AI CFO agent architecture (§9.3). Reversa’s GitHub organization has
   zero public repositories.
   The asymmetry in available pre-work between Track 01 and Track 02 is large, and runs
   opposite to the direction the public data availability runs (§10 favours Track 02 for
   evaluation data, §9 favours Track 01 for system knowledge).
10. **The Track 03 owner’s master’s thesis is about calibrated uncertainty, and it is on
    GitHub.** Alexandre Shinebourne’s Cambridge/NIAB phenotyping code implements an
    MC-dropout U-Net whose uncertainty estimates drive an active-learning loop (§9.5).
    When the rubric awards 20% for “safe autonomy and restraint,” that is not
    boilerplate — it is the challenge owner’s own research question.
11. **This is a hiring funnel run by three founders from one ecosystem, and it says
    so.** Google for Startups Campus Madrid produced the relationships; its successor
    venue hosts the event; Reversa’s CRO comes from a founding partner of that venue.
    The stated reward includes “a conversation about doing it for real.”
    Optimizing purely for the leaderboard misreads the room.
12. **The public audio feed is the wrong audio for the multilingual case.** When a
    deputy speaks a co-official language, the Congress’s outbound signal carries the
    original voice **with the interpreter’s voice mixed over it**. That makes every such
    intervention 100% overlapped speech containing two speakers and two languages in one
    channel — which breaks diarization, speaker identification, language identification
    and word timestamps simultaneously, on exactly the interventions the challenge is
    nominally about (§11.3). Which channel the inherited pipeline consumes is the first
    question to ask.
13. **Both Spanish chambers already have production AI transcription, and eleven
    parliamentary chambers worldwide do.** The Senado runs an Etiqmedia system that
    catalogues and transcribes **while the session is still live** and produces a draft
    for the session diary; the Congreso awarded its transcription lot to **Pangeanic**
    from 1 January 2025 at **€2.85 per minute** for co-official languages and **€1.65**
    for Spanish. Estonia’s HANS reports **~5% error** on a 101-member chamber; Portugal’s
    STAAR reports **WER 1.7–11.3%** (§11.14). The framing “the record lands weeks later”
    is about the tail and about other jurisdictions, not about a vacuum — and a team
    should price its pitch against €1.65–2.85 per minute, not against zero.
14. **What none of those deployments claims is robust named attribution at plenary
    scale.** Estonia’s system covers a 101-seat chamber; the European Parliament’s
    public material is transcription and translation; Portugal reports WER, not
    attribution accuracy.
    Combined with the Albayzin evidence, this locates the actual open problem precisely
    — and it is the one the Reversa metric names.
15. **Open-set identification degrades measurably at exactly the Congress’s scale.**
    Published results show EER rising from **1.43% at 100 enrolled speakers to 3.04% at
    700** as out-of-set scores shift upward; the Congreso has 350 deputies plus
    ministers and officers.
    Agenda-constrained candidate sets are the cheap fix, and calibrated abstention is
    the correct behaviour at low margin — which is also 20% of the rubric (§11.7).

## Comparison Matrix: choosing a track

**[Analysis]** Assessed against what is knowable today.

| Criterion | Track 01 Talky (Accounting) | Track 02 Reversa (Democracy) | Track 03 Tunen (Agriculture) |
| --- | --- | --- | --- |
| Problem clarity | High — precision-constrained coverage | Medium — two objectives, exact weighting unknown | Low — two conflicting official framings |
| Metric legibility | Very high — one number with a stated floor | Medium — composite of latency and attribution | Medium — distance to held-out ground truth |
| Public data for pre-work | Low — proprietary ledgers, nothing comparable public | **High, but DIY** — open per-intervention session video and official transcripts, plus VoxPopuli (166 h Spanish) and Europarl-ASR; **no Spanish parliamentary corpus exists off the shelf** (EuroSpeech excludes Spain), so the aligned set has to be built | High — Sentinel/Landsat, weather, soil, and topography are public but fragmented |
| Owner’s public code | **High** — full hackathon scaffold, DynamoDB schema and agent architecture on GitHub (§9.3) | **None** — Reversa’s GitHub org has zero public repos | Medium — no product code, but the owner’s uncertainty-quantification thesis is public (§9.5) |
| Off-the-shelf model availability | Medium — document extraction is mature, ledger reasoning is not | **High** — strong open multilingual ASR and diarization | Medium — remote sensing needs assembly |
| Dominant difficulty | Calibrated abstention at 99% precision | Named speaker attribution (cpWER 30–35, DER-with-identity 37.2%); editorial normalization; interpretation-mixed audio | Source reconciliation; field-edge mixed pixels |
| 12-hour feasibility | Medium — broad surface area | Medium-high if pre-built | Low-medium — heavy data engineering |
| Fit to “restraint” weighting | Strongest — abstention is the metric | Good — confidence per attribution | Good — Luma framing asks for confidence scores |

## Recommendations

**[Analysis]** For a team intending to enter:

1. **Register immediately.** Seats are capped at 40, entry requires host approval, and
   registration closes 25 September 2026 — 23 days from this report.
   Lead the application with shipped, linkable artifacts; the organizers state they
   select for evidence of finishing things.
   Register as a complete team of three if possible.
2. **Ask the owners to resolve the discrepancies before choosing a track** —
   specifically whether the Tunen challenge is yield mapping or bank-facing valuation,
   whether the “five minutes / five languages” targets are current, and how the Reversa
   metric weights latency against speaker accuracy.
   For Track 02 there are four questions that change the build, in priority order
   (§11.15): **(a)** which audio channel the inherited pipeline consumes, and whether
   the floor feed is separable from the interpretation feed; **(b)** whether the scored
   reference text is verbatim or the edited official record; **(c)** whether “how often
   the right speaker is named” is per intervention, per word (cpWER/tcpWER), or per unit
   time (DER-with-identity); **(d)** whether conference-system microphone metadata is
   part of the inherited pipeline.
3. **If taking Track 02, build the evaluation harness now.** Download Congreso and
   Senado session video with the corresponding official *Diario de Sesiones* text, align
   them with EuroSpeech’s open two-stage CER aligner (built for exactly this: multi-hour
   audio against non-verbatim transcripts), and measure a baseline.
   Score with **MeetEval** under one fixed protocol, reporting DER, cpWER and
   per-intervention attribution accuracy side by side so whichever definition the
   organizers use can be matched on the day.
   Build the speaker enrollment set from the Archivo Audiovisual’s per-intervention
   MP4s, which are already named.
   Start from `BSC-LT/whisper-large-v3-LoS-punctuated` for four-language coverage with
   punctuation in one model, and hold `projecte-aina/whisper-large-v3-ca-3catparla` and
   `HiTZ/whisper-large-v3-eu` as per-language fallbacks.
4. **Design the attribution layer as evidence fusion, not as diarization.** Priority
   order (§11.16): conference-system microphone metadata → the chair’s spoken
   introduction parsed from the transcript → the published order of business as a
   candidate-set constraint → acoustic identification against archive-derived enrollment
   profiles, restricted to that candidate set → on-screen name overlay and
   active-speaker detection.
   Prefer a target-speaker (TS-VAD-style) formulation over cluster-then-name: it handles
   overlap natively and does not propagate a clustering error into the identity stage.
   Emit a citation for every attribution — this serves the metric and the 15%
   auditability weight simultaneously.
5. **Build abstention in from the first commit.** Every output should carry a confidence
   and a threshold above which it is emitted and below which it is flagged.
   This is 20% of the score, and at a 350-member watchlist it is also technically
   required: the false-alarm curve for open-set identification is measurably worse at
   that scale, and the correct low-margin output is “unidentified speaker, candidates
   {…}” rather than the nearest neighbour.
   It is invisible if retrofitted at 20:00.
6. **Have a thin end-to-end slice running before the 17:00 checkpoint**, and rehearse
   the 21:00 demo. Ambition and live demo are 10%, and a system that fails live scores
   nothing on the other 90%.
7. **Read Talky’s March 2026 hackathon repositories before choosing a track.** They are
   public, cloned in `attic/`, and show the brief style, the scaffold, the stack, and
   the data model you would be working against (§9.3).
8. **Do not depend on the provider credits.** They arrive during the event.
   Keep a local or free-tier fallback for every external service.
9. **Do not spend the day on WER, and do not pitch as if the incumbents did not exist.**
   Off-the-shelf Spanish ASR is already at 2–5%; the compute for a whole session is
   minutes on one modern GPU (§11.12). Both Spanish chambers already run production
   transcription (§11.14). The defensible claim is named attribution with auditable
   evidence and calibrated abstention — say that, rather than presenting transcription
   itself as the contribution.

## Next Steps

- [ ] Confirm with the user whether the intent is to enter, and which track.
- [ ] Register on Luma before 25 September 2026 if entering.
- [ ] Send the discrepancy questions (Track 03 framing, Track 02 targets and metric
  weighting) to the organizers.
- [ ] If Track 02: assemble the aligned audio-plus-transcript evaluation set from
  Congreso and Senado open data, using the EuroSpeech aligner and MeetEval scoring, and
  build the enrollment set from the per-intervention MP4s.
- [ ] If Track 02: verify empirically whether the public Congreso feed carries the
  interpreter’s voice over the floor audio on co-official-language interventions, and
  measure the attribution accuracy cost if it does (§11.3).
- [ ] If Track 02: measure a baseline on Spanish-only plenary audio with
  `BSC-LT/whisper-large-v3-LoS-punctuated` plus `pyannote` community-1 and DiariZen,
  under one fixed DER protocol, before adding any non-acoustic evidence — the delta from
  evidence fusion is the finding worth demoing.
- [ ] Update this report when the briefs are released the week of 26 September 2026.
- [ ] Before building for Track 02, read the companion research agenda; its §7 ranks the
  experiments and its §4 fixes the scoring protocol.

## Methodology

GitHub was searched by organization handle, by user search, and by code search against
each company’s domain; every candidate account was verified through commit authorship,
profile bio, or configured production endpoints rather than name similarity, and the
repositories that matched were cloned into `attic/` and read.
A credential scan across the clones found nothing exposed.

Sources were gathered on 2026-09-02 by fetching the English and Spanish event pages
directly and extracting their full text, fetching the three company sites and the Luma
listing, and running parallel web searches in both English and Spanish across the
companies, the founders, the venue, the Spanish parliamentary record, and the relevant
speech-technology literature.

**Second pass (§11, Track 02 technical landscape).** The expanded §11 was researched in
a session whose network egress policy allowed only GitHub hosts, so the evidence there
comes from two kinds of source with different reliability, and they are distinguishable:

- **Read directly, verbatim.** Repository files fetched from `raw.githubusercontent.com`
  or cloned: the `pyannote.audio` benchmark tables (DER and self-hosted speed), the
  DiariZen benchmark table and its explicit no-collar protocol, the VoxPopuli
  per-language hours and speaker counts, the MeetEval metric inventory, the
  `whisper_streaming` README and its 3.3 s latency claim, and the full EuroSpeech
  repository — whose per-parliament directories are what establish that **Spain is not
  in the corpus**, correcting the first pass of this report.
- **Read through search-result summaries.** Everything else: the
  Albayzin/IberSPEECH-RTVE results, the open-set identification degradation figures, the
  Streaming Sortformer latency and speaker-count numbers, the deployment descriptions
  (Estonia, Portugal, Brazil, the Spanish chambers), the procurement figures, and the
  Iberian-language WER tables.
  These are attributed to their named primary sources in the References, but the primary
  PDFs and vendor pages could not be opened from this session and the figures have not
  been checked against them line by line.
  Anything load-bearing for a build decision should be re-verified against the source
  before it is relied on.
  All findings are reported here in English; Spanish sources were translated, and the
  Spanish event copy was compared line by line against the English to detect divergence.

A third pass ran a fact-check over eighteen load-bearing figures in this section (10
confirmed, 5 adjusted, 3 unverified); the adjustments are applied in place and the
verdict table is in the companion report’s §2.1.

**What could not be verified:**

- Prize amounts, technology-credit providers, and the identity of the two parliamentary
  chambers in the Reversa pipeline are not public.
- Reversa’s funding and revenue figures conflict across secondary sources and have no
  primary confirmation: the pre-seed is reported as both €300K and €400K, and ARR as
  both “€60k” and “six-figure” for the same five-month period.
- Íñigo Martín Llorente’s title is given as CPO by the company and COO by a press
  profile.
- **Jorge Beamonte’s background is the largest gap in this report.** No prior employers,
  education, or founding history for Talky’s CEO surfaced in any English or Spanish
  source searched.
- Whether Jacob von der Decken is a Tunen co-founder or a later hire is not settled;
  Rockstart named only Shinebourne and von Davier in March 2024, and his title is given
  as CCO.
- Tunen’s funding amount and customers are unpublished; only the Rockstart AgriFood Fund
  II participation is confirmed.
- **LinkedIn returns HTTP 999 to automated fetching**, so all profile details are drawn
  from search indexes and secondary sources rather than read directly.
- Four attempted sources returned errors: the El Referente profile of Reversa (HTTP
  403), the HubLegalTech member page (HTTP 404), the Endole registry profile for Tunen
  Limited (HTTP 403), and a Tunen about/team page (HTTP 404 — the site has no team
  page).
- The Spanish Talky site is client-rendered and returned only its title to direct
  fetching; the English version at usetalky.com/en/ did return content.
- **Startup databases conflate at least three unrelated companies named “Talky”**
  (§8.2), so third-party funding and headcount figures for “Talky” are unreliable.
- No search result surfaced independent third-party coverage of the Madrid Open itself
  beyond the organizers’ own channels, consistent with a small, recently announced,
  private event.
- **Which two parliamentary chambers the inherited Reversa pipeline covers is still not
  public**, and no Reversa product page describes a transcription product at all — its
  public positioning is regulatory monitoring and legislative twins.
- **Whether the Congreso’s public stream can be separated into a floor channel and an
  interpretation channel is not established.** The reporting establishes that the
  outbound signal carries the interpreter over the original; it does not establish
  whether an isolated feed exists for external consumers (§11.3).
- **Reported DER, WER and cpWER figures in §11 use different protocols** — collar,
  overlap scoring, and dataset splits vary, and vendor-published benchmarks are
  self-reported. They are comparable in order of magnitude, not decimal place.
- **Whether Pangeanic’s Congreso system performs speaker attribution at all** is not
  stated in the public contract reporting; only transcription of co-official languages
  is described.

## References

**Event and organizers (primary)**

- [Madrid Open (Vol.1) — event site, English](https://reversa.ai/madrid-open)
- [Madrid Open (Vol.1) — event site, Spanish](https://reversa.ai/es/madrid-open)
- [Madrid Open (Vol.1) — Luma registration](https://luma.com/event/evt-Ooqpabo6jDOft5s)
- [Reversa — home](https://reversa.ai/)
- [Reversa — about and founders](https://reversa.ai/about)
- [Reversa — news index](https://reversa.ai/news)
- [Talky](https://usetalky.com/)
- [Tunen](https://tunen.ai/)
- [Mad Tech Campus](https://www.madtechcampus.com/en/)

**Company coverage (press)**

- [Turning regulatory chaos into structured intelligence: Reversa — Built in Europe](https://builtineurope.substack.com/p/turning-regulatory-chaos-into-structured-intelligence-reversa)
- [Reversa revoluciona el cumplimiento normativo — Derecho Práctico](https://derechopractico.es/reversa-revoluciona-el-cumplimiento-normativo-con-una-plataforma-de-ia-que-convierte-el-riesgo-en-ventaja-competitiva/)
- [Tomás Burgaleta Alonso — LinkedIn](https://es.linkedin.com/in/tomasburgaletaalonso)
- [Iñigo Martin Llorente — LinkedIn](https://www.linkedin.com/in/i%C3%B1igo-martin-llorente-3160b71b7/)
- [List of Startups Fundraising, March 2026 — Learning VC (Reversa pre-seed entry)](https://luisllorens.substack.com/p/list-of-startups-fundraising-march-b9d)
- [Open roles with founders hailing from NVIDIA, Citadel, Palantir and MIT — a16z jobs (Reversa entry)](https://a16zjobs.substack.com/p/open-roles-with-founders-hailing-1a3)
- [Reversa — El Referente startup profile](https://elreferente.es/startup/reversa/)
  (returned HTTP 403)
- [Talky, la startup que automatiza la contabilidad de los restaurantes — Telemadrid](https://www.telemadrid.es/programas/madrid-trabaja/Talky-la-startup-que-se-encarga-de-automatizar-la-contabilidad-de-los-restaurantes-9-2854604528--20260120052845.html)
- [Maximilian Letón — LinkedIn](https://www.linkedin.com/in/maximilian-leton/)
- [Talky Labs — LinkedIn company page](https://www.linkedin.com/company/talkylabs)
- [Talky — English product site](https://usetalky.com/en/)
- [Talky (unrelated 2013 US company) — Tracxn profile](https://tracxn.com/d/companies/talky/__t4k738QcdiSBptY_GWQLJ8aMmM5X-q5muVenc12vn5c),
  included to document the name collision
- [Talky (unrelated gettalky.ai) — RocketReach management profile](https://rocketreach.co/talky-management_b6e16519c6fd7c90),
  included to document the name collision

**Tunen founders (press)**

- [Graduation Story: Alex Shinebourne — Darwin College, Cambridge](https://www.darwin.cam.ac.uk/news/graduation-story-alex-shinebourne/)
- [Introducing: Rockstart AgriFood Spring 2024 — Rockstart](https://rockstart.com/blog/2024/03/15/introducing-rockstart-agrifood-spring-2024/)
- [Alexandre Shinebourne — LinkedIn](https://uk.linkedin.com/in/alexandre-shinebourne-47530617b)
- [Jacob von der Decken — LinkedIn](https://www.linkedin.com/in/jacob-von-der-decken/)
- [Tunen Limited — Endole company profile](https://open.endole.co.uk/insight/company/15107539-tunen-limited)
  (returned HTTP 403)
- [Germany’s retiring Mittelstand owners struggle to find successors — Yahoo Finance/Reuters](https://uk.finance.yahoo.com/news/germanys-retiring-mittelstand-owners-struggle-060717218.html)
- [Tunen — news and events log](https://tunen.ai/)
- [Mad Tech Campus toma el relevo de Google — El Diario de Madrid](https://www.eldiariodemadrid.es/articulo/emprendedores/mad-tech-campus-madrid-hub-tecnologico-startups/20260513102435130363.html)
- [Mad Tech Campus en Matadero — El Capital Digital](https://elcapitaldigital.com/mad-tech-campus-en-madrid-un-nuevo-espacio-para-startups-en-matadero-impulsado-por-tetuan-valley-seedrocket-y-backfund/)
- [Nazareth Rodrigáñez lidera Tetuan Valley en el Mad Tech Campus — Madrid Dealers](https://madriddealers.es/actualidad/nazareth-rodriganez-lidera-tetuan-valley-en-el-mad-tech-campus-de-madrid/)

**Spanish parliamentary record (primary and press)**

- [Congreso de los Diputados — open data](https://www.congreso.es/en/datos-abiertos)
- [Congreso de los Diputados — Diario de Sesiones, serie histórica](https://app.congreso.es/est_sesiones/)
- [Senado de España — Diarios de Sesiones](https://www.senado.es/web/actividadparlamentaria/publicacionesoficiales/senado/diariossesiones/index.html)
- [Canal Parlamento — Congreso, YouTube](https://www.youtube.com/@CanalParlamento-Congreso_Es/streams)
- [Diarios de sesiones y documentos audiovisuales en la web — Revista de las Cortes Generales](https://revista.cortesgenerales.es/rcg/article/download/16/46/)
- [Diarios de sesiones del Congreso y el Senado — La Linterna del Traductor](https://lalinternadeltraductor.org/n19/diarios-sesiones.html)
- [Las taquígrafas del Congreso se plantan — The Objective](https://theobjective.com/espana/politica/2023-09-23/congreso-taquigrafas-lenguas/)
- [La reforma del Reglamento del Congreso para el uso de las lenguas cooficiales — Newtral](https://www.newtral.es/uso-lenguas-cooficiales-congreso-diputados/20230919/)
- [El Congreso destina más de 53.000 euros en la traducción de las lenguas cooficiales — Newtral](https://www.newtral.es/traduccion-congreso-cooficiales/20230918/)
- [Congreso de los Diputados — Intervenciones (open data)](https://www.congreso.es/es/opendata/intervenciones)
- [Congreso de los Diputados — Archivo audiovisual](https://www.congreso.es/es/archivo-audiovisual)
- [Cuándo se podrán utilizar en el Congreso catalán, euskera, gallego, valenciano y aranés — Infobae](https://www.infobae.com/espana/2023/09/07/cuando-se-podran-utilizar-en-el-congreso-catalan-euskera-gallego-valenciano-y-aranes-tras-la-reforma-del-reglamento/)
- [Así ha sido la transformación del Congreso para habilitarlo a las lenguas cooficiales: 280.000 euros — COPE](https://www.cope.es/actualidad/espana/noticias/asi-sido-transformacion-del-congreso-para-habilitarlo-las-lenguas-cooficiales-280000-euros-20230919_2904942)
- [El Congreso gasta 1,6 millones en adaptar 8 salas a la traducción simultánea — OKDiario](https://okdiario.com/espana/congreso-gasta-16-millones-adaptar-8-salas-traduccion-simultanea-lenguas-cooficiales-14448733)
- [El Congreso adjudica los contratos para la traducción de lenguas cooficiales por 3,9 millones anuales — Infobae](https://www.infobae.com/espana/agencias/2024/11/19/el-congreso-adjudica-los-contratos-para-la-traduccion-de-lenguas-cooficiales-por-39-millones-anuales/)
- [Anuncio de formalización de contratos, expediente CON-2024/9900775.0 — servicios de traducción, interpretación y transcripción de lenguas cooficiales y subtitulado en directo](https://www.derecho.com/boe/anuncio-de-formalizacion-de-contratos-de-mesa-del-congreso-de-los-diputados-objeto-servicios-de-traduccion-interpretacion-y-transcripcion-de-lenguas-cooficiales-asi-como-el-subtitulado-en-directo-en-el-congreso-de-los-diputados-expediente-con202499007750-789676)
- [El Congreso gastó cerca de 920.000 euros en 2025 en traducción e interpretación de lenguas cooficiales — Press Digital](https://www.pressdigital.es/articulo/politica/2026-06-14/5918633-congreso-gasto-cerca-920000-euros-2025-servicios-traduccion-e-interpretacion-lenguas-cooficiales)
- [Por qué las pantallas del Congreso ofrecen una transcripción en castellano — Maldita.es](https://maldita.es/malditateexplica/20230928/presidenta-congreso-armengol-espanol-transcripcion-pantallas/)
- [El Congreso aclara que transcribe en pantalla intervenciones en castellano por accesibilidad — Servimedia](https://www.servimedia.es/noticias/congreso-aclara-transcribe-pantalla-intervenciones-castellano-accesibilidad-para-personas-discapacidad/3970912)
- [El Congreso estrena el uso de las lenguas cooficiales — Junior Report (audio original con la voz del traductor por encima)](https://junior-report.media/el-congreso-estrena-el-uso-de-las-lenguas-cooficiales/)
- [El Debate, 20 September 2023 — doblaje al español en la primera sesión plurilingüe](https://www.eldebate.com/espana/20230920/doblaje-solo-espanol-primera-sesion-plurilinguee-congreso-sirve-reafirmar-lengua-comun_140931.html)
- [El Español, 19 September 2023 — pinganillos y traductores autónomos](https://www.elespanol.com/espana/politica/20230919/congreso-compra-pinganillos-ficha-autonomos-traducir-plenospoliglotas/795420837_0.html)
- [European Parliament Multimedia Centre — webstreaming with selectable interpretation channels](https://www.europarl.europa.eu/website/multimedia-centre/en/webstreaming.html)
- [ParlamentParla — OpenSLR 59](https://www.openslr.org/59/)
- [proxectonos/Nos_Parlaspeech-GL](https://huggingface.co/datasets/proxectonos/Nos_Parlaspeech-GL)
- [ParlaMint-ES 5.0 — CLARIN.SI](https://www.clarin.si/repository/xmlui/handle/11356/2004)

**Public code (primary)**

- [Talky-SP — GitHub organization](https://github.com/Talky-SP)
- [Talky-SP/hackathon-base-2026-03-06 — AI Evals Hackathon frontend](https://github.com/Talky-SP/hackathon-base-2026-03-06)
- [Talky-SP/hackathon-backend-base-2026-03-06 — hackathon AWS CDK backend](https://github.com/Talky-SP/hackathon-backend-base-2026-03-06)
- [jorgebp226 — GitHub profile](https://github.com/jorgebp226)
- [tunen-org — Tunen Limited GitHub organization](https://github.com/tunen-org)
- [a-shine — Alexandre Shinebourne GitHub profile](https://github.com/a-shine)
- [a-shine/niab-automated-phenotyping — Cambridge AI4ER MRes / NIAB project](https://github.com/a-shine/niab-automated-phenotyping)
- [reversa-ai — GitHub organization](https://github.com/reversa-ai) (no public
  repositories; identification unconfirmed)

**ASR models and benchmarks (technical)**

- [Whisper-LM: Improving ASR Models with Language Models for Low-Resource Languages — arXiv 2503.23542](https://arxiv.org/html/2503.23542v1)
- [Optimizing ASR for Catalan-Spanish Code-Switching: A Comparative Analysis of Methodologies — Interspeech 2025](https://www.isca-archive.org/interspeech_2025/mena25_interspeech.html)
- [BSC-LT/BSCs_Code_Switching_CA-ES_ASR_Test — code-switching evaluation set](https://huggingface.co/datasets/BSC-LT/BSCs_Code_Switching_CA-ES_ASR_Test)
- [Adapting Whisper for Code-Switching through Encoding Refining and Language-Aware Decoding — arXiv 2412.16507](https://arxiv.org/html/2412.16507v2)
- [Unified model for code-switching ASR and language identification via a concatenated tokenizer — arXiv 2306.08753](https://arxiv.org/pdf/2306.08753)
- [Exploring Spoken Language Identification Strategies for Multilingual Broadcast and Institutional Speech — arXiv 2406.09290](https://arxiv.org/pdf/2406.09290)
- [Open ASR Leaderboard: Towards Reproducible and Transparent Multilingual and Long-Form Speech Recognition Evaluation — arXiv 2510.06961](https://arxiv.org/html/2510.06961v4)
- [Investigation of Whisper ASR Hallucinations Induced by Non-Speech Audio — arXiv 2501.11378](https://arxiv.org/pdf/2501.11378)
- [Calm-Whisper: Reduce Whisper Hallucination On Non-Speech — arXiv 2505.12969](https://arxiv.org/pdf/2505.12969)
- [WhisperX: Time-Accurate Speech Transcription of Long-Form Audio — arXiv 2303.00747](https://arxiv.org/pdf/2303.00747)
- [Turning Whisper into Real-Time Transcription System (Whisper-Streaming, LocalAgreement) — arXiv 2307.14743](https://arxiv.org/html/2307.14743v2)
- [ufal/whisper_streaming — README, read directly](https://github.com/ufal/whisper_streaming)
- [ufal/SimulStreaming — successor project](https://github.com/ufal/SimulStreaming)
- [Simul-Whisper: Attention-Guided Streaming Whisper with Truncation Detection — arXiv 2406.10052](https://arxiv.org/pdf/2406.10052)
- [BSC-LT/whisper-large-v3-LoS — Spanish, Catalan, Galician, Basque](https://huggingface.co/BSC-LT/whisper-large-v3-LoS)
- [BSC-LT/whisper-large-v3-LoS-punctuated](https://huggingface.co/BSC-LT/whisper-large-v3-LoS-punctuated)
- [projecte-aina/whisper-large-v3-ca-3catparla — Catalan](https://huggingface.co/projecte-aina/whisper-large-v3-ca-3catparla)
- [HiTZ/whisper-large-v3-eu — Basque](https://huggingface.co/HiTZ/whisper-large-v3-eu)
- [Whisper v3-Turbo on H100: 597× realtime ASR benchmark — InferenceBench](https://inferencebench.io/blog/whisper-large-v3-turbo-597x-realtime-asr-benchmark/)
- [SYSTRAN/faster-whisper](https://github.com/SYSTRAN/faster-whisper)
- [AI Transcription Accuracy in 2026: Real Benchmarks and WER](https://voicetonotes.ai/blog/state-of-ai-transcription-accuracy/)
- [ASR in 2025–2026: A Deep Dive into Speech Recognition Technology Selection](https://ruoqijin.com/blog/asr-deep-dive-2025-2026)

**Diarization, speaker identification, and speaker-attributed ASR (technical)**

- [pyannote/pyannote-audio — benchmark tables for 3.1, community-1 and precision-2, read directly](https://github.com/pyannote/pyannote-audio)
- [Community-1: Unleashing open-source diarization — pyannoteAI](https://www.pyannote.ai/blog/community-1)
- [Setting a new standard with Precision-2 — pyannoteAI](https://www.pyannote.ai/blog/precision-2)
- [BUTSpeechFIT/DiariZen — benchmark table, no-collar protocol, read directly](https://github.com/BUTSpeechFIT/DiariZen)
- [Efficient and Generalizable Speaker Diarization via Structured Pruning of Self-Supervised Models — arXiv 2506.18623](https://arxiv.org/html/2506.18623)
- [Benchmarking Diarization Models — arXiv 2509.26177](https://arxiv.org/pdf/2509.26177)
- [Streaming Sortformer: Speaker Cache-Based Online Speaker Diarization with Arrival-Time Ordering — Interspeech 2025](https://www.isca-archive.org/interspeech_2025/medennikov25_interspeech.pdf)
- [nvidia/diar_streaming_sortformer_4spk-v2](https://huggingface.co/nvidia/diar_streaming_sortformer_4spk-v2)
- [Identify speakers in meetings, calls and voice apps in real time with NVIDIA Streaming Sortformer](https://developer.nvidia.com/blog/identify-speakers-in-meetings-calls-and-voice-apps-in-real-time-with-nvidia-streaming-sortformer/)
- [Target-Speaker Voice Activity Detection: a Novel Approach for Multi-Speaker Diarization — arXiv 2005.07272](https://arxiv.org/abs/2005.07272)
- [Profile-Error-Tolerant Target-Speaker Voice Activity Detection — arXiv 2309.12521](https://arxiv.org/pdf/2309.12521)
- [VoxWatch: An open-set speaker recognition benchmark on VoxCeleb — arXiv 2307.00169](https://arxiv.org/pdf/2307.00169)
- [VoxBlink2: A 100K+ Speaker Recognition Corpus and the Open-Set Speaker-Identification Benchmark — arXiv 2407.11510](https://arxiv.org/html/2407.11510v1)
- [Experiments on Open-Set Speaker Identification with Discriminatively Trained Neural Networks — arXiv 1904.01269](https://arxiv.org/pdf/1904.01269)
- [ECAPA-TDNN: Emphasized Channel Attention, Propagation and Aggregation — arXiv 2005.07143](https://arxiv.org/pdf/2005.07143)
- [Deep Speaker Embeddings for Far-Field Speaker Recognition on Short Utterances — arXiv 2002.06033](https://arxiv.org/html/2002.06033v1)
- [Disentangling Speaker and Language Effects in Cross-Lingual Speaker Verification for Iberian Languages — arXiv 2607.01161](https://arxiv.org/html/2607.01161v1)
- [DiarizationLM: Speaker Diarization Post-Processing with Large Language Models — arXiv 2401.03506](https://arxiv.org/html/2401.03506v4)
- [Do We Still Need Audio? Rethinking Speaker Diarization with a Text-Based Approach — arXiv 2506.11344](https://arxiv.org/pdf/2506.11344)
- [MeetEval — meeting transcription evaluation toolkit, read directly](https://github.com/fgnt/meeteval)
- [MeetEval: A Toolkit for Computation of Word Error Rates for Meeting Transcription — CHiME 2023](https://www.isca-archive.org/chime_2023/neumann23_chime.pdf)
- [Recent Trends in Distant Conversational Speech Recognition: A Review of CHiME-7 and 8 DASR Challenges — arXiv 2507.18161](https://arxiv.org/pdf/2507.18161)
- [The USTC-NERCSLIP Systems for the CHiME-8 NOTSOFAR-1 Challenge — arXiv 2409.02041](https://arxiv.org/pdf/2409.02041)
- [8 Best Speaker Diarization Solutions & APIs in 2026 — AssemblyAI (vendor-published cpWER figures)](https://www.assemblyai.com/blog/top-speaker-diarization-libraries-and-apis)
- [Speaker Diarization Models Guide: Benchmarks and Failure Modes 2026 — Kili](https://kili-technology.com/blog/speaker-diarization-models-guide-benchmarks-and-failure-modes-2026)
- [State of Speaker Diarization in 2026 — Picovoice](https://picovoice.ai/blog/state-of-speaker-diarization/)
- [Speaker Diarization vs Speaker Identification — Picovoice](https://picovoice.ai/blog/speaker-diarization-vs-speaker-recognition-identification/)
- [Speaker identification — Speechmatics docs](https://docs.speechmatics.com/speech-to-text/features/speaker-identification)

**Spanish-language and broadcast evaluation (technical)**

- [An Overview of the IberSpeech-RTVE 2022 Challenges on Speech Technologies — Applied Sciences 13(15):8577](https://www.mdpi.com/2076-3417/13/15/8577)
- [Intelligent Voice Speaker Recognition and Diarization System for the IberSpeech 2022 Speaker Diarization and Identity Assignment Challenge](https://www.isca-archive.org/iberspeech_2022/shrestha22_iberspeech.pdf)
- [Albayzin Evaluation IberSPEECH-RTVE 2022 Speaker Diarization — evaluation plan](https://catedrartve.unizar.es/reto2022/SDIAC2022_Evalplan.pdf)
- [Diarization and Identity Attribution Compatibility in the Albayzin 2020 Challenge — IberSPEECH 2021](https://www.isca-archive.org/iberspeech_2021/vinals21_iberspeech.html)
- [ViVoLAB Multimodal Diarization System for RTVE 2020 Challenge — IberSPEECH 2021](https://www.isca-archive.org/iberspeech_2021/mingote21_iberspeech.pdf)
- [Multimodal Diarization Systems by Training Enrollment Models as Identity Representations — Applied Sciences 12(3):1141](https://doi.org/10.3390/app12031141)
- [Albayzin 2018 Evaluation: The IberSpeech-RTVE Challenge — Applied Sciences 9(24):5412](https://www.mdpi.com/2076-3417/9/24/5412)

**Parliamentary speech corpora (technical)**

- [VoxPopuli: A Large-Scale Multilingual Speech Corpus — arXiv 2101.00390](https://arxiv.org/pdf/2101.00390)
- [VoxPopuli — code and data, per-language hours read directly](https://github.com/facebookresearch/voxpopuli)
- [Europarl-ASR: A Large Corpus of Parliamentary Debates — Interspeech 2021](https://www.isca-archive.org/interspeech_2021/garcesdiazmunio21_interspeech.pdf)
- [EuroSpeech: A Multilingual Speech Corpus — NeurIPS 2025](https://papers.neurips.cc/paper_files/paper/2025/file/58ea63de01321ee52d06b48026981c40-Paper-Datasets_and_Benchmarks_Track.pdf)
- [SamuelPfisterer/EuroSpeech — toolkit and per-parliament coverage, cloned and read](https://github.com/SamuelPfisterer/EuroSpeech)
- [ParlaMint corpora of parliamentary proceedings — Language Resources and Evaluation](https://link.springer.com/article/10.1007/s10579-021-09574-0)
- [ParlaSpeech 3.0: Richly Annotated Spoken Parliamentary Corpora — arXiv 2511.01619](https://arxiv.org/pdf/2511.01619)
- [The ParlaSpeech Collection — arXiv 2409.15397](https://arxiv.org/pdf/2409.15397)
- [gttsehu/basque_parliament_1 — bilingual Basque–Spanish parliamentary dataset](https://huggingface.co/datasets/gttsehu/basque_parliament_1)
- [A Bilingual Basque–Spanish Dataset of Parliamentary Sessions — Applied Sciences 14(5):1951](https://doi.org/10.3390/app14051951)
- [Semisupervised Speech Data Extraction from Basque Parliament Sessions — Applied Sciences 13(14):8492](https://doi.org/10.3390/app13148492)
- [3CatParla: A New Open-Source Corpus of Broadcast TV in Catalan — IberSPEECH 2024](https://www.isca-archive.org/iberspeech_2024/hernandezmena24_iberspeech.pdf)
- [Althingi Parliamentary Speech — LDC2021S01](https://catalog.ldc.upenn.edu/LDC2021S01)
- [Finnish Parliament ASR corpus: analysis, benchmarks and statistics — arXiv 2203.14876](https://arxiv.org/pdf/2203.14876)
- [FT Speech: Danish Parliament Speech Corpus — arXiv 2005.12368](https://arxiv.org/pdf/2005.12368)

**Text-side speaker attribution and multimodal identity (technical)**

- [Out of the Mouths of MPs: Speaker Attribution in Parliamentary Debates — LREC-COLING 2024](https://aclanthology.org/2024.lrec-main.1098/)
- [Speaker attribution in German parliamentary debates with QLoRA-adapted LLMs — arXiv 2309.09902](https://arxiv.org/pdf/2309.09902)
- [Transcription and Recognition of Italian Parliamentary Speeches Using Vision-Language Models — arXiv 2603.28103](https://arxiv.org/html/2603.28103v1)
- [AVA-ActiveSpeaker: An Audio-Visual Dataset for Active Speaker Detection — arXiv 1901.01342](https://arxiv.org/pdf/1901.01342)
- [Political corpus creation through automatic speech recognition on EU debates — arXiv 2304.08137](https://arxiv.org/pdf/2304.08137)

**Deployed parliamentary transcription systems (press and technical)**

- [Parlamento-ai/open-source-asr — reproducible parliamentary ASR study with workload and cost data](https://github.com/Parlamento-ai/open-source-asr)

- [Parlamento.ai — home](https://parlamento.ai/)

- [Televic CoCon API guide](https://manuals.plus/m/e4b32f39ab18a388953d977c4ba39f7919ee60808609d12c8f5469a962fc9c7a)

- [Bosch DICENTIS integration — Extron](https://www.extron.com/article/bosch)

- [PERCOLI at REPERE 2013 — ResearchGate](https://www.researchgate.net/publication/257207780)

- [The role of AI in parliaments — Inter-Parliamentary Union](https://www.ipu.org/ai-guidelines/role-ai-in-parliaments)

- [Automatic transcription of parliamentary sessions — IPU AI use cases](https://www.ipu.org/ai-use-cases/automatic-transcription-parliamentary-sessions)

- [AI-powered verbatim records system (HANS) — IPU AI use cases](https://www.ipu.org/ai-use-cases/ai-powered-verbatim-records-system-hans)

- [Automated Hansard report system: converting parliamentary audio to text using AI — IPU](https://www.ipu.org/ai-use-cases/automated-hansard-report-system-converting-parliamentary-audio-text-using-ai)

- [HANS, AI support tool for the Estonian Parliament — e-Estonia](https://e-estonia.com/hans-ai-support-tool-for-estonian-parliament/)

- [Estonian parliament uses speech recognition technology to create verbatim records — Finestmedia](https://finestmedia.ee/en/2020/09/14/estonian-parliament-uses-speech-recognition-technology-to-create-verbatim-records/)

- [The Althingi ASR System — Interspeech 2019](https://www.isca-archive.org/interspeech_2019/helgadottir19_interspeech.html)

- [Manual Post-editing of Automatically Transcribed Speeches from the Icelandic Parliament — arXiv 1807.11893](https://arxiv.org/pdf/1807.11893)

- [Automatic transcription system for parliamentary debates in the context of the Assembly of the Republic of Portugal (STAAR) — International Journal of Speech Technology](https://link.springer.com/article/10.1007/s10772-024-10126-4)

- [The influence of AI on grammatical correction in Portuguese Parliament plenary session reports — Intersteno](https://tiro.intersteno.org/2026/06/the-influence-of-ai-on-grammatical-correction-in-portuguese-parliament-plenary-session-reports-preliminary-conclusions/)

- [Integrating AI into legislative services: the Ulysses Suite in the Chamber of Deputies of Brazil](https://library.bussola-tech.co/p/ulysses-chamber-deputies-brazil)

- [Catalogación automática en el Senado — Etiqmedia](https://etiqmedia.com/catalogacion-automatica-en-el-senado/)

- [Transcripción automática de audio en directo — Etiqmedia](https://etiqmedia.com/tecnologia/transcripcion-automatica-audio-directo/)

- [Pangeanic will implement AI transcription technology in the Spanish Parliament](https://blog.pangeanic.com/pangeanic-will-implement-ai-transcription-technology-in-spanish-parliament)

- [Parliamentary conferencing systems — Televic Conference](https://www.televic.com/en/conference/markets/parliaments)

- [Bosch DICENTIS for parliaments — product brief](https://www.keenfinity-group.com/media/en/pb/media/products_1/conference_systems_1/190000770-bosch-nl-appl-dicentis-prlmnt-emea.pdf)

- [Hansard — UK Parliament (edited verbatim report)](https://www.parliament.uk/about/how/publications/hansard/)

**European Parliament deployments (press)**

- [RWS wins European Parliament tender for live speech-to-text solution](https://www.rws.com/about/news/2021/rws-wins-eu-parliament-tender/)
- [RWS to supply European Parliament with translation/transcription service — MultiLingual](https://multilingual.com/rws-to-supply-european-parliament-with-translation-transcription-service/)
- [EU Parliament awards Translated with contract for automatic speech translation — Slator](https://slator.com/eu-parliament-awards-translated-with-contract-for-automatic-speech-translation/)
- [European Parliament: the AI of Translated, FBK and PerVoice for real-time translation of debates — FBK](https://www.fbk.eu/en/result/european-parliament-the-artificial-intelligence-of-translated-fbk-and-pervoice-for-real-time-translation-of-debates/)
- [Bringing the European Parliament closer to citizens through AI — Microsoft EU Policy Blog](https://blogs.microsoft.com/eupolicy/2020/09/15/bringing-european-parliament-closer-citizens-through-ai/)

<!-- This document follows common-doc-guidelines.md.
See github.com/jlevy/practical-prose and review guidelines before editing.
-->
