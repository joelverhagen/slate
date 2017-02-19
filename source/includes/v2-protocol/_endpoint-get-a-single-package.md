## Endpoint: get a single package

```http
GET /nuget/Packages(Id='Kittens',Version='1.2.0-beta') HTTP/1.1
Host: example.com
````

```powershell
Invoke-WebRequest `
  -Uri http://example.com/nuget/Packages(Id='Kittens',Version='1.2.0-beta')
```

> The above command returns an XML response like this:

```xml
HTTP/1.1 200 OK
Content-Length: 1905
Content-Type: application/atom+xml;type=feed;charset=utf-8
Date: Sat, 18 Feb 2017 17:04:18 GMT

<?xml version="1.0" encoding="UTF-8"?>
<entry xmlns="http://www.w3.org/2005/Atom" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:georss="http://www.georss.org/georss" xmlns:gml="http://www.opengis.net/gml" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xml:base="http://example.com/nuget">
  <id>http://example.com/nuget/Packages(Id='Kittens',Version='1.2.0-beta')</id>
  <category term="V2FeedPackage" scheme="http://schemas.microsoft.com/ado/2007/08/dataservices/scheme" />
  <link rel="edit" href="http://example.com/nuget/Packages(Id='Kittens',Version='1.2.0-beta')" />
  <link rel="self" href="http://example.com/nuget/Packages(Id='Kittens',Version='1.2.0-beta')" />
  <title type="text">Kittens</title>
  <updated>2017-02-07T09:28:34Z</updated>
  <author>
    <name>joelverhagen</name>
  </author>
  <content type="application/zip" src="http://example.com/nuget/package/Kittens/1.2.0-beta" />
  <m:properties>
    <d:Version>1.2.0-beta</d:Version>
    <d:NormalizedVersion>1.2.0-beta</d:NormalizedVersion>
    <d:Copyright m:null="true" />
    <d:Dependencies />
    <d:Description>Descriptions are important!</d:Description>
    <d:DownloadCount m:type="Edm.Int32">27</d:DownloadCount>
    <d:IconUrl m:null="true" />
    <d:IsLatestVersion m:type="Edm.Boolean">false</d:IsLatestVersion>
    <d:IsAbsoluteLatestVersion m:type="Edm.Boolean">true</d:IsAbsoluteLatestVersion>
    <d:IsPrerelease m:type="Edm.Boolean">true</d:IsPrerelease>
    <d:Language m:null="true" />
    <d:PackageSize m:type="Edm.Int64">4103</d:PackageSize>
    <d:ProjectUrl m:null="true" />
    <d:ReleaseNotes m:null="true" />
    <d:RequireLicenseAcceptance m:type="Edm.Boolean">false</d:RequireLicenseAcceptance>
    <d:Tags m:null="true" />
    <d:Title m:null="true" />
    <d:MinClientVersion m:null="true" />
    <d:LicenseUrl m:null="true" />
  </m:properties>
</entry>
```

This endpoint is used to fetch metadata about a single package.

### HTTP Request

`GET http://example.com/nuget/Packages(Id='{package ID}',Version='{package version}')`

### URL Parameters

Name              | Required | Description
----------------- | -------- | -----------
{package ID}      | true     | The ID of the package fetched
{package version} | true     | The version of the package to be fetched

**TODO**: document what OData query parameters are supported

### Response

The response body is an XML document containing a single package entity.

Status Code   | Meaning
--------------| -------
200 OK        | The package exists and the metadata has been returned
404 Not Found | No package with the provided package ID and version exists
