---
layout: post
title:  "Recap: Michele Mosca on Why Quantum-Safe Cryptography Cannot Wait"
date:   2026-10-03 00:30:00 +0800
author: qcsp
image: assets/images/mosca_fireside_chat.png
categories: News
comments: false
---
On **September 18, 2026**, the Quantum Computing Society of the Philippines (QCSP) joined the **Quantum Ecosystems & Technology Council of India (QETCI)** for a fireside chat with **Dr. Michele Mosca** on quantum security. It was a special edition of QETCI's monthly *Quantum Pulse* series and the first one the council has run in partnership with an organization in another country, bringing together audiences from India and the Philippines on Zoom and YouTube.

Dr. Mosca is a professor at the University of Waterloo, a co-founder of the Institute for Quantum Computing, and the co-founder, President, and CEO of evolutionQ. He is also the originator of **Mosca's Theorem**, the simple inequality that many organizations now use to decide when to start migrating to post-quantum cryptography. The conversation was moderated by **Bobby Corpus Jr.**, President of QCSP, and **Reena Dayal Yadav**, Founder and CEO of QETCI.

<div class="embed-responsive embed-responsive-16by9 mb-4">
<iframe class="embed-responsive-item" src="https://www.youtube.com/embed/RhRbQY91A2M" title="QETCI Quantum Pulse - Fireside Chat with Michele Mosca" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

*The full recording, hosted on QETCI's YouTube channel.*

## Introducing QCSP to a New Audience

Bobby opened by introducing QCSP to the Indian audience and to many Filipino viewers: a society working to help make the Philippines a quantum-ready country by bringing together academe, industry, government, and international partners. He described QCSP's yearly lecture series on quantum fundamentals, its train-the-trainers program for university professors, and its hackathons. He also noted that QCSP is now moving "upstream" to introduce quantum and computing concepts in high school and elementary school.

## From Skeptic to Translator

Dr. Mosca shared that he came to quantum reluctantly. As a mathematics student working on the classical cryptanalysis of public-key cryptography, he first dismissed quantum computing as science fiction, even after meeting people working on quantum key distribution and Shor's algorithm. Three realizations changed his mind: quantum computers change what counts as a fundamentally hard problem, atoms can in fact be trapped and controlled, and quantum error correction means the engineering obstacles can be overcome. Since then, he says, he has served as a kind of translator between the cryptography and quantum communities for more than 30 years.

## The Dilemma, and Mosca's Theorem

Dr. Mosca framed quantum security as the latest case of an old pattern: a new capability that can be weaponized is ignored until it starts causing harm. The people responsible for security are busy with today's fires, and the people building the technology are focused on building it. The result is that risk mitigation moves far slower than risk creation. Quantum computing matters because it threatens the cryptography that underpins trust in the digital economy, from confidentiality to the authenticity of software updates.

Mosca's Theorem puts the problem in terms of timing: if the time your data must stay confidential (X) plus the time your systems need to migrate (Y) exceeds the time until a cryptographically relevant quantum computer arrives (Z), you are already in trouble. He said he came up with it as a way to reach a small group of people who protect long-lived, high-impact assets, and that he never expected it to be so widely adopted. In a Q&A answer he added that the shelf life and migration time are largely within an organization's control, while the likelihood and impact of an attack feed into a normal risk assessment. Organizations with low risk tolerance and very high impact should act first.

## Four Postures Toward the Quantum Threat

Dr. Mosca described four postures that governments and organizations take:

1. **Wait and see**, where nearly everyone was five years ago.
2. **Quantum readiness theater**, which is many activities, such as proofs of concept and a cryptographic inventory, that never become a serious mitigation program. He cautioned against using the inventory as a reason to delay, since organizations already know their most critical dependencies, such as firmware signing.
3. **A serious post-quantum cryptography (PQC) migration program**, with timelines and priorities. This is where more and more governments are heading.
4. **Cryptographic resilience**, which he called the only sustainable end state: re-architecting cryptography so that if one trust assumption fails, the damage is detectable, recoverable, and bounded, much as multi-factor authentication does for passwords.

