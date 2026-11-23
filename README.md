# KiTplayer SDK for iOS

KiT 앨범을 인식하고 재생하는 플레이어를 앱에 넣을 수 있는 SDK 입니다.
마이크로 KiT 를 인식하고, 앨범의 음원·영상·사진·가사를 SDK 가 제공하는 화면에서 재생합니다.

자세한 안내는 [KiTplayer SDK 문서](https://kitbetter-web.github.io/kit-player-sdk-docs/iOS/tutorial/) 를 참고하세요.

## 요구 사항

| 항목 | 버전 |
| --- | --- |
| iOS | 15.0 이상 |
| Xcode | 26 이상 |

SDK 를 사용하려면 발급받은 `Client ID` 와 `Secret Key` 가 필요합니다.

## 설치

### Swift Package Manager

1. Xcode 에서 **File → Add Package Dependencies…** 를 선택합니다.
2. 아래 주소를 입력하고 **Add Package** 를 누릅니다.

```text
https://github.com/kitbetter-web/muzlive-kit-player-sdk-ios
```

`Package.swift` 로 추가할 때는 아래처럼 적습니다.

```swift
dependencies: [
    .package(url: "https://github.com/kitbetter-web/muzlive-kit-player-sdk-ios", from: "1.0.0"),
],
targets: [
    .target(
        name: "YourApp",
        dependencies: [
            .product(name: "KiTplayerSDK", package: "muzlive-kit-player-sdk-ios"),
        ]
    ),
]
```

`1.0.62-beta1` 처럼 버전 뒤에 이름이 붙은 시험판은 버전 범위로 지정하면 받아지지 않습니다. 시험판을 쓰려면 정확한 버전을 지정하세요.

### CocoaPods

SDK 와 의존 라이브러리(Bitmovin)의 spec 저장소를 함께 추가합니다.

```ruby
source 'https://github.com/bitmovin/cocoapod-specs.git'
source 'https://github.com/kitbetter-web/cocoapod-specs.git'

target 'YourApp' do
  pod 'kitplayer-sdk'
end
```

## 프로젝트 설정

SDK 가 쓰는 기능에 맞춰 `Info.plist` 에 아래 권한 설명을 추가합니다.

| 키 | 쓰는 곳 |
| --- | --- |
| `NSMicrophoneUsageDescription` | KiT 인식 |
| `NSCameraUsageDescription` | QR 코드 인식 |
| `NSLocalNetworkUsageDescription` | 크롬캐스트 |
| `NSBluetoothAlwaysUsageDescription` | 크롬캐스트 |

```xml
<key>NSMicrophoneUsageDescription</key>
<string>키트 인식을 위해 마이크 권한이 필요합니다.</string>
<key>NSCameraUsageDescription</key>
<string>QR코드 인식을 위해 카메라 권한이 필요합니다.</string>
<key>NSLocalNetworkUsageDescription</key>
<string>크롬캐스트 동작을 위해 로컬 네트워크 권한이 필요합니다.</string>
<key>NSBluetoothAlwaysUsageDescription</key>
<string>크롬캐스트 동작을 위해 블루투스 권한이 필요합니다.</string>
```

## 사용법

### 1. 초기화

앱이 시작될 때 한 번 초기화합니다.

```swift
import KiTplayerSDK

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {
        KiTplayer.initialize(with: "YOUR_CLIENT_ID", "YOUR_SECRET_KEY")
        return true
    }
}
```

> `Secret Key` 는 소스 코드나 공개 저장소에 그대로 넣지 마세요. 빌드 설정이나 별도 파일로 분리해 관리하는 것을 권장합니다.

### 2. 플레이어 실행

```swift
// 기본 화면
KiTplayer.shared.start()

// 앱 화면 안에 넣는 형태
KiTplayer.shared.start(with: .embeded)
```

### 3. 에러 받기

```swift
final class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        KiTplayer.shared.addListenerDelegate(self)
    }
}

extension ViewController: KiTPlayerListenerDelegate {
    func didOccurError(_ errorCode: KiTplayerErrorCode) {
        print(errorCode)
    }
}
```

### 4. 전체 화면 회전 지원

영상 전체 화면에서 가로 회전을 쓰려면 `AppDelegate` 에 아래를 추가합니다.

```swift
func application(
    _ application: UIApplication,
    supportedInterfaceOrientationsFor window: UIWindow?
) -> UIInterfaceOrientationMask {
    if let mask = KiTplayer.shared.handleFullScreen(window) {
        return mask
    }
    return .portrait
}
```

## 샘플 앱

`KiTplayerSampleApp` 폴더에 위 내용을 적용한 예제가 있습니다.

## 개인정보 보호와 서명

- SDK 안에 Apple 이 요구하는 개인정보 매니페스트(`PrivacyInfo.xcprivacy`)가 들어 있습니다.
- 1.0.62 부터 xcframework 는 `Apple Distribution: KIHNO Inc. (L8WBW3DU2X)` 로 서명되어 배포됩니다. 서명한 팀은 Xcode 의 File inspector 나 `codesign -dv` 로 확인할 수 있습니다.

## 라이선스

[LICENSE.md](LICENSE.md) 를 참고하세요.
