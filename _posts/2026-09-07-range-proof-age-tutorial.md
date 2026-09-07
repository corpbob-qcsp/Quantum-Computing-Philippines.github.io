---
layout: post
title:  "How to Prove You Are Above 15 Without Revealing Your Age"
author: earl
image: assets/images/123rf-cryptography.png
categories: Cryptography
comments: false
---

*This is an introductory, non-mathematical-heavy explanation of a real cryptographic possibility: a person can prove the statement "my age is above 15" without revealing their name, exact age, or date of birth to the verifier. The article uses analogies first and a small amount of algebra second. It places the idea in a Philippine National ID and Meta authorization context, separates authorization from age validation, addresses common misinformation (FUD), and identifies deployed systems that use related zero-knowledge or selective-disclosure protocols. The Philippine integration described here is a design pattern, not a claim that the current PhilSys production service already implements this exact range proof.*

## The question this paper answers

Imagine a service that needs only one answer:

$$
\text{Is the presenter above 15?}
$$

The conventional answer is to show a National ID, passport, or birth date. That is usually too much information. It may expose a person's full name, PhilSys number, address, photograph, date of birth, and other attributes when the service needs only a yes-or-no age result.

A zero-knowledge proof (ZKP) offers a different conversation:

> Presenter: "I can prove that my age is above 15."<br>
> Verifier: "Show me the proof."<br>
> Presenter: sends a proof.<br>
> Verifier: "TRUE: the bound was satisfied."

The verifier need not learn who the presenter is or the presenter's exact age, provided that identity is not separately required by the service and that the credential and protocol are designed to avoid linkability.

In this paper, "above 15" means $a>15$, so the smallest accepted age is 16. If a policy means "15 or older", replace the predicate with $a\geq15$. That one-character policy difference matters in a real deployment.

## Three characters in the story

There are three roles, which should not be confused:

**Issuer.** A trusted authority checks an underlying identity document and issues a credential containing an age or date-of-birth claim. In a Philippine National ID setting, the issuer and its legal authority must be defined by the PhilSys and applicable government processes.

**Holder.** The person keeps the credential, usually in a wallet or application. The holder generates a fresh proof for each request.

**Verifier.** A relying service asks for one predicate, such as $a>15$. It verifies the proof and receives only the claims that the protocol intentionally discloses.

The issuer does the identity checking. The ZKP does not magically establish that a person is a particular citizen. It proves a statement about a value that has already been bound to an issuer's credential. This separation is the key to understanding both the power and the limitations of the idea.

## A sealed envelope analogy

Suppose the holder writes their age on a card, puts the card in a sealed envelope, and gives the verifier a special mathematical seal. The seal has two useful properties:

1. The verifier cannot read the number through the envelope.
2. The holder cannot later open the same envelope and convincingly claim that a different number was inside.

The holder can then perform a mathematical demonstration about the sealed number. For example, they can demonstrate that subtracting 16 from the number gives a non-negative result. The verifier checks the demonstration, but never opens the envelope.

The envelope is a commitment. The demonstration is a zero-knowledge proof. The analogy is not a physical security claim; it is a way to remember the roles of hiding, binding, and proving.

## Why the seal is difficult to break

In computer science, a commitment is not secure merely because it looks complicated. It is secure because a feasible computer is not expected to undo the mathematical operation needed to cheat.

An everyday analogy is a padlock. It is easy to close the lock with a key, but, without the key, opening it should require an impractical amount of work. A cryptographic commitment has a similar "easy to make, hard to cheat" shape:

- Anyone can make a commitment quickly.
- The owner can later show the secret value and the random opening information.
- Someone who did not choose the secret should not be able to find a different value that also fits the same commitment.

People sometimes summarize this by saying that a commitment is based on the difficulty of "reversing" it. That is a helpful first picture, but the more precise statement is: an attacker should not be able to find a forbidden opening within the resources available to them. The exact hard problem depends on the cryptographic family. The names of the main families below are explained in plain language in the glossary at the end of this article; readers can follow the analogies without learning the formal mathematics.

### Three families in everyday language

| Family | Intuition | What the attacker faces |
|---|---|---|
| Discrete logarithm (DLP) | Repeatedly applying a known operation is easy; figuring out how many times it was applied is hard. | Recover the hidden number of steps from the public result. |
| Elliptic-curve DLP (ECDLP) | The same idea, but the operation happens on points of an elliptic curve. | Recover the hidden multiplier from a public curve point. |
| Lattice-based cryptography | The public information describes a huge, finely structured grid with a little noise. | Find a hidden short vector or remove the noise in a very high-dimensional problem. |

