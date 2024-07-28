Fastlane 설치 및 운영법에 대한 설명 

Github Actions + Fastlane 으로 iOS 배포 자동화에 대한 글을 작성한다. 

## Fastlane 설정 
```
brew install fastlane // 설치
fastlane init
```
프로젝트 내에  fastlane 폴더 생기게 됩니다. 
fastlane 폴더 내에는  Appflie, Fastfile 파일이 생기며 기능은 아래와 같습니다.

### Appfile 
앱의 id 등 배포 시 필요한 환경에 대한 정보 
```
# app_identifier("[[APP_IDENTIFIER]]") # The bundle identifier of your app
# apple_id("[[APPLE_ID]]") # Your Apple Developer Portal username


# For more information about the Appfile, see:
#     https://docs.fastlane.tools/advanced/#appfile
app_identifier "com.********" # 개발 환경 번들 식별자
apple_id "**@******"
team_id "*******"

for_lane :release do
  app_identifier "com.********" # 프로덕션 환경 번들 식별자
end
```

### Fastfile 
어떤 작업을 할건지 lane 을 정의 

```
default_platform(:ios)

platform :ios do
  desc "Push a new beta build to TestFlight"
  lane :debug do
     begin
      increment_build_number(xcodeproj: "*.xcodeproj")

      build_app(workspace: "*.xcworkspace", scheme: "* Debug")
      upload_to_testflight(
        skip_waiting_for_build_processing: true
      )
      UPLOAD_SYMBOLS_PATH=`xcodebuild -project ./*.xcodeproj -showBuildSettings | grep -m 1 "BUILD_DIR" | grep -oEi "\/.*" | sed 's:Build/Products:SourcePackages/checkouts/firebase-ios-sdk/Crashlytics/upload-symbols:' | tr -d '\n'`
   
      upload_symbols_to_crashlytics(
        gsp_path: "./*/Firebase/GoogleService-Info-dev.plist",
          binary_path: UPLOAD_SYMBOLS_PATH
       )
      clean_build_artifacts
    rescue => ex
      UI.error "Failed to fetch latest TestFlight build number: #{ex.message}"
    end
end
```
###  App Store 인증 

appleid.apple.com 에서 앱 암호를 생성하여 로컬에 저장 후 사용하는 방식 
fastlane 2FA 인증으로 .env 파일을 생성

FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD=""
FASTLANE_SESSION=""
FASTLANE_ITC_TEAM_NAME=""

### Fastlane 실행 
```
fastlane [정의한 lane 이름]
fastlane debug 
```

### 성공 화면 
<img width="815" alt="image" src="https://github.com/user-attachments/assets/3ccd65d5-29f8-443e-90fb-54f49cb75a4f">



## Github Actions 
- GitHub 저장소에서 CI/CD 플팻폼으로 특정 트리거로 실행하여 fastlane 을 실행시켜 테스트플라이트에 배포하도록 작성한다.
```
name: iOS Build
on:
  push:
    branches:
      - 'main'
    tags:
      - 'v*'

jobs:
  upload_testflight:
    runs-on: ["self-hosted", "macOS"]
    steps:
      - uses: actions/checkout@v4
      - uses: ruby/setup-ruby@v1  
        with:   
          ruby-version: '3.2.0'
          self-hosted: true
      - uses: shimataro/ssh-key-action@v2
        with:
          key: ${{ secrets.SSH_KEY }}
          known_hosts: ${{ secrets.KNOWN_HOSTS }}
      - name: Install bundle
        run: bundle install

      - name: Update fastlane 
        run: bundle update fastlane

      - name: Install CocoaPods
        run: |
          gem install cocoapods
          pod install

      - name: Clean Build Folder
        run: |
          xcodebuild clean -project ###.xcodeproj -scheme #### -configuration Debug

      - name: Setup Keychain
        run: |
          security create-keychain -p ${{ secrets.KEYCHAIN_PASSWORD }} ${{ secrets.KEYCHAIN_NAME }}
          security list-keychains -s ${{ secrets.KEYCHAIN_NAME }}
          security unlock-keychain -p ${{ secrets.KEYCHAIN_PASSWORD }} ${{ secrets.KEYCHAIN_NAME }}
          security set-keychain-settings -t 3600 -l ~/Library/Keychains/${{ secrets.KEYCHAIN_NAME }}

      - name: Print code signing identities
        run: security find-identity -v -p codesigning

      - name: Run Fastlane debug
        run: bundle exec fastlane debug --verbose
        env:
          MATCH_PASSWORD: ${{ secrets.MATCH_PASSWORD }}
          KEYCHAIN_NAME: ${{ secrets.KEYCHAIN_NAME }}
          KEYCHAIN_PASSWORD: ${{ secrets.KEYCHAIN_PASSWORD }}
          APP_STORE_CONNECT_API_KEY_KEY_ID: ${{ secrets.APP_STORE_CONNECT_API_KEY_KEY_ID }}
          APP_STORE_CONNECT_API_KEY_ISSUER_ID: ${{ secrets.APP_STORE_CONNECT_API_KEY_ISSUER_ID }}
          APP_STORE_CONNECT_API_KEY_KEY: ${{ secrets.APP_STORE_CONNECT_API_KEY_KEY }}
