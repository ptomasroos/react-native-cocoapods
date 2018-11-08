## How to get started with React-Native and cocoapods

```
react-native init MyApp
cd MyApp

tee Gemfile << END
source "https://rubygems.org"

gem "cocoapods"
gem "cocoapods-check"
gem "cocoapods-fix-react-native"
END

tee ios/Podfile << END
using_bundler = defined? Bundler
unless using_bundler
  puts "\nPlease re-run using:".red
  puts "  bundle exec pod install\n\n"
  exit(1)
end

react_native_path = '../node_modules/react-native'

platform :ios, '9.0'
inhibit_all_warnings!
plugin 'cocoapods-fix-react-native'

pod 'yoga', :path => react_native_path + '/ReactCommon/yoga'

# Third party deps
pod 'DoubleConversion', :podspec => react_native_path + '/third-party-podspecs/DoubleConversion.podspec'
pod 'glog', :podspec => react_native_path + '/third-party-podspecs/glog.podspec'
pod 'Folly', :podspec => react_native_path + '/third-party-podspecs/Folly.podspec'

pod 'React', subspecs: [
  'Core',
  'CxxBridge',
  'ART',
  'RCTAnimation',
  'RCTPushNotification',
  'RCTActionSheet',
  'RCTGeolocation',
  'RCTImage',
  'RCTLinkingIOS',
  'RCTNetwork',
  'RCTSettings',
  'RCTText',
  'RCTVibration',
  'RCTWebSocket',
  'DevSupport'
], path: react_native_path
END

bundle install
cd ios
bundle exec pod install 
```