**DLP.** Imagine a calculator button that moves a marker around a very large circle. Moving the marker 7 times is easy if one knows the number 7. If the marker's starting point and final point are public, finding that hidden number of moves can be extremely difficult when the circle is large and the operation is chosen well. This is the discrete logarithm problem.

**ECDLP.** Elliptic-curve cryptography uses a different-looking playground: points on a mathematical curve, with a special addition operation. A public point such as $Q=kP$ is easy to calculate when $k$ is known. Finding $k$ from $P$ and $Q$ is the elliptic-curve discrete logarithm problem. Thus ECDLP is not a completely unrelated kind of "reversal"; it is the discrete-log idea in an elliptic-curve setting. Elliptic curves can provide strong security with smaller public keys, but they still require careful parameter choices and correct implementations.

**Lattices.** Picture a floor tiled with an enormous number of points. The public data gives an imperfect map of that floor, with small errors mixed in. It is easy to produce a noisy map from a hidden short route. It should be hard to recover the route, or to find another short route that explains the map. This is the intuition behind lattice problems such as Learning With Errors (LWE) and the Short Integer Solution (SIS) problem. Lattice-based constructions are especially important because their security is not built on the same discrete-log assumptions and they are leading candidates for resisting large-scale quantum attacks.

These are metaphors, not security proofs. The real assurances come from formal definitions, reductions to hard problems, parameter selection, cryptanalysis, and implementation review. Also, not every commitment is based on DLP, ECDLP, or lattices: hash-based and other constructions exist. The right question is always "which security property is claimed, and under which assumption?"

## What "hiding" means

The commitment formula below uses a few number-theory words. They are explained in the glossary at the end of this article; for now, think of them as the rules for manipulating a sealed mathematical object. A Pedersen commitment to an age $a$ with random blinding value $r$ is

$$
C = g^a h^r.
$$

In words, the commitment combines the public generator $g$ according to the age and the public generator $h$ according to fresh random noise $r$ (see the glossary for "generator"). The result $C$ is the sealed-looking object shown to the verifier. The verifier does not see $a$ or $r$.

The random value $r$ is like a fresh random lining inside the envelope. Even if the same age is committed twice, different random values produce unrelated commitments. Under the standard assumptions:

**Hiding.** The commitment reveals no useful information about $a$ when $r$ is chosen uniformly and kept secret. In the ideal mathematical model this hiding is perfect.

**Binding.** It is computationally infeasible to find two different openings for the same commitment. This relies on the difficulty of a discrete-logarithm problem: given a starting object and a resulting object, finding the hidden number of repeated operations should be impractical.

Hiding is not the same as anonymity. A website can still identify a browser through an account, IP address, device fingerprint, timing, or a login that happens outside the proof. A ZKP protects the claim being proved; the whole application must protect the surrounding metadata too.

## What is a homomorphism?

In plain language, a *homomorphism* is a translation that preserves a particular kind of calculation. Imagine translating a recipe from one unit system to another. If adding two quantities before translation gives the same result as translating each quantity first and then adding them, the translation preserves addition.

For example, doubling numbers preserves addition:

$$
2(3+4)=2\cdot3+2\cdot4.
$$

The numbers have been changed, but the addition relationship survives. In cryptography, the two representations are usually a secret value and its public-looking protected form.

More formally, if a map $f$ translates values from one system to another, then it is homomorphic for an operation when

$$
f(x+y)=f(x)\mathbin{\star}f(y),
$$

where $+$ is the operation on the original values and $\star$ is the matching operation on their translated values. The symbols may look different, but the calculation has the same structure.

This does not mean that every calculation remains possible. A particular cryptographic construction may preserve addition but not multiplication, or may preserve multiplication by a public number but not multiplication of two hidden values. The phrase "homomorphic" must always be read together with the operation being preserved.

## Homomorphic evaluation: doing arithmetic while values stay hidden

The commitment has an additive homomorphic property. If

$$
C_a=g^a h^{r_a}, \qquad C_b=g^b h^{r_b},
$$

then multiplying the commitments gives

$$
C_a C_b = g^{a+b}h^{r_a+r_b} = \mathsf{Com}(a+b).
$$

The verifier can therefore combine sealed values and know that the result is a commitment to their sum, without seeing either input. Likewise,

$$
C_a^k = g^{ka}h^{kr_a} = \mathsf{Com}(ka)
$$

