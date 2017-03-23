If you don't want to or can't use Wifi to communicate with WDA you can easily proxy communication via USB with:

- [mobiledevice](https://github.com/imkira/mobiledevice):
```
# Install
$ brew install mobiledevice

# Start proxy
$ mobiledevice tunnel 8100 8100
```

- [usbmuxd](https://github.com/libimobiledevice/usbmuxd):
```
# Install
$ brew install usbmuxd

# Start proxy
$ iproxy 8100 8100
```