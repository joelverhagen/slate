## Endpoint: enumerate packages

```http
GET /nuget/Packages()?$filter=IsAbsoluteLatestVersion&$orderby=Id&$skip=4&$top=1 HTTP/1.1
Host: example.com
````

```powershell
Invoke-WebRequest `
  -Uri http://example.com/nuget/Packages()?$filter=IsAbsoluteLatestVersion&$orderby=Id&$skip=4&$top=2
```

> The above command returns an XML response like this:

```xml
HTTP/1.1 200 OK
Content-Length: 3724
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
  <entry>
    <id>http://example.com/nuget/Packages(Id='Puppies',Version='0.1.0')</id>
    <category term="V2FeedPackage" scheme="http://schemas.microsoft.com/ado/2007/08/dataservices/scheme" />
    <link rel="edit" href="http://example.com/nuget/Packages(Id='Puppies',Version='0.1.0')" />
    <link rel="self" href="http://example.com/nuget/Packages(Id='Puppies',Version='0.1.0')" />
    <title type="text">Puppies</title>
    <updated>2017-02-07T09:28:34Z</updated>
    <author>
      <name>joelverhagen</name>
    </author>
    <content type="application/zip" src="http://example.com/nuget/package/Puppies/0.1.0" />
    <m:properties>
      <d:Version>0.1.0</d:Version>
      <d:NormalizedVersion>0.1.0</d:NormalizedVersion>
      <d:Copyright m:null="true" />
      <d:Dependencies />
      <d:Description />
      <d:DownloadCount m:type="Edm.Int32">2</d:DownloadCount>
      <d:IconUrl m:null="true" />
      <d:IsLatestVersion m:type="Edm.Boolean">true</d:IsLatestVersion>
      <d:IsAbsoluteLatestVersion m:type="Edm.Boolean">true</d:IsAbsoluteLatestVersion>
      <d:IsPrerelease m:type="Edm.Boolean">false</d:IsPrerelease>
      <d:Language m:null="true" />
      <d:PackageSize m:type="Edm.Int64">8309</d:PackageSize>
      <d:ProjectUrl m:null="true" />
      <d:ReleaseNotes m:null="true" />
      <d:RequireLicenseAcceptance m:type="Edm.Boolean">false</d:RequireLicenseAcceptance>
      <d:Tags m:null="true" />
      <d:Title m:null="true" />
      <d:MinClientVersion m:null="true" />
      <d:LicenseUrl m:null="true" />
    </m:properties>
  </entry>
</feed>
```

This endpoint is used to enumerate all packages available on the V2 package source.

### HTTP Request

`GET http://example.com/nuget/Packages`

### Query Parameters

Name     | Required
-------- | --------
$filter  | false   
$orderby | false   
$select  | false   
$skip    | false   
$top     | false   

**TODO**: document the availability for these parameters.

Note the [NuGet.org has implemented filtering](https://github.com/NuGet/Home/wiki/Filter-OData-query-requests) on
specific combinations of OData query parameters.

### Response

The response body is an XML document containing a collection of package entities. There can be zero or more package
entities.

**TODO**: document when the URL to the next page is available.

<aside>There is some inconsistency in the error status codes returned from different server implementations.</aside>

Status Code     | Meaning
--------------- | -------
200 OK          | The result set has been returned
400 Bad Request | The parameter values are invalid or the set of parameters is not supported