```

### Match 인증 방식 

https://docs.fastlane.tools/actions/match/  
fastlane  코드 서명과 프로비저닝 프로파일 관리를 자동화하는 도구 
https://github.com/walkmining/fastlane-certificate 

Git 저장소 기반 인증: 
match는 암호화된 인증서와 프로비저닝 프로파일을 Git 저장소에 저장합니다.
팀원들은 이 저장소에 접근하여 필요한 인증서를 공유할 수 있습니다.
저장소는 비공개로 유지되며, 암호로 보호됩니다.

Github action 의 특정 트리거로  Fastlane 실행 
```
APP_STORE_CONNECT_API_KEY_KEY_ID = ENV["APP_STORE_CONNECT_API_KEY_KEY_ID"]
APP_STORE_CONNECT_API_KEY_ISSUER_ID = ENV["APP_STORE_CONNECT_API_KEY_ISSUER_ID"]
APP_STORE_CONNECT_API_KEY_KEY =  ENV["APP_STORE_CONNECT_API_KEY_KEY"]
KEYCHAIN_NAME = ENV["KEYCHAIN_NAME"]
KEYCHAIN_PASSWORD = ENV["KEYCHAIN_PASSWORD"]
```

### Fastlane 
```
default_platform(:ios)
platform :ios do
  #슬랙URL
  before_all do
    ENV["SLACK_URL"] = "https://hooks.slack.com/services/############"
  end

  # Debug 버전 빌드/배포
  lane :debug do
     begin
     
     # SwiftGen 실행
     project_root = File.expand_path('..', Dir.pwd)
     swiftgen_path = File.join(project_root, 'Pods', 'SwiftGen', 'bin', 'swiftgen')
     config_path = File.join(project_root, 'swiftgen.yml')
  
     if File.exist?(swiftgen_path) && File.exist?(config_path)
       sh("#{swiftgen_path} config run --config #{config_path}")
     else
      UI.important("SwiftGen or config file not found")
     end
     
     # 키체인 생성
     create_keychain(
        name: "#{KEYCHAIN_NAME}",
        password: "#{KEYCHAIN_PASSWORD}",
        timeout: 1800,
        default_keychain: true,
        unlock: true,
        lock_when_sleeps: false
     )
     
     # App store connect 연동
     api_key = app_store_connect_api_key(
       key_id: "#{APP_STORE_CONNECT_API_KEY_KEY_ID}",
       issuer_id: "#{APP_STORE_CONNECT_API_KEY_ISSUER_ID}",
       key_content: "#{APP_STORE_CONNECT_API_KEY_KEY}",
       is_key_content_base64: false,
       in_house: false
     )
     
     # 빌드번호 생성
     new_build_number = latest_testflight_build_number(api_key: api_key, app_identifier: "com.dw.walkmining.debug") + 1
     version = get_version_number(target:"Walkmining") + "(" + "#{new_build_number}" + ")"
     
     # 빌드 Increment
     increment_build_number(
        build_number: new_build_number
     )
     
     # signing 세팅
     update_code_signing_settings(
       use_automatic_signing: false,  # 자동 서명 사용 안함
       team_id: "RBFRAWL2VK",  # 팀 ID
       targets: [
         {
          target: "Walkmining Debug",  # 첫 번째 타겟 이름
          build_configuration: "Debug",
          code_sign_identity: "Apple Distribution",
          profile_name: "match AppStore com.dw.walkmining.debug"
         },
        {
          target: "OneSignalNotificationServiceExtension",  # 두 번째 타겟 이름
          build_configuration: "Debug",
          code_sign_identity: "Apple Distribution",
          profile_name: "match AppStore com.dw.walkmining.debug.OneSignalNotificationServiceExtension"
        }
      ]
    )
    sync_code_signing(type: "appstore",
                    app_identifier: ["com.dw.walkmining.debug","com.dw.walkmining.debug.OneSignalNotificationServiceExtension"],
                    readonly: true
     )
     # 인증서 발급
     match(type: "appstore",
           app_identifier: ["com.dw.walkmining.debug","com.dw.walkmining.debug.OneSignalNotificationServiceExtension"],
           readonly: true,
           force_for_new_devices: true,
           keychain_name: "#{KEYCHAIN_NAME}",
           keychain_password: "#{KEYCHAIN_PASSWORD}"
           )
     
     setup_ci
    
     # 빌드 시작
     slack(
            message: "iOS Debug 빌드 시작 :rocket:",
            payload: {
                "Version": version
            }
     )
     
     gym(
        workspace: "Walkmining.xcworkspace",
        scheme: "Walkmining Debug",
        export_method: "app-store",
        export_options: {
            provisioningProfiles: {
            "com.dw.walkmining.debug" => "match AppStore com.dw.walkmining.debug",
            "com.dw.walkmining.debug.OneSignalNotificationServiceExtension" => "match AppStore com.dw.walkmining.debug.OneSignalNotificationServiceExtension"
          }
        }
      )
      
     # 테스트 플라이트 업로드
     upload_to_testflight(
        api_key: api_key,
        skip_waiting_for_build_processing: true
     )
     
     # Dsyms 업로드
     download_dsyms(version: "latest")
     UPLOAD_SYMBOLS_PATH=`xcodebuild -project ./Walkmining.xcodeproj -showBuildSettings | grep -m 1 "BUILD_DIR" | grep -oEi "\/.*" | sed 's:Build/Products:SourcePackages/checkouts/firebase-ios-sdk/Crashlytics/upload-symbols:' | tr -d '\n'`
     upload_symbols_to_crashlytics(
          gsp_path: "./Walkmining/Resources/Firebase/GoogleService-Info-dev.plist",
          binary_path: UPLOAD_SYMBOLS_PATH
       )
       
     clean_build_artifacts
     
     slack(
            message: "iOS Debug TestFlight 업로드 완료 :tada:",
            payload: {
                "Version": version
            }
     )
     
    rescue => ex
      slack(
        message: ex.message,
        success: false
      )
    end
  end
end
```
