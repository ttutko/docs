﻿#Commands : SearchAsync

Use **SearchAsync** method to fetch list of files matching a specified query.

## Syntax

{CODE search_1@FileSystem\ClientApi\Commands\Search.cs /}

| Parameters | | |
| ------------- | ------------- | ----- |
| **query** | string | The query containing search criteria (you can use [built-in fields](../../../../indexing) or metadata entries) consistent with [Lucene syntax](http://lucene.apache.org/core/old_versioned_docs/versions/3_0_0/queryparsersyntax.html) |
| **sortFields** | string[] | The fields to sort by |
| **start** | int | The start number to read index results |
| **pageSize** | int | The max number of results that will be returned |

<hr />

| Return Value | |
| ------------- | ------------- |
| **Task&lt;SearchResults&gt;** | A task that represents the asynchronous operation. The task result is [`SearchResults`](../../../../../glossary/search-results) object which represents results of a specified query. |

## Example

In order to get the list of files that has `Everyone` value under `AllowRead` metadata key, returned in ascending order by a full file name (stored under built-in `__key` field) you need to build the following query:

{CODE search_2@FileSystem\ClientApi\Commands\Search.cs /}