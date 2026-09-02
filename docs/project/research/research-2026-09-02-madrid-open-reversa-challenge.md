---
title: The Madrid Open (Vol.1) and the Reversa Real-Time Parliamentary Record Challenge
description: A full dossier on the 3 October 2026 Madrid Open competition, the three startups that own its challenges, and an in-depth analysis of the Reversa democracy track and its underlying speech-recognition problem
author: Claude Opus 5
---
# Research: The Madrid Open (Vol.1) and the Reversa Real-Time Parliamentary Record Challenge

**Date:** 2026-09-02 (last updated 2026-09-02)

**Author:** Claude Opus 5

**Status:** Complete

## Overview

This report assembles everything publicly available about the **Madrid Open (Vol.1)**, a
one-day, forty-person, invitation-screened build competition held on **Saturday 3
October 2026** at Mad Tech Campus in Madrid, jointly presented by three Madrid-connected
startups: **Talky** (accounting), **Reversa** (regulation and democracy), and **Tunen**
(agriculture).

It covers five things:

1. The competition itself: format, rules, selection, schedule, scoring rubric, prizes,
   and logistics.
2. The three startups that own the challenges, with the deepest treatment of Reversa,
   whose site hosts the event.
3. All nine identifiable founders and principals across the three companies, with what
   is and is not on the public record for each.
4. All three challenge tracks as stated, with the discrepancies between the two official
   descriptions surfaced rather than smoothed over.
5. The problem domain behind **Track 02 (Reversa / Democracy)**—real-time parliamentary
   transcription and speaker attribution—including the state of the art, the public
   datasets and models that bear on it, and why the stated target is hard.

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
4. What precisely is each challenge asking for, and what is the measured metric?
5. For the Reversa track: what is the real-world problem, why does the official record
   lag, and what is the current state of the art in the relevant technologies?
6. What does the scoring rubric imply about how a twelve-hour build should be
   prioritized?
7. What remains unknown until the briefs are released?

## Scope

**Included:** the public record on the event, its organizers, and their founders as of
2026-09-02; the technical literature bearing on multilingual ASR, speaker diarization,
and speaker identification in parliamentary settings; and analysis of the stated rubric.

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

### 9. The three challenges as stated

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
is not a raw transcript, a point developed in §10.

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

### 10. Deep dive: the Reversa problem domain

#### 10.1 Why the official record lags

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

#### 10.2 The multilingual complication

**[Press]** Since **19 September 2023**, deputies in the Congreso de los Diputados may
speak in the co-official languages, with simultaneous translation into Spanish.
Coverage from the time lists Catalan, Basque, and Galician as the principal languages,
with the broader reform text also referencing Valencian, Aranese, and Bable.
The Congress allocated over €53,000 for translation and transcription from that first
plenary.
Contemporary reporting noted that the Congress’s stenographers stated they would
not take responsibility for translated texts.

**[Analysis]** This is the exact seam where the human process is weakest and where an
automated system has the most room to win.
It also explains a plausible “five languages” reading: Spanish, Catalan, Basque,
Galician, Valencian.
Two further difficulties follow.
First, **code-switching**: speakers move between languages within a single intervention,
so language identification has to operate at sub-utterance granularity rather than per
session or per speaker.
Second, the co-official languages are **lower-resource** than Spanish, so off-the-shelf
multilingual ASR degrades exactly where the human process also degrades.

#### 10.3 State of the art: ASR

**[Technical]** The most relevant independent English benchmark is Artificial Analysis’s
**AA-WER v2.0**, which tested 41 models across three real-world datasets—AgentTalk,
VoxPopuli, and Earnings-22—explicitly covering conversational speech, **parliamentary
speech**, and earnings calls.
Reported leaders: **ElevenLabs Scribe v2 at 2.3% WER**, **Gemini 3 Pro at 2.9%**, and
**Gemini 3 Flash at 3.1%**. The architectural trend from 2025 onward is toward **encoder
\+ LLM-decoder end-to-end** models.

**[Technical]** For the Iberian languages, open fine-tuned models exist and are directly
relevant:

- **`projecte-aina/whisper-large-v3-ca-3catparla`** — Catalan, fine-tuned on 710 hours,
  reporting **WER 0.96** on its own in-domain 3CatParla test set.
  A `faster-whisper` conversion is published alongside it.
- **`HiTZ/whisper-large-v3-eu`** — Basque, reporting **10.62% WER** on Common Voice
  13.0; the earlier v2 model reported 11.34%.
- **`BSC-LT/whisper-large-v3-LoS`** — a single multilingual model covering **Spanish,
  Catalan, Galician, and Basque**, trained on 8,110 hours, with a punctuated variant
  (`-LoS-punctuated`) also published.

