# Authentication

## Roles

There are typically two different roles when interacting with a package source:

- **reader**: able to discover and download packages.
- **writer**: able to upload and delete packages.

Some server implementations give the writer role access to all packages on the source. Other implementations only give
access to the package IDs which were originally uploaded by that author.

<aside>
How users with these different roles are authenticated varies from one package source implementation to the next.
</aside>

## NuGet.org, NuGet.Server, MyGet

The **reader** role is assigned to anonymous users. A caller can download any package from NuGet.org without providing
any form of credentials.

The **writer** role authentication and authorization is achieved using the `X-NuGet-ApiKey` HTTP header.

## Visual Studio Team Services (VSTS)

Authentication to VSTS is typically done using personal access tokens (PAT).

The **reader** role is assigned to users with read access to the package feed and who are using a PAT that has the
"Packaging (read)", "Packaging (read and write)" or "Packaging (read, write, and manage)" scope.

The **writer** role is assigned to users with write access to the package feed and who are using a PAT that has the
"Packaging (read and write)" or "Packaging (read, write, and manage)" scope.

The `X-NuGet-ApiKey` is required by the protocol but is ignored by VSTS since all authentication and authorization
information is contained in the PAT.
