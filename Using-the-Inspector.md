- Clone WebDriverAgent

```
git clone https://github.com/facebook/WebDriverAgent.git
```


- Run bootstrap.sh script. Setup dev tools and deps.

```
./Scripts/bootstrap.sh
DevToolsSecurity --enable
carthage bootstrap --platform ios
```

Update "iPhone 6" to be the name of a valid simulator on your system.

> xcodebuild -project WebDriverAgent.xcodeproj -scheme WebDriverAgentRunner -destination 'platform=iOS Simulator,name=iPhone 6' test

Update app path to be valid on your system


> curl -X POST -H "Content-Type: application/json" -d "{\"desiredCapabilities\":{\"bundleId\":\"com.your.bundle\", \"app\":\"/path/to/Build/Products/Debug-iphonesimulator/your.app\"}}" http://localhost:8100/session/

- Inspector GUI

```
http://localhost:8100/inspector
```

- JSON elements tree

```
http://localhost:8100/tree
```