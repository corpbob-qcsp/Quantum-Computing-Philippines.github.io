---
layout: post
title:  "Nothing Worth Stealing: Zero-Knowledge Architecture as the Defence Against AI Swarm Attacks"
author: earl
image: assets/images/zkp-wide.png
categories: Cryptography
comments: false
---

*A position paper for policymakers, regulators, standards bodies, and security and technology leaders, written in November 2026. This post follows the earlier article on [proving you are above 15 without revealing your age](/range-proof-age-tutorial/). That article showed how one zero-knowledge proof works. This one asks what happens when a whole service is built on them.*

## Executive summary

**The issue.** By late November 2026, a run of breaches had been disclosed in which cooperating AI agents, often called *AI swarms*, carried out intrusions at machine speed. Some reached privileged access in under three hours, and a single supply-chain compromise led to attempted break-ins at more than 2,250 downstream organisations. Speed is the new part. The weakness is old: again and again these breaches, like the breaches of the previous two decades, began with a *reusable secret* or a centrally stored personal attribute that an attacker could take and use. Defences that rely on detecting and responding to theft lose ground as attackers get faster.

**The position.**

> Public-facing services should be built so that a full compromise of their servers yields no usable personal data: only public keys, commitments, tokens and ciphertext. Zero-knowledge proofs, anchored in keys that never leave the device's secure hardware, make this possible today.

In practice, this means no reusable secret, human or machine, is ever stored. Data that is only checked is proven, never collected. Personal data lives in the user's own vault, with the server holding only commitments to it. Where the law requires an institution to keep records, plaintext exists only at the point of use, in separate, hardened systems that release one record at a time, for a stated purpose, under limits and monitoring.

**Why it matters now.** This is the one class of defence whose strength does not depend on speed. A public key is as useless to a swarm of a thousand agents working for a minute as to one human working for a year. Every component is already standardised or shipping, from passkeys to the post-quantum signature standard ML-DSA. The European Union has specified zero-knowledge proofs for its Digital Identity Wallet but has not made them mandatory. The November 2026 breaches make the case for requiring them now.

**Key recommendations.**

1. **Remove reusable secrets.** Replace passwords with hardware-bound passkeys, and static API keys with short-lived, key-bound machine credentials.
2. **Verify, do not collect.** Check age, identity and eligibility with zero-knowledge proofs instead of storing documents and personal data.
3. **Bind sessions and tokens to hardware,** so that stolen cookies and tokens are useless elsewhere.
4. **Treat enrolment and recovery as cryptographic ceremonies,** not help-desk procedures.
5. **Make zero-knowledge presentation mandatory** in government digital identity systems, starting with the EU Digital Identity Wallet specifications TS13 and TS14.
6. **Build in post-quantum security from the start,** using ML-DSA and hash-based proof systems.
7. **Keep personal data with the user.** Servers store only commitments, and users prove facts from their own vaults. Where the law requires institutions to hold records, keep them in separate vaults that release one record at a time.
8. **Recognise "verify, do not collect" in regulation** as the state of the art for data minimisation and data protection by design.

