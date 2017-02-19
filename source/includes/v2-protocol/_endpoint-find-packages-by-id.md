## Endpoint: find packages by ID

```http
GET /nuget/FindPackagesById()?id='Kittens' HTTP/1.1
Host: example.com
````

```powershell
Invoke-WebRequest `
  -Uri http://example.com/nuget/FindPackagesById()?id='Kittens'
```

> The above command returns an XML response like this:

```xml
HTTP/1.1 200 OK
Content-Length: 3823
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
      <d:IsAbsoluteLatestVersion m:type="Edm.Boolean">false</d:IsAbsoluteLatestVersion>
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
  <entry>
    <id>http://example.com/nuget/Packages(Id='Kittens',Version='1.3.0')</id>
    <category term="V2FeedPackage" scheme="http://schemas.microsoft.com/ado/2007/08/dataservices/scheme" />
    <link rel="edit" href="http://example.com/nuget/Packages(Id='Kittens',Version='1.3.0')" />
    <link rel="self" href="http://example.com/nuget/Packages(Id='Kittens',Version='1.3.0')" />
    <title type="text">Kittens</title>
    <updated>2017-02-07T09:28:34Z</updated>
    <author>
      <name>joelverhagen</name>
    </author>
    <content type="application/zip" src="http://example.com/nuget/package/Kittens/1.3.0" />
    <m:properties>
      <d:Version>1.3.0</d:Version>
      <d:NormalizedVersion>1.3.0</d:NormalizedVersion>
      <d:Copyright m:null="true" />
      <d:Dependencies />
      <d:Description>Descriptions are important! Especially when a package releases a stable version.</d:Description>
      <d:DownloadCount m:type="Edm.Int32">739</d:DownloadCount>
      <d:IconUrl m:null="true" />
      <d:IsLatestVersion m:type="Edm.Boolean">true</d:IsLatestVersion>
      <d:IsAbsoluteLatestVersion m:type="Edm.Boolean">true</d:IsAbsoluteLatestVersion>
      <d:IsPrerelease m:type="Edm.Boolean">false</d:IsPrerelease>
      <d:Language m:null="true" />
      <d:PackageSize m:type="Edm.Int64">4431</d:PackageSize>
      <d:ProjectUrl m:null="true" />
      <d:ReleaseNotes m:null="true" />
      <d:RequireLicenseAcceptance m:type="Edm.Boolean">false</d:RequireLicenseAcceptance>
      <d:Tags m:null="true" />
      <d:Title m:null="true" />
      <d:MinClientVersion m:null="true" />
      <d:LicenseUrl m:null="true" />
    </m:properties>
  </entry>
  <link rel="next" href="http://example.com/nuget/FindPackagesById()?id=%27Kittens%27&$skip=2" />
</feed>
```

This endpoint is used to enumerate all versions of a given package ID.

### HTTP Request

`GET http://example.com/nuget/FindPackagesById()?id='{package ID}'`

### Query Parameters

Name | Required | Description
---- | -------- | -----------
id   | true     | Package ID to fetch all of the versions. Note that the ID must be surrounded in single quotes.

<aside>There is some inconsistency in support for the optional query parameters.</aside>

Name     | Required | [Availability](#quirk-abbreviations)
-------- | -------- | ------------------------------------
$filter  | false    | MY, NG, NS2, NS3, VS
$orderby | false    | MY, NG, NS2, NS3
$select  | false    | MY, NG ([bug](https://github.com/NuGet/NuGetGallery/issues/3579)), NS2, NS3
$skip    | false    | MY, NG, NS2, NS3
$top     | false    | MY, NG, NS2, NS3

### Response

The response body is an XML document containing a collection of package entities. There can be zero or more package
entities. If the package ID does not exist on the package source, an empty result set is returned.

For large result sets (packages that have many versions), a `<link rel="next" href="..." />` element can be returned.
This should be followed to fetch the next set of results.

Status Code     | Meaning
--------------- | -------
200 OK          | The result set has been returned
404 Bad Request | The provided set of query parameters is not valid or not supported