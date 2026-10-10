# Clash-Legacy

English · [简体中文](README.zh-CN.md)

[![Active repository](https://img.shields.io/badge/ProjectClash-Active_repository-2563EB?logo=github)](https://github.com/ProjectClash/Clash)
[![Website](https://img.shields.io/badge/Website-Official-2563EB)](https://clash.md/)
[![App Store](https://img.shields.io/badge/App_Store-Download-black?logo=apple&logoColor=white)](https://apps.apple.com/app/id6794257189)

> [!IMPORTANT]
> **Development has moved to [Clash](https://github.com/ProjectClash/Clash).**
>
> This is the historical repository formerly located at `TokenPLS/Hako`, transferred to ProjectClash on October 10, 2026. It is retained as an archive and will receive no further code updates or releases.
>
> Use the active repository for current source, build instructions, releases and [new issues](https://github.com/ProjectClash/Clash/issues). Outstanding reports have been transferred with their discussions; their original issue links redirect to the new locations.

Historical commits, contributor attribution, branches, tags, releases and download assets are retained. Stars and fork relationships remain attached to this historical repository. Earlier names and build instructions below describe the historical version, not the current project.

<details>
<summary>Historical README</summary>

<p align="center">
  <img src="assets/hako-logo-256.png" width="128" height="128" alt="Hako">
</p>

# Hako

English · [简体中文](README.zh-CN.md)

[![Website](https://img.shields.io/badge/Website-Official-2563EB)](https://clash.md/)
[![App Store Download](https://img.shields.io/badge/App_Store-Download-black?logo=apple&logoColor=white)](https://apps.apple.com/app/id6794257189)
[![Telegram Channel](https://img.shields.io/badge/Telegram-Channel-26A5E4?logo=telegram&logoColor=white)](https://t.me/clashbyhako)
[![Telegram Group](https://img.shields.io/badge/Telegram-Group-26A5E4?logo=telegram&logoColor=white)](https://t.me/+t__WNRvjUbk3M2Nl)

Hako is a proxy kernel based on **mihomo v1.19.31**, with Go bindings and build tooling for Apple applications. It produces `Hako.xcframework` for iOS, macOS and tvOS.

To install the official app, use the App Store link above. This repository is for developers building or integrating the kernel.

## Upstream history and modifications

Hako is an independent derivative of [MetaCubeX/mihomo](https://github.com/MetaCubeX/mihomo), based on [v1.19.31](https://github.com/MetaCubeX/mihomo/tree/v1.19.31), commit `ab405bad5beeeac8b003bb01f60f134f6df54471`. It is not affiliated with MetaCubeX. The upstream project asks unaffiliated downstream projects not to use “mihomo” in their names.

This repository preserves the original upstream commits and contributor identities alongside Hako's existing public history. Stable upstream upgrades are recorded as merge commits: the first parent continues Hako's history and the second points to the official upstream commit. Hako changes continue as incremental commits for separate logical changes. Compare the upstream baseline with a Hako revision to inspect the complete delta, including Apple bindings, SDK build tooling and kernel adaptations.

The upstream GPL-3.0 license remains in [LICENSE](LICENSE). See [NOTICE](NOTICE) and [THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md) for attribution and dependency licenses.

## Project repositories

| Repository | Contents |
| --- | --- |
| [Hako](https://github.com/TokenPLS/Hako) | Proxy kernel, Go bindings and Apple SDK build tools |
| [Hako-Adapter](https://github.com/TokenPLS/Hako-Adapter) | Swift packet-flow bridge and provider lifecycle components |
| [Hako-Client](https://github.com/TokenPLS/Hako-Client) | Native iOS, iPadOS, macOS and tvOS applications and extensions |

The upstream baseline version describes the proxy engine. It is separate from the App Store app version and the SDK release tag. Untagged source on `main` is pre-release; pin a specific revision when integrating it. Published SDK versions and downloads are listed in [Releases](https://github.com/TokenPLS/Hako/releases).

SDK downloads include all five Apple slices, license notices and a source manifest. Each release also provides the notices separately, the source archive for the embedded EasyTier core used by macOS, and `SHA256SUMS` for checking downloaded assets.

## What is included

- The mihomo-based proxy engine: protocol implementations, DNS, routing rules, proxy groups and providers.
- `bind/hako`: the API exposed to Apple applications through gomobile.
- `cmd/build_libbox`: SDK generation and platform packaging.
- [`docs/config.yaml`](docs/config.yaml): configuration reference included with the source.

An application supplies configuration, storage, the Network Extension integration and signing. Platform capabilities differ; a configuration accepted by the parser does not establish end-to-end support for every protocol or rule on every platform.

The iOS and tvOS SDK slices use `no_easytier` and do not include EasyTier. The macOS slice does not apply that exclusion. Platform permissions, TUN stacks and outbound implementations still determine what is available at runtime.

## Build the Apple SDK

Use macOS with the full Xcode installation and the iOS, macOS and tvOS SDKs. The current build was checked with Xcode 27.0. The binding module declares Go 1.25.0 and selects toolchain Go 1.26.6 in [`bind/hako/go.mod`](bind/hako/go.mod); allow Go to obtain that toolchain, or install it explicitly.

Clone the repository with its history and tags, then install the pinned gomobile tools:

```sh
git clone https://github.com/TokenPLS/Hako.git
cd Hako
go install github.com/sagernet/gomobile/cmd/gomobile@v0.1.13
go install github.com/sagernet/gomobile/cmd/gobind@v0.1.13
make lib_apple
```

The core version comes from `UPSTREAM_VERSION`, shipped with the source, so a source upgrade does not inherit an older SDK tag. The SDK release version still comes from a release tag at the current commit; ordinary source builds are labeled `dev-<commit>`.

The output is `Hako.xcframework`, containing five platform slices:

| Platform | Architectures |
| --- | --- |
| iOS device | arm64 |
| iOS Simulator | arm64, x86_64 |
| macOS | arm64, x86_64 |
| tvOS device | arm64 |
| tvOS Simulator | arm64, x86_64 |

The SDK framework is static: choose **Do Not Embed** when linking it directly, and link `libresolv`. An app and its extension can share a dynamic framework wrapping the static SDK to avoid packaging the kernel twice; the iOS and macOS Client projects use this arrangement. Use the generated headers for the API of your pinned revision. For a working application integration, see [Hako-Client](https://github.com/TokenPLS/Hako-Client).

## Development and feedback

For the root Go module:

```sh
go build ./...
go test ./...
```

The Apple binding has its own module and tests under `bind/hako`. An SDK build alone does not establish runtime behavior on a signed device.

Report kernel problems in this repository's [Issues](https://github.com/TokenPLS/Hako/issues). Include the source revision, platform, reproduction steps, expected behavior and observed result. Use a minimal sample configuration with secrets removed. For app interface or installation problems, use [Hako-Client Issues](https://github.com/TokenPLS/Hako-Client/issues).

For security reports, follow [SECURITY.md](SECURITY.md).

## License and credits

Hako is based on [MetaCubeX/mihomo](https://github.com/MetaCubeX/mihomo) and the work of its contributors. Hako is an independent project and is not affiliated with or endorsed by MetaCubeX.

The project is licensed under [GPL-3.0](LICENSE). See [NOTICE](NOTICE) and [THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md) for attribution and dependency licenses.

</details>