The [full recommendations](#recommendations), with a phased timeline for service operators, come at the end.

## 1. Introduction: the age of machine-speed breaches

### A season of successive breaches

By late November 2026, major breach disclosures had arrived one after another.

- On 13 November the FBI warned that ARTEX, an open-source, multi-agent AI penetration-testing framework, had been used by a range of criminal groups since at least August 2026 against financial services, government, technology, healthcare and other sectors worldwide. Some intrusions progressed from initial targeting to privileged access in under three hours, and one supply-chain compromise of ConnectWise Cloud Backup led to attempted sign-ins to more than 2,250 downstream tenants [[1]](#ref-1).
- Barracuda Networks described a 44-hour intrusion, driven by ARTEX's planner and worker agents, that crossed about 10 cloud accounts on five platforms. It began with a cloud access key that Barracuda had given to a vendor for an earlier project and never retired. The attacker appears to have found it after compromising the vendor's development environment [[2]](#ref-2).
- India's CERT-In reported attackers using several AI offensive frameworks against non-bank lenders, digital lending platforms, fintechs, payment aggregators and cryptocurrency exchanges. The attackers entered through internet-exposed test and staging systems, moved into production using shared credentials and tokens, and validated unauthorised fund flows before executing large transfers [[3]](#ref-3).
- Attacks on South Korean financial institutions using ARTEX exfiltrated the personal information of about 67,000 people [[1]](#ref-1)[[4]](#ref-4).
- Amazon reported AI agents that found a previously unknown vulnerability on their own [[5]](#ref-5).
- The European Commission confirmed that an attack on its internal systems used an AI tool [[6]](#ref-6).
- A ransomware group whose malware framework showed code patterns of AI-assisted development compromised dozens of organisations in at least seven countries and territories across Europe, Asia and Africa [[7]](#ref-7)[[8]](#ref-8).

### An escalation that was announced in advance

None of this came without warning. In August 2025 Anthropic reported a criminal using its Claude Code agent to run a data-theft and extortion campaign against at least 17 organisations [[9]](#ref-9). In November 2025 it reported a campaign attributed to a Chinese state-sponsored group, in which AI agents performed most of the intrusion work against about 30 targets [[10]](#ref-10). Within a year, government advisories were naming multi-agent attack frameworks and tying them to incidents on several continents [[1]](#ref-1)[[3]](#ref-3). AI moved from the attacker's assistant to the attacker's workforce.

### The collapse of reaction time

Today's security operations assume there is a window to act. An alert fires, an analyst investigates, a stolen credential is revoked. AI swarms close that window. An adversary that can reach privileged access in under three hours, as the FBI observed [[1]](#ref-1), and attempt sign-ins at thousands of tenants at once, has already finished by the time the response begins.

On 19 November, former NSA hacker David Kennedy told CNN he was responding to an attack in which more than 30 cooperating AI agents logged in with a company's usernames and passwords, found a flaw in its two-factor authentication, exfiltrated data and deployed ransomware in 14 minutes. The company did not detect it for about four and a half hours [[11]](#ref-11). The account has not been independently verified and the victim has not been named [[12]](#ref-12), but it illustrates the problem exactly. Faster detection helps, but it is an arms race the defender cannot win outright: every gain in detection speed is matched by a gain in attack speed from the same technology.

### The constant beneath the change

What has *not* changed is where breaches begin. The FBI's analysis of ARTEX intrusions lists exposed cloud credentials and storage keys, compromised vendor credentials, hard-coded administrator credentials and forged authentication tokens among the ways attackers got in, alongside exploited application flaws [[1]](#ref-1). At Barracuda, it was a forgotten access key [[2]](#ref-2). In India, shared credentials and tokens carried the attackers from test systems into production [[3]](#ref-3). In the 2025 Verizon Data Breach Investigations Report, stolen credentials were the most common way attackers got in, involved in 22% of breaches [[13]](#ref-13). Two decades of history show the same pattern at human speed: plaintext and weakly hashed password databases, identity documents held in bulk, and credentials harvested by malware and reused (see [section 4](#4-the-evidence-two-decades-of-stealable-secrets)).

AI did not create this weakness. It made exploiting it cheap, fast and repeatable at scale. The right response is therefore not only to react faster. It is to change what a successful attacker finds.

> **Statement of position.** Public-facing services should be built so that a full compromise of their servers yields no usable personal data: only public keys, commitments, tokens and ciphertext. Zero-knowledge proofs, anchored in keys that never leave the device's secure hardware, make this possible today.

## 2. Basis for the position

The position rests on three premises.

**Premise 1: Attack speed now exceeds response speed.** Agentic AI lets attackers move through reconnaissance, credential use, escalation and exfiltration faster than human-centred detection and response can act (section 1).

**Premise 2: Breaches are profitable because servers hold something reusable.** The most common entry point is a stolen credential, and the most damaging payload is centrally stored identity data (section 4).

**Premise 3: Zero-knowledge architecture removes both from the server.** When a service verifies proofs instead of storing secrets and attributes, its databases contain only public material (sections 3 and 5).

**It follows that** zero-knowledge architecture is a defence whose effectiveness does not decline as attackers get faster. A public key is as useless to a swarm of a thousand agents working for a minute as to one human working for a year. It belongs alongside patching, segmentation, encryption and monitoring as a foundation of breach resilience, and it is the layer that holds even when those fail.

## 3. Foundations

### What a zero-knowledge proof guarantees

A zero-knowledge proof [[14]](#ref-14) lets a *prover* convince a *verifier* that a statement is true while revealing nothing else. It has three properties:

- **Completeness.** An honest prover always convinces the verifier.
- **Soundness.** A prover without the secret cannot convince the verifier, except with negligible probability.
- **Zero knowledge.** Everything the verifier sees could have been produced without the secret, so the verifier learns nothing it could reuse.

The third property is the key to breach resilience. If the verifier learns nothing reusable during a login, then the verifier's database, the thing attackers steal, contains nothing reusable either.

### Schnorr: the canonical proof of possession

Let $g$ generate a group of prime order $q$. The user holds a secret $x$, and the service stores only $y = g^x$ [[15]](#ref-15).

1. The prover sends a commitment $t = g^r$ for a random $r$.
2. The verifier sends a random challenge $c$.
3. The prover replies $s = r + cx \bmod q$.
4. The verifier accepts if $g^s = t \cdot y^c$.

The Fiat–Shamir transform [[16]](#ref-16) replaces the verifier's challenge with a hash, which yields a *signature scheme derived from a zero-knowledge proof*. Ed25519 is built this way. Modern public-key authentication, including FIDO2 passkeys [[17]](#ref-17), rests on the same principle even when it uses other signature algorithms such as ECDSA: the server checks a proof of possession against a public key and never holds the secret.

### ML-DSA: the same proof, made post-quantum

A server that stores Schnorr or elliptic-curve public keys is safe against today's attackers. A future quantum computer running Shor's algorithm [[18]](#ref-18) could, however, turn a public key stolen today into a private key later. The post-quantum standard ML-DSA (FIPS 204 [[19]](#ref-19)) closes this gap. It keeps the Schnorr structure and rebuilds it on lattice problems believed to resist quantum attack. Like Schnorr signatures, ML-DSA is a signature scheme derived, through the Fiat–Shamir transform, from a zero-knowledge identification protocol. It is the only one of NIST's post-quantum signature standards built this way: Falcon uses a lattice trapdoor, and SLH-DSA is built from hash functions.

| Year | Milestone |
|---|---|
| 1985 | Goldwasser, Micali and Rackoff define zero-knowledge proofs [[14]](#ref-14). |
| 1986 | Fiat and Shamir: practical identification, and the transform from proofs to signatures [[16]](#ref-16). |
| 1988 | Feige, Fiat and Shamir: zero-knowledge proofs of identity [[21]](#ref-21). |
| 1989–91 | Schnorr's identification and signature scheme for smart cards [[15]](#ref-15). |
| 1996 | Pointcheval and Stern prove Fiat–Shamir signatures secure [[22]](#ref-22). |
| 2009 | Lyubashevsky's *Fiat–Shamir with aborts* brings the Schnorr pattern to lattices [[23]](#ref-23). |
| 2012–13 | Practical lattice signatures without trapdoors [[24]](#ref-24), and BLISS [[25]](#ref-25). |
| 2017–18 | CRYSTALS-Dilithium [[26]](#ref-26); security against quantum adversaries analysed [[20]](#ref-20). |
| 2022 | NIST selects Dilithium for standardisation. |
| 2024 | NIST publishes FIPS 204, ML-DSA, on 13 August [[19]](#ref-19). |

In ML-DSA the secret is a pair of short vectors $\mathbf{s}_1, \mathbf{s}_2$ and the server stores $\mathbf{t} = \mathbf{A}\mathbf{s}_1 + \mathbf{s}_2$. The prover commits to the high bits of $\mathbf{A}\mathbf{y}$ for a short random mask $\mathbf{y}$, receives a sparse challenge $c$, and responds with $\mathbf{z} = \mathbf{y} + c\mathbf{s}_1$. The verifier checks that $\mathbf{z}$ is short and that $\mathrm{HighBits}(\mathbf{A}\mathbf{z} - c\mathbf{t})$ matches the commitment. One new step makes the lattice version zero-knowledge: *rejection sampling*. Any response that could leak information about the secret is discarded and the prover tries again, so released responses are independent of the key.[^mldsa]

| | Schnorr | ML-DSA |
|---|---|---|
| Server stores | $y = g^x$ | $\mathbf{t} = \mathbf{A}\mathbf{s}_1 + \mathbf{s}_2$ |
| Commitment | $g^r$ | $\mathrm{HighBits}(\mathbf{A}\mathbf{y})$ |
| Response | $r + cx$ | $\mathbf{y} + c\mathbf{s}_1$, with abort |
| Hides the secret by | Uniform mask | Rejection sampling |
| Hard problem | Discrete logarithm | MLWE / MSIS |
| Quantum computer | Breaks it | No known efficient attack |

For this paper the important point is simple. *The architecture does not change when the cryptography becomes post-quantum.* A stolen ML-DSA public key is as useless to a quantum-equipped attacker as a stolen Schnorr key is to a classical one.

### Beyond possession: proving attributes

Proofs of possession answer "is this the right user?". A second family answers "does this user meet the requirement?" without revealing who they are or anything else about them:

- **Anonymous credentials and selective disclosure** (Camenisch–Lysyanskaya [[27]](#ref-27), BBS signatures [[28]](#ref-28)) let a user prove "age ≥ 18" or "resident of this city" from an issuer-signed credential.
- **Succinct proofs** (zk-SNARKs [[29]](#ref-29), zk-STARKs [[30]](#ref-30), Bulletproofs [[31]](#ref-31)) prove arbitrary statements, such as "my income, signed by the tax authority, is above the threshold".
- **Proofs over existing government credentials.** Google open-sourced its Longfellow zero-knowledge libraries in July 2025 to prove age over ECDSA-signed mobile ID credentials [[32]](#ref-32). This shows the approach works with the credentials governments already issue.

## 4. The evidence: two decades of stealable secrets

The first table lists breaches whose damage came from a reusable secret or from bulk storage of identity data that a verification-only design would not have needed.

| Incident | Year | What was stolen | Under a zero-knowledge architecture |
|---|---|---|---|
| RockYou [[33]](#ref-33) | 2009 | ~32M passwords in plaintext | No passwords exist on the server |
| LinkedIn [[34]](#ref-34) | 2012 | >100M unsalted SHA-1 hashes, mostly cracked | Only public keys, nothing to crack |
| Adobe [[35]](#ref-35) | 2013 | ~150M passwords, "encrypted" with 3DES-ECB plus plaintext hints | No encrypted secrets to analyse |
| Yahoo [[36]](#ref-36) | 2013 | ~3B accounts, MD5 hashes, security questions | Only public keys; no knowledge-based recovery questions |
| Comeleak, Philippines [[37]](#ref-37) | 2016 | ~55M voter records, including passport data of overseas voters | Identity checks by proof against an issuer; no bulk copies |
| Marriott / Starwood [[38]](#ref-38) | 2014–18 | Up to ~339M guest records with passport numbers | Passport checks by proof; no numbers retained |
| Colonial Pipeline [[39]](#ref-39) | 2021 | One VPN password, no MFA; led to national fuel disruption | No password to leak |
| Optus [[40]](#ref-40) | 2022 | Up to ~9.8M customers' ID document numbers | ID verified by proof; numbers never stored |
| 23andMe [[41]](#ref-41) | 2023 | Reused passwords exposed ~6.9M users' genetic-relative data | Nothing to stuff: no passwords |
| Change Healthcare [[42]](#ref-42) | 2024 | Stolen remote-access credentials; ~193M people's health data | Entry point closed: hardware-bound, phishing-resistant login |
| Snowflake customers [[43]](#ref-43) | 2024 | Infostealer-harvested passwords used against cloud data stores | Malware finds no usable credential |

The second table shows that the AI-driven intrusions of 2026 started in the same place.

| Victim | Entry point | Under a zero-knowledge architecture |
|---|---|---|
| ConnectWise Cloud Backup and downstream tenants [[1]](#ref-1) | Supply-chain compromise; attempted sign-ins to 2,250+ downstream tenants | Tenant access requires proof of possession of hardware-held keys; nothing reusable passes through the supplier |
| Barracuda [[2]](#ref-2) | Unretired cloud access key held by a third-party vendor | No long-lived keys exist; workload identity is short-lived and federated |
| Indian financial institutions [[3]](#ref-3) | Exposed test systems, then shared credentials and tokens into production | Credentials and tokens bound to hardware keys; nothing shared to reuse |
| ARTEX intrusions generally [[1]](#ref-1) | Exposed cloud credentials, vendor credentials, hard-coded administrator credentials, forged SAML tokens, and application flaws | Credential paths closed; application flaws still need patching |
| Unnamed company (reported on CNN by D. Kennedy; not independently verified) [[11]](#ref-11) | Valid usernames and passwords, then a flaw in the two-factor implementation; 14 minutes from login to ransomware | No password to log in with and no code-based second factor to bypass; possession proven by hardware, bound to origin |

The lesson of both tables is the same. Attackers, human or machine, go where reusable secrets and bulk identity data are kept. Remove them, and the most common entry point and the most damaging payload both disappear.

## 5. The architecture: nine pillars

Zero-knowledge proofs are a tool, not a system. The pillars below turn them into an architecture in which every link of the authentication and verification chain resists theft at machine speed.

### Pillar 1: Keys are born in hardware

Every user and device secret is a high-entropy key generated inside a secure element, TPM or equivalent, and it never leaves. A key that was never chosen by a human cannot be guessed. A key that cannot be exported cannot be copied by malware or phished. The device unlocks it with a PIN or biometric whose attempts the hardware itself limits. This removes the weak link in password-derived schemes: the server's verifier is backed by real entropy, not by a memorable string an attacker can test offline.

### Pillar 2: Login is a proof of possession, bound to the site

Users log in by proving possession of their hardware key with FIDO2 passkeys [[17]](#ref-17). The browser binds every proof to the website's real origin, so a proof made on a look-alike phishing site is useless at the real one. The server stores only public keys. Where passwords must remain for a transition period, they are handled with OPAQUE (RFC 9807 [[44]](#ref-44)), so that even the server never sees them.

Adding a one-time code to a password is not enough. Codes sent by SMS or generated by an app are shared secrets checked by server logic, and they can be phished, relayed or bypassed through flaws in how the check is implemented, as the attack Kennedy described shows [[11]](#ref-11). A hardware-bound proof of possession has no password to steal and no code to intercept, and it cannot be replayed to another site.

### Pillar 3: Machines prove possession too

The 2026 incidents show that machine credentials are now the main target. Static API keys and long-lived bearer tokens are eliminated. Workloads use short-lived, federated identities; keys live in HSMs or TPMs; services authenticate with mutual TLS; and OAuth tokens are sender-constrained with DPoP [[45]](#ref-45), so a token is useless without the private key that signed the request. A long-lived key handed to a vendor and forgotten, as at Barracuda, simply does not exist in this model.

### Pillar 4: Verify, do not collect

Age, citizenship, residency, licensing, income bands and eligibility are proven with selective-disclosure credentials or zero-knowledge proofs over credentials from authoritative issuers. Websites request them through the W3C Digital Credentials API [[46]](#ref-46), and the user's wallet presents a proof bound to a key in the device's secure element. The service receives the answer to its question, such as *yes, over 18*, and nothing else. Single-use nullifiers allow rate limits, one-per-person rules and fraud controls without building a tracking database.

Services keep personal data only when they genuinely need it: because the law requires it, as with customer records at a bank or patient records at a hospital, or because the service cannot be delivered without it, as with a name and address for a delivery. That data is kept encrypted, segmented and minimised. Anything that is only ever *checked*, such as age, eligibility or an identity document number, is proven instead of collected.

### Pillar 5: Sessions are bound to devices

A secure login is wasted if the session cookie that follows it can be stolen and replayed, which is exactly what infostealer malware does. Sessions are bound to a hardware key using Device Bound Session Credentials [[47]](#ref-47), so a stolen cookie fails on any other machine. High-risk actions, such as payments, payee changes and credential changes, require a fresh proof of possession.

### Pillar 6: Enrolment and recovery are cryptographic ceremonies

Strong cryptography is only as strong as the process that binds a key to a person, and the process that replaces it. Enrolment is anchored to an authoritative issuer, such as a national ID system. Every account is enrolled on more than one device from the start, so losing one device does not require recovery. When recovery is needed, it uses threshold or multi-device approval with enforced waiting periods and notifications, never a password reset by phone or an SMS code. Help-desk overrides are privileged, logged and require multiple people. This closes the path that attackers use when the cryptography holds, as at MGM Resorts in 2023 [[48]](#ref-48).

### Pillar 7: Post-quantum from the start

New deployments plan for ML-DSA [[19]](#ref-19) for proofs of possession and for hash-based or lattice-based proof systems for attribute proofs, with algorithm agility built into protocols and devices. Public material collected today then stays useless even to a future quantum attacker. This goes beyond the EU's current zero-knowledge specifications, which still rest on ECDSA and pairing-based signatures (see [section 8](#8-precedent-and-pressure-the-eu-digital-identity-wallet)).

### Pillar 8: Assurance proportional to the stakes

Critical paths use standardised, audited constructions: WebAuthn, OPAQUE, ML-DSA, BBS and established credential formats. Custom proof circuits are used only where necessary and are independently audited and formally verified, avoiding the bug classes that history has already exposed [[49]](#ref-49)[[50]](#ref-50). Hardware is chosen from certified secure elements with a credible plan to revoke and reissue keys if a flaw is found, as Estonia did after ROCA [[51]](#ref-51). Defenders also turn the attackers' tools around: the same AI agents that find vulnerabilities are used to fuzz and audit circuits, verifiers and recovery flows before deployment.

### Pillar 9: Data lives with the user; the server holds commitments

The strongest form of the position moves personal data off the service entirely. The server stores only a cryptographic *commitment* to each user's data, and the data itself lives in the user's own vault: their digital wallet or secure element. The server becomes a verifier and never acts as a custodian.

**How it works.** At enrolment, an issuer or the service verifies the user's data once. The user's vault keeps the data together with the issuer's signature, and the server stores only a hiding commitment $C = \mathrm{Commit}(\mathit{data};\, r)$, such as a Pedersen commitment [[52]](#ref-52), where the random blinding value $r$ is known only to the user. When the service later needs a fact, such as whether the user is over 18, is eligible, or lives inside a delivery zone, the user's vault returns a zero-knowledge proof that the data inside $C$ satisfies the condition, and the server checks the proof against $C$.

A condition that would be a SQL `WHERE` clause over a central table, such as `birth_date <= '2008-11-22'`, becomes a circuit the user evaluates in their own vault, so the central table need not exist at all. When someone must actually *act* on the data, such as a courier delivering a parcel, the vault releases it at that moment, encrypted directly to that party.

(This is the same idea as the [range proof article](/range-proof-age-tutorial/): the age stays hidden inside a commitment, and only the proof that it satisfies the bound is shown.)

**Why a breach yields nothing.** A stolen set of hiding commitments reveals nothing about the data behind them. Pedersen commitments are *perfectly* hiding: even an attacker with unlimited computing power, including a quantum computer, learns nothing from them. This model also defeats the economics of an AI swarm. Breaching one database once yielded millions of records. Here there is no central store; the data is spread across millions of separate devices, and compromising one device exposes one user, an attack that does not scale.

**Design requirements.**

- **Blinding.** Every commitment uses a fresh random $r$. Without it, a commitment to a low-entropy value such as a birth year or postcode could be brute-forced by trying every value.
- **Truth, not just consistency.** A commitment binds the user to their data but does not prove it is true. The data must carry an issuer's signature, or the service signs the commitment after verifying the data once and then discards the data.
- **Freshness.** The server keeps only the latest commitment; an update replaces it, so a user cannot present an outdated record.
- **Post-quantum binding.** Pedersen hiding survives quantum attack, but its binding rests on discrete logarithms. Long-lived records use hash-based or lattice-based commitments.
- **Availability.** The vault is backed up in encrypted form under the user's control, synchronised across devices and recoverable through the ceremonies of Pillar 6.
- **The user is offline.** Where processing must happen without the user, such as billing or emergency medical access, the user grants scoped, pre-authorised delegation in advance.
- **Cross-user questions.** Statistics and fraud detection that need many users' data are answered by users contributing proofs or aggregates, using multi-party computation or differential privacy, for defined questions.

**Fallback: the institutional vault.** Some data must be held by the institution itself: a bank's customer records under anti-money-laundering law, a hospital's patient histories, a tax authority's filings. Such data never sits in plain form on a public-facing server; those servers hold only commitments, tokens and ciphertext. The real values live in a separate, hardened vault in its own trust zone, with keys held in an HSM. The vault releases one record at a time, for a stated purpose, under rate limits, logging and monitoring, so bulk extraction is blocked and any attempt is visible. Where the law requires retention but not routine access, records can instead be held in encrypted escrow that opens only with the agreement of several parties, such as the institution and a regulator, under legal process. An attacker who reaches an institutional vault can obtain records only slowly, one at a time, and in plain view, turning a silent mass breach into a small, detectable one.

**The vault answers questions, not records.** Most requests to an institutional vault do not need the data itself; they need an answer about it. A partner asks whether an account is in good standing, or whether a patient is covered for a procedure. The vault evaluates the condition and returns only the answer, as a signed yes or no or, where the partner must check it independently, as a zero-knowledge proof over a committed database. Systems that commit to a database at insert time and prove query results against that commitment, using query circuits prepared in advance, already exist [[53]](#ref-53)[[54]](#ref-54)[[55]](#ref-55). The same technique lets an institution prove aggregate facts to auditors or the public, such as "every account has completed identity checks" or "N citizens enrolled, by region", without releasing the underlying records.[^sql]

## 6. What an AI swarm finds

Assume the worst: an AI swarm achieves full administrative compromise of a public-facing service. This table compares what it can take.

| Asset | Conventional service | Zero-knowledge architecture |
|---|---|---|
| User credentials | Password hashes, crackable offline and reusable elsewhere | Public keys: no login, no cracking, no reuse |
| Identity documents | ID scans, passport and national ID numbers | None held; only yes/no proof results |
| Data only checked | Full dates of birth, incomes, eligibility records | None held; only the predicate the service needed |
| Machine credentials | API keys and long-lived tokens for partner systems | Short-lived tokens bound to keys the swarm cannot extract |
| Session cookies | Replayable from any machine | Fail away from the bound device |
| Recovery channel | Help-desk resets, SMS codes, security questions | Multi-party, time-delayed recovery with notification |
| Lateral movement | Stolen secrets open other systems and tenants | Nothing on the server opens anything else |
| Long-term value | Data reusable for years | Public material; post-quantum keys stay useless |
| Data the service must use (names, delivery addresses, account and health records) | Plaintext in databases, often kept indefinitely | Commitments only; the data stays in users' own vaults. Where the law requires the institution to hold it, a separate vault releases one record at a time (Pillar 9) |

The swarm's speed does not matter if there is nothing to take that can be used. For credentials and for data that is only checked, a breach becomes an availability and integrity incident to be repaired, not a confidentiality disaster that spreads through every system the stolen secrets can reach.

### What remains: data the service must use

Zero-knowledge architecture does not remove every piece of personal data from every institution. Under Pillar 9, data that only the user and the party acting on it need, such as a delivery address, stays in the user's own vault. But a bank must keep its customers' records under anti-money-laundering law, and a hospital its patients' histories. That data remains with the institution and can be exposed if its vault is breached. It is governed by data-protection law, which already requires that personal data be limited to what is necessary, kept no longer than needed, and protected by appropriate security measures such as encryption and pseudonymisation [[58]](#ref-58). Equivalent obligations exist in data-protection laws worldwide.

The architecture strengthens compliance with those obligations in two ways. First, it shrinks the data they apply to: whatever is only checked is never collected. Second, it changes what the remaining data is worth to an attacker. A stolen list of names and addresses is a real privacy harm, but without passwords, identity numbers or dates of birth beside it, it cannot be used to log in, take over accounts or impersonate anyone.

For the data that remains, established techniques reduce exposure further:

- **Field-level encryption with keys in an HSM.** Sensitive fields are decrypted only when needed, by a separate and tightly controlled service, so a stolen database dump is ciphertext. An attacker in full control of the application may still request decryption, so decryption is rate-limited, logged and monitored.
- **End-to-end encryption where the server need not read the data.** Messages, stored files and backups can be encrypted on the user's device, leaving the server with ciphertext it cannot decrypt.
- **Tokenisation.** Card and identity numbers are replaced with meaningless tokens, and the real values are kept in a separate, hardened vault.
- **Retention limits.** Data is deleted when it is no longer needed. Data that no longer exists cannot leak.
- **Segmentation.** Each category of data lives in its own isolated system, so one compromise does not reach everything.
- **Privacy-preserving computation.** For specific analytics, homomorphic encryption and multi-party computation allow results to be computed without exposing the underlying records.

Pillar 9 combines these into one design rule. The resulting claim is precise. Login secrets and checked attributes never reach the server. Data the service must use is minimised, protected as data-protection law requires, held in plain form only at the point of use and, if stolen, useless for impersonation.

## 7. Why now: every component exists

This is not a research agenda. Every pillar rests on published standards or shipping technology:

| Component | Status |
|---|---|
| Passkeys (FIDO2 / WebAuthn) | W3C Recommendation; supported by all major browsers and platforms [[17]](#ref-17) |
| OPAQUE password protocol | RFC 9807, July 2025 [[44]](#ref-44) |
| Post-quantum signatures | ML-DSA, FIPS 204, August 2024 [[19]](#ref-19) |
| Sender-constrained tokens | OAuth DPoP, RFC 9449, 2023 [[45]](#ref-45) |
| Digital credentials on the web | W3C Digital Credentials API, shipping in major browsers since 2025 [[46]](#ref-46) |
| ZK proofs over government ID | Google Longfellow ZK, open-sourced July 2025 [[32]](#ref-32) |
| Government ZK specifications | EUDI Wallet TS13 and TS14, 2026, going to ETSI [[59]](#ref-59)[[60]](#ref-60) |
| Device-bound sessions | DBSC, W3C draft with browser implementation [[47]](#ref-47) |
| Hardware key storage | Secure elements and TPMs in phones, laptops and security keys |

The economics also favour action. Breaches of stored secrets and identity data have produced some of the largest penalties on record, from the Equifax settlement of at least $575 million [[61]](#ref-61) to the UK fine against Marriott [[38]](#ref-38). Passkeys also cut password resets and help-desk load. Data a service never holds cannot appear in a breach notification, a regulator's fine or a class action.

## 8. Precedent and pressure: the EU Digital Identity Wallet

### Zero-knowledge proofs are already in the EU's design

The European Union is building the architecture this paper proposes at continental scale. Under the revised eIDAS regulation, every member state must offer its citizens a European Digital Identity (EUDI) Wallet by the end of 2026 [[62]](#ref-62). Zero-knowledge proofs are part of that design. The Architecture and Reference Framework has a dedicated discussion topic on them, which notes that certain privacy properties can only be achieved with ZKPs [[63]](#ref-63). In early 2026 the European Commission published two technical specifications for adding them to the wallet:

- **TS13: ZKPs based on arithmetic circuits** (v1.0.1, January 2026) [[59]](#ref-59). The wallet proves, inside a circuit, that it holds a valid ECDSA-signed credential in the existing mdoc or SD-JWT VC formats, and reveals only the attribute requested. Issuers need no changes. Device binding uses a signature from the wallet's secure cryptographic device (WSCD), which the circuit verifies; the proof library never accesses a secret key. No trusted setup is permitted. Reported performance for an `age_over_18` proof is about 800 ms on a mid-range smartphone, using Google's Longfellow library [[32]](#ref-32).
- **TS14: ZKPs based on multi-message signatures** (v1.0, February 2026) [[60]](#ref-60). Credentials are issued with BBS signatures that natively support unlinkable selective disclosure. Because today's secure elements support only ECDSA, TS14 defines two phases: a first phase using Schnorr-based device binding through the secure element's standard ECDSA interface, and a second phase with native BBS device binding once hardware supports it.

Both are described as exploratory and are being handed to ETSI for standardisation as ETSI TS 119 476-2. This is Pillars 1 and 4 of this paper, specified by a regulator for 27 countries: keys held in a secure element, attributes proven rather than collected.

### Specified, but not mandatory

Zero-knowledge proofs are not a mandatory part of the wallets due at the end of 2026. TS13 and TS14 are exploratory, not normative, and support for them in wallet software is optional. To meet the deadline with already-approved cryptography, the baseline privacy mechanism is instead *salted-hash selective disclosure with batch issuance*: the wallet receives many single-use copies of each credential, each with fresh hashes and its own signature, so that relying parties cannot link presentations by matching values [[62]](#ref-62). Observers had warned that ZKPs were unlikely to be required at this stage because implementations were still complex and lacked standardisation and certification audits [[64]](#ref-64).

Batch issuance has a specific weakness. It protects against linking by relying parties, but not by the issuer: if an issuer keeps its issuance records and obtains presentation records from relying parties, *through collusion or through a data leak*, it can trace presentations back to individual users [[62]](#ref-62)[[65]](#ref-65). Its privacy therefore rests on the assumption that issuer and relying-party databases do not leak.

### November 2026 changes the calculation

That assumption did not survive November 2026. In the month before the wallets' launch, AI-assisted intrusions reached financial institutions in India and South Korea, attempted sign-ins at thousands of cloud tenants through a single compromised supplier, and the European Commission's own internal systems [[1]](#ref-1)[[3]](#ref-3)[[6]](#ref-6). A privacy model whose guarantee ends at the first database breach is a weak foundation for an identity system that will be used by hundreds of millions of people for banking, health and government services.

The breaches therefore add pressure to make TS13 and TS14 a mandatory part of the deployment, not an optional extra. Each offers a practical path:

- **TS13 can be deployed first.** It needs no change to issuers or to the credentials the first wallets will already hold, and it works with today's secure elements. Age verification, where the EU is already piloting it, is the obvious starting point.
- **TS14 provides the longer-term solution.** Its first phase works with existing hardware, and its second phase gives native unlinkability that holds even if the issuer and relying parties collude or are breached.

Making zero-knowledge presentation mandatory, rather than relying on batch issuance, turns the wallet's privacy guarantee from a property of how carefully databases are guarded into a property of the mathematics. That is exactly the shift this paper argues for.

### The remaining gap: post-quantum

Neither specification yet addresses quantum attacks. TS13's hash-based proof systems may be quantum-resistant at the proof layer, but the ECDSA signatures of issuers and devices beneath them are not. TS14's BBS signatures rely on pairings, which are not quantum-resistant either. A complete post-quantum path combines ML-DSA [[19]](#ref-19) for issuer and device signatures with hash-based circuits for the zero-knowledge layer (Pillar 7). This is where the next generation of specifications, and other countries adopting the EU's model, can go further.

## 9. Recommendations {#recommendations}

AI swarms do not respect borders. A single compromised supplier led to attempted break-ins at thousands of tenants, and one ransomware campaign reached organisations across Europe, Asia and Africa [[1]](#ref-1)[[7]](#ref-7). A defence adopted only in some places leaves the weakest link exposed for everyone. The recommendations below are therefore addressed to every group of actors that shapes how public-facing services are built.

### For operators of public-facing services

1. **Remove reusable secrets (within 6 months).** Offer passkeys to all users and make them the default for staff and administrators. Inventory and remove static API keys and secrets from code repositories. Require phishing-resistant authentication on every remote-access and administrative portal. Bind high-risk sessions to devices.
2. **Stop collecting what is only checked (6 to 18 months).** Accept digital credentials for age, identity and eligibility checks. Delete stored copies of identity documents that are no longer needed or legally required. Move machine identity to short-lived, federated, sender-constrained tokens. Move personal data into users' own vaults, keeping only commitments on the server; data the institution must keep goes into a separate vault that releases one record at a time. Redesign account recovery as a multi-party ceremony.
3. **Make proofs the default and go post-quantum (18 to 36 months).** Introduce ML-DSA alongside existing algorithms. Extend zero-knowledge proofs to derived facts such as income bands and credit eligibility. Make "a full server compromise exposes no reusable secret" a measured, audited property of every public-facing service.

### For governments, regulators, standards bodies and industry

4. **Governments and identity issuers** should issue digital credentials bound to keys in secure hardware and support zero-knowledge presentation from the first release, not as a later option. Zero-knowledge presentation should be mandatory for high-risk uses such as age verification, financial onboarding and access to health and government services. In the EU, this means bringing TS13 and TS14 into the mandatory scope of the Digital Identity Wallet.
5. **Data-protection authorities** should recognise "verify, do not collect" as the state of the art for data minimisation and data protection by design, as required by the GDPR (Article 5(1)(c) and Article 25) [[58]](#ref-58) and by equivalent laws worldwide. They should give regulatory credit to services whose full breach would expose no reusable secrets, and expect breach notifications to state whether reusable secrets were exposed.
6. **Financial and sector regulators** should require phishing-resistant, hardware-bound authentication for customers, staff and administrators, and sender-constrained tokens for financial and open-banking APIs, where the 2026 intrusions moved money directly.
7. **Standards bodies** (ETSI, ISO, IETF, W3C, NIST) should complete the standards for zero-knowledge credentials, add ML-DSA to authentication and credential formats, and specify fully post-quantum zero-knowledge credentials.
8. **Platform and hardware vendors** should ship secure elements that support user verification inside the chip, post-quantum signatures, and the primitives that native zero-knowledge device binding requires, and should make device-bound sessions the default in browsers.
9. **Governments acting together** should agree on mutual recognition, so that a zero-knowledge proof from a credential issued in one jurisdiction can be verified in another, and should share intelligence on AI-driven attack frameworks as quickly as attackers share tools.

## 10. Conclusion

AI swarms have changed the speed of attack, and they will keep getting faster. Defenders cannot win a race measured in minutes by running faster alone. They can change what the race is for. The breaches of the last two decades, and the AI-driven breaches of 2026, began with something stored on a server that an attacker could take and use. Zero-knowledge architecture removes it. Keys are born in hardware and never leave. Logins and API calls are proofs of possession bound to their destination. Attributes are proven, not collected. Sessions, enrolment and recovery are held to the same standard. The cryptography is post-quantum from the start.

Under this architecture, a compromised server yields only public material that grants nothing, now or later. That is the one defence that becomes no weaker as attackers become faster. The standards exist, the technology ships, and the cost of waiting is already measured in billions of stolen records. Public-facing services should adopt it now.

[^mldsa]: Precisely: ML-DSA is the Fiat–Shamir transform of an honest-verifier zero-knowledge identification protocol, and that protocol is a *relaxed* proof of knowledge. From two accepting responses to the same commitment, an extractor obtains short vectors satisfying a related lattice equation, not necessarily the secret $\mathbf{s}_1$ itself. ML-DSA's security is proven by reduction to the MLWE and (Self-Target) MSIS problems, including against quantum adversaries [[20]](#ref-20).

[^sql]: Zero-knowledge circuits replace queries that *check* or *summarise*. They do not replace the database for data a service must store or process at high volume. Many verifiable-SQL systems also guarantee only that a query was executed correctly, not that rows outside the result stay hidden, and in all of them the prover still holds the data in plain form, so they protect data from the *querier*, not from a breach of the prover. Encrypted databases that run SQL over order- or equality-revealing ciphertext [[56]](#ref-56) have been shown to be vulnerable to inference attacks that recover much of the underlying data [[57]](#ref-57).

## References

<a id="ref-1"></a>[1] U.S. Federal Bureau of Investigation, "Threat Overview of ARTEX Autonomous Penetration Testing Framework," FBI FLASH-20261113-001, TLP:CLEAR, 13 November 2026. [ic3.gov](https://www.ic3.gov/CSA/2026/261113.pdf)

<a id="ref-2"></a>[2] A. Kjoelen, "Forty-Four Hours: Anatomy of a Machine-Speed Intrusion," Barracuda Blog, 11 November 2026. [blog.barracuda.com](https://blog.barracuda.com/2026/11/11/anatomy-of-a-machine-speed-intrusion)

<a id="ref-3"></a>[3] CERT-In, "Escalation of AI-Driven Attacks on BFSI Entities by Threat Actor CRAFTY FALCON," Advisory CIAD-2026-0058, 4 November 2026 (updated 22 November 2026). [cert-in.org.in](https://www.cert-in.org.in/s2cMainServlet?pageid=PUBVLNOTES02&VLCODE=CIAD-2026-0058)

<a id="ref-4"></a>[4] BleepingComputer, "Hacker Used ARTEX AI and Claude Agents to Target South Korean Banks," November 2026. [bleepingcomputer.com](https://www.bleepingcomputer.com/news/security/hacker-used-artex-ai-and-claude-agents-to-target-south-korean-banks/)

<a id="ref-5"></a>[5] Amazon Threat Intelligence, "Chinese-Nexus Actor's Cairn AI Agents Found a Zero-Day on Their Own," AWS Security Blog, 20 November 2026. [aws.amazon.com](https://aws.amazon.com/blogs/security/chinese-nexus-actors-cairn-ai-agents-found-a-zero-day-on-their-own/)

<a id="ref-6"></a>[6] A. Martin, "European Commission Confirms Cyberattack, Says Hackers Used AI Tool," The Record by Recorded Future News, 19 November 2026. [therecord.media](https://therecord.media/european-commission-confirms-cyberattack-using-ai)

<a id="ref-7"></a>[7] Kaspersky, "Kaspersky Uncovers AuditTeam Ransomware Campaign Spanning Europe, Asia and Africa," 18 November 2026. [kaspersky.com](https://www.kaspersky.com/about/press-releases/kaspersky-uncovers-auditteam-ransomware-campaign-spanning-europe-asia-and-africa)

<a id="ref-8"></a>[8] V. Nestor (Kaspersky GReAT), "The Forced Audit: How AuditTeam's OpsAgent Framework Masks Ransomware as Consulting," Securelist, 18 November 2026. [securelist.com](https://securelist.com/auditteam-opsagent-forced-audit/122031/)

<a id="ref-9"></a>[9] Anthropic, "Detecting and Countering Misuse of AI: August 2025," 27 August 2025. [anthropic.com](https://www.anthropic.com/news/detecting-countering-misuse-aug-2025)

<a id="ref-10"></a>[10] Anthropic, "Disrupting the First Reported AI-Orchestrated Cyber Espionage Campaign," 13 November 2025. [anthropic.com](https://www.anthropic.com/news/disrupting-AI-espionage)

<a id="ref-11"></a>[11] IBTimes UK, "Living on Borrowed Time: Cybersecurity Journalist Warns After AI Agents Breach Company Systems in 14 Minutes" (reporting D. Kennedy's interview on CNN's *Erin Burnett OutFront*, 19 November 2026), November 2026. [ibtimes.co.uk](https://www.ibtimes.co.uk/ai-attack-bypassed-two-factor-authentication-14-minutes-1833605)

<a id="ref-12"></a>[12] Morphisec, "A 14-Minute AI Attack Shows Fast Response Doesn't Help When the Attack Is Faster," press release, EIN Presswire, November 2026. [einnews.com](https://www.einnews.com/pr_news/958228846/a-14-minute-ai-attack-shows-fast-response-doesn-t-help-when-the-attack-is-faster)

<a id="ref-13"></a>[13] Verizon, *2025 Data Breach Investigations Report*. [verizon.com](https://www.verizon.com/business/resources/articles/credential-stuffing-attacks-2025-dbir-research/)

<a id="ref-14"></a>[14] S. Goldwasser, S. Micali, C. Rackoff, "The Knowledge Complexity of Interactive Proof Systems," *SIAM Journal on Computing*, 18(1):186–208, 1989.

<a id="ref-15"></a>[15] C. P. Schnorr, "Efficient Signature Generation by Smart Cards," *Journal of Cryptology*, 4(3):161–174, 1991.

<a id="ref-16"></a>[16] A. Fiat, A. Shamir, "How to Prove Yourself: Practical Solutions to Identification and Signature Problems," *CRYPTO 1986*, LNCS 263, pp. 186–194.

<a id="ref-17"></a>[17] W3C, "Web Authentication: An API for Accessing Public Key Credentials," W3C Recommendation.

<a id="ref-18"></a>[18] P. W. Shor, "Algorithms for Quantum Computation: Discrete Logarithms and Factoring," *FOCS 1994*, pp. 124–134.

<a id="ref-19"></a>[19] National Institute of Standards and Technology, *Module-Lattice-Based Digital Signature Standard*, FIPS 204, 13 August 2024. [doi.org/10.6028/NIST.FIPS.204](https://doi.org/10.6028/NIST.FIPS.204)

<a id="ref-20"></a>[20] E. Kiltz, V. Lyubashevsky, C. Schaffner, "A Concrete Treatment of Fiat–Shamir Signatures in the Quantum Random-Oracle Model," *EUROCRYPT 2018*, LNCS 10822.

<a id="ref-21"></a>[21] U. Feige, A. Fiat, A. Shamir, "Zero-Knowledge Proofs of Identity," *Journal of Cryptology*, 1(2):77–94, 1988.

<a id="ref-22"></a>[22] D. Pointcheval, J. Stern, "Security Proofs for Signature Schemes," *EUROCRYPT 1996*, LNCS 1070.

<a id="ref-23"></a>[23] V. Lyubashevsky, "Fiat–Shamir with Aborts: Applications to Lattice and Factoring-Based Signatures," *ASIACRYPT 2009*, LNCS 5912, pp. 598–616.

<a id="ref-24"></a>[24] V. Lyubashevsky, "Lattice Signatures without Trapdoors," *EUROCRYPT 2012*, LNCS 7237.

<a id="ref-25"></a>[25] L. Ducas, A. Durmus, T. Lepoint, V. Lyubashevsky, "Lattice Signatures and Bimodal Gaussians," *CRYPTO 2013*, LNCS 8042.

<a id="ref-26"></a>[26] L. Ducas, E. Kiltz, T. Lepoint, V. Lyubashevsky, P. Schwabe, G. Seiler, D. Stehlé, "CRYSTALS-Dilithium: A Lattice-Based Digital Signature Scheme," *IACR TCHES*, 2018(1):238–268.

<a id="ref-27"></a>[27] J. Camenisch, A. Lysyanskaya, "An Efficient System for Non-transferable Anonymous Credentials with Optional Anonymity Revocation," *EUROCRYPT 2001*, LNCS 2045.

<a id="ref-28"></a>[28] D. Boneh, X. Boyen, H. Shacham, "Short Group Signatures," *CRYPTO 2004*, LNCS 3152.

<a id="ref-29"></a>[29] J. Groth, "On the Size of Pairing-Based Non-interactive Arguments," *EUROCRYPT 2016*, LNCS 9666.

<a id="ref-30"></a>[30] E. Ben-Sasson, I. Bentov, Y. Horesh, M. Riabzev, "Scalable, Transparent, and Post-Quantum Secure Computational Integrity," IACR ePrint 2018/046.

<a id="ref-31"></a>[31] B. Bünz, J. Bootle, D. Boneh, A. Poelstra, P. Wuille, G. Maxwell, "Bulletproofs: Short Proofs for Confidential Transactions and More," *IEEE S&P 2018*.

<a id="ref-32"></a>[32] Google, "Opening Up Zero-Knowledge Proof Technology to Promote Privacy in Age Assurance," The Keyword (Google blog), 3 July 2025. [blog.google](https://blog.google/innovation-and-ai/technology/safety-security/opening-up-zero-knowledge-proof-technology-to-promote-privacy-in-age-assurance/)

<a id="ref-33"></a>[33] Imperva, "Imperva Releases Detailed Analysis of 32 Million Breached Consumer Passwords," 21 January 2010. [imperva.com](https://www.imperva.com/company/press_releases/imperva-releases-detailed-analysis-of-32-million-breached-consumer-passwords/)

<a id="ref-34"></a>[34] LinkedIn Corporation, "Protecting Our Members," May 2016 (regarding the 2012 breach).

<a id="ref-35"></a>[35] Adobe Systems, customer security alert, October 2013; see also P. Ducklin, "Anatomy of a Password Disaster: Adobe's Giant-Sized Cryptographic Blunder," Sophos Naked Security, November 2013.

<a id="ref-36"></a>[36] Yahoo / Oath, "Yahoo Provides Notice to Additional Users Affected by Previously Disclosed 2013 Data Theft," 3 October 2017.

<a id="ref-37"></a>[37] National Privacy Commission (Philippines), "Privacy Commission Finds Bautista Criminally Liable for Comeleak Data Breach," 5 January 2017 (NPC Case No. 16-001). [privacy.gov.ph](https://privacy.gov.ph/privacy-commission-finds-bautista-criminally-liable-for-comeleak-data-breach/)

<a id="ref-38"></a>[38] UK Information Commissioner's Office, Penalty Notice, Marriott International Inc., 30 October 2020 (£18.4 million). [ico.org.uk](https://ico.org.uk/media2/migrated/2618524/marriott-international-inc-mpn-20201030.pdf)

<a id="ref-39"></a>[39] J. Blount (CEO, Colonial Pipeline), testimony before the U.S. Senate Committee on Homeland Security and Governmental Affairs, 8 June 2021. [hsgac.senate.gov](https://www.hsgac.senate.gov/wp-content/uploads/imo/media/doc/Testimony-Blount-2021-06-08.pdf)

<a id="ref-40"></a>[40] Optus, customer notices on the September 2022 cyberattack; Office of the Australian Information Commissioner proceedings.

<a id="ref-41"></a>[41] 23andMe, "Addressing Data Security Concerns – Action Plan," updated 5 December 2023.

<a id="ref-42"></a>[42] U.S. Department of Health and Human Services, Office for Civil Rights, "Change Healthcare Cybersecurity Incident Frequently Asked Questions," updated 2025. [hhs.gov](https://www.hhs.gov/hipaa/for-professionals/special-topics/change-healthcare-cybersecurity-incident-frequently-asked-questions/index.html)

<a id="ref-43"></a>[43] Mandiant (Google Cloud), "UNC5537 Targets Snowflake Customer Instances for Data Theft and Extortion," 10 June 2024. [cloud.google.com](https://cloud.google.com/blog/topics/threat-intelligence/unc5537-snowflake-data-theft-extortion)

<a id="ref-44"></a>[44] D. Bourdrez, H. Krawczyk, K. Lewi, C. A. Wood, "The OPAQUE Augmented Password-Authenticated Key Exchange (aPAKE) Protocol," RFC 9807, July 2025. [rfc-editor.org](https://www.rfc-editor.org/info/rfc9807/)

<a id="ref-45"></a>[45] D. Fett, B. Campbell, J. Bradley, T. Lodderstedt, M. Jones, D. Waite, "OAuth 2.0 Demonstrating Proof of Possession (DPoP)," RFC 9449, 2023.

<a id="ref-46"></a>[46] W3C, "W3C Digital Credentials API Publication: The Next Step to Privacy-Preserving Identities on the Web," W3C blog, 2025. [w3.org](https://www.w3.org/blog/2025/w3c-digital-credentials-api-publication-the-next-step-to-privacy-preserving-identities-on-the-web/)

<a id="ref-47"></a>[47] W3C WebAppSec, "Device Bound Session Credentials," Editor's Draft. [w3c.github.io](https://w3c.github.io/webappsec-dbsc/)

<a id="ref-48"></a>[48] CISA and FBI, "Scattered Spider," Joint Cybersecurity Advisory AA23-320A, November 2023. [cisa.gov](https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-320a)

<a id="ref-49"></a>[49] Electric Coin Company, "Zcash Counterfeiting Vulnerability Successfully Remediated," February 2019 (CVE-2019-7167). [nvd.nist.gov](https://nvd.nist.gov/vuln/detail/cve-2019-7167)

<a id="ref-50"></a>[50] Trail of Bits, "Coordinated Disclosure of Vulnerabilities Affecting Girault, Bulletproofs, and PlonK," 13 April 2022. [blog.trailofbits.com](https://blog.trailofbits.com/2022/04/13/part-1-coordinated-disclosure-of-vulnerabilities-affecting-girault-bulletproofs-and-plonk/)

<a id="ref-51"></a>[51] Estonian Information System Authority (RIA), "ROCA Vulnerability and eID: Lessons Learned," 2018. [ria.ee](https://www.ria.ee/sites/default/files/documents/2022-11/Roca-vulnerability-and-eID-lessons-learned-2018.pdf)

<a id="ref-52"></a>[52] T. P. Pedersen, "Non-Interactive and Information-Theoretic Secure Verifiable Secret Sharing," *CRYPTO 1991*, LNCS 576, pp. 129–140.

<a id="ref-53"></a>[53] Y. Zhang, D. Genkin, J. Katz, D. Papadopoulos, C. Papamanthou, "vSQL: Verifying Arbitrary SQL Queries over Dynamic Outsourced Databases," *IEEE Symposium on Security and Privacy*, 2017.

<a id="ref-54"></a>[54] "PoneglyphDB: Efficient Non-interactive Zero-Knowledge Proofs for Arbitrary SQL-Query Verification," arXiv:2411.15031, 2024. [arxiv.org](https://arxiv.org/abs/2411.15031)

<a id="ref-55"></a>[55] Space and Time, "Proof of SQL," open-source zero-knowledge prover for SQL queries. [github.com](https://github.com/spaceandtimefdn/sxt-proof-of-sql)

<a id="ref-56"></a>[56] R. A. Popa, C. M. S. Redfield, N. Zeldovich, H. Balakrishnan, "CryptDB: Protecting Confidentiality with Encrypted Query Processing," *ACM SOSP*, 2011.

<a id="ref-57"></a>[57] M. Naveed, S. Kamara, C. V. Wright, "Inference Attacks on Property-Preserving Encrypted Databases," *ACM CCS*, 2015.

<a id="ref-58"></a>[58] European Parliament and Council, Regulation (EU) 2016/679 (General Data Protection Regulation), Articles 5(1)(c) (data minimisation), 5(1)(e) (storage limitation), 25 (data protection by design) and 32 (security of processing).

<a id="ref-59"></a>[59] European Commission, "Specification for the Implementation of Zero-Knowledge Proofs Based on Arithmetic Circuits in the EUDI Wallet" (TS13), v1.0.1, 30 January 2026. [github.com](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts13-zksnarks.md)

<a id="ref-60"></a>[60] European Commission, "Specification for the Implementation of Zero-Knowledge Proofs Based on Multi-Message Signatures in the EUDI Wallet" (TS14), v1.0, 27 February 2026. [github.com](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts14-zkps-from-mms.md)

<a id="ref-61"></a>[61] U.S. Federal Trade Commission, "Equifax to Pay $575 Million as Part of Settlement with FTC, CFPB, and States Related to 2017 Data Breach," 22 July 2019. [ftc.gov](https://www.ftc.gov/news-events/news/press-releases/2019/07/equifax-pay-575-million-part-settlement-ftc-cfpb-states-related-2017-data-breach)

<a id="ref-62"></a>[62] Hasso Plattner Institute, "EUDI Wallet: What Is Possible in Terms of Privacy, and Where the Limits Lie" (interview with A. Lehmann), 30 October 2026. [hpi.de](https://hpi.de/en/article/eudi-wallet-what-is-possible-in-terms-of-privacy-and-where-the-limits-lie/)

<a id="ref-63"></a>[63] European Commission, EUDI Wallet Architecture and Reference Framework, Discussion Topic G: Zero Knowledge Proof (ARF v3.0.0). [github.io](https://eu-digital-identity-wallet.github.io/eudi-doc-architecture-and-reference-framework/3.0.0/discussion-topics/g-zero-knowledge-proof/)

<a id="ref-64"></a>[64] E. Podda, P. Hölzmer, A. Amard, G. Fridgen, J. Sedlmeir, "The Impact of Zero-Knowledge Proofs on Data Minimization Compliance of Digital Identity Wallets," *Internet Policy Review*, 2025. [policyreview.info](https://policyreview.info/articles/analysis/impact-zero-knowledge-proofs)

<a id="ref-65"></a>[65] ETSI, TR 119 476-1 V1.3.1, "Selective Disclosure and Zero-Knowledge Proofs Applied to Electronic Attestation of Attributes; Part 1: Feasibility Study." [etsi.org](https://www.etsi.org/deliver/etsi_tr/119400_119499/11947601/01.03.01_60/tr_11947601v010301p.pdf)
