### Wildcard Links

To use wildcard app links, you must make sure that the subdomain and sub subdomains can serve the `apple-app-site-association` file.    For instance:

app.domain.com (must serve the apple-app-site-association file)
server1.app.domain.com (must serve the apple-app-site-association)
server2.app.domain.com (must serve the apple-app-site-association)

### Troubleshooting

Use the Console app, click on the Devices, and look for `applinks` in the system messages.

### Testing on Simulator

Click on the Home button and make sure the app is currently not in the foreground.  To launch the simulator, you can type this command at the Terminal prompt:

```bash
xcrun simctl openurl booted “https://www.example.com/content?id=2”
```

### References

* <https://objectivetidbits.com/working-with-universal-links-on-ios-simulator-adffb7767801>
* <https://developer.apple.com/ios/universal-links/>


