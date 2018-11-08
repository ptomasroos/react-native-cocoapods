## How to get started with React-Native and cocoapods

This is a very short and 60 seconds shell script to get started with react-native and cocoapods and have it compiling within a minute!


```
react-native init MyApp
cd MyApp

echo "ios/Pods/" >> .gitignore

tee Gemfile << END
source "https://rubygems.org"

gem "xcodeproj"
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

abstract_target 'App' do
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
  
  target 'MyApp'
  target 'MyAppTests'
end
END

bundle install
cd ios
bundle exec pod install 

ruby << END
require 'xcodeproj'
project = Xcodeproj::Project.open('MyApp.xcodeproj')

project.targets.each do |target|
  target.frameworks_build_phase.files_references.each do |pbx_file_reference|
    puts 'puts pbx_file_reference.isa = ' + pbx_file_reference.isa.to_s
    puts 'pbx_file_reference.path = ' + pbx_file_reference.path.to_s
    if pbx_file_reference.path.start_with?("libRCT", "libReact")
      target.frameworks_build_phase.remove_file_reference(pbx_file_reference)
    end
  end
end

project.objects.each do |object|
  if object.respond_to?(:name)
    puts 'object.display_name = ' + object.display_name.to_s
    puts 'object.name = ' + object.name.to_s
    next if object.display_name.end_with?("MyApp.xcodeproj", "MyAppTests.xcodeproj")
    if object.display_name.end_with?(".xcodeproj", "tvOS.a", "tvOS" "tvOS.app", "-tvOSTests.xctest", "libReact.a")
      object.remove_from_project
      puts '^ removed'
    end
  end
end

project.save
END
```
