# IMSLP MusicXML and UniFFI source delivery

This archive supplies upstream library sources, notices and the integration's
build/conversion scripts. `manifest.json` records the download URLs and SHA256
digests. `SHA256SUMS` covers every included payload file. It contains no private
IMSLP application source, credentials, user data or application binary.

## Contents

- Verovio 6.3.0, commit `425dd7b6ba26edcecbe345c10b15586d095743d9`: the complete
  upstream archive, including embedded libraries, engraving fonts, generated
  LibMEI code and copyright/license notices. The engine is LGPL-3.0-or-later;
  embedded components retain their own licenses. The integration disables
  Humdrum support and does not modify the toolkit sources.
- FluidSynth 2.6.0-imslp.1: upstream 2.6.0 plus the included local
  CVE-2026-111123 patch. GCEM and Signalsmith Audio Basics are pinned to the
  release's Git tree. Signalsmith's linear, dsp and hilbert-iir dependencies
  are also archived by immutable commit and SHA256, with their original notices.
  iOS builds a dynamic framework without libsndfile. Android builds a shared
  engine for API 24 and retains the official release's codec libraries.
- Android source dependencies matching FluidSynth's release pipeline: libsndfile
  1.2.2, libogg 1.3.6, libvorbis 1.3.7, FLAC 1.4.3, Opus 1.5.2 and Oboe 1.10.0.
  Oboe is included for reproducing the historical upstream release; the local
  patched engine disables its unused Oboe driver. The app obtains its Oboe
  runtime from react-native-audio-api, not the FluidSynth ZIP.
- GeneralUser GS 2.0.3: original SF2 bank and its complete License v2.0. Android
  converts this bank to SF3; iOS uses the original SF2. Sample-provenance
  qualifications in the original license are retained.
- MS Basic: complete pinned MIT notice and acknowledgements. This optional
  Debug-only comparison bank is not included in a Release app. Its pinned SF3
  URL and SHA256 are in `setup_debug_ms_basic.sh`; that script converts to SF2.
- UniFFI 0.28.3: the nine locked MPL-2.0 crate archives, including build-time
  components. Their digests match the app's Cargo.lock. Each crate contains its
  source, original license notices and SPDX metadata. The complete MPL-2.0
  text is supplied separately under `build-materials/scripts/licenses/texts/`.
  Including a build-time crate here does not mean its executable code is shipped
  in the app.

FFmpeg is distributed separately through
<https://github.com/imslp/ffmpeg-kit/tree/imslp-v6.0-lgpl-16kb>.

## Verify and unpack

From the unzipped delivery directory, run `shasum -a 256 -c SHA256SUMS`.
Source archives are under `sources/`. Build materials retain their repository
paths under `build-materials/`. Use a fresh working directory when running
upstream build scripts; some scripts remove their own previous build output.
Do not run them in a directory containing unrelated work.

## Rebuild libraries

### Verovio

Prerequisites: CMake, a C++20 toolchain; Xcode for iOS or Android NDK 27 and
Android SDK CMake 3.22.1 for Android.

Unpack `verovio-6.3.0.tar.gz`. For iOS, configure the included
`build-materials/apps/native/modules/verovio-module/ios/cmake` project with
`VEROVIO_SOURCE` pointing to the unpacked source. The exact two SDK invocations,
framework headers/module map and XCFramework packaging are documented by
`setup_verovio_ios.sh`. Its checkout assertions apply when using a Git checkout;
the supplied source archive instead has the digest in `manifest.json`.

For Android, use the source's `cmake` project with the four ABI configurations
and flags from `setup_verovio.sh`, including `BUILD_AS_ANDROID_LIBRARY=ON` and
`NO_HUMDRUM_SUPPORT=ON`. That script also identifies the packaged Bravura,
Leipzig and text metrics resources. The complete font sources and original
notices remain in the upstream archive.

### FluidSynth

