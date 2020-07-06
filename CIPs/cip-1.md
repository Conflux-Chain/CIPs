---
CIP No.: 1
Title: CIP Purpose and Guidelines
Status: Active
Type: Meta
Author: Guang Yang
Created: 2020-07-06
Updated: 
---

## What is a CIP?

CIP stands for Conflux Improvement Proposal. A CIP is a design document providing information to the Conflux community, or describing a new feature for Conflux or its processes or environment. The CIP should provide a concise technical specification of the feature and a rationale for the feature. 

We intend CIPs to be the primary mechanisms for proposing new features, for collecting community input on an issue, and for documenting the design decisions that have gone into Conflux. The CIP author is responsible for building consensus within the community and documenting dissenting opinions. Because the CIPs are maintained as text files in a versioned repository, their revision history is the historical record of the feature proposal.

For Conflux implementers, CIPs are a convenient way to track the progress of their implementation. Ideally implementation maintainers would list the CIPs that they have implemented. This will give end users a convenient way to know the current status of a given implementation or library.

## CIP Types

There are three types of CIP:

- A **Standard Track CIP** describes any change that affects most or all Conflux implementations, such as a change to the network protocol, a change in block or transaction validity rules, proposed application standards/conventions, or any change or addition that affects the interoperability of applications using Conflux. Furthermore Standard CIPs can be broken down into the following categories. Standards Track CIPs consist of three parts, a design document, implementation, and finally if warranted an update to the [formal specification].
  - **Core** - improvements requiring a consensus fork, as well as changes that are not necessarily consensus critical but may be relevant to “core dev” discussions.
  - **Networking** - includes improvements to network protocol specifications.
  - **Interface** - includes improvements around client [API/RPC] specifications and standards, and also certain language-level standards like method names and contract ABIs.
  - **CRC** - application-level standards and conventions, including contract standards such as token standards, name registries, URI schemes, library/package formats, and wallet formats.
- A **Meta CIP**, a.k.a. a  **Process CIP**, describes a process surrounding Conflux or proposes a change to (or an event in) a process. Process CIPs are like Standards Track CIPs but apply to areas other than the Conflux protocol itself. They may propose an implementation, but not to Conflux's codebase; they often require community consensus; unlike Informational CIPs, they are more than recommendations, and users are typically not free to ignore them. 
- An **Informational CIP** describes a Conflux design issue, or provides general guidelines or information to the Conflux community, but does not propose a new feature. Informational CIPs do not necessarily represent Conflux community consensus or a recommendation, so users and implementers are free to ignore Informational CIPs or follow their advice.

It is highly recommended that a single CIP contains a single key proposal or new idea. The more focused the CIP is, the more successful it tends to be. A change to one client doesn't require a CIP; a change that affects multiple clients, or defines a standard for multiple apps to use, does.

A successful CIP must meet certain minimum criteria. It must be a clear and complete description of the proposed enhancement. The enhancement must represent a net improvement. The proposed implementation, if applicable, must be solid and must not complicate the protocol unduly.

### Special requirements for Core CIPs

If a **Core** CIP mentions or proposes changes to the Virtual Machine, it should refer to the instructions by their mnemonics and define the opcodes of those mnemonics at least once. A preferred way is the following:
```
REVERT (0xfe)
```

## CIP Work Flow

### Shepherding a CIP

