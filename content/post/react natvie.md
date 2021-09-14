---
title: "React-Native依赖项目配置整理"
date: 2021-09-13
draft: false
summary: "React-Native依赖项目配置整理"
---

# React-Native 依赖项目配置整理

## 1. Android

### 1.1. 修改build.gradle

```
allprojects {
    repositories {
        jcenter()
        maven { url "https://maven.google.com" }
        maven {
            // All of React Native (JS, Android binaries) is installed from npm
            url "$rootDir/rncomponent_sdk/node_modules/react-native/android"
        }
    }
}
```

### 1.2. 修改settings.gradle

```
include ':react-native-image-picker'
project(':react-native-image-picker').projectDir = new File(rootProject.projectDir, './rncomponent_sdk/node_modules/react-native-image-picker/android')

```

### 1.3. 配置权限

```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

## 2. iOS

### 2.1. 删除TVOS依赖

**在xcode中打开项目并删除libRNSVG-tvOS.a的依赖**

### 2.2. 修改Podfile

**添加如下代码**

```
pod 'React', :path => './RNComponent/node_modules/react-native', :subspecs => [

  'Core',

  'ART',

  'CxxBridge',

  'DevSupport', # Include this to enable In-App Devmenu if RN >= 0.43

  'RCTText',

  'RCTAnimation',

  'RCTActionSheet',

  'RCTGeolocation',

  'RCTImage',

  'RCTNetwork',

  'RCTPushNotification',

  'RCTSettings',

  'RCTVibration',

  'RCTLinkingIOS',

  'RCTWebSocket', # needed for debugging

  # Add any other subspecs you want to use in your project

]

# Explicitly include Yoga if you are using RN >= 0.42.0

pod 'yoga', :path => './RNComponent/node_modules/react-native/ReactCommon/yoga'

# Third party deps podspec link

pod 'DoubleConversion', :podspec => './node_modules/react-native/third-party-podspecs/DoubleConversion.podspec'

pod 'GLog', :podspec => './node_modules/react-native/third-party-podspecs/GLog.podspec'

pod 'Folly', :podspec => './node_modules/react-native/third-party-podspecs/Folly.podspec'

pod 'react-native-image-picker', :path => './node_modules/react-native-image-picker'

```
**然后在项目下执行: pod install**

### 2.3. 配置权限

**设置camera、photo library使用权限**


```
<!-- 相册 -->   
<key>NSPhotoLibraryUsageDescription</key>   
<string>App需要您的同意,才能访问相册</string>   
<!-- 相机 -->   
<key>NSCameraUsageDescription</key>   
<string>App需要您的同意,才能访问相机</string>   
```
### 2.4. 集成到原生项目中

**创建一个ViewController**

```
- (void)viewDidLoad {
    [super viewDidLoad];
    
    NSString * strUrl = @"http://localhost:8081/index.ios.bundle?platform=ios&dev=true";
    NSURL * jsCodeLocation = [NSURL URLWithString:strUrl];
    
    RCTRootView * rootView = [[RCTRootView alloc] initWithBundleURL:jsCodeLocation
                                                         moduleName:@"NativeRN"
                                                  initialProperties:nil
                                                      launchOptions:nil];
    self.view = rootView;
    
}
```
### 2.5. 配置info.plist

**还需要在info.plist文件中配置一下**

```
<key>NSAppTransportSecurity</key>
  <dict>
    <key>NSExceptionDomains</key>
    <dict>
      <key>localhost</key>
      <dict>
       <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
       <true/>
      </dict>
    </dict>
  </dict>
```




