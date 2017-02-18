---
title: API Reference

language_tabs:
  - powershell

toc_footers:
  - Lovingly authored by <a href='https://joelverhagen.com/'>Joel Verhagen</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

This is unofficial documentation for both the web services that support NuGet V2 and V3 HTTP package source protocol.
A package source is either V2 or V3. V1 package sources are not covered here.

Historically, the NuGet HTTP protocol has not been document and has been an implementation detail of the official NuGet
client (nuget.exe and NuGet inside of Visual Studio). As time went on, the HTTP protocol was closely inspected by
parties outside of Microsoft to provide alternative package sources to NuGet.org and third-party clients.

Because the protocol was never clearly documented, there are often subtle quirks between different server
implementations and even different versions of the official client (nuget.exe).

This documentation is an attempt to clarify all of the HTTP endpoints currently available on major NuGet package
sources.

# Disclaimer

This is unofficial documentation and is still a work in progres. Although I am on the NuGet team, I am by no means the
owner of the NuGet protocol. This document is meant for informational purposes and should not be seen at the protocol
specification.

# Quirks

Since the NuGet HTTP protocol was never documented in detail, it's not always useful to make unqualified, firm
statements in this documentation. For this reason, there will yellow notices when quirks or differences between server or
client implementations should be noted. For example:

<aside>The NuGet HTTP protocol has its warts, but it's essential for building awesome NuGet-y tools!</aside>

Where possible, any different behavior between client or server implementation will be enumerated and explained.

# Terminology

A **NuGet package** is an archive containing some metadata and some assets. Typically these assets are .NET assemblies
that another .NET project can consume. However, there are packages used for other things including but certainly not
limited to:

- native dependencies (e.g. [SQLite](https://www.nuget.org/packages/SQLite/))
- JavaScript (e.g. [jQuery](https://www.nuget.org/packages/jQuery/))
- CSS (e.g. [Bootstrap](https://www.nuget.org/packages/bootstrap/))
- Windows applications (e.g. [Firefox](https://chocolatey.org/packages/Firefox), via Chocolatey)
- deployment packages (via [Octopus Deploy](https://octopus.com/docs/packaging-applications))
- And certainly many other creative things!

However, NuGet packages have these attributes in common:

- Having the `.nupkg` file extension
- Being a Zip archive in disguise
- Containing a .nuspec in the root of the archive
- Having an ID
- Having a version number

The **package ID** is a human-readable string that package consumers configure into their projects or tooling so that
the package can be fetched from their packages sources. The package ID is a case-insensitive string, although there are
some quirks when it comes to casing.

The **package version** is SemVer 1.0.0 version string (although SemVer 2.0.0 support is coming). As a package changes
over time, the package version is changes to reflect this but the package ID stays the same.

The **identity of a package** is the pair of package ID and package version. This pair is meant to unambiguously allow
a package to be retrieved from a package source.

A **package source** is a collection of HTTP endpoints that allow a NuGet client to discovery, download, and find
metadata about NuGet packages. Each package source has a root URL that is configured into the client.

There are two package source protocols covered here.

The **V3 protocol** is the latest version of the package source protocol. Its data responses are serialized using JSON
and has a built in mechanism for versioning of existing endpoints and addition of new endpoints. A V3 package source URL
must end in `index.json`.

The **V2 protocol** is more widely supported by third party server implementations. Its data responses are serialized
using XML. It looks a lot like an OData Version 2.0 feed but there are quirks and limitations on a V2 package source
that are typically not there on an OData endpoint.

The **V1 protocol** is not covered in this document.

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

