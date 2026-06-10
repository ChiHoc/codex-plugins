# Android Platform Adapter

Use this adapter when the Maestro target is an Android app running on an emulator or physical device. Android Native, Jetpack Compose, React Native, and Flutter Android targets all use this adapter; framework differences belong in the framework-specific notes below.

## Supported Target

The target is supported when Maestro can connect through ADB, the app is installed or installable on the target device, the `appId` maps to the Android package/applicationId, and flows can reach user-visible UI from a repeatable state.

## Preflight

Run or report:

```bash
maestro --version
java -version
adb devices
./gradlew tasks --all
./gradlew :<module>:assemble<Variant>
adb install -r <apk-path>
maestro test .maestro/<feature>/<flow>.yaml
```

Discover and record:

- Gradle module and build variant.
- APK path.
- Android package/applicationId used as Maestro `appId`.
- Emulator or physical device id.
- App launch route and any required deep link or debug fixture.
- Existing `.maestro/` flow conventions.
- Reset strategy, usually `launchApp: { clearState: true }`, `clearState`, deep links, or debug fixtures.

## Minimal Flow Shape

```yaml
appId: ${APP_ID}
---
- launchApp:
    clearState: true
- extendedWaitUntil:
    visible: "<ready marker>"
    timeout: 10000
- tapOn: "<stable action target>"
- assertVisible: "<expected visible result>"
```

Use `maestro test -e APP_ID=<android.package> .maestro/<feature>/<flow>.yaml` when keeping flows cross-platform.

## Selector Strategy

Prefer, in order:

1. Stable user-visible text.
2. Content descriptions / accessibility labels.
3. Android resource IDs.
4. Compose semantics/test tags that are visible to Maestro.
5. Relative selectors such as `childOf`, `below`, `above`, or `containsChild`.

Coordinates, screenshots, OCR, and index-only selectors are last resorts and must be recorded as brittle.

## Framework-Specific Notes

- Jetpack Compose: expose stable semantics/test tags or content descriptions for important controls and dynamic state.
- XML/View: prefer resource IDs and content descriptions when product text is unstable.
- React Native Android: prefer accessibility labels and stable visible text; confirm the rendered hierarchy before relying on framework-level test IDs.
- Flutter Android: expose semantics for actionable controls and important assertions; avoid selecting text that only exists in canvas-like rendering without semantics.

## Android System Dialogs

Android permission dialogs are outside app selectors. Handle expected dialogs explicitly and optionally:

```yaml
- tapOn:
    text: "Allow"
    optional: true
- tapOn:
    text: "While using the app"
    optional: true
```

If a permission dialog blocks the target behavior unexpectedly, classify it as setup/permission failure, not RED.

## Failure Classification

Before deciding a flow is RED, classify:

- environment: Maestro, Java, adb, device, emulator, or Gradle unavailable,
- build/install: APK missing, install failed, wrong variant,
- launch: wrong applicationId or app cannot start,
- fixture/setup: seed, fake data, deep link, backend/mock, permission, or reset failed,
- YAML: syntax or unsupported command,
- selector: target not found because selector is wrong or brittle,
- runtime crash: app crashes before the behavior under test,
- Android driver: Maestro driver package, instrumentation, gRPC, or `deviceInfo` failed before the app flow starts,
- masked action: `optional: true` hid a required interaction,
- missing behavior: flow reached the target state and failed because the requested user-visible behavior does not exist.

Only missing behavior can count as a correct RED.

## Android Driver gRPC Gate

If Maestro fails before the app flow starts with an Android driver error such as:

```text
maestro.drivers.AndroidDriver.runDeviceCall: Not able to reach the gRPC server while processing deviceInfo command
io.grpc.StatusRuntimeException: UNAVAILABLE
Command failed (tcp:<port>): closed
```

classify it as an environment/driver blocker, not RED. Diagnose before changing production code:

```bash
adb devices -l
adb shell pm path dev.mobile.maestro
adb shell pm path dev.mobile.maestro.test
adb shell pm list instrumentation | rg "dev.mobile.maestro|maestro"
adb forward --list
tail -n 200 ~/.maestro/tests/<latest>/maestro.log
adb logcat -d -t 1000 | rg "dev\.mobile\.maestro|AndroidRuntime|FATAL|INSTRUMENTATION|MaestroDriver|Connection refused"
```

If the device is connected but the driver app/server package is missing or Maestro's automatic driver reinstall path keeps failing, use manual fixed-port driver recovery and rerun the same flow:

```bash
APK_DIR="${TMPDIR:-/tmp}/maestro-driver-apks"
mkdir -p "$APK_DIR"
(cd "$APK_DIR" && jar xf "$HOME/.maestro/lib/maestro-client.jar" maestro-app.apk maestro-server.apk)

adb -s "$DEVICE_ID" install -r "$APK_DIR/maestro-app.apk"
adb -s "$DEVICE_ID" install -r "$APK_DIR/maestro-server.apk"
adb -s "$DEVICE_ID" forward --remove-all
adb -s "$DEVICE_ID" shell am force-stop dev.mobile.maestro || true
adb -s "$DEVICE_ID" shell am instrument -w -m \
  -e debug false \
  -e class 'dev.mobile.maestro.MaestroDriverService#grpcServer' \
  -e port 61000 \
  dev.mobile.maestro.test/androidx.test.runner.AndroidJUnitRunner \
  > "$APK_DIR/maestro-instrumentation.log" 2>&1 &

maestro --driver-host-port 61000 --device "$DEVICE_ID" test --no-reinstall-driver .maestro/<feature>/<flow>.yaml
```

Only classify RED/GREEN after `deviceInfo` succeeds and the flow reaches the app screen.

## Diagnosis Source Order

1. Code context for the behavior under test: screen, ViewModel/Presenter, state holder, fixtures, deep links, command wiring, and async side effects.
2. Layout source that affects Maestro observability: XML/resource files or Compose UI, semantics, content descriptions, and test tags.
3. `adb logcat` around the failure window, filtered to app/package logs when useful.
4. Runtime hierarchy inspection for selector visibility and text/id/content-description state.
5. Screenshots only as supplemental evidence when the previous sources do not explain the problem, or when the issue is specifically visual.

Screenshots are not a replacement for reading implementation context or logcat.
