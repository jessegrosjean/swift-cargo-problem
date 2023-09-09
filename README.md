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
