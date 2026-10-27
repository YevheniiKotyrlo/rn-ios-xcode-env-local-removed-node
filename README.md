# `pod install` keeps an `ios/.xcode.env.local` whose node is gone, so the iOS build fails after a node upgrade

The app is the template, unchanged: any React Native app reproduces it.

macOS with nvm, React Native 0.88.0, Xcode 27.1, CocoaPods 1.17.0:

```sh
nvm install 24.18 && nvm use 24.18
cd ReproducerApp && yarn install
cd ios && pod install && cat .xcode.env.local
# export NODE_BINARY=/Users/<you>/.nvm/versions/node/v24.18.1/bin/node

nvm use default && nvm uninstall 24.18.1
pod install && cat .xcode.env.local
# unchanged: export NODE_BINARY=/Users/<you>/.nvm/versions/node/v24.18.1/bin/node

cd .. && xcodebuild -workspace ios/ReproducerApp.xcworkspace -scheme ReproducerApp -configuration Release \
  -sdk iphoneos -destination 'generic/platform=iOS' CODE_SIGNING_ALLOWED=NO build
```

Expected: the build uses the node that runs it. Seen, in the `[CP-User] [Hermes] Replace Hermes for the right
configuration, if needed` and `[CP-User] [RNDeps] Replace React Native Dependencies for the right configuration, if
needed` script phases:

```
Script-46EB2E00011B30.sh: line 9: /Users/<you>/.nvm/versions/node/v24.18.1/bin/node: No such file or directory
Command PhaseScriptExecution failed with a nonzero exit code
** BUILD FAILED **
```
