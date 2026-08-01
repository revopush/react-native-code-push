# Revopush React Native SDK

![NPM Downloads](https://img.shields.io/npm/dm/%40revopush%2Freact-native-code-push)

### Next-generation OTA updates for React Native and Expo

[Revopush](https://revopush.org/) delivers JavaScript and asset updates directly to your users without waiting for a new App Store or Google Play release. Revopush 2.x replaces full-bundle downloads with automatic byte-level diffs, while keeping the familiar CodePush API and workflows.

| **100x smaller payloads** | **Diffs from the first OTA** | **CodePush compatible** |
|---|---|---|
| Download only what changed. Actual savings depend on the update. | Create a native base release and the first OTA on top of it can already be a diff. | Migrate existing apps without rebuilding your release workflow from scratch. |

[Dashboard](https://app.revopush.org) · [Documentation](https://docs.revopush.org/) · [How binary diff updates work](https://revopush.org/react-native-ota-payloads-binary-diffs)

## Proven at global scale

| **1B+ requests processed** | **300M+ SDK installations** | **99.9% historical SLA** | **SOC 2 compliant** |
|---|---|---|---|
| Production traffic handled by the Revopush platform. | A mature delivery footprint across real-world applications. | A consistent track record of service availability. | Independently assessed security controls for production workloads. |

Revopush runs on a geo-distributed architecture designed to deliver releases with low latency in every region, so teams can ship updates quickly to users anywhere in the world.

## Contents

- [Proven at global scale](#proven-at-global-scale)
- [What is Revopush?](#what-is-revopush)
- [How it works](#how-it-works)
- [Supported versions](#supported-versions)
- [Getting Started](#getting-started)
  - [Set up with an AI agent](#set-up-with-an-ai-agent)
  - [Manual React Native setup](#manual-react-native-setup)
- [Expo Support](#expo-support)
- [Plugin Usage](#plugin-usage)
- [Releasing Updates](#releasing-updates)
- [Advanced Workflows](#advanced-workflows)
- [API Reference](#api-reference)
- [Store Guideline Compliance](#store-guideline-compliance)
- [Troubleshooting](#troubleshooting)

## What is Revopush?

Revopush is a hosted OTA platform for React Native and Expo. It combines a dashboard, CLI, global delivery service, and client SDK so teams can publish, target, monitor, promote, and roll back JavaScript and asset updates.

- Compatible with the CodePush JavaScript API and established deployment workflows
- Supports modern React Native, including the New Architecture
- Supports Expo projects through the official config plugin
- Includes rollback controls for reverting a problematic release
- Provides extended analytics by individual release and across the entire application
- Supports percentage-based rollouts, from a controlled user cohort to 100%
- Integrates with popular CI/CD platforms including GitHub Actions, Bitrise, and CircleCI
- Supports multiple deployments and code signing

OTA updates can change JavaScript and bundled assets. Changes to native code, native dependencies, permissions, or platform configuration still require a new store binary.

## How it works

A React Native store binary contains a JavaScript bundle and its assets. The Revopush SDK checks the deployment configured in that binary, downloads a compatible update, verifies it, and applies it according to your install policy.

Revopush 2.x can use the JavaScript and assets inside an IPA, APK, or AAB as a base release. When a new OTA is published, the service generates byte-level patches and sends supported clients only the bytes they need. If a diff cannot be used, Revopush safely falls back to the full update.

After you create a native base release with `revopush release-native`, even the first OTA update on top of that store release can be delivered as a diff. Revopush 2.0 can reduce payloads by 10–100x depending on what changed; this is a potential reduction, not a fixed guarantee.

## Supported versions

Use the Revopush 2.x SDK for all new integrations:

| Project | Supported version | Revopush SDK |
|---|---:|---:|
| React Native | `< 0.76` | Not supported; upgrade React Native or use the [legacy Microsoft client](https://github.com/microsoft/react-native-code-push) |
| React Native | `0.76–0.82` | `@revopush/react-native-code-push@2.5.1` |
| React Native | `0.83+` | `@revopush/react-native-code-push@2.6.x` |
| Expo | `52–54` | SDK `2.5.1` + Expo plugin `1.0.1` |
| Expo | `55+` | SDK `2.6.0` + Expo plugin `1.1.0` |

## Getting Started

### Set up with an AI agent

The fastest way to integrate Revopush is with the official [Revopush agent skills](https://github.com/revopush/skills). They help an AI coding agent detect your project type and apply the React Native or Expo integration.

For Codex, Cursor, Gemini CLI, and other agents that support the portable skills format:

```bash
npx skills@latest add revopush/skills
```

For Claude Code:

```text
/plugin marketplace add revopush/skills
/plugin install revopush
```

Then ask your agent:

> Set up Revopush OTA updates in my React Native app. Detect my React Native or Expo version, install the compatible Revopush 2.x SDK, configure iOS and Android, wrap the root component with `codePush()`, and use deployment key placeholders that I can replace.

The agent configures the codebase, but you still need to [create a Revopush account](https://app.revopush.org/register), create your applications and deployments, and provide the iOS and Android deployment keys from the dashboard.

### Manual React Native setup

1. [Create an account](https://app.revopush.org/register), then create separate applications for iOS and Android. Each application includes Staging and Production deployments with their own keys.
2. Install the SDK version that matches your React Native version:

   ```bash
   # React Native 0.76–0.82
   npm install --save @revopush/react-native-code-push@2.5.1

   # React Native 0.83+
   npm install --save @revopush/react-native-code-push@2.6.0
   ```

3. Complete the native setup for [iOS](docs/setup-ios.md) and [Android](docs/setup-android.md). Use `https://api.revopush.org` as the server URL and configure a different deployment key for each platform.
4. Wrap your root component with `codePush()` as shown in [Plugin Usage](#plugin-usage).
5. Install the [Revopush CLI](#releasing-updates) and publish a test update from a release build.

For the current end-to-end flow and modern native templates, see the [full setup guide](https://docs.revopush.org/intro/getting-started).

## Expo Support

Revopush supports Expo SDK 52 and newer through the official [`@revopush/expo-code-push-plugin`](https://github.com/revopush/expo-code-push-plugin). The plugin applies the required native changes during prebuild.

Revopush does **not** work in Expo Go because it requires native code. Use a development/native build, EAS Build, or your own native build pipeline.

Install the SDK and config plugin as a matched pair:

```bash
# Expo SDK 52–54
npx expo install @revopush/react-native-code-push@2.5.1 @revopush/expo-code-push-plugin@1.0.1

# Expo SDK 55+
npx expo install @revopush/react-native-code-push@2.6.0 @revopush/expo-code-push-plugin@1.1.0
```

Add the plugin to the `plugins` array in `app.json` or `app.config.js`:

```json
[
  "@revopush/expo-code-push-plugin",
  {
    "ios": {
      "CodePushDeploymentKey": "YOUR_IOS_DEPLOYMENT_KEY",
      "CodePushServerUrl": "https://api.revopush.org"
    },
    "android": {
      "CodePushDeploymentKey": "YOUR_ANDROID_DEPLOYMENT_KEY",
      "CodePushServerUrl": "https://api.revopush.org"
    }
  }
]
```

Generate the native projects:

```bash
npx expo prebuild --clean
```

Wrap the Expo Router root layout in `app/_layout.tsx`:

```tsx
import codePush from "@revopush/react-native-code-push";

function RootLayout() {
  // ...
}

export default codePush(RootLayout);
```

Deployment keys are written into the native projects during prebuild. Run `npx expo prebuild --clean` again whenever you change the Revopush plugin configuration. See the [complete Expo guide](https://docs.revopush.org/intro/expo) for release builds, iOS deployment targets, and troubleshooting.

## Plugin Usage

For most apps, wrap the root component with the `codePush()` higher-order component:

```tsx
import codePush from "@revopush/react-native-code-push";

function App() {
  // ...
}

export default codePush(App);
```

By default, the SDK checks on app start, downloads an available update silently, and installs non-mandatory updates on the next restart. Mandatory updates use the immediate install mode by default.

For a custom update UI or schedule, switch to manual checks and call `sync()` yourself:

```tsx
import codePush from "@revopush/react-native-code-push";

const options = {
  checkFrequency: codePush.CheckFrequency.MANUAL,
};

async function checkForUpdates() {
  await codePush.sync({
    installMode: codePush.InstallMode.ON_NEXT_RESTART,
  });
}

function App() {
  // Call checkForUpdates() from your own UI or lifecycle logic.
}

export default codePush(options)(App);
```

See the [JavaScript API reference](docs/api-js.md) for update dialogs, progress callbacks, install modes, `checkForUpdate()`, and fully manual update handling.

## Releasing Updates

Install the current Revopush CLI and authenticate:

```bash
npm install -g @revopush/code-push-cli
revopush login
revopush -v
```

### Enable diffs from the first OTA

For each store binary that should serve as a diff baseline, create a native base release from its IPA, APK, or AAB:

```bash
revopush release-native <appName> ios ./path/to/app.ipa
revopush release-native <appName> android ./path/to/app.apk
revopush release-native <appName> android ./path/to/app.aab
```

The base release is a snapshot of the JavaScript and assets shipped in that binary. After it exists, publish regular updates as usual:

```bash
# Bare React Native
revopush release-react <appName> ios -d Staging
revopush release-react <appName> android -d Staging

# Expo
revopush release-expo <appName> ios -d Staging
revopush release-expo <appName> android -d Staging
```

Diff generation is automatic on the 2.x SDK line. Revopush generates the best compatible patch for each client and falls back to a full update when required.

Useful release options include:

```bash
# Mandatory update with release notes
revopush release-react MyApp ios -d Staging -m --description "Fix checkout crash"

# Gradual rollout to 25% of users
revopush release-react MyApp android -d Production -r 25

# Target compatible 1.2.x store binaries
revopush release-react MyApp android -d Staging -t "~1.2.0"
```

Read the [Revopush CLI documentation](https://docs.revopush.org/cli/getting-started) for more CLI workflows and commands.

> Test OTA delivery in a release build. Debug builds load JavaScript from Metro and do not apply the downloaded OTA bundle.

## Advanced Workflows

### Staging, promotion, and rollback

Each application includes Staging and Production deployments. A typical production flow is:

1. Publish to Staging with `revopush release-react` or `revopush release-expo`.
2. Install the update in a staging/release build and verify it on real devices.
3. Promote the tested release without rebuilding it:

   ```bash
   revopush promote <appName> Staging Production
   revopush promote <appName> Staging Production -r 25 --des "gradual rollout"
   ```

4. If necessary, roll Production back:

   ```bash
   revopush rollback <appName> Production
   revopush rollback <appName> Production -r v4
   ```

Use separate applications and deployment keys for iOS and Android so releases cannot cross platforms.

### Dynamic deployment assignment

You can override the deployment embedded in the binary at runtime. This is useful for beta groups, internal testers, or controlled experiments:

```tsx
codePush.sync({ deploymentKey: userProfile.codePushDeploymentKey });
```

Create a custom deployment and target releases to it:

```bash
revopush deployment add MyApp beta
revopush release-react MyApp android -d beta
```

Treat deployment keys as routing identifiers. Keep authorization and audience eligibility in your own backend or application logic.

### CI/CD

Revopush can be added to an existing release pipeline with:

- [GitHub Actions](https://github.com/revopush/revopush-github-action)
- [Bitrise](https://github.com/revopush/bitrise-steplib)
- [CircleCI](https://github.com/revopush/revopush-circleci-orb)

### Migrating from App Center

Existing CodePush applications can keep the familiar client API and deployment model. Follow the [App Center migration guide](https://docs.revopush.org/migration/guide) to move applications, deployments, and release automation to Revopush.

## API Reference

- [JavaScript API](docs/api-js.md)
- [Objective-C API for iOS](docs/api-ios.md)
- [Java API for Android](docs/api-android.md)
- [Multi-deployment testing for iOS](docs/multi-deployment-testing-ios.md)
- [Multi-deployment testing for Android](docs/multi-deployment-testing-android.md)

## Store Guideline Compliance

Apple and Google permit some interpreted-code and JavaScript update scenarios, but your application and every OTA update remain subject to the current store agreements and review policies. Revopush cannot ship native code changes or bypass platform security.

Before enabling OTA updates in production, review the current [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/) and [Google Play Device and Network Abuse policy](https://support.google.com/googleplay/android-developer/answer/9888379). Avoid using OTA delivery to materially change the purpose or native behavior of an approved application.

## Troubleshooting

Start with the SDK logs, which are prefixed with `[CodePush]`, then check these common causes:

| Symptom | What to check |
|---|---|
| No update appears | Run a release build, not a debug build or Expo Go. |
| Update is not discovered | Confirm the app version matches the release target and the binary uses a key from the same deployment. |
| Update downloads but is not visible | The default mode installs on the next restart. Launch once to download, then relaunch to apply. |
| Update rolls back after restart | When using a fully manual flow without the HOC, call `codePush.notifyAppReady()` after a healthy launch. |
| iOS receives an Android update, or vice versa | Use separate applications and deployment keys for each platform. |
| Expo configuration changes have no effect | Run `npx expo prebuild --clean` again and create a new native build. |
| Server returns `404` | Verify the deployment key with `revopush deployment ls <appName> -k`. |

For more help, see [common problems](https://docs.revopush.org/troubleshooting/troubleshooting) or contact [support@revopush.org](mailto:support@revopush.org).

## License

MIT
