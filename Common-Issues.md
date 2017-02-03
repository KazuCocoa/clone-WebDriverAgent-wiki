### Can't connect via Wifi to iOS10.x devices in China

It has been reported that client can't connect to China devices via WiFi. Issue is unknown, but you should have a look at [this issue](https://github.com/facebook/WebDriverAgent/issues/288).

Best workaround is to use [iproxy](http://iphonedevwiki.net/index.php/SSH_Over_USB) or [USB client](https://github.com/facebook/WebDriverAgent/wiki/USB-support). [Interesting post on that topic]( https://testerhome.com/topics/6331)
___
### Failed to authorize rights (0x1) with status: -60007

If developer mode isn't enabled then you will see similar message to this:
> DTServiceHub: Instruments wants permission to analyze other processes. Please enter an administrator username and password to allow this.
> Failed to authorize rights (0x1) with status: -60007.

In order to enable developer mode in terminal you can use:
```
$ DevToolsSecurity --enable
```
___
### The bundle “WebDriverAgentRunner” couldn’t be loaded because it is damaged or missing necessary resources
You might see this message when trying to running WebDriverAgent on devices. To resolve this issue you should:
* Set Team in General / Signing section to your Apple Developer Account for both `WebDriverAgentLib` and `WebDriverAgentRunner`
* Make sure you have available valid wildcard provisioning profile

___
### Booting WDA on device stops after `Set Up and does not actually start WDA.

This is known Apple bug (check https://github.com/facebook/WebDriverAgent/issues/458) that device at some point wills to working with any XCUITest (not only WDA). Only way to fix it is to reboot device and host (your mac).