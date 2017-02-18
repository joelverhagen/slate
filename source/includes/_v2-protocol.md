# V2 Protocol

## Package entity

At the core of the V2 protocol is the OData package entity. A single package entity is represented by a `<entry>`
element. A collection of package entities is represented by a `<feed>` element with zero or more `<entry>` child
elements. 

The identity (i.e. key) of the package entity is the Id and Version properties.

<aside>
The set of properties available varies between the server implementations.
</aside>

The following properties are available on the package entity. The types are [OData primitive data types](http://www.odata.org/documentation/odata-version-2-0/overview/#AbstractTypeSystem).

Name                     | Type         | Availability
------------------------ | ------------ | --------------
Authors                  | Edm.String   | MY, NS, NU, VS
Copyright                | Edm.String   | MY, NS, NU, VS
Created                  | Edm.DateTime | MY, NU, VS
Dependencies             | Edm.String   | MY, NS, NU, VS
Description              | Edm.String   | MY, NS, NU, VS
DevelopmentDependency    | Edm.Boolean  | NS
DownloadCount            | Edm.Int32    | MY, NS, NU, VS
GalleryDetailsUrl        | Edm.String   | MY, NU
IconUrl                  | Edm.String   | MY, NS, NU, VS
Id                       | Edm.String   | MY, NS, NU, VS
IsAbsoluteLatestVersion  | Edm.Boolean  | MY, NS, NU, VS
IsLatestVersion          | Edm.Boolean  | MY, NS, NU, VS
IsPrerelease             | Edm.Boolean  | MY, NS, NU, VS
Language                 | Edm.String   | MY, NS, NU, VS
LastEdited               | Edm.DateTime | MY, NU, VS
LastUpdated              | Edm.DateTime | MY, NS, NU, VS
LicenseNames             | Edm.String   | MY, NU
LicenseReportUrl         | Edm.String   | MY, NU
LicenseUrl               | Edm.String   | MY, NS, NU, VS
Listed                   | Edm.Boolean  | NS, VS
MinClientVersion         | Edm.String   | MY, NS, NU, VS
NormalizedVersion        | Edm.String   | MY, NS, NU, VS
Owners                   | Edm.String   | NS
PackageHash              | Edm.String   | MY, NS, NU
PackageHashAlgorithm     | Edm.String   | MY, NS, NU
PackageSize              | Edm.Int64    | MY, NS, NU, VS
ProjectUrl               | Edm.String   | MY, NS, NU, VS
Published                | Edm.DateTime | MY, NS, NU, VS
ReleaseNotes             | Edm.String   | MY, NS, NU, VS
ReportAbuseUrl           | Edm.String   | MY, NU
RequireLicenseAcceptance | Edm.Boolean  | MY, NS, NU, VS
Summary                  | Edm.String   | MY, NS, NU, VS
Tags                     | Edm.String   | MY, NS, NU, VS
Title                    | Edm.String   | MY, NS, NU, VS
Version                  | Edm.String   | MY, NS, NU, VS
VersionDownloadCount     | Edm.Int32    | MY, NS, NU

## Endpoint: Push

```http
POST /nuget HTTP/1.1
Host: example.com
X-NuGet-ApiKey: meowmeow
Content-Length: 35195

(bytes of the .nupkg)
````

```powershell
Invoke-WebRequest `
  -Method PUT `
  -Uri http://example.com/nuget/ `
  -InFile Kittens.1.2.0-beta.nupkg `
  -Headers @{ "X-NuGet-ApiKey" = "meowmeow" }
```

> The above command returns a response like this:

```
HTTP/1.1 201 Created
Content-Length: 0
Date: Sat, 18 Feb 2017 17:04:18 GMT
```

This endpoint is used to add new packages and replace existing packages. The identity (package ID and version) of the
package being pushed is defined in the .nuspec of uploaded package.

<aside>Some server implementations do not allow existing packages to be replaced.</aside>

Server       | Supports package replace
---------    | ------------------------
NuGet.org    | no
NuGet.Server | no, unless `allowOverrideExistingPackageOnPush` is set to `true`
MyGet        | yes, unless "Forbid overwriting of existing packages?" package setting is enabled
VSTS         | no

### HTTP Request

`PUT http://example.com/nuget`

<aside>NuGet.org accepts <code>POST</code> requests in addition to <code>PUT</code>.</aside>

### Headers

Name           | Required | Description
-------------- | -------- | -----------
X-NuGet-ApiKey | true     | The API key used to authenticate the caller.

### Request Body

The request body can come in one of two forms.

#### Multipart Form Data

The request header `Content-Type` is `multipart/form-data` and the first item in the response body is the raw bytes of
the .nupkg being pushed. Subsequent items in the multipart body are ignored.

#### Raw Bytes

If the response body is not `multipart/form-data`, the entire request body is assumed to be the raw bytes of the .nupkg
being pushed.

### Response

<aside>There is some inconsistency in the error status codes returned from different server implementations.</aside>

Status Code      | Meaning
---------------- | -------
201 Created      | The package has been successfully uploaded
400 Bad Request  | The package in the request body could not be extracted or has invalid metadata
401 Unauthorized | The provided API key or credentials are not valid
403 Forbidden    | The provided API key or credentials does not have write access to the provided package ID
409 Conflict     | A package with the provided identity already exists

**TODO:** document which server implementations return what status codes.

The official NuGet client treats all 200-level responses as success and everything else as a failure.

## Endpoint: Delete

```http
DELETE /nuget/Kittens/1.2.0-beta HTTP/1.1
Host: example.com
X-NuGet-ApiKey: meowmeow
````

```powershell
Invoke-WebRequest `
  -Method DELETE `
  -Uri http://example.com/nuget/Kittens/1.2.0-beta `
  -Headers @{ "X-NuGet-ApiKey" = "meowmeow" }
```

> The above command returns a response like this:

```
HTTP/1.1 204 No Content
Content-Length: 0
Date: Sat, 18 Feb 2017 17:04:18 GMT
```

This endpoint is used to delete an existing package. Server implementations are allowed to implement the delete
operation either as complete package removal or as the unlist operation.

Server       | Delete or unlist
---------    | ------------------------
NuGet.org    | unlist
NuGet.Server | delete, unless `enableDelisting` is set to `true`

**TODO:** document MyGet and VSTS

### HTTP Request

`DELETE http://example.com/nuget/{package ID}/{package version}`

### Headers

Name           | Required | Description
-------------- | -------- | -----------
X-NuGet-ApiKey | true     | The API key used to authenticate the caller.

### Response

<aside>There is some inconsistency in the error status codes returned from different server implementations.</aside>

Status Code      | Meaning
---------------- | -------
204 No Content   | The package was successfully deleted or unlisted
404 Bad Request  | The provided package identity does not exist
401 Unauthorized | The provided API key or credentials are not valid
403 Forbidden    | The provided API key or credentials does not have write access to the provided package ID

**TODO:** document which server implementations return what status codes.

The official NuGet client treats all 200-level responses as success and everything else as a failure.

## Endpoint: Packages

```http
GET /nuget/Packages()?$filter=IsAbsoluteLatestVersion&$orderby=Id&$skip=4&$top=1 HTTP/1.1
Host: example.com
````

```powershell
Invoke-WebRequest `
  -Uri http://example.com/nuget/Packages()?$filter=IsAbsoluteLatestVersion&$orderby=Id&$skip=4&$top=1
```

> The above command returns an XML response like this:

```xml
HTTP/1.1 200 OK
Content-Length: 2881
Content-Type: application/atom+xml;type=feed;charset=utf-8
Date: Sat, 18 Feb 2017 17:04:18 GMT

<?xml version="1.0" encoding="UTF-8"?>
<feed xmlns="http://www.w3.org/2005/Atom" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xml:base="http://example.com/nuget">
  <id>http://schemas.datacontract.org/2004/07/</id>
  <title />
  <updated>2017-02-18T21:38:03Z</updated>
  <link rel="self" href="http://example.com/nuget/Packages" />
  <entry>
    <id>http://example.com/nuget/Packages(Id='Kittens',Version='1.2.0-beta')</id>
    <category term="NuGetGallery.OData.V2FeedPackage" scheme="http://schemas.microsoft.com/ado/2007/08/dataservices/scheme" />
    <link rel="edit" href="http://example.com/nuget/Packages(Id='Kittens',Version='1.2.0-beta')" />
    <link rel="self" href="http://example.com/nuget/Packages(Id='Kittens',Version='1.2.0-beta')" />
    <title type="text">Kittens</title>
    <updated>2017-02-07T09:28:34Z</updated>
    <author>
      <name>joelverhagen</name>
    </author>
    <content type="application/zip" src="http://example.com/nuget/package/Kittens/1.2.0-beta" />
    <m:properties>
      <d:Id>Kittens</d:Id>
      <d:Version>1.2.0-beta</d:Version>
      <d:NormalizedVersion>1.2.0-beta</d:NormalizedVersion>
      <d:Authors>joelverhagen</d:Authors>
      <d:Copyright m:null="true" />
      <d:Created m:type="Edm.DateTime">2017-02-07T09:28:34.88</d:Created>
      <d:Dependencies />
      <d:Description>to learn dll</d:Description>
      <d:DownloadCount m:type="Edm.Int32">27</d:DownloadCount>
      <d:IconUrl m:null="true" />
      <d:IsLatestVersion m:type="Edm.Boolean">false</d:IsLatestVersion>
      <d:IsAbsoluteLatestVersion m:type="Edm.Boolean">true</d:IsAbsoluteLatestVersion>
      <d:IsPrerelease m:type="Edm.Boolean">false</d:IsPrerelease>
      <d:Language m:null="true" />
      <d:LastUpdated m:type="Edm.DateTime">2017-02-07T09:28:34.88</d:LastUpdated>
      <d:Published m:type="Edm.DateTime">2017-02-07T09:28:34.88</d:Published>
      <d:PackageHash>LWeQ5xfhdlD3cd41KjJedTV4d7JBZRw+rmCF+7ezHNPi4bmUQACwK0y9Qlhrfr6Y2yxaRtJQ2K1d/Z2AguL7Sw==</d:PackageHash>
      <d:PackageHashAlgorithm>SHA512</d:PackageHashAlgorithm>
      <d:PackageSize m:type="Edm.Int64">4103</d:PackageSize>
      <d:ProjectUrl m:null="true" />
      <d:ReleaseNotes m:null="true" />
      <d:RequireLicenseAcceptance m:type="Edm.Boolean">false</d:RequireLicenseAcceptance>
      <d:Summary m:null="true" />
      <d:Tags m:null="true" />
      <d:Title m:null="true" />
      <d:VersionDownloadCount m:type="Edm.Int32">27</d:VersionDownloadCount>
      <d:MinClientVersion m:null="true" />
      <d:LastEdited m:null="true" />
      <d:LicenseUrl m:null="true" />
      <d:LicenseNames m:null="true" />
      <d:LicenseReportUrl m:null="true" />
    </m:properties>
  </entry>
</feed>
```

This endpoint is used to enumerate all packages available on the V2 package source.

### HTTP Request

`GET http://example.com/nuget/Packages`

### Query Parameters

Name           | Description
-------------- | -----------
$filter        | [OData $filter option](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#FilterSystemQueryOption) used to determine if a package should be in the result set.
$orderby       | [OData $orderby option](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#OrderBySystemQueryOption) used to sort the result set.
$skip          | [OData $skip option](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#SkipSystemQueryOption) used for paging across the result set.
$top           | [OData $top option](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#TopSystemQueryOption) used for paging across the result set.

### Response

The response body is an XML document containing a collection of package entities. There can be zero or more package
entities.

**TODO**: document when the URL to the next page is available.

<aside>There is some inconsistency in the error status codes returned from different server implementations.</aside>

Status Code      | Meaning
---------------- | -------
200 OK           | The result set has been returned.
400 Bad Request  | The parameter values are invalid or the set of parameters is not supported.
