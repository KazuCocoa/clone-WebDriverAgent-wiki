WebDriverAgent can be started as any UITest bundle. We recommend using `Xcode`, `xcodebuild` or [FBSimulatorControl](https://github.com/facebook/FBSimulatorControl). In order to start any UITest you need a test hosting application, which is used only to run test runner code and it should NOT be tested application. `Xcode` and `xcodebuild` will prepare test hosting application for you, however with `FBSimulatorControl` you need to use your own and point it explicitly or use system applications e.g. `Safari`.

After `WebDriverAgentRunner` is launched, you can get service URL from device logs (or Xcode console). Just search for line:
```
ServerURLHere->http://[SOME_IP]:8100<-ServerURLHere
```

### A: Using Xcode
Simply open `WebDriverAgent.xcodeproj` and start `WebDriverAgentRunner` test.

### B: Using xcodebuild
```
xcodebuild -project WebDriverAgent.xcodeproj \
           -scheme WebDriverAgentRunner \
           -destination 'platform=iOS Simulator,name=iPhone 6' \
           test
```
### C: Using fbsimctl from [FBSimulatorControl](https://github.com/facebook/FBSimulatorControl) framework.
```
./fbsimctl --state=booted \
           launch_xctest [path]/WebDriverAgentRunner.xctest com.apple.mobilesafari --port 8100 -- \
           listen
```

### D: Using [FBSimulatorControl](https://github.com/facebook/FBSimulatorControl) framework directly
Checkout [FBSimulatorInteraction+XCTest.h](https://github.com/facebook/FBSimulatorControl/blob/master/FBSimulatorControl/Interactions/FBSimulatorInteraction%2BXCTest.h)