for a public integer $k$.

For the age predicate, the holder can form a commitment to the slack

$$
d=a-16.
$$

The statement $a>15$ is equivalent to $d\geq0$. The holder does not reveal $a$ or $d$; they prove that the hidden $d$ has a valid non-negative range. The commitment operations let the proof system evaluate the linear relation $d=a-16$ while the values remain hidden.

This is sometimes called homomorphic evaluation, but it is important not to overclaim what it means:

- Pedersen commitments natively support addition and multiplication by a public scalar.
- They do not, by themselves, support arbitrary hidden-times-hidden multiplication. A proof system adds constraints or interactive subprotocols when a more complicated computation is needed.
- A range proof is a proof that the hidden computation satisfies a set of constraints. It is not the same thing as fully homomorphic encryption, which is designed to compute general programs on encrypted data.

## A small worked proof

Assume the issuer has certified an age $a$ in a credential, and the holder has committed to the same $a$. To prove $a>15$, define $d=a-16$. In a toy four-bit example the holder proves

$$
d=d_0+2d_1+4d_2+8d_3, \qquad d_i\in\{0,1\}.
$$

For illustration, suppose the hidden age is 21. Then $d=5$, and the hidden bits are

$$
5=1+0\cdot2+1\cdot4+0\cdot8, \qquad (d_0,d_1,d_2,d_3)=(1,0,1,0).
$$

The holder commits to the bits and proves three facts without revealing them:

1. each $d_i$ is either 0 or 1;
2. the committed bits add up to the same hidden $d$;
3. the hidden $d$ is linked to the credential age by $d=a-16$.

