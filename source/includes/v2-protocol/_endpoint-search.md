## Endpoint: search for packages

```http
GET /nuget/Search()?searchTerm='kittens'&targetFramework=''&includePrerelease=true HTTP/1.1
Host: example.com
````

```powershell
Invoke-WebRequest `
  -Uri http://example.com/nuget/Search()?searchTerm='kittens'&targetFramework=''&includePrerelease=true
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
      <d:Description>A package that is definitely not about kittens.</d:Description>
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
  <link rel="next" href="http://example.com/nuget/FindPackagesById()?id=%27Kittens%27&$skip=2" />
</feed>
```

This endpoint is used to search package metadata for an arbitrary search term. This is the primary method used to
discovery packages on a package source.

### HTTP Request

`GET http://example.com/nuget/Search()?searchTerm='{search term}'&targetFramework='{target frameworks}'&includePrerelease={true | false}`

### Query Parameters

Name                | Required | Description
------------------- | -------- | -----------
`searchTerm`        | true     | The search term to look for in package metadata
`targetFramework`   | true     | Zero or more target framework monikers, seperated by `|`
`includePrerelease` | true     | `true` or `false` dictating whether prerelease packages should be included in the output

In general, the `searchTerm` is tokenized (typically by whitespace) and used to match terms found in a package's title,
ID, description, and tags. What set of fields are searched and how the search term is tokenized varies greatly from one
server implementation to the next.

In the example to the right, the Puppies package is matched because its description contains the search term "kittens".

<aside>Some server implementations ignore the `targetFramework` parameter completely.</aside>

<aside>Some server allow these query parameters to be excluded.</aside>

**TODO**: document the availability of the standard OData parameters

### Response

The response body is an XML document containing a collection of package entities. There can be zero or more package
entities. If the search term, target monikers, or prerelease flag eliminate all results, an empty result set is
returned.

For large result sets (packages that have many versions), a `<link rel="next" href="..." />` element can be returned.
This should be followed to fetch the next set of results.

Status Code     | Meaning
--------------- | -------
200 OK          | The result set has been returned
404 Bad Request | A required parameter is missing or the provided set of query parameters is not valid or not supported
