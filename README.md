# Conflux Improvement Proposals
Conflux Improvement Proposals (CIPs) describe standards for the Conflux platform, including core protocol specifications, client APIs, and contract standards.

# Contributing

1. Review [CIP-1](https://github.com/Conflux-Chain/CIPs/blob/master/CIPs/cip-1.md).
2. Fork the repository by clicking "Fork" in the top right.
3. Add your CIP to your fork of the repository. There is a [template CIP here](cip-template.md).
4. Submit a Pull Request to Conflux's [CIPs repository](https://github.com/Conflux-Chain/CIPs).

Your first PR should be a first draft of the final CIP. It must meet the formatting criteria enforced by the build (largely, correct metadata in the header). An editor will manually review the first PR for a new CIP and assign it a number before merging it. Make sure you include a `discussions-to` header with the URL to a discussion forum or open GitHub issue where people can discuss the CIP as a whole.

If your CIP requires images, the image files should be included in a subdirectory of the `assets` folder for that CIP as follows: `assets/CIP-N` (where **N** is to be replaced with the CIP number). When linking to an image in the CIP, use relative links such as `../assets/CIP-1/image.png`.

Once your first PR is merged, we have a bot that helps out by automatically merging PRs to draft CIPs. For this to work, it has to be able to tell that you own the draft being edited. Make sure that the 'author' line of your CIP contains either your GitHub username or your email address inside. If you use your email address, that address must be the one publicly shown on [your GitHub profile](https://github.com/settings/profile).

When you believe your CIP is mature and ready to progress past the draft phase, you should ask to have your issue added to the agenda of an upcoming All Core Devs meeting, where it can be discussed for inclusion in a future hard fork. If implementers agree to include it, the CIP editors will update the state of your CIP to **Accepted**.

# CIP Status Terms

- **Draft** - a CIP that is undergoing rapid iteration and changes.
- **Last Call** - a CIP that is done with its initial iteration and ready for review by a wide audience.
- **Accepted** - a CIP that has been in **Last Call** for at least 2 weeks and any technical changes that were requested have been addressed by the author. The process for Core Devs to decide whether to encode a CIP into their clients as part of a hard fork is not part of the CIP process. If such a decision is made, the CIP will move to **Final**.
- **Final** - a CIP that the Core Devs have decided to implement and release in a future hard fork or has already been released in a hard fork.