Parties involved in the process are you, the champion or *CIP author*, the [*CIP editors*](#cip-editors), and the [*Conflux Core Developers*].

Before you begin writing a formal CIP, you should vet your idea. Ask the Conflux community first if an idea is original to avoid wasting time on something that will be rejected based on prior research. It is thus recommended to open a discussion thread in [the Issues section of this repository]. 

In addition to making sure your idea is original, it will be your role as the author to make your idea clear to reviewers and interested parties, as well as inviting editors, developers and community to give feedback on the aforementioned channels. You should try and gauge whether the interest in your CIP is commensurate with both the work involved in implementing it and how many parties will have to conform to it. For example, the work required for implementing a Core CIP will be much greater than for a CRC and the CIP will need sufficient interest from the Conflux client teams. Negative community feedback will be taken into consideration and may prevent your CIP from moving past the Draft stage.

### Core CIPs

For Core CIPs, given that they require client implementations to be considered **Final** (see "CIPs Process" below), you will need to either provide an implementation for clients or convince clients to implement your CIP.

*In short, your role as the champion is to write the CIP using the style and format described below, shepherd the discussions in the appropriate forums, and build community consensus around the idea.* 

### CIP Process 

Following is the process that a successful non-Core CIP will move along:

```
[ WIP ] -> [ DRAFT ] -> [ LAST CALL ] -> [ FINAL ]
```

Following is the process that a successful Core CIP will move along:

```
[ IDEA ] -> [ DRAFT ] -> [ LAST CALL ] -> [ ACCEPTED ] -> [ FINAL ]
```

Each status change is requested by the CIP author and reviewed by the CIP editors. Use a pull request to update the status. Please include a link to where people should continue discussing your CIP. The CIP editors will process these requests as per the conditions below.

* **Idea** -- Once the champion has asked the Conflux community whether an idea has any chance of support, they will write a draft CIP as a pull request. Consider including an implementation if this will aid people in studying the CIP.
  * :arrow_right: Draft -- If agreeable, CIP editor will assign the CIP a number (generally the issue or PR number related to the CIP) and merge your pull request. The CIP editor will not unreasonably deny a CIP.
  * :x: Draft -- Reasons for denying draft status include being too unfocused, too broad, duplication of effort, being technically unsound, not providing proper motivation or addressing backwards compatibility, or not in keeping with the Conflux philosophy.
* **Draft** -- Once the first draft has been merged, you may submit follow-up pull requests with further changes to your draft until such point as you believe the CIP to be mature and ready to proceed to the next status. A CIP in draft status must be implemented to be considered for promotion to the next status (ignore this requirement for core CIPs).
  * :arrow_right: Last Call -- If agreeable, the CIP editor will assign Last Call status and set a review end date (`review-period-end`), normally 14 days later.
  * :x: Last Call -- A request for Last Call status will be denied if material changes are still expected to be made to the draft. We hope that CIPs only enter Last Call once.
* **Last Call** -- This CIP will be listed prominently on the website.
  * :x: -- A Last Call which results in material changes or substantial unaddressed technical complaints will cause the CIP to revert to Draft.
  * :arrow_right: Accepted (Core CIPs only) -- A successful Last Call without material changes or unaddressed technical complaints will become Accepted.
  * :arrow_right: Final (Non-Core CIPs) -- A successful Last Call without material changes or unaddressed technical complaints will become Final.
* **Accepted (Core CIPs only)** -- This status signals that material changes are unlikely and Conflux client developers should consider this CIP for inclusion. Their process for deciding whether to encode it into their clients as part of a hard fork is not part of the CIP process.
  * :arrow_right: Draft -- The Core Devs can decide to move this CIP back to the Draft status at their discretion. E.g. a major, but correctable, flaw was found in the CIP.
  * :arrow_right: Rejected -- The Core Devs can decide to mark this CIP as Rejected at their discretion. E.g. a major, but uncorrectable, flaw was found in the CIP.
  * :arrow_right: Final -- Standards Track Core CIPs must be implemented before it can be considered Final. When the implementation is complete and adopted by the community, the status will be changed to “Final”.
* **Final** -- This CIP represents the current state-of-the-art. A **Final CIP** should only be updated to correct errata.

Other exceptional statuses include:

* **Active** -- Some Informational and Process CIPs may also have a status of “Active” if they are never meant to be completed. E.g. CIP-1 (this CIP).
* **Abandoned** -- This CIP is no longer pursued by the original authors or it may not be a (technically) preferred option anymore.
  * :arrow_right: Draft -- Authors or new champions wishing to pursue this CIP can ask for changing it to Draft status.
* **Rejected** -- A CIP that is fundamentally broken or a Core CIP that was rejected by the Core Devs and will not be implemented. A CIP cannot move on from this state.
* **Superseded** -- A CIP which was previously Final but is no longer considered state-of-the-art. Another CIP will be in Final status and reference the Superseded CIP. A CIP cannot move on from this state.

## What belongs in a successful CIP?

Each CIP should have the following parts:

- Preamble - RFC 822 style headers containing metadata about the CIP, including the CIP number, a short descriptive title (limited to a maximum of 44 characters), and the author details. See below for details.
- Abstract - A short (~200 word) description of the technical issue being addressed.
- Motivation (*optional) - The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.
- Specification - The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platform ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).
- Rationale - The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.
- Backwards Compatibility - All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.
- Test Cases - Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.
- Implementations - The implementations must be completed before any CIP is given status “Final”, but it need not be completed before the CIP is merged as draft. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of “rough consensus and running code” is still useful when it comes to resolving many discussions of API details.
- Security Considerations - All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. A CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.
- Copyright Waiver - All CIPs must be in the public domain. See the bottom of this CIP for an example copyright waiver.

