![OCM Lens: a fast, minimal viewer for OCM deliveries](docs/banner.png)

[![Open VSX](https://img.shields.io/open-vsx/v/everbright-it/ocmlens?label=Open%20VSX)](https://open-vsx.org/extension/everbright-it/ocmlens)
[![License: Apache-2.0](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)

# OCM Lens

A fast, minimal viewer for [Open Component Model](https://ocm.software) (OCM)
deliveries. Drop a CTF archive, a component archive, or a plain component
descriptor and see what is actually inside: components, resources, references,
the transported artifact contents, and whether digests and signatures hold.
Everything runs client-side, in your browser or your editor; files never leave
your machine.

**Try it:** <https://ocm-lens.everbright-it.de/app/> (a demo delivery is one
click away). The landing page lives at <https://ocm-lens.everbright-it.de/>.
The VS Code extension is on
[Open VSX](https://open-vsx.org/extension/everbright-it/ocmlens) and the
[VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=everbright-it.ocmlens).
**OCM Lens 2.0 is out as a pre-release** in VS Code; see
[below](#ocm-lens-20-pre-release).

![OCM Lens: the Explore view with a demo delivery loaded, showing a Helm chart resource with OCM identity, localBlob access, a verified digest, and the artifact content preview](docs/hero-explore.png)

## Why OCM Lens

- **It reads the delivery, not just the descriptor.** Common Transport Format
  archives (both index layouts) and component archives open directly; so do
  standalone component descriptors (schema v2 fully, v3alpha1 best effort,
  YAML or JSON). Component references resolve across everything you load
  into one continuous tree.
- **Artifact contents are visible.** Transported blobs are inspected in a Web
  Worker: Helm charts show their file list and Chart.yaml / values.yaml
  previews, OCI artifact sets show manifest and layer tables, text and
  JSON/YAML blobs get previews, binaries a hex head. Raw bytes never reach
  the UI thread.
- **Digests are checked, not just displayed.** `genericBlobDigest/v1` and
  `ociArtifactDigest/v1` are recomputed per resource and reported as match,
  mismatch, or unchecked. Never guessed.
- **Signatures verify in the browser.** Paste a public key or certificate and
  OCM Lens recomputes the normalised descriptor digest (`jsonNormalisation`
  v4alpha1, v3, v2) and checks the RSA signature (RSASSA-PSS and
  PKCS1-v1_5, SHA-256/512) with the browser's own WebCrypto, verified
  byte-for-byte against the real `ocm` CLI. No server involved, no upload.
  Pin a trusted key once and every signed delivery verifies on open.
- **Structural lint.** Every descriptor is checked against the spec's
  structural rules and reports warnings (`OCM_SCHEMA_*`) without refusing
  to load.
- **Embedded SBOMs join the tree.** SPDX and CycloneDX SBOMs shipped inside a
  delivery are extracted, linked to their resources, and resolved into the
  same cascade, so a release, its components, and their SBOMs read as one
  structure.
- **Vulnerability communication.** Load OpenVEX documents or CSAF 2.0
  advisories next to a delivery and see what the supplier says about known
  vulnerabilities in the shipped packages, matched by package URL and CPE.
  No scanner and no CVE database: the viewer shows what the vendor
  communicates.
- **Registry browsing (VS Code).** Pull a component version straight from an
  OCI registry and browse it like a local delivery, SBOMs, digest verdicts,
  and signatures included. Unresolved component references offer a
  fetch-from-registry button prefilled from the descriptor's repository
  contexts; private registries take a per-host token kept in VS Code secret
  storage. Comparing two versions of a component is one command: both are
  pulled into one panel and the diff view opens on them. Pull-only, like
  everything else here.
- **Analysis included.** Cross-delivery inventory, version conflicts, and a
  release-to-release diff that also sees content changes (same version,
  different digest). Quality reports run on declarative compliance profiles,
  with an "OCM component essentials" preset built in. When an embedded SBOM
  lists file checksums, *Check delivery* compares the files you were
  actually delivered against them: match, mismatch, missing, or
  unverifiable, plus extras the SBOM never mentioned.

## Get it

- **Web app:** <https://ocm-lens.everbright-it.de/app/>. Nothing to install.
- **VS Code / VSCodium:** install `everbright-it.ocmlens` from
  [Open VSX](https://open-vsx.org/extension/everbright-it/ocmlens) or the
  [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=everbright-it.ocmlens).
  It opens `component-descriptor.yaml` files and `.ctf` / component-archive
  tars, scans the workspace for deliveries, and pulls component versions
  straight from OCI registries.

## OCM Lens 2.0 (pre-release)

OCM Lens is rebuilt as an OCM-native delivery explorer. Instead of mapping a
delivery onto an SBOM document viewer, 2.0 shows it in the spec's own terms:
a dashboard that answers what is in the bundle and whether it can be
deployed without network access, the component reference graph as
navigation, every artifact of the delivery in one table, and a Verify view
with digest and signature checks plus an exportable delivery acceptance
report.

**2.0.0 is on the pre-release channel of the VS Code extension.** Open OCM
Lens in the Extensions view and choose *Switch to Pre-Release Version*. The
release channel and the hosted web app stay on 1.x (the feature list above)
until 2.x goes stable. 2.0 deliberately leaves some 1.x features out for now
(Inventory, Conflicts, Licenses, Map, and Diff views, the VEX/CSAF overlay,
compliance profiles, comparing two registry versions); the
[changelog](https://gitlab.com/everbrightit-group/sbom-lens/-/blob/main/apps/ocm-lens/CHANGELOG.md)
lists every change. Note for Open VSX users: Open VSX lists 2.0.0 as its
newest version, and editors that ignore the pre-release flag may install it
directly.

2.0 lives in the monorepo under
[`apps/ocm-lens`](https://gitlab.com/everbrightit-group/sbom-lens/-/tree/main/apps/ocm-lens)
and versions independently on its own 2.x line. Design notes:
[docs/ocm-lens-2.md](https://gitlab.com/everbrightit-group/sbom-lens/-/blob/main/docs/ocm-lens-2.md).

## Where the code lives

OCM Lens is developed in the
[sbom-lens monorepo](https://gitlab.com/everbrightit-group/sbom-lens)
together with its sibling product
[SBOM Lens](https://sbom-lens.everbright-it.de/), an SPDX viewer. OCM Lens
1.x shares the viewer engine, the web UI, and the VS Code shell with SBOM
Lens and released in lockstep with it (0.x versions). OCM Lens 2.x keeps the
shared core (delivery walker, digests, signature verification) and the VS
Code shell but has its own UI and its own version line, tagged
`ocm-lens-vX.Y.Z`. A CI gate keeps each product's bundle free of the other
product's code.

This repository is the product home for OCM Lens. **Code, issues, and merge
requests live in the monorepo:**
<https://gitlab.com/everbrightit-group/sbom-lens> (use the `ocm-lens` label
for issues). Releases are tagged there. Changelogs: 2.x in
[apps/ocm-lens/CHANGELOG.md](https://gitlab.com/everbrightit-group/sbom-lens/-/blob/main/apps/ocm-lens/CHANGELOG.md),
1.x in the shared
[CHANGELOG.md](https://gitlab.com/everbrightit-group/sbom-lens/-/blob/main/CHANGELOG.md).

## Scope and limits

Read-only by design: OCM Lens views and verifies deliveries, it does not
create, sign, or transport them. Signature verification covers RSASSA-PSS and
PKCS1-v1_5 with SHA-256/512; certificate chains, timestamping, and
`jsonNormalisation/v1` are out of scope and reported as unverifiable rather
than guessed. Plain `.tar` deliveries stream from disk, so multi-GB release
bundles open, in the browser and in VS Code; blobs over 64 MB are indexed
without a content preview (their digest verdict still runs), compressed
`.tgz` is capped at 2 GiB decompressed. The full list, stated plainly, lives in
[docs/ocm.md](https://gitlab.com/everbrightit-group/sbom-lens/-/blob/main/docs/ocm.md).

## Security

Report vulnerabilities privately to <tech@everbright-it.de>. Policy:
[SECURITY.md](SECURITY.md).

## License

[Apache-2.0](LICENSE). OCM Lens is an independent open-source project by
[EverBright IT](https://everbright-it.de) and is not affiliated with the
Open Component Model project or the NeoNephos Foundation.
