# Introduction

This is unofficial documentation for both NuGet V2 and V3 HTTP package source protocol.

A package source is either V2 or V3. V1 package sources are not covered here.

Historically, the NuGet HTTP protocol has not been documented and has been an implementation detail between the official
NuGet client (e.g. nuget.exe) and NuGet.org. As time went on, the HTTP protocol was closely inspected by parties outside
of the NuGet team with the goal of providing alternate package sources and client implementations.

Because the protocol was never clearly documented, there are many subtle quirks between different server
implementations. This document is an attempt to explain all of the HTTP endpoints available.

**"Retro-specing" is the operative term!**

## Disclaimer

This is unofficial documentation and is still a work in progress. Although I am on the NuGet team, I am by no means the
owner of the NuGet protocol. This document is meant for informational purposes and should not be seen at the protocol
specification.

See the logo on this page? Yeah.

## Terminology

A NuGet **package** is an archive containing some metadata and some assets. Typically these assets are .NET assemblies
that a .NET project can consume (often through Visual Studio). However, there are packages used for other things
including but certainly not limited to:

- native dependencies (e.g. [SQLite](https://www.nuget.org/packages/SQLite/))
- JavaScript (e.g. [jQuery](https://www.nuget.org/packages/jQuery/))
- CSS (e.g. [Bootstrap](https://www.nuget.org/packages/bootstrap/))
- Windows applications (e.g. [Firefox](https://chocolatey.org/packages/Firefox), via Chocolatey)
- deployment packages (via [Octopus Deploy](https://octopus.com/docs/packaging-applications))
- And certainly many other creative things!

However, NuGet packages have these attributes:

- Having the .nupkg file extension
- Being a Zip archive in disguise
- Containing a .nuspec in the root of the archive
- Having an ID
- Having a version number

The **package ID** is a human-readable string that package consumers configure into their projects or tooling so that
the package can be fetched from their packages sources. The package ID is a case-insensitive string, although there are
some quirks when it comes to casing.

The **package version** is SemVer 1.0.0 version string (although SemVer 2.0.0 support is coming). As a package changes
over time, the package version is changes to reflect this but the package ID stays the same. Equivalency of two version
strings in general is defined by the SemVer specification. However, NuGet package versions can have a fourth integer in
the version number (to align with `System.Version`) and comparison is done in a *case-insensitive* manner. The best
definition

The **identity of a package** is the two-tuple of package ID and package version. This pair is meant to unambiguously
allow a package to be retrieved from a package source.

A **package source** is a collection of HTTP endpoints that allow a NuGet client to discover, download, and fetch
metadata about NuGet packages. Each package source has a root URL that is configured into the client allowing all
endpoints within the package source to be inferred based on the root URL.

The **V3 protocol** is the latest version of the package source protocol. Its data responses are serialized using JSON
and has a built in mechanism for versioning of existing endpoints and addition of new endpoints. A V3 package source URL
must end in `index.json`.

The **V2 protocol** is more widely supported by third party server implementations. Its data responses are serialized
using XML. It looks a lot like an OData Version 2.0 feed but there are quirks and limitations on a V2 package source
that are typically not there on an OData endpoint.

# Quirks

Since the NuGet HTTP protocol was never documented in detail, it's not always useful to make unqualified, firm
statements in this document. For this reason, there will yellow notices when quirks or differences between server or
client implementations should be noted. For example:

<aside>The NuGet HTTP protocol has its warts, but it's essential for building awesome NuGet-y tools!</aside>

Where possible, any different behavior between client or server implementation will be enumerated and explained. To make
the explanation of different implementations more terse, the following abbreviations will be used:

## Quirk Abbreviations

Abbreviation | Implementation
------------ | --------------
MY           | [MyGet](https://www.myget.org/)
NG           | [NuGetGallery](https://github.com/NuGet/NuGetGallery) (NuGet.org)
NS2          | [NuGet.Server on WCF](https://github.com/NuGet/NuGet.Server/tree/master)
NS3          | [NuGet.Server on Web API](https://github.com/NuGet/NuGet.Server/tree/dev)
VS           | [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/en-us/docs/integrate/api/packaging/overview)
