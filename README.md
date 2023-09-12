# BuildToolsInspector
Portable packages inspector (and downloader) for VS Build Tools.

### Why Did I Make This?
~~It all started when I was born...~~

Originally, I only needed the build tools because it was required by Flutter. I don't like installing VS directly because of its size and the installation is not fully transparent (will mess up my file associations, require admin rights, etc). So I tried [PortableBuildTools](https://github.com/Data-Oriented-House/PortableBuildTools), but it still doesn't meet the Flutter requirement and doesn't have much customization. I checked the Flutter doctor source code to see what's wrong and it's [more complicated than I thought](https://github.com/flutter/flutter/blob/master/packages/flutter_tools/lib/src/windows/visual_studio.dart).

Currently, this tool still doesn't achieve what it's intended to do, but I think some people might want to check it. It consumes much of my free time so I'm not sure if I can finish it (especially the file extraction part). PR(s) are always welcome and I will see what I can do to help.

### Disclaimer
- I don't know much about Visual Studio, all I care is to build a desktop app using Flutter (of course without installing Visual Studio)
- This tool is unofficial and there's no guarantee that everything will continue to work properly
- By using this tool you agree that I'm not responsible for anything and you're ready to accept any risk
- If something is broken, start looking at [Visual Studio Bootstrapper Docs](https://github.com/MicrosoftDocs/visualstudio-docs/blob/main/docs/install/command-line-parameter-examples.md) or this [simple wiki](_docs/WIKI.md) I made.

### Features
- No need to install Visual Studio
- Ability to use specific build tools versions (e.g. `VS 2022`)
- Customize workloads (e.g. `Desktop development with C++`, `.NET desktop development`)
- Filter packages based on preferred arch and language (e.g. `X64`, `en-US`)
- Remove unneeded packages or add packages that are not included in workloads
- Check package types, total files, and download size
- Download and verify files checksum (or just export the details as `csv` and `json`)

### Todo
|Status|Task|Comment|
|-|-|-|
|:white_check_mark:|Extract vsix files|There are some tiny vsix files that can't be extracted, but I think they're not important at all
|:white_large_square:|Extract Windows SDK files|I have an idea how to implement it but kind of busy for now|
|:white_large_square:|Set environment variables and registry keys|Should be easy to implement once the issue with Windows SDK is solved|
|:white_large_square:|Compatibility with Flutter|May need to manipulate `%PROGRAMFILES(X86)%` (when launching Flutter/VS Code) and use `vswhere`. Will likely break current directory structures|
|:white_large_square:|Use pure Python and remove external libraries|Not top priority, already possible to convert `ipynb` to `py`|

### Screenshots

<details>
    <summary>Show screenshots (may be outdated)</summary>
    <p align="center">
        <img src="_docs/Screenshot_1.png"/>
        <img src="_docs/Screenshot_2.png"/>
        <img src="_docs/Screenshot_3.png"/>
    </p>
</details>

### License
- BuildToolsInspector under [The Unlicense](LICENSE)
- VS Build Tools (2022) under [Microsoft License](https://go.microsoft.com/fwlink/?LinkId=2180117)