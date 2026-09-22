## General Info

The Vectorworks 2027 SDK requires:
- **Win**: Visual Studio 2022 version 17.12 (toolset v143)
- **Mac**: Xcode version 26.3

Before debugging Vectorworks on the Mac, please make sure to turn off Metal API Validation. This option can be turned off by going to **Edit Scheme** → **Diagnostics** → **API Validation**.
![Metal Validation](images/MetalValidation.png)

## Plugin Credential files

Vectorworks 2026 introduces the requirement for script plugins that are encrypted/obfuscated and SDK plugins to have a satellite credentials file that defines who is the developer of the plugins is.

Vectorworks 2027 same credentials file apply.

More information can be found here: [Plugin Credentials](https://github.com/Vectorworks/developer-scripting/blob/main/Common/Tasks/Info/PluginCredentials.md)

## Examples

[SDK Examples on GitHub](https://github.com/VectorworksDeveloper/SDKExamples)
