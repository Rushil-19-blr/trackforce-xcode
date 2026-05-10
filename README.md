# 🏎️ TrackForce

Unlock the Hidden Power of your Mac's Force Touch Trackpad.

TrackForce is a high-performance macOS utility and developer framework that turns your trackpad into a multi-dimensional input device. It combines deep hardware integration with beautiful 3D visualizations and productivity-boosting "Force Shortcuts."

---

## 🌟 Key Features

### 🛠️ TrackForce Shortcuts (Menu Bar Utility)
   Force-Triggered Shortcuts: Launch any app by pressing hard on the trackpad while hitting a key (e.g., Force + M ➔ Music).
   High-Precision Thresholds: Customize exactly how much pressure is required to trigger an action (defaulted to a deliberate 150g+).
   Menu Bar Resident: Runs silently in the background with a native macOS menu bar interface. No Dock clutter.
   Global Monitoring: Works across the entire OS, no matter which app is in focus.

### 🎨 Advanced Force Visualisation
   3D Isometric Mesh: Watch your trackpad transform into a flexible grid that physically bends and dips in real-time as you apply pressure.
   Pressure Heatmaps: Dynamic color-shifting (Cyan ➔ Purple) based on the intensity of your touch.
   Live Force Graph: A scrolling history of your pressure levels with peak force tracking.
   Spray Paint Mode: An organic, pressure-sensitive canvas where harder presses create larger, deeper "splatters."

### 💻 Developer Framework (`OpenMultitouchSupport`)
   Raw Data Access: Get X/Y coordinates, pressure, axis major/minor, and finger angle for every touch.
   Multi-Device Support: Seamlessly switch between built-in and external Magic Trackpads.
   Haptic Integration: Programmatically trigger the trackpad's haptic engine with custom IDs.

---

## 🚀 Installation & Usage

### For Users (App)
1.  Download: Get the latest binary from the [Releases](https://github.com/Rushil-19-blr/trackforce-xcode/releases) page.
2.  Install: Move `TrackForce.app` to your `/Applications` folder.
3.  Run: Open the app. Look for the finger icon in your Menu Bar.
4.  Permissions: If prompted, allow the app to access input/multitouch data. 
        Note: Since this uses private frameworks, you may need to click "Open Anyway" in System Settings > Privacy & Security.

### For Developers (Framework)
Add this repository to your project via Swift Package Manager:

```swift
import OpenMultitouchSupport

let manager = OMSManager.shared

// Start listening for global events
manager.startListening()

Task {
    for await touches in manager.touchDataStream {
        for touch in touches {
            print("Pressure: \(touch.pressure) at (\(touch.position.x), \(touch.position.y))")
        }
    }
}
```

---

## 🏗️ Technical Requirements
   Hardware: A Mac with a Force Touch trackpad (MacBook 2015+, or Magic Trackpad 2+).
   OS: macOS 13.0 Ventura or later.
   Build Tools: Xcode 16.0+ (if building from source).

---

## ⚖️ License & Credits
TrackForce is built upon the `OpenMultitouchSupport` library, a fork of [Kyome22/OpenMultitouchSupport](https://github.com/Kyome22/OpenMultitouchSupport). 

Special thanks to the reverse-engineering work by mhuusko5 and calftrail.

Created with ❤️ for the macOS Power User.


### The data you can get are as follows

```swift
struct OMSPosition: Sendable {
    var x: Float
    var y: Float
}

struct OMSAxis: Sendable {
    var major: Float
    var minor: Float
}

enum OMSState: String, Sendable {
    case notTouching
    case starting
    case hovering
    case making
    case touching
    case breaking
    case lingering
    case leaving
}

struct OMSTouchData: Sendable {
    var id: Int32
    var position: OMSPosition
    var total: Float // total value of capacitance
    var pressure: Float
    var axis: OMSAxis
    var angle: Float // finger angle
    var density: Float // area density of capacitance
    var state: OMSState
    var timestamp: String
}
```

## Development Workflow

This package uses a hybrid approach with both Swift wrapper code and binary XCFramework distribution. Here's how to update the library:

### Updating Swift Wrapper Code Only

When you need to modify the Swift API layer (files in `Sources/OpenMultitouchSupport/`):

1. Edit the Swift files in `Sources/OpenMultitouchSupport/`
2. Commit and push changes to GitHub
3. In consuming projects: Update packages in Xcode (File → Packages → Update to Latest Package Versions)

Since consuming projects typically use `branch: main`, they will automatically get the latest Swift wrapper changes.

### Updating the Binary XCFramework

When you need to modify the underlying Framework code:

1. Update the Framework code in `Framework/OpenMultitouchSupportXCF/`
2. Build new XCFramework: 
   ```bash
   ./build_framework.sh
   ```
3. Create new release:
   ```bash
   ./release.sh 1.0.x  # Replace x with next version number
   ```
4. Update Package.swift to point to the new release URL and checksum
5. Commit and push the Package.swift changes
6. In consuming projects: Update packages in Xcode

### Release Script

The `release.sh` script automates the binary release process:
- Builds the XCFramework
- Creates a GitHub release with the binary artifact
- Updates Package.swift with the correct URL and checksum
- Commits the changes

This workflow allows rapid iteration on the Swift API while maintaining stable binary releases for the underlying multitouch framework.