The verifier learns that $d$ is in $[0,16)$, so $a$ is in $[16,32)$. The verifier can safely output "above 15" while learning neither 21 nor the identity of the holder from this proof itself. A production range proof uses larger ranges and a standardized construction such as [Bulletproofs](https://doi.org/10.1109/SP.2018.00020); four bits are used here only so that the arithmetic fits on the page.

### How "above 15" becomes equality checks

This is the central trick behind a bit-based range proof. A computer is very good at checking equalities, but "is this age above 15?" is not itself an equality. We therefore rewrite the question as a distance that must be a non-negative number.

**First step: make the distance out of bits.** In this example, the hidden distance $d$ is written using $n$ secret bits:

$$
d=d_0+2d_1+4d_2+\cdots+2^{n-1}d_{n-1}, \qquad d_i\in\{0,1\}.
$$

The proof checks two kinds of equalities: (1) every $d_i$ is either 0 or 1; and (2) the weighted sum of the committed bits equals the commitment to $d$.

Because each $d_i$ is only 0 or 1, the sum cannot be smaller than 0 or larger than $2^n-1$. Thus equality checks have proved the range

$$
0\leq d<2^n.
$$

This is why a range proof is sometimes described as "proving an inequality by proving a collection of equalities."

**Second step: turn "above 15" into a distance.** For a strict lower bound $x>L$, define the hidden distance

$$
d=x-L-1.
$$

The prover proves

$$
x-L-1=d_0+2d_1+\cdots+2^{n-1}d_{n-1},
$$

which establishes $x>L$ without revealing $x$.

For this paper's age example, $x=a$ and $L=15$, so

$$
d=a-15-1=a-16.
$$

If the prover can show $d\geq0$, then $a-16\geq0$, which means $a\geq16$, and therefore $a>15$.

### How a violation of the age condition is detected

The verifier does not see the age, so it cannot simply compare a displayed number with 15. Instead, it checks that every part of the proof is valid: (1) the committed age is the age certified by the issuer; (2) the committed distance satisfies $d=a-16$; and (3) the distance has a valid bit decomposition, so $0\leq d<16$ in this four-bit example.

For an honest age of 21, $d=21-16=5$, and the bits $(d_0,d_1,d_2,d_3)$ can represent 5. Every equality and every bit check can pass, so the verifier accepts the statement "above 15."

Now consider an age of 15, which violates the condition. The linked distance would have to be

$$
d=15-16=-1.
$$

Four ordinary bits cannot represent $-1$, so the prover cannot satisfy the bit-decomposition equality with valid bits. The verifier rejects the proof. The same reasoning applies to ages below 15: their distances are negative, and therefore are outside the proven range $[0,16)$.

The finite-field idea from the glossary explains why the range check is important. Field arithmetic is like a clock: after the largest value, it starts again at zero. If the field has size $q$, then

$$
-1\equiv q-1\pmod{q}.
$$

Without a range proof, the negative value $-1$ could therefore appear in an equality as the large field value $q-1$. A dishonest prover might hope to use that wraparound to make an invalid age look valid. But four valid bits add up only to one of the ordinary integers $0,1,\ldots,15$; they cannot add up to $q-1$ when the field is chosen sufficiently large. Thus either the bit proof or the equality linking the bits to $d$ fails, and the verifier rejects.

The protocol must choose the field size and declared age range so that valid values do not themselves wrap around. In this toy example, choose $q>32$ and use a canonical age representation in $[0,32)$. Production protocols use larger ranges and explicitly constrain both the age and the derived distance. They also bind the commitment to the issuer's credential. These conditions ensure that an accepted proof means the actual credential-backed age is above 15, rather than merely satisfying a wrapped field equation.

## Philippine National ID and Meta authorization context

The Philippine Identification System (PhilSys) provides a useful trust and credential context for this design. The National ID can support an issuer workflow in which an authorized authority establishes identity and issues a credential. A relying service could then request only an age predicate rather than the complete identity record. The [Philippine Statistics Authority's official PhilSys materials](https://philsys.gov.ph/) and [privacy notice](https://philsys.gov.ph/privacy-policy/) should control the actual attributes, identifiers, consent, retention, and disclosure rules.

In this paper, "Meta authorization" means the authorization layer in which a person permits a relying application to request a particular proof. That authorization layer can say:

> "This application may ask for the predicate age $>15$ for this session."

It should not silently turn an age check into permission to copy a full National ID profile. Authorization and validation are separate:

| Authorization | Validation |
|---|---|
| The holder consents to this request, for this verifier and purpose. | The cryptographic proof verifies that the credential-backed age satisfies $a>15$. |

The proof cannot grant a service permission that the service does not have, and authorization cannot make a false age proof valid. A production Philippine deployment would also need policy decisions about issuer trust, credential expiry, revocation, replay resistance, accessibility, offline operation, auditability, and remedies when a person cannot use a digital wallet.

## FUD and what the mathematics actually says

There is a lot of FUD (fear, uncertainty, and doubt) about zero-knowledge proofs on the internet. Some claims are wrong; some are valid warnings stated too broadly. The following distinctions are useful:

**"Zero knowledge means the verifier learns nothing at all."** False. The verifier learns that the requested statement passed, and may learn metadata outside the proof. Zero knowledge concerns the witness and the protocol transcript, under a defined security model.

**"A ZKP can prove any claim about a fake ID."** False when the proof is properly bound to an issuer signature or credential. A range proof alone only proves a statement about a commitment; it does not authenticate the committed value.

**"The proof is magic and needs no trust."** False. The system still needs trusted issuance, correct circuit or constraint design, secure random number generation, sound cryptographic parameters, key management, and a sound revocation model.

**"If a system uses a blockchain, it is automatically private."** False. Public ledgers can create permanent metadata and linkability. A privacy-preserving design should minimize what is written to a ledger and avoid publishing personal identifiers.

**"ZKPs are only theoretical."** False. Related zero-knowledge and anonymous-credential protocols are used in deployed products and networks, although a deployment using a related protocol is not proof that a particular Philippine ID service uses this exact range proof.

## Examples of deployment

The phrase "already deployed" needs precision. There are deployed systems using zero-knowledge proofs, anonymous credentials, or selective disclosure; there are also pilots and standards that specify how predicate proofs can be used. They should not all be described as national age-verification systems.

**[Zcash](https://zips.z.cash/protocol/protocol.pdf).** Zcash has deployed zero-knowledge proofs in a public cryptocurrency network to show that a transaction satisfies validity rules without exposing all transaction details. This is a different use case, but it demonstrates that ZKPs can operate in production at scale.

**[IRMA](https://privacybydesign.foundation/irma/)/[Yivi](https://www.yivi.app/) in the Netherlands.** IRMA and its successor Yivi use attribute-based credentials and privacy-preserving presentations. Their documented use cases include disclosing only needed attributes and proving predicates such as age-related conditions.

**[IDunion](https://idunion.org/) in Germany and Europe.** IDunion is a German-led self-sovereign identity ecosystem and pilot network based on verifiable credentials and privacy-preserving proofs.

**European public infrastructure.** [EBSI](https://digital-strategy.ec.europa.eu/en/policies/european-blockchain-services-infrastructure-ebsi) and the [European Digital Identity](https://digital-strategy.ec.europa.eu/en/policies/eudi-regulation) Wallet [architecture](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework) support verifiable credentials and selective disclosure. These are infrastructure and rollout programmes, not evidence that every member state already operates the same production age-check service.

These examples support the narrow claim made by this paper: the cryptographic idea is mathematically possible and related protocols are already deployed. They do not support the stronger and inaccurate claim that PhilSys currently performs this exact ZKP age validation.

## Conclusion

An age credential can be treated as a hidden value, and homomorphic commitment operations can connect that value to a range-proof constraint. The holder can then prove $a>15$ and receive a verifier result of TRUE without exposing their name or exact age through the proof. In a Philippine National ID and Meta authorization setting, the practical work is to define trustworthy issuance, explicit consent, minimal disclosure, and accountable verification around the mathematics.

The point is not that cryptography removes every social or operational risk. The point is narrower and testable: under standard assumptions, a protocol can prove the requested age predicate while keeping the underlying age hidden.

*This tutorial was motivated by public discussion and uncertainty surrounding possible Meta–PhilSys integration.*

---

## Glossary: number-theory vocabulary

The article above avoids these terms where possible. This glossary gives a short reference for readers who want to understand the notation behind commitments and wraparound.

**Modulo: arithmetic on a clock.** When we calculate "modulo 5," we keep only the remainder after dividing by 5. For example, $7\pmod{5}=2$ and $12\pmod{5}=2$. It is like a clock with five positions: after position 4, we go back to position 0. Therefore $-1\pmod{5}=4$. Cryptography uses very large clocks, so going around the clock is easy, while guessing a hidden number of turns can be difficult.

**Finite field.** A finite field is a carefully designed, finite collection of numbers on such a clock. We can add, subtract, and multiply its numbers, always wrapping back around when necessary. We can also divide by any non-zero number. In this paper, the field's size is written as $q$; the exact construction is less important than remembering that field arithmetic can wrap around.

**Group.** A group is a collection of objects together with one allowed way to combine them. Combining two allowed objects gives another allowed object, there is a do-nothing object, and each object has an undoing operation. A group is not necessarily a collection of ordinary numbers: cryptography often uses points or other objects. The group rules make the calculations predictable without revealing the secrets used to create them.

**Generator.** A generator is a public starting object in a group. Repeating the group's combining operation produces a sequence of public-looking objects: starting once, twice, three times, and so on. It is easy to produce the object for a known number of repetitions. The security assumption is that, given the starting and final objects, finding the hidden number of repetitions is impractical when the parameters are chosen correctly.

These definitions explain the notation used above. $G$ denotes a group, while $g$ and $h$ are two public generators in that group. An expression such as $g^a$ means that the group operation is applied repeatedly according to the number $a$; it is not ordinary schoolbook exponentiation.

## References

1. B. Bünz, J. Bootle, D. Boneh, A. Poelstra, P. Wuille, and G. Maxwell, "Bulletproofs: Short Proofs for Confidential Transactions and More," *IEEE Symposium on Security and Privacy*, 2018. [doi.org/10.1109/SP.2018.00020](https://doi.org/10.1109/SP.2018.00020)
2. Philippine Statistics Authority, "Philippine Identification System (PhilSys)," official portal. [philsys.gov.ph](https://philsys.gov.ph/)
3. Philippine Statistics Authority, "PhilSys Privacy Policy." [philsys.gov.ph/privacy-policy](https://philsys.gov.ph/privacy-policy/)
4. Electric Coin Company, "Zcash Protocol Specification." [zips.z.cash/protocol/protocol.pdf](https://zips.z.cash/protocol/protocol.pdf)
5. Privacy by Design Foundation, "IRMA." [privacybydesign.foundation/irma](https://privacybydesign.foundation/irma/)
6. Yivi, "What is Yivi?" [yivi.app](https://www.yivi.app/)
7. IDunion e.V., "IDunion: Self-sovereign identities for a digital Europe." [idunion.org](https://idunion.org/)
8. European Commission, "European Blockchain Services Infrastructure." [digital-strategy.ec.europa.eu](https://digital-strategy.ec.europa.eu/en/policies/european-blockchain-services-infrastructure-ebsi)
9. European Commission, "European Digital Identity." [digital-strategy.ec.europa.eu](https://digital-strategy.ec.europa.eu/en/policies/eudi-regulation)
10. European Commission, "Architecture and Reference Framework for the European Digital Identity Wallet." [github.com/eu-digital-identity-wallet](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework)
