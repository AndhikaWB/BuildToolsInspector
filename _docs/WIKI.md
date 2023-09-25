## Overview
There are many variables scattered in BuildToolsInspector that can be modified to change the script (`main.ipynb`) behavior. The most important ones are written at the beginning of the script (`vs_package_download`, `vs_package_extract`, `install_path`, etc). While seemed simple, please be aware that those variables may also indirectly affect one another. For example, `vs_package_extract` obviously will not work if `vs_package_download` is set to false.

When running the script, a data folder will be created to store some important files (e.g. `manifest.json`, `payloads.json`). You can inspect these files in case something breaks. However, modifying them usually will not affect the script (it only dumps files, but doesn't load them). The only exception is the manifest files (`channel_info.json` and `manifest.json`) which can be loaded locally to avoid redownloading every time the script is run. Other than data folder, there is also cache/packages folder. As the name implies, it stores downloaded packages in there.

At the end of the script, environment variables and registry keys setup will also be created as `vs_env_setup.bat`, `vs_register.bat`, and `vs_unregister.bat`. You will need to run these files manually if you want to make it recognizable by CMake, [vswhere](https://github.com/microsoft/vswhere), or other similar tools. The register/unregister thingy must be run as admin as it will manipulate the `HKLM` registry branch. However, please review it before running because it will also break the non-portable VS instance(s). Some details have been explained [here](https://gist.github.com/mmozeiko/7f3162ec2988e81e56d5c4e22cde9977?permalink_comment_id=4688841#gistcomment-4688841) and [there](https://github.com/Data-Oriented-House/PortableBuildTools/issues/2#issuecomment-1732924904) (or just view the script source code directly).

## Packages
Packages in `manifest.json` are basically a dict list that are composed of keys and values below. It is quite important to know because broken things will likely be caused by mishandled packages.

```jsonc
[
    {
        "id": "example.package.id",
        "version": "1.0.0.0",
        "type": "Component",
        "chip": "neutral",
        "language": "neutral",
        "productArch": "neutral",
        "machineArch": "neutral",
        "payloads": [
            {
                "fileName": "example.file.name.msi",
                "sha256": "d01n33dt03xpl41nth1s...",
                "size": 12345,
                "url": "https://example.com/example.file.name.msi"
            },
            // Other payloads...
        ],
        "dependencies": {
            "other.example.package.id": "[1.0,2.0)",
            "another.example.package.id": {
                "version": "[1.0,2.0)",
                "chip": "x64"
            },
            "yet.another.example.package.id_12345": {
                "id": "yet.another.example.package.id",
                "version": "[1.0,2.0)",
                "chip": "x64",
                "language": "en-US",
                "machineArch": "x64",
                "when": [
                    "Microsoft.VisualStudio.Product.BuildTools",
                    // Other product ids...
                ]
            },
            // Other dependencies...
        },
        // Other keys and values...
    },
    // Other packages...
]
```

There are many keys in a package but the most important ones are:

- `id`: The package id that can be referred by other packages (as dependency). Package id should be treated as case-insensitive string because the case of dependency id and package id may not always match. However, because Python `dict` key is case-sensitive, then we should overload/modify the `dict` class to treat key as case-insensitive. Some dependencies may also refer to its own parent id so this situation can create endless loop if not checked properly.
- `version`: Version in package is always written as exact version, but if the version is written as dependency then it could also be a range (e.g. `[1.0,2.0)`, `[2.0,]`). If version is written as dependency requirement, it doesn't mean to match the package version, but rather it should match VS build tools version.
- `type`: There are several package types, some examples of the low level ones are `Vsix`, `Exe`, `Msi`, `Msu`, and `Zip`. There are also high level types which are basically just a wrapper for other dependencies (`Component`, `Workload`, etc). High level types usually don't have their own payloads. Type should also be treated as case-insensitive.
- `chip`: Chip is not so different from arch, but I think it only has 3 possible values (`neutral`, `x86`, `x64`). For example, if you're using arm64 then your chip is still `x64`. Chip and machine/product arch don't always exist as a dict key and the value should be treated as case-insensitive.
- `machineArch`: Just like chip but the possible values are `neutral`, `x86`, `x64`, and `arm64`. Machine arch should match your system arch. The treatment should be the same as chip (case-insensitive).
- `productArch`: Product arch is a bit ambiguous, unlike chip and host arch, it was never listed as dependency requirement. Therefore, product arch should not be equalized as the target system arch. Even if it's the same, this key may not always exist, so you should not rely on it.
- `payloads`: I think what's written on the payloads is self-explanatory.
- `when`: It means that the dependency should only be included if the product id is listed here. If you're using build tools, the product id should be `Microsoft.VisualStudio.Product.BuildTools`. The id should be treated as case-insensitive.