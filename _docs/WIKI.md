Manifest packages are a list of dict that is basically composed of these keys (and values):

```jsonc
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
        // ...
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
                // ...
            ]
        },
        // ...
    },
    // ...
}
```

Explanation for most of the keys:

- `id`: The package id that can be referred by other packages (as dependency). Package id should be treated as case-insensitive string because the case of dependency id and package id may not always match. However, because Python `dict` key is case-sensitive, then we should overload/modify the `dict` class to treat key as case-insensitive. Some dependencies may also refer to its own parent id so this situation can create endless loop if not checked properly.
- `version`: Version in package is always written as exact version, but if the version is written as dependency then it could also be a range (e.g. `[1.0,2.0)`, `[2.0,]`). If version is written as dependency requirement, it doesn't mean to match the package version, but rather it should match VS build tools version.
- `type`: There are several package types, some examples of the low level ones are `Vsix`, `Exe`, `Msi`, `Msu`, and `Zip`. There are also high level types which are basically just a wrapper for other dependencies (`Component`, `Workload`, etc). High level types usually don't have their own payloads. Type should also be treated as case-insensitive.
- `chip`: Chip is not so different from arch, but I think it only has 3 possible values (`neutral`, `x86`, `x64`). For example, if you're using arm64 then your chip is still `x64`. Chip and machine/product arch don't always exist as a dict key and the value should be treated as case-insensitive.
- `machineArch`: Just like chip but the possible values are `neutral`, `x86`, `x64`, and `arm64`. Machine arch should match your system arch. The treatment should be the same as chip (case-insensitive).
- `productArch`: Product arch is a bit ambiguous, unlike chip and host arch, it was never listed as dependency requirement. Therefore, product arch should not be equalized as the target system arch. Even if it's the same, this key may not always exist, so you should not rely on it.
- `payloads`: I think what's written on the payloads is self-explanatory.
- `when`: It means that the dependency should only be included if the product id is listed here. If you're using build tools, the product id should be `Microsoft.VisualStudio.Product.BuildTools`. The id should be treated as case-insensitive.