---
layout: post
title:  "Using Nuget.exe and Octo.exe in a TFS 2015 build definition to deploy to an Octopus feed"
date:   July 1, 2016
categories: Octopus
tags:
  - Octopus
  - Deploy
  - TFS2015
comments: true
header: octo-nuget.png
---
Rather than using Octopack 2/3 to package the application by tying into the MSBuild process with additional MSBuild arguments, Nuget.exe can be utilized to package the project in a separate build step (outside of MSBuild). Additionally, once packaged, octo.exe can be used to push the package to the Octopus server in order to streamline the build and deployment process.

**Why nuget/octo executables instead of OctoPack?**

- Building & packaging separation of concerns.
- Language/Framework agnostic.

**Build Server Prerequisites**

- Nuget.exe installed & accessible in the PATH.
- Octo.exe installed & accessible in the PATH.

**Project Prerequisites**

- Nuget manifest (.nuspec file) containing packaging details about the project.

## Packaging your project with nuget.exe

To package your application with Nuget.exe, the `nuget pack` utility can be used.

`nuget pack \Path\To\My\Project.nuspec` or `nuget pack \Path\To\My\Project.csproj`

> **Additional advantages of targeting .csproj file:**
> The nuget package will be populated with the assembly version specified in AssemblyInfo.cs. Otherwise, if we only point to the *.nuspec* file, we would have to come up with a solution for parsing the verison from AssemblyInfo.cs at build time.

By pointing to the *.csproj* file, we inherently get the versioning information from the assembly at the cost of sacrificing our soul to Microsoft. This is probably okay for ASP.NET applications, but it is nice to have the option to point to the *.nuspec* file for other platforms.

### Packaging ASP.NET Applications

We found that pointing the `nuget pack` command to the *.csproj* file made it easier to package files marked as *Content*. Pointing nuget to the *.csproj* file will honor the assembly version of the project that it is packaging while honoring the other settings from the *.nuspec* file.

#### Sample ASP.NET Project .nuspec

```xml
<?xml version="1.0"?>
<package >
  <metadata>
    <id>MyApplication</id>
    <version>$version$</version>
    <title>Compliance</title>
    <authors>Team Superstar</authors>
    <owners>Team Superstar</owners>
    <requireLicenseAcceptance>false</requireLicenseAcceptance>
    <description>My application description</description>
    <releaseNotes>Summary of changes made in this release of the package.</releaseNotes>
    <copyright>Copyright 2016</copyright>
    <tags>Tag1 Tag2</tags>
  </metadata>

  <files>
    <file src="bin\**\*" target="content\bin"/>
    <file src="Web.Release.config" target="content" /> <!-- stupid nuget not copying this file as content, don't know why!!! -->
  </files>
</package>
```

Note the inclusion of the bin folder and Web.Release.config into the content folder. The *.nuspec* file can be used for other platforms besides ASP.NET, however, your *files* property will likely be different for that particular platform.

## Adding your project to the built-in Octopus feed with octo.exe

Octo.exe is a CLI tool provided by Octopus that provides a `octo push` command. This command can be used to push a nuget/zip package to an Octopus Deploy feed.

> Side note:
> This tool can also do the packaging for you as well with `octo pack`, however, we found that this tool did not provide us enough flexibility as it does not use a *.nuspec* file.

The complete `octo push` API documentation [can be found here](http://docs.octopusdeploy.com/display/OD/Pushing+packages){:target="_blank"}.

`octo push --package MyApplication.nupkg --server http://octopus.example.com --apiKey API-123456789ABCDEFGHIJKLMNOP`

## Sample script for packing + deploying your application

In the TFS 2015 build definition, there is an option to run a PowerShell script. The following script is an example of how you can utilize nuget.exe and octo.exe to package and deploy your application to the internal Octopus feed. Although this script can be greatly improved, this is the gist. We added our script to TFS source control so that the TFS build definition could access it. As far as I'm aware, this is the only way to access it (I would love to know if there is an alternative).

```powershell
# todo store octo info in central config

# replace with your values.
$octoServer = "http://octopus.example.com"    
$apiKey = "API-123456789ABCDEFGHIJKLMNOP"

# there is a known issue with our version of TFS that prevents TFS from seeing environment variables.
$tools = "C:\ProgramData\chocolatey\bin"
$nuget = $tools + "\nuget.exe"
$octo = $tools + "\octo.exe"
$projectLocation = $env:ProjectLocation

& $nuget pack $projectLocation

# todo make this support multiple packages
$deployPackageName = dir *.nupkg | Select Name -first 1

& $octo push --package $deployPackageName.Name --replace-existing --server $octoServer --apiKey $apiKey
```

### Setting up the build definition in TFS 2015

In the TFS web interface, once you step up a new build definition you should be able to add build steps to it.

![Add Build Step](/assets/images/posts/content/TFS-AddBuildStep.png)

Add a new PowerShell script:

![Add PowerShell](/assets/images/posts/content/TFS-AddPowerShell.PNG)

Point the PowerShell build step **Script filename** to the previously mentioned script in TFS.

![PowerShell build step](/assets/images/posts/content/TFS-PowerShell.PNG)