He said agility alone is not enough, because switching only helps if there is something diverse and trustworthy to switch to, and defense in depth is needed where advance warning cannot be assumed.

## Advice for Countries Like the Philippines and India

Asked what developing and middle-power countries should do first, Dr. Mosca urged them to drop the assumption that they will not be targeted. Cybercrime today is driven by return on investment, and well-protected giants are harder targets than less-prepared ones. His practical steps:

- Run a top-down quantum risk assessment now. It takes weeks or months, not years.
- Understand how cryptographic modernization actually gets done in your country, including who is in charge, because often no one is.
- Set up a team with a clear mandate, executive support, and its own resources.
- Learn from others, including the Canadian Forum for Digital Infrastructure Resilience, the Quantum Safe Financial Forum, FS-ISAC, the World Economic Forum, and peers in the telecom sector.

## AI and Quantum

On the relationship between AI and quantum computing, Dr. Mosca described it as asymmetrical. AI is already helping quantum computing through better qubit design, pulse control, error-correcting codes, compilation, and algorithm optimization, and it will likely help find useful applications. It will also help attackers discover weaknesses, which makes cryptographic resilience a necessity rather than a nice-to-have. In the other direction, he said, there is no evidence that quantum computing will make all AI vastly more powerful, though he expects it to help with specific building blocks. On AI-assisted quantum attacks, his message was that there is nothing to fear if the defensive side is prepared, and that the fix is known. His worry is that it will not be put in place in time. He also said that AI agents and AI models themselves are becoming a new attack surface that needs resilient security built in from the start.

## Can the Post-Quantum Algorithms Be Trusted?

The conversation also went into the **dihedral coset problem**, which underlies lattice-based cryptography. Dr. Mosca explained why it is believed to be hard and why a better algorithm would force larger keys or force some schemes out of use, rather than end cryptography altogether. He noted that recent serious attempts to attack it did not succeed, and that this is how confidence in these schemes is built. He said no one should expect rigorous proofs that breaking a code is hard, only a narrowing of the possible attacks. He pointed to the need for diversity, agility, and defense in depth in case any one assumption fails.

## Red Teams, Disclosure, and Careers

- **Red-teaming quantum attacks.** Because broken cryptography is invisible to the victim, Dr. Mosca called for stronger vendor disclosure expectations and for penetration testers and cryptographers to work together on realistic attack playbooks, repeating the exercise until the scenarios converge. He compared the likely progression to other technologies: first replacing parts of what attackers already do, then reinventing their playbooks.
- **Cybersecurity or quantum first?** Both paths work. Most quantum-security work needs little quantum physics, and the hardest part is often the human one: translating risk for executives and boards and managing change. He urged scientists to value communicators, as long as they remain faithful and do not exaggerate.
- **Specialize early?** Rather than choosing a narrow degree too soon, he suggested a strong traditional discipline with a specialization on top, and advised students to look for the overlap among what they love, what they are good at, and what has impact.

## Audience Questions

Participants from India and the Philippines used the live Q&A to ask how Mosca's Theorem connects to Q-Day preparedness, how frameworks such as OWASP's work on quantum security can build effective testing methods, how to protect autonomous AI agent systems, whether businesses should also explore opportunities beyond PQC and QKD, such as multi-party computation and homomorphic encryption, and how entanglement relates to security. On the last two, Dr. Mosca said the time is right to explore these capabilities as billions are invested in quantum networking, and that entanglement-based protocols are often easier to validate against physical security assumptions, even though prepare-and-measure QKD can establish keys without entanglement.

## Thank You

Bobby closed by thanking Dr. Mosca for his insights and everyone who joined. QCSP thanks **QETCI** and **Reena Dayal Yadav** for the partnership and for hosting the session, and looks forward to more joint work between the Philippine and Indian quantum communities.

Watch the full conversation in the video above, and see the [Events page](/events/) for QCSP's upcoming activities.