**[Analysis]** The sub-1% Catalan figure should not be read as transferable.
It is reported on the model’s own in-domain broadcast-derived test set; parliamentary
floor audio with crosstalk, interruptions, applause, gavel, and variable microphone
discipline is a different distribution.
The `BSC-LT` multilingual model is the most interesting starting point for this specific
challenge because it covers four of the likely five languages in one model, and the
punctuated variant removes a separate restoration stage.
**[Technical]** The literature also shows that pairing Whisper with an external language
model measurably improves low-resource performance (*Whisper-LM*, arXiv 2503.23542),
which is a cheap, well-trodden accuracy lever.

#### 10.4 State of the art: diarization versus speaker identification

**[Technical]** These are two different problems and the challenge needs the harder one:

- **Diarization** answers “who spoke when,” assigning anonymous labels (Speaker 1,
  Speaker 2). The standard metric is **Diarization Error Rate (DER)**—the fraction of
  audio time attributed to the wrong speaker, summing missed speech, false alarms, and
  speaker confusion, evaluated per the NIST “who spoke when” task.
- **Speaker identification** answers “who is speaking,” matching voice against enrolled
  profiles. Enrollment typically requires 5–30 second clips per speaker where they speak
  alone, and multiple clips under different acoustic conditions improve robustness.

**[Technical]** A distinction that matters more in production than in papers: DER
measures diarization in isolation from the transcript, whereas **cpWER** (concatenated
minimum- permutation WER) measures whether the right speaker label lands on the right
*words*. One published benchmark puts diarization cpWER at **36.87**, an order of
magnitude worse than the headline WER figures, which is the honest signal about where
difficulty lives.
Accuracy is highest with two to four speakers and **degrades as speaker
count climbs**.

**[Analysis]** This is the crux of Track 02. The metric is “how often the right speaker
is **named**”—not clustered, named.
A plenary chamber has hundreds of possible speakers, which is the regime where pure
acoustic identification degrades most.
But a parliament is an unusually favorable environment for beating that limit with
non-acoustic evidence:

1. **The presiding officer names the next speaker aloud**, almost always, by name and
   often by parliamentary group.
   That is a textual cue in the transcript itself, upstream of the audio.
2. **The speaking order is published** as the order of business, constraining the
   candidate set at any moment to a small number.
3. **Enrollment data is free and abundant.** Every member has hours of prior floor audio
   in the public archive, already attributed by the existing official records.
4. **The camera cuts to the speaker**, so the video channel carries an independent
   identity signal.

A system that fuses acoustic identification with the chair’s announcements and the
agenda should substantially outperform acoustic-only diarization, and—critically for the
15% auditability weight—can *cite* its evidence for each attribution.

#### 10.5 Prior deployments and available corpora

**[Press/Technical]** The **European Parliament** is the closest existing production
analogue.
It ran an 18-month competitive evaluation before awarding a live speech-to-text
contract to a consortium of **RWS**, **CEDAT85**, and **Bertin IT**, combining RWS’s
Language Weaver machine translation with CEDAT85’s speech recognition, to transcribe and
translate debates across the institution’s **24 official languages** in real time on
screen, including as an accessibility provision for members with hearing impairments.
A separate contract for automatic speech translation was awarded to **Translated**,
working with **FBK** and **PerVoice**.

**[Technical]** Public corpora directly on point:

- **VoxPopuli** (Meta/FAIR) — European Parliament recordings from 2009–2020: **400,000
  hours of unlabeled speech in 23 languages**, 9,000–18,000 hours per language, plus
  **1,800 hours of transcribed speech in 15 languages**, with a pipeline that segments
  by speaker or silence and aligns to transcripts.
  It is also one of the three AA-WER v2.0 benchmark datasets.
- **Europarl-ASR** — a large corpus of parliamentary debates (Interspeech 2021).
- **EuroSpeech** — a more recent multilingual parliamentary speech corpus (NeurIPS 2025
  Datasets and Benchmarks track).

