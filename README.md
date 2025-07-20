# CloudNimble.Build.Documentation

An MSBuild SDK that enables documentation projects (`.docsproj`) in .NET solutions without compilation overhead or bin/obj folder clutter.

## 🚀 Quick Start

### 1. Install the SDK

Add to your `global.json`:
<!--
```json
{
  "msbuild-sdks": {
    "CloudNimble.Build.Documentation": "1.0.0"
  }
}
```-->
### 1. Create a .docsproj

```xml
<Project Sdk="CloudNimble.Build.Documentation/1.0.0">
  <!-- That's it! Everything is configured automatically -->
</Project>
```

### 2. Add to Your Solution

For .slnx files:
```xml
<Project Path="docs/MyDocs.docsproj" Type="{9A19103F-16F7-4668-BE54-9A1E7A4F7556}" projectTypeName="SharedProject" />
```

## 📚 Features

- 🚀 **Zero Configuration** - Works out of the box for common documentation types
- 📁 **Clean Folders** - No `bin/obj` folders cluttering your documentation directories  
- 🔍 **Auto-Detection** - Automatically detects documentation type (Mintlify, DocFX, MkDocs, Jekyll, Hugo)
- 📝 **Smart Includes** - Automatically includes relevant files based on documentation type
- 🛠️ **Integration** - Optional build targets for linting, preview, and deployment
- 🎯 **Visual Studio** - Full IntelliSense and editing support in Visual Studio
- 🔧 **EasyAF.Tools** - Automatic detection and installation guidance for enhanced features

## 📖 Documentation

For complete documentation, see the [CloudNimble.Build.Documentation README](CloudNimble.Build.Documentation/README.md).

## 🤝 Contributing

This SDK is part of the [CloudNimble](https://github.com/CloudNimble) ecosystem. Contributions welcome!

## 📄 License

MIT License - see [LICENSE](LICENSE) for details.