Before building, unpack the two submodule archives into the corresponding empty
directories in `fluidsynth-2.6.0` (strip each archive's top directory):

- `gcem/`: commit `012ae73c6d0a2cb09ffe86475f5c6fba3926e200`.
- `signalsmith-audio-basics/`: commit `012d2be17b0eb6839628f8c73687c4ccccc1bb01`.

`manifest.json` records their `unpackTo` paths and digests. Missing Signalsmith
silently disables effects that the shipped framework includes, so do not build
from the top-level GitHub archive alone. The upstream `test/manual` submodule
contains optional manual-test data, not library code, and is not included.

Use the included `tools/build_fluidsynth.py` from the score-synth module,
with Python 3.11.8+, CMake 3.24+ and Xcode (iOS/host) or Android NDK 27.1.12297006.
It reads this delivery's `manifest.json` and verifies the included archives under
`sources/`; no Git checkout or moving toolchain download is required. It applies
the local patch, enables Signalsmith reverb/limiter from the pinned sources,
and fails if those features are absent. The upstream version remains 2.6.0;
the runtime reports `2.6.0-imslp.1` to identify the local security revision.

From this delivery's root, for an iOS XCFramework:

```sh
python3 build-materials/apps/native/modules/score-synth-module/tools/build_fluidsynth.py ios --work /tmp/imslp-fluidsynth-rebuild
```

Use `host` instead of `ios` for a static ASan/UBSan test build. The JSON build
record gives the source and output directories. Compile `tests/modulator-bounds.c`
against its `src/libfluidsynth.a`, using the source `include/` and build `include/`
paths, `-fsanitize=address,undefined -fno-sanitize-recover=all -lc++ -lpthread -lm`.
Then run `tests/check-modulator-bounds.py <executable> <outside-output-directory>`.
It creates synthetic sine-wave SoundFonts and exercises the real loader, note-on,
control-change and PCM output, including PMOD, IMOD, DMOD and public API bounds.

For Android, use `android --ndk <NDK> --android-prebuilt <verified-release-dir>`.
The prebuilt directory comes from the SHA256-pinned API-24 ZIP documented in
`setup_score_synth.sh`. Only its libsndfile and codec libraries are reused;
FluidSynth itself is rebuilt, with 16 KiB ELF alignment. Its libsndfile header
comes from the included matching 1.2.2 source. The upstream `.azure` files and
included dependency archives also document rebuilding those unchanged codecs.
The NDK/system SDK and host tools are separate prerequisites. These instructions
do not claim bit-for-bit reproduction across toolchain versions.

### SoundFonts and UniFFI

`setup_score_synth.sh` documents GeneralUser's SF3 conversion using the included
`sf2_to_sf3.py` and host FFmpeg/Vorbis quality 8. `setup_score_synth_ios.sh`
documents the unchanged SF2 hash and patched source build.

`setup_debug_ms_basic.sh` pins the optional bank, its notice and host Python
decoder versions; `sf3_to_sf2.py` is included. Host decoder packages do not enter
the iOS app. The scripts' download steps can use the verified matching files
already present in this archive instead of fetching them again.

UniFFI sources can be obtained directly from each matching `.crate` archive;
they are standard tar.gz archives. No modifications to these upstream crates
are made by this source-delivery change. MPL source availability applies to the
covered files, not to unrelated proprietary application files.

## Publication and release boundary

Source bundles are distributed through the versioned Releases in
<https://github.com/imslp/third-party-sources>. Download the named source ZIP
and its accompanying `.zip.sha256` asset from the release matching the bundle's
`manifest.json`. GitHub's automatically generated repository source archive
does not contain these library sources. Keep old versions available while
corresponding app versions are distributed. The app's source entries link to
the matching published release; components outside that bundle retain their
version-specific upstream source links.

This package is not the private application's object-code/relinking kit. A
recipient's compatible-library replacement/relinking and applicable installation
route under the actual signed-store terms still need separate verification.
An iOS simulator build does not establish that route. Verovio's store metadata
credit and the selected EULA also remain release responsibilities.
