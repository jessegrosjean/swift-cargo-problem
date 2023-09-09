Demonstrates problem generating two packages with `cargo-swift` and then trying to include them both in same app. To reproduce:

> cd Package1
> cargo swift package
> cd Package2
> cargo swift package

Open Xcode Workspace. Mabye restart/clean/something to make workspace recognize and create schemes for packages. I don't know if i'm doing something wrong or if Xcode problem.

Once Workspace sees packages and creates schemes you'll see error:

```
multiple targets named 'RustFramework' in: 'package1', 'package2'; consider using the `moduleAliases` parameter in manifest to provide unique names
```

Fix that problem by renaming "RustFramework" to "RustFramework2" in Package2/Package.swift

Once I do that all targets build in the workspace and there are no warnings.

Next go to App target and add one package to "Frameworks, Libraries, and Embedded Content". Everything should continue to build. And finally add second package to that same section... at this point I get errors that I don't know how to work around.

```
Multiple commands produce '/Users/jessegrosjean/Library/Developer/Xcode/DerivedData/Workspace-calowfpnuicujwaojewvtedhfjmy/Build/Products/Debug/include/libFFI.h'

Multiple commands produce '/Users/jessegrosjean/Library/Developer/Xcode/DerivedData/Workspace-calowfpnuicujwaojewvtedhfjmy/Build/Products/Debug/include/module.modulemap'

Multiple commands produce '/Users/jessegrosjean/Library/Developer/Xcode/DerivedData/Workspace-calowfpnuicujwaojewvtedhfjmy/Build/Products/Debug/libmylib.a'
```

Any idea how to fix?

# A fix!

Matt Massicotte helped me find fix. I'm not sure that all steps are neccessary, but these sets seem to work.

The basic problem is that XCFrameworks need unique names/paths in many underdocumented cases if you plan to use more then one in a single app. To fix the probem in both of the above generated packages I did:

1. Make name of generated RustFramework.xcframework unique. Also make sure `.binaryTarget(name: "..."` is unique.
2. Inside the RustFramework.xcframework make sure generated `libmylib.a` is unqiue.
3. Inside the RustFramework.xcframework make generate a unique named folder in `Headers` folder. And use same name that you will use for enclosed `.h` file. So for example I made `Headers/libFFI1` and then put both `libFFI1.h` and `module.modulemap` inside that folder.
4. Inside the RustFramework.xcframework make sure `Info.plist` is updated to point to the uniquely name `.a` file. I did not seem to need to update the `Headers` value in the plist.
5. Also will need to make sure that the module name in `module.modulemap` is unique, I used same convention that I used for `.h` file. The `module.modulemap` on the other hand does not need to be unique.
6. Make sure to update `lib.swift` to import the updated module name.

I think that's it.

Generally I think it makes sense to incorporate the package name into all of these values. Or mabye if you want things shorter add a param for a unque ID.