## CIP Formats and Templates

CIPs should be written in [markdown] format.
Image files should be included in a subdirectory of the `assets` folder for that CIP as follows: `assets/cip-N` (where **N** is to be replaced with the CIP number). When linking to an image in the CIP, use relative links such as `../assets/cip-1/image.png`.

## CIP Header Preamble

Each CIP must begin with an [RFC 822](https://www.ietf.org/rfc/rfc822.txt) style header preamble, preceded and followed by three hyphens (`---`). This header is also termed ["front matter" by Jekyll](https://jekyllrb.com/docs/front-matter/). The headers must appear in the following order. Headers marked with "*" are optional and are described below. All other headers are required.

` cip:` *CIP number* (this is determined by the CIP editor)

` title:` *CIP title*

` author:` *a list of the author's or authors' name(s) and/or username(s), or name(s) and email(s). Details are below.*

` * discussions-to:` *a url pointing to the official discussion thread*

` status:` *Draft | Last Call | Accepted | Final | Active | Abandoned | Rejected | Superseded*

`* review-period-end:` *date review period ends*

` type:` *Standards Track | Informational | Meta*

` * category:` *Core | Networking | Interface | CRC* (Standards Track CIPs only)

` created:` *date created on*

` * updated:` *comma separated list of dates*

` * requires:` *CIP number(s)*

` * replaces:` *CIP number(s)*

` * superseded-by:` *CIP number(s)*

` * resolution:` *a url pointing to the resolution of this CIP*

Headers that permit lists must separate elements with commas.

Headers requiring dates will always do so in the format of ISO 8601 (yyyy-mm-dd).

#### `author` header

The `author` header optionally lists the names, email addresses or usernames of the authors/owners of the CIP. Those who prefer anonymity may use a username only, or a first name and a username. The format of the author header value must be:

> Random J. User &lt;address@dom.ain&gt;

or

> Random J. User (@username)

if the email address or GitHub username is included, and

> Random J. User

if the email address is not given.

#### `resolution` header

The `resolution` header is required for Standards Track CIPs only. It contains a URL that should point to an email message or other web resource where the pronouncement about the CIP is made.

#### `discussions-to` header

While a CIP is a draft, a `discussions-to` header will indicate the mailing list or URL where the CIP is being discussed. No `discussions-to` header is necessary if the CIP is being discussed privately with the author.

As a single exception, `discussions-to` cannot point to GitHub pull requests.

#### `type` header

The `type` header specifies the type of CIP: Standards Track, Meta, or Informational. If the track is Standards please include the subcategory (core, networking, interface, or CRC).

#### `category` header

The `category` header specifies the CIP's category. This is required for standards-track CIPs only.

#### `created` header

The `created` header records the date that the CIP was assigned a number. Both headers should be in yyyy-mm-dd format, e.g. 2001-08-14.

#### `updated` header

The `updated` header records the date(s) when the CIP was updated with "substantial" changes. This header is only valid for CIPs of Draft and Active status.

#### `requires` header

CIPs may have a `requires` header, indicating the CIP numbers that this CIP depends on.

#### `superseded-by` and `replaces` headers

CIPs may also have a `superseded-by` header indicating that a CIP has been rendered obsolete by a later document; the value is the number of the CIP that replaces the current document. The newer CIP must have a `replaces` header containing the number of the CIP that it rendered obsolete.

## Auxiliary Files

CIPs may include auxiliary files such as diagrams. Such files must be named `CIP-N-Y.ext`, where **N** is the CIP number, **Y** is a serial number (starting at 1), and “ext” is replaced by the actual file extension (e.g. “png”).

## Transferring CIP Ownership

It occasionally becomes necessary to transfer ownership of a CIP to a new champion. In general, we'd like to retain the original author as a co-author of the transferred CIP, but that's really up to the original author. A good reason to transfer ownership is because the original author no longer has the time or interest in updating it or following through with the CIP process, or has fallen off the face of the 'net (i.e. is unreachable or isn't responding to email). A bad reason to transfer ownership is because you don't agree with the direction of the CIP. We try to build consensus around a CIP, but if that's not possible, you can always submit a competing CIP.

