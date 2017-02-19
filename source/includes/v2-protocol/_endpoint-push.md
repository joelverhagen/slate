## Endpoint: push package

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
NuGetGallery | no
NuGet.Server | no, unless `allowOverrideExistingPackageOnPush` is set to `true`
MyGet        | yes, unless "Forbid overwriting of existing packages?" package setting is enabled
VSTS         | no

### HTTP Request

`PUT http://example.com/nuget`

<aside>NuGetGallery accepts <code>POST</code> requests in addition to <code>PUT</code>.</aside>

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
