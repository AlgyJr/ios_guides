## Provisioning Profiles

All your provisioning profiles should be located in `~/Library/MobileDevice/Provisioning Profiles`.

If you wish to view the contents of a profile, type:

```bash
security cms -D -i <XXXX.mobileprovision>
```

### Starting over

Sometimes you may have obsolete provisioning profiles (i.e removed, expired, etc.):

```bash
rm -rf ~/Library/MobileDevice/Provisioning Profiles
```

You can also go to Xcode and visit `Xcode`->`Preferences` and removing your account attached and readding.

If you want to find out what code identities currently installed, you can type:

```bash
security find-identity -v -p codesigning
```