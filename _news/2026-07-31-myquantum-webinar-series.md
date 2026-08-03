---
layout: post
title:  "QCSP, MyQuantum Open Webinar Series with Focus on Post-Quantum Digital Trust"
date:   2026-07-31 00:00:00 -0800
author: qcsp
image: assets/images/elija_majik.jpg
categories: News
comments: false
---
**Filipino software developer Joseph Elijah de los Santos Fabian presented tools designed to help users verify the authenticity of digital files and identities amid growing concerns over deepfakes, online impersonation, and future quantum-computing threats.**

The Quantum Computing Society of the Philippines (QCSP) and the MyQuantum Extension Project formally opened the MyQuantum webinar series on July 31, 2026, with a session examining how post-quantum cryptography could protect digital work, identity, and reputation.

| ![](/assets/images/elija_majik.jpg) |
|:--:|
| *Joseph Elijah de los Santos Fabian presents Magic Signature and Magic Universal ID during the MyQuantum webinar.* |

The online kickoff featured Joseph Elijah de los Santos Fabian, founder and lead software engineer of Magica Solutions OPC, who introduced Magic Signature and Magic Universal ID—tools he said were developed to make cryptographic verification more accessible to individuals, freelancers, creative professionals, businesses, and institutions.

In his opening remarks, QCSP President Bobby Corpus Jr. described the organization as a volunteer-led nonprofit civic society working to broaden quantum education and awareness throughout the country. He said QCSP wants opportunities in quantum computing to reach Filipinos regardless of their location, school, or professional background.

"We want the entire Philippines to benefit, not just a few quantum experts here in Metro Manila," Corpus said during the webinar. He emphasized a whole-of-nation approach connecting government, academe, industry, educators, researchers, students, and volunteers.

Corpus also highlighted QCSP's lecture series, hackathons, workshops, educator training, and regional partnerships. He noted that the organization is a founding member of the Southeast Asia Quantum Network, which brings together quantum communities from the Philippines, Singapore, Malaysia, Indonesia, Thailand, and Vietnam.

## Verifying files in an age of AI manipulation

Fabian's presentation centered on an increasingly difficult question: how can people prove that a document, photograph, video, recording, or other digital file is authentic and unchanged?

He said generative artificial intelligence, deepfakes, and voice-cloning technologies have made it easier to manipulate content and impersonate public figures, journalists, creators, and ordinary users. Digital signatures, he explained, can provide recipients with a way to check a file's source and integrity instead of relying solely on visual inspection or reputation.

Magic Signature was presented as a hybrid file-signing application that combines the classical Ed25519 digital-signature algorithm with ML-DSA-87, a post-quantum signature standard. According to Fabian, the software can sign multiple file formats—including documents, images, audio, video, archives, and software files—and embed the signature in the file itself rather than require a separate signature file.

Fabian said signing and verification can be performed locally, allowing private keys to remain on the user's device. Internet access is only needed for optional online services such as trusted timestamps. The application also includes visual stamps for supported documents, batch processing, command-line controls, and integration options intended for automated organizational workflows.

He positioned the hybrid design as preparation for "harvest now, forge later" risks, in which an attacker could retain traditionally signed material and attempt to forge it once sufficiently capable quantum computers become available. However, Fabian acknowledged that no security system should be described as permanently unbreakable and that cryptographic protection must continue to evolve.

## Linking a signature to a verified identity

The second tool, Magic Universal ID, is intended to connect signed files to a public identity profile. Fabian said users may undergo government-ID verification, a liveness check, facial matching, and IP analysis through a third-party identity-verification provider.

When used together, Magic Signature and Magic Universal ID are designed to let a recipient check both whether a file has been altered and whether it is associated with the claimed signer. Fabian cited possible uses for journalists, public officials, influencers, photographers, filmmakers, lawyers, researchers, schools, businesses, and industrial systems.

He also demonstrated how the tools could support media-production pipelines, certificate verification, academic records, contracts, and other workflows where unauthorized changes could create disputes. A separate experimental feature for signing online links remains under development, he said.

During the question-and-answer session, participants raised concerns about screenshots, AI-edited images, online-platform compression, AES-256 security, and the Philippines' readiness for post-quantum cryptography. Fabian explained that altering or recompressing a signed file would change its underlying data and cause verification to fail, while a trusted timestamp could help establish when an original was signed.

On national readiness, Corpus said Philippine organizations remain largely in the planning stage. He cited an earlier quantum-readiness workshop attended by representatives from banking, telecommunications, government, industry, and academe as part of ongoing efforts to develop awareness and coordination.

The organizers also announced upcoming QCSP activities, including an IEEE-supported Quantum Roadshow scheduled for August 17 to 21 and the Quantum Information Science and Technology Conference, or QISTCon, set for November 9 to 11, 2026, in Cebu City.

The webinar concluded with QCSP expressing interest in using open-source signing technology to authenticate and verify digital certificates. Organizers encouraged further collaboration as the Philippines builds its quantum-technology community and prepares institutions for emerging cybersecurity challenges.