If you are interested in assuming ownership of a CIP, send a message asking to take over, addressed to both the original author and the CIP editor. If the original author doesn't respond to email in a timely manner, the CIP editor will make a unilateral decision (it's not like such decisions can't be reversed :)).

## CIP Editors

The current CIP editors are

` * Fan Long (@fanlong)`

``* Ming Wu (@sparkmiw)`` 

` * Guang Yang (@Regulusyg)`

` * Zhe Yang (@yangzhe1990)`

` * Dong Zhou (@dongz9)`

## CIP Editor Responsibilities

For each new CIP that comes in, an editor does the following:

- Read the CIP to check if it is ready: sound and complete. The ideas must make technical sense, even if they don't seem likely to get to final status.
- The title should accurately describe the content.
- Check the CIP for language (spelling, grammar, sentence structure, etc.), markup (GitHub flavored Markdown), code style

If the CIP isn't ready, the editor will send it back to the author for revision, with specific instructions.

Once the CIP is ready for the repository, the CIP editor will:

- Assign a CIP number (generally the PR number or, if preferred by the author, the Issue # if there was discussion in the Issues section of this repository about this CIP)

- Merge the corresponding pull request

- Send a message back to the CIP author with the next step.

The CIP editors monitor CIP changes, and correct any structure, grammar, spelling, or markup mistakes we see.

The editors don't pass judgment on CIPs. We merely do the administrative & editorial part.

## History

This document was derived heavily from [Ethereum's EIP-1](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1.md) written by Martin Becze, Hudson Jameson et al. and [Bitcoin's BIP-0001](https://github.com/bitcoin/bips) written by Amir Taaki which in turn were derived from [Python's PEP-0001](https://www.python.org/dev/peps/) written by Barry Warsaw, Jeremy Hylton, and David Goodger. Although in many places text was simply copied and modified from previous works, their authors are not responsible for its use in the Conflux Improvement Process, and should not be bothered with technical questions specific to Conflux or the CIP. Please direct all comments to the CIP editors.

### Bibliography

[pull request]: https://github.com/Conflux-Chain/CIPs/pulls
[the Issues section of this repository]: https://github.com/Conflux-Chain/CIPs/issues
[markdown]: https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet
[Ethereum's EIP-1]: https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1.md
[Bitcoin's BIP-0001]: https://github.com/bitcoin/bips/
[Python's PEP-0001]: https://www.python.org/dev/peps/



## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).