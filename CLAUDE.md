# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

### Development Builds
```bash
# Rootfull (default jailbreak)
./build.sh --rootfull
# Output: com.bandarhl.bhTwitter_[version]_iphoneos-arm.deb

# Rootless (newer jailbreaks)
./build.sh --rootless
# Output: com.bandarhl.bhTwitter_[version]_iphoneos-arm64.deb

# Sideloaded (requires decrypted IPA in packages/)
# Place com.atebits.Tweetie2.ipa in packages/ first
./build.sh --sideloaded
# Output: packages/BHTwitter-sideloaded.ipa

# TrollStore (requires decrypted IPA)
# Place com.atebits.Tweetie2.ipa in packages/ first
./build.sh --trollstore
# Output: packages/BHTwitter-trollstore.tipa
```

### Clean Build
```bash
make clean
rm -rf .theos
```

### Makefile Configuration
- Target iOS: 14.0 minimum
- Architecture: arm64
- Main tweak name: BHTwitter
- Dependencies: Theos build system, cyan tool (for IPA builds)

## Architecture Overview

### Core Components

**Tweak.x** - Main Objective-C++ tweak file containing all Twitter app hooks and modifications. Uses Logos preprocessor syntax (`%hook`, `%orig`, `%end`).

**BHTManager** - Central manager class handling:
- User preferences and settings
- Feature flags management
- Download operations coordination
- Theme and customization state

**TWHeaders.h** - Contains reversed Twitter app class interfaces and method signatures needed for hooking.

### Feature Modules

**BHDownload/** - Video/media download functionality with quality selection
- Direct integration with Twitter's media player
- Private account support

**CustomTabBar/** - Custom tab bar implementation allowing user-defined tab arrangements

**ThemeColor/** - Twitter Blue-style theming system without subscription

**AppIcon/** - Dynamic app icon switching functionality

**BHTBundle/** - Resources bundle containing assets and localization

### Third-party Libraries (vendored)

- **JGProgressHUD/** - Progress indicators
- **SAMKeychain/** - Keychain access for secure storage
- **Colours/** - Color utilities

### Build System

The project uses Theos, a cross-platform build system for iOS tweaks:
- Hooks are processed through Logos preprocessor
- Supports multiple deployment targets (jailbreak, sideloaded, TrollStore)
- Automatic .deb packaging for jailbreak deployment
- IPA injection via cyan tool for sideloaded/TrollStore

### Key Implementation Patterns

1. **Method Swizzling**: Uses Logos `%hook` blocks to intercept Twitter app methods
2. **Feature Flags**: Checks Twitter's internal feature flags before enabling modifications
3. **Preference Storage**: Uses NSUserDefaults with "BHTwitter" prefix
4. **Bundle Resources**: Assets stored in `layout/Library/Application Support/BHT/`

### Testing Approach

No automated tests - features are manually tested on device with Twitter app. Build verification happens through:
1. Successful compilation via `make`
2. Package generation without errors
3. Manual installation and runtime testing

## Important Notes

- Always check Twitter's feature flags availability - features may break when Twitter removes server-side support
- The tweak targets Twitter app bundle ID: `com.atebits.Tweetie2`
- Sideloaded/TrollStore builds require a decrypted Twitter IPA
- GitHub Actions workflow available for automated builds