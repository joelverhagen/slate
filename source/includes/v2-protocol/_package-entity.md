## Package entity

At the core of the V2 protocol is the OData package entity. A single package entity is represented by a `<entry>`
element. A collection of package entities is represented by a `<feed>` element with zero or more `<entry>` child
elements. 

The identity (i.e. key) of the package entity is the Id and Version properties.

<aside>
The set of properties available and where the values are defined varies between the server implementations.
</aside>

### Properties from `<entry>`

The following elements are available on every known server implementation.

Element                       | Description
----------------------------- | -----------
`<id>`, text content          | URL to [get metadata about a single package](#endpoint-get-a-single-package)
`<content>`, `src` attribute  | URL to download the .nupkg
`<summary>`, text content     | Summary **only when Summary is defined**
`<author>`, `<name>` children | Authors string

The following properties vary from one server implementation to the next.

Element                                  | Description                          | [Availability](#quirk-abbreviations)
---------------------------------------- | ------------------------------------ | ------------------------------------
`<title>` as the ID, text content        | ID of the package                    | NG, NS2, NS3, VS
`<title>` as the Title, text content     | Title of the package                 | MY
`<summmay>` defaulting to Description    | **Only when Summary is not defined** | MY
`<summary>` with no text content         | **Only when Summary is not defined** | NS2
No `<summary>` element at all            | **Only when Summary is not defined** | NG, NS3, VS

### Properties from `<m:properties>`

These values are available under the `<m:properties>` element (a direct child of the `<entry>` element).
The types are [OData primitive data types](http://www.odata.org/documentation/odata-version-2-0/overview/#AbstractTypeSystem).

The following elements are available on every known server implementation. 

Element                        | Type         
------------------------------ | ----
`<d:Copyright>`                | Edm.String
`<d:Dependencies>`             | Edm.String
`<d:Description>`              | Edm.String
`<d:DownloadCount>`            | Edm.Int32
`<d:IconUrl>`                  | Edm.String
`<d:IsAbsoluteLatestVersion>`  | Edm.Boolean
`<d:IsLatestVersion>`          | Edm.Boolean
`<d:IsPrerelease>`             | Edm.Boolean
`<d:Language>`                 | Edm.String
`<d:LicenseUrl>`               | Edm.String
`<d:MinClientVersion>`         | Edm.String
`<d:NormalizedVersion>`        | Edm.String
`<d:PackageSize>`              | Edm.Int64
`<d:ProjectUrl>`               | Edm.String
`<d:ReleaseNotes>`             | Edm.String
`<d:RequireLicenseAcceptance>` | Edm.Boolean
`<d:Tags>`                     | Edm.String
`<d:Title>`                    | Edm.String
`<d:Version>`                  | Edm.String

The following properties vary from one server implementation to the next.

Element                        | Type         | [Availability](#quirk-abbreviations)
------------------------------ | ------------ | ------------------------------------
`<d:Authors>`                  | Edm.String   | NG NS3 VS
`<d:Created>`                  | Edm.DateTime | MY NG VS
`<d:DevelopmentDependency>`    | Edm.Boolean  | NS2 NS3
`<d:GalleryDetailsUrl>`        | Edm.String   | MY NG
`<d:Id>`                       | Edm.String   | MY NG NS3 VS
`<d:LastEdited>`               | Edm.DateTime | MY NG VS
`<d:LastUpdated>`              | Edm.DateTime | NG NS3 VS
`<d:LicenseNames>`             | Edm.String   | MY NG
`<d:LicenseReportUrl>`         | Edm.String   | MY NG
`<d:Listed>`                   | Edm.Boolean  | NS2 NS3 VS
`<d:Owners>`                   | Edm.String   | NS2 NS3
`<d:PackageHash>`              | Edm.String   | MY NG NS2 NS3
`<d:PackageHashAlgorithm>`     | Edm.String   | MY NG NS2 NS3
`<d:Published>`                | Edm.DateTime | MY NG NS3 VS
`<d:ReportAbuseUrl>`           | Edm.String   | MY NG
`<d:Summary>`                  | Edm.String   | NG NS2 NS3 VS
`<d:VersionDownloadCount>`     | Edm.Int32    | MY NG NS2 NS3

### Client recommendations

#### Downloading .nupkgs

Tthe `src` attribute of the `<content>` element (a direct child element within the `<entry>`) is the URL to where the
package (.nupkg file) can be downloaded. This download URL has no specific pattern. Clients should discover .nupkg URLs
by inspecting the XML `<content>` element for the desired package and should not depend on the URLs having a particular
structure.

#### Determining the package ID

Unfortunately, NuGet.Server on WCF (and perhaps other server implementations) do not include the ID in the
`<m:properties>` element. All other server implementations do. To work around this, use the following algorithm to
determine a package's ID.

1. Check for the `<d:Id>` element under `<d:properties>`.
1. If it exists, use the text content found there.
1. If it does not exist, use the text content found under the `<title>` element (found under the `<entry>` element).

It may be tempting to parse the ID out of the URL found in the `<id>` element. However, this is not guaranteed to have
SemVer 2.0.0 build metadata.