**[Primary]** For Spain specifically, the Congreso de los Diputados publishes open data
in XML, JSON, and CSV at
[congreso.es/datos-abiertos](https://www.congreso.es/en/datos-abiertos); streams
plenaries and up to five simultaneous committees via *Congreso en Directo*; and
maintains an audiovisual archive where debates and individual interventions are
cataloged and downloadable as MP4 after each session.
Sessions are also streamed on the **Canal Parlamento** YouTube channel.
The Senado publishes its own *Diarios de Sesiones*.

**[Analysis]** The combination of open MP4 archives and already-published official
transcripts means a participant can assemble an aligned audio-plus-attributed-transcript
evaluation set for Spain **before the event**, without any provided data.
That is the single highest-value piece of pre-work available under the rules, which
explicitly permit designing ahead.
It also supplies the enrollment clips needed for speaker identification.

#### 10.6 Why “five minutes” is hard, if that is the target

**[Analysis]** Assuming the Luma target, the latency budget has to cover, after a
session ends, the following for a multi-hour session:

1. Audio acquisition and segmentation.
2. Language identification at sub-utterance granularity for code-switched speech.
3. ASR in five languages.
4. Diarization plus **named** speaker resolution.
5. Punctuation, casing, and the editorial normalization that turns oral speech into a
   written record.
6. Assembly, ordering, and output formatting.

Five minutes after a session ends is not a real-time streaming requirement, but neither
is it a batch requirement—it is a **bounded-lag** requirement, which strongly favors
processing the session incrementally as it happens so that only the tail remains at the
end.
The architecture implied is a streaming pipeline with a short finalization pass, not
a post-hoc batch job.

The genuinely underestimated component is **step 5**. The organizers say “official
records,” and an official record is an edited artifact: false starts removed, oral
syntax regularized, interjections handled by convention.
Reproducing that editorial layer is a text-transformation problem distinct from ASR, and
it is where a raw transcript will diverge from the ground truth it is scored against
**even when every word was heard correctly**. If the metric is computed against
published *Diario de Sesiones* text, this alone could dominate the error.
This should be an early question for the challenge owners at 09:00.

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
3. **Track 02’s difficulty is attribution, not transcription.** Headline WER is
   approaching 2–3% on parliamentary-style speech, while speaker-attributed cpWER sits
   around 37 in published benchmarks.
   The metric names speaker accuracy explicitly.
   The leverage is in fusing non-acoustic evidence—the chair’s spoken announcements, the
   published order of business, prior attributed archives, the camera feed—rather than
   in pushing an acoustic model.
4. **The strongest legal pre-work is building your own evaluation set.** The rules
   permit unlimited advance design; Spain’s Congress publishes downloadable session
   video and the corresponding official transcripts; VoxPopuli, Europarl-ASR, and
   EuroSpeech are public.
   A participant can arrive with a working pipeline, measured against real parliamentary
   audio, and spend the twelve hours adapting rather than building.
5. **The two official sources disagree about Track 03 and under-specify Track 02.** The
   site and Luma give materially different descriptions of the Tunen challenge, and Luma
   alone supplies the “five minutes, five languages” targets.
   Clarify before choosing a track.
6. **The editorial-normalization gap is the hidden risk in Track 02.** An official
   parliamentary record is an edited document, not a verbatim transcript.
   If scoring is against published records, the gap between “heard correctly” and
   “matches the official text” may exceed the ASR error itself.
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
9. **This is a hiring funnel run by three founders from one ecosystem, and it says so.**
   Google for Startups Campus Madrid produced the relationships; its successor venue
   hosts the event; Reversa’s CRO comes from a founding partner of that venue.
   The stated reward includes “a conversation about doing it for real.”
   Optimizing purely for the leaderboard misreads the room.

## Comparison Matrix: choosing a track

**[Analysis]** Assessed against what is knowable today.

| Criterion | Track 01 Talky (Accounting) | Track 02 Reversa (Democracy) | Track 03 Tunen (Agriculture) |
| --- | --- | --- | --- |
| Problem clarity | High — precision-constrained coverage | Medium — two objectives, exact weighting unknown | Low — two conflicting official framings |
| Metric legibility | Very high — one number with a stated floor | Medium — composite of latency and attribution | Medium — distance to held-out ground truth |
| Public data for pre-work | Low — proprietary ledgers, nothing comparable public | **Very high** — open session video, official transcripts, VoxPopuli, Europarl-ASR, EuroSpeech | High — Sentinel/Landsat, weather, soil, and topography are public but fragmented |
| Off-the-shelf model availability | Medium — document extraction is mature, ledger reasoning is not | **High** — strong open multilingual ASR and diarization | Medium — remote sensing needs assembly |
| Dominant difficulty | Calibrated abstention at 99% precision | Named speaker attribution; editorial normalization | Source reconciliation; field-edge mixed pixels |
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
3. **If taking Track 02, build the evaluation harness now.** Download Congreso and
   Senado session video with the corresponding official *Diario de Sesiones* text, align
   them, and measure a baseline.
   Use the archive to build the speaker enrollment set.
   Start from `BSC-LT/whisper-large-v3-LoS-punctuated` for four-language coverage with
   punctuation in one model.
4. **Design the attribution layer as evidence fusion, not as diarization.** Parse the
   chair’s spoken introductions, ingest the published order of business, and treat
   acoustic identification as one signal among several.
   Emit a citation for every attribution — this serves the metric and the 15%
   auditability weight simultaneously.
5. **Build abstention in from the first commit.** Every output should carry a confidence
   and a threshold above which it is emitted and below which it is flagged.
   This is 20% of the score and is invisible if retrofitted at 20:00.
6. **Have a thin end-to-end slice running before the 17:00 checkpoint**, and rehearse
   the 21:00 demo. Ambition and live demo are 10%, and a system that fails live scores
   nothing on the other 90%.
7. **Do not depend on the provider credits.** They arrive during the event.
   Keep a local or free-tier fallback for every external service.

## Next Steps

- [ ] Confirm with the user whether the intent is to enter, and which track.
- [ ] Register on Luma before 25 September 2026 if entering.
- [ ] Send the discrepancy questions (Track 03 framing, Track 02 targets and metric
  weighting) to the organizers.
- [ ] If Track 02: assemble the aligned audio-plus-transcript evaluation set from
  Congreso and Senado open data.
- [ ] Update this report when the briefs are released the week of 26 September 2026.

## Methodology

Sources were gathered on 2026-09-02 by fetching the English and Spanish event pages
directly and extracting their full text, fetching the three company sites and the Luma
listing, and running parallel web searches in both English and Spanish across the
companies, the founders, the venue, the Spanish parliamentary record, and the relevant
speech-technology literature.
All findings are reported here in English; Spanish sources were translated, and the
Spanish event copy was compared line by line against the English to detect divergence.

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

**Speech technology (technical)**

- [VoxPopuli: A Large-Scale Multilingual Speech Corpus — arXiv 2101.00390](https://arxiv.org/pdf/2101.00390)
- [VoxPopuli — code and data](https://github.com/facebookresearch/voxpopuli)
- [Europarl-ASR: A Large Corpus of Parliamentary Debates — Interspeech 2021](https://www.isca-archive.org/interspeech_2021/garcesdiazmunio21_interspeech.pdf)
- [EuroSpeech: A Multilingual Speech Corpus — NeurIPS 2025](https://papers.neurips.cc/paper_files/paper/2025/file/58ea63de01321ee52d06b48026981c40-Paper-Datasets_and_Benchmarks_Track.pdf)
- [Whisper-LM: Improving ASR Models with Language Models for Low-Resource Languages — arXiv 2503.23542](https://arxiv.org/html/2503.23542v1)
- [Exploring Spoken Language Identification Strategies for Multilingual Broadcast and Institutional Speech — arXiv 2406.09290](https://arxiv.org/pdf/2406.09290)
- [BSC-LT/whisper-large-v3-LoS — Spanish, Catalan, Galician, Basque](https://huggingface.co/BSC-LT/whisper-large-v3-LoS)
- [BSC-LT/whisper-large-v3-LoS-punctuated](https://huggingface.co/BSC-LT/whisper-large-v3-LoS-punctuated)
- [projecte-aina/whisper-large-v3-ca-3catparla — Catalan](https://huggingface.co/projecte-aina/whisper-large-v3-ca-3catparla)
- [HiTZ/whisper-large-v3-eu — Basque](https://huggingface.co/HiTZ/whisper-large-v3-eu)
- [State of Speaker Diarization in 2026 — Picovoice](https://picovoice.ai/blog/state-of-speaker-diarization/)
- [Speaker Diarization vs Speaker Identification — Picovoice](https://picovoice.ai/blog/speaker-diarization-vs-speaker-recognition-identification/)
- [Speaker identification — Speechmatics docs](https://docs.speechmatics.com/speech-to-text/features/speaker-identification)
- [AI Transcription Accuracy in 2026: Real Benchmarks and WER](https://voicetonotes.ai/blog/state-of-ai-transcription-accuracy/)
- [ASR in 2025–2026: A Deep Dive into Speech Recognition Technology Selection](https://ruoqijin.com/blog/asr-deep-dive-2025-2026)

**European Parliament deployments (press)**

- [RWS wins European Parliament tender for live speech-to-text solution](https://www.rws.com/about/news/2021/rws-wins-eu-parliament-tender/)
- [RWS to supply European Parliament with translation/transcription service — MultiLingual](https://multilingual.com/rws-to-supply-european-parliament-with-translation-transcription-service/)
- [EU Parliament awards Translated with contract for automatic speech translation — Slator](https://slator.com/eu-parliament-awards-translated-with-contract-for-automatic-speech-translation/)
- [European Parliament: the AI of Translated, FBK and PerVoice for real-time translation of debates — FBK](https://www.fbk.eu/en/result/european-parliament-the-artificial-intelligence-of-translated-fbk-and-pervoice-for-real-time-translation-of-debates/)
- [Bringing the European Parliament closer to citizens through AI — Microsoft EU Policy Blog](https://blogs.microsoft.com/eupolicy/2020/09/15/bringing-european-parliament-closer-citizens-through-ai/)

<!-- This document follows common-doc-guidelines.md.
See github.com/jlevy/practical-prose and review guidelines before editing.
-->
