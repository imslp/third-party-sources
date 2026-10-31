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
- FluidSynth 2.6.0: upstream source plus its separately archived GCEM and
  Signalsmith Audio Basics submodules, pinned to the release's Git tree.
  Their original Apache-2.0 and MIT notices are included. iOS uses the official dynamic
  framework and disables libsndfile. Android uses the official API-24 release.
- Android source dependencies matching FluidSynth's release pipeline: libsndfile
  1.2.2, libogg 1.3.6, libvorbis 1.3.7, FLAC 1.4.3, Opus 1.5.2 and Oboe 1.10.0.
  Oboe here is a build dependency for reproducing upstream FluidSynth; the app
  obtains its Oboe runtime from react-native-audio-api, not the FluidSynth ZIP.
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

The source includes the upstream build instructions. For iOS, follow
`contrib/ios_build.sh` from the source root: it builds a shared XCFramework,
uses the C++11 OS abstraction and disables libsndfile/readline. Its upstream
script downloads the ios-cmake toolchain from its master branch; that historical
dependency is not pinned by FluidSynth 2.6.0. This package therefore does not
claim bit-for-bit reproduction of the upstream prebuilt iOS framework.

For Android, `.azure/azure-pipelines-android.yml` and `.azure/cmake-android.yml`
contain the dependency versions, per-ABI configuration and build commands.
Use the accompanying dependency source archives. The pipeline uses NDK
27.3.13750724 and Android API 24. Its published artifact contains additional
optional libraries; `setup_score_synth.sh` explicitly lists the seven `.so`
files IMSLP copies and strips. The NDK/system SDK and host build tools are
separate prerequisites, not app-distributed source archives.

### SoundFonts and UniFFI

`setup_score_synth.sh` documents GeneralUser's SF3 conversion using the included
`sf2_to_sf3.py` and host FFmpeg/Vorbis quality 8. `setup_score_synth_ios.sh`
documents the unchanged SF2 and official iOS framework hashes.

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
