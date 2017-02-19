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
NuGetGallery | unlist
NuGet.Server | delete, unless `enableDelisting` is set to `true`

**TODO:** document MyGet and VSTS

### HTTP Request

`DELETE http://example.com/nuget/{package ID}/{package version}`

### URL Parameters

Name              | Required | Description
----------------- | -------- | -----------
{package ID}      | true     | The ID of the package to be deleted
{package version} | true     | The version of the package to be deleted

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
