# IMSLP third-party sources

Source archives, build materials, and license notices for third-party components distributed with IMSLP apps.

Download the source bundle that matches the version identified by your app's **Open source licenses** page. Bundles are published as versioned GitHub Release assets. A bundle can cover multiple app releases when the covered components and build materials have not changed.

## Available source bundles

| Bundle                                                                                                                                 | Main components                                                                                     | Downloads                                                                                                                                                                                                                                                                                                                                  |
| -------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [MusicXML and UniFFI — 2026-10-31](https://github.com/imslp/third-party-sources/releases/tag/imslp-musicxml-uniffi-sources-2026-10-31) | Verovio 6.3.0, FluidSynth 2.6.0, GeneralUser GS 2.0.3, UniFFI 0.28.3, and accompanying dependencies | [Source ZIP](https://github.com/imslp/third-party-sources/releases/download/imslp-musicxml-uniffi-sources-2026-10-31/imslp-musicxml-uniffi-sources-2026-10-31.zip) · [SHA256](https://github.com/imslp/third-party-sources/releases/download/imslp-musicxml-uniffi-sources-2026-10-31/imslp-musicxml-uniffi-sources-2026-10-31.zip.sha256) |
| [MusicXML and UniFFI — 2026-10-30](https://github.com/imslp/third-party-sources/releases/tag/imslp-musicxml-uniffi-sources-2026-10-30) | Verovio 6.3.0, FluidSynth 2.6.0, GeneralUser GS 2.0.3, UniFFI 0.28.3, and accompanying dependencies | [Source ZIP](https://github.com/imslp/third-party-sources/releases/download/imslp-musicxml-uniffi-sources-2026-10-30/imslp-musicxml-uniffi-sources-2026-10-30.zip) · [SHA256](https://github.com/imslp/third-party-sources/releases/download/imslp-musicxml-uniffi-sources-2026-10-30/imslp-musicxml-uniffi-sources-2026-10-30.zip.sha256) |

The MusicXML bundle contains source and build materials for the iOS and Android integration. It includes the original GeneralUser SF2 bank and its license. The optional Debug-only MS Basic bank is not included; its complete notice and pinned download/conversion instructions are included. The UniFFI inventory includes both runtime and build-time crates.

See the [complete manifest](bundles/imslp-musicxml-uniffi-sources-2026-10-31/manifest.json), [rebuild instructions](bundles/imslp-musicxml-uniffi-sources-2026-10-31/README.md), and [archive identity](bundles/imslp-musicxml-uniffi-sources-2026-10-31/publication.json).

FFmpeg sources and build artifacts continue to be distributed separately through [imslp/ffmpeg-kit](https://github.com/imslp/ffmpeg-kit/releases/tag/imslp-v6.0-lgpl-16kb).

## Verify a download

Download the named `.zip` and `.zip.sha256` assets from the same release into one directory. GitHub's automatically generated **Source code (zip)** and **Source code (tar.gz)** contain this repository's metadata, not the full dependency sources.

For the MusicXML and UniFFI bundle above:

```sh
shasum -a 256 -c imslp-musicxml-uniffi-sources-2026-10-31.zip.sha256
unzip imslp-musicxml-uniffi-sources-2026-10-31.zip -d imslp-musicxml-uniffi-sources-2026-10-31
cd imslp-musicxml-uniffi-sources-2026-10-31
shasum -a 256 -c SHA256SUMS
```

The archive's `README.md` explains how to use the sources and build materials. Upstream archives retain their original license and copyright files. The manifest records pinned download URLs and hashes; `SHA256SUMS` covers every payload file in the ZIP.

## Licenses and scope

Each component retains its own license. This repository does not replace or relicense those terms; read the original notices included with the relevant component. GeneralUser GS and the optional MS Basic bank have separate licenses from the notation and synthesis engines.

These bundles supply third-party sources, notices, and selected build/conversion materials. They do not contain private IMSLP application source, app binaries, credentials, or user data. Source publication alone is not a statement that all distribution-channel requirements have been satisfied, and these bundles are not application relinking kits.

## Version retention

Published tags and source assets are retained for recipients of the corresponding app versions. Corrections or changes to the covered materials receive a new source-bundle version; existing assets are not silently replaced. App release numbers and source-bundle versions are independent.
