## OData parameters

OData supports a number of standard query parameters to manipulate the result set.

Name                                                                                                       | Required | Description
---------------------------------------------------------------------------------------------------------- | -------- | -----------
[$filter](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#FilterSystemQueryOption)   | false    | Used to determine if a package should be in the result set
[$orderby](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#OrderBySystemQueryOption) | false    | Used to sort the result set
[$select](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#SelectSystemQueryOption)   | false    | Used to return a subset of properties on each package.
[$skip](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#SkipSystemQueryOption)       | false    | Used for paging across the result set
[$top](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/#TopSystemQueryOption)         | false    | Used for paging across the result set

The query parameters have varying support across different endpoints and server implementations.

**TODO**: Document support for $count, $format, $expand, $inlinecount, etc.
