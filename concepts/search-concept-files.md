---
title: "Use the Microsoft Search API in Microsoft Graph to search files"
description: "You can use the Microsoft Search API to search files stored in SharePoint or OneDrive."
author: "nmoreau"
localization_priority: Normal
ms.prod: "search"
---

# Use the Microsoft Search API in Microsoft Graph to search files

You can use the Microsoft Search API to search files stored in SharePoint or OneDrive. The Microsoft Search API uses a relevance model that makes use of signals from Microsoft Graph about users' relationships and activities. This enables you to return and promote the content that users care about, in a file search experience that is consistent with the **Files** tab that lists search results in SharePoint. 

[!INCLUDE [search-api-preview-signup](../includes/search-api-preview-signup.md)]

The API can also surface external files exposed via the [externalFile](/graph/api/resources/externalfile?view=graph-rest-beta) resource.

## Search SharePoint or OneDrive files

You can use KQL in search terms of queries for SharePoint and OneDrive. For example:

- `"query": "contoso filetype:docx OR filetype:doc"` scopes the query to Word documents.
- `"query": "test path:\\"https://contoso.sharepoint.com/sites/Team Site/Documents/Project\\""` scopes the query to a particular folder within a site.

In order to be valid, properties restriction should specify a valid, queryable managed property name in the condition.

### Example

#### Request

```HTTP
POST /search/query
Content-Type: application/json
```

```json
{
  "requests": [
    {
      "entityTypes": [
        "microsoft.graph.driveItem"
      ],
      "query": {
        "query_string": {
          "query": "contoso"
        }
      },
      "from": 0,
      "size": 25
    }
  ]
}
```

#### Response

<!---TODO nmoreau team Include one example of externalItem response.-->
```json
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#search",
  "value": [
    {
      "searchTerms": [
        "test"
      ],
      "hitsContainers": [
        {
          "total": 350,
          "moreResultsAvailable": true,
          "hits": [
            {
              "_id": "FlULeN/ui/1GjLx1rUfio5UAAEl",
              "_score": 1,
              "_sortField": "Relevance",
              "_summary": "<c0>Contoso</c0> Detailed Design <ddd/>",
              "_source": {
                "@odata.type": "#microsoft.graph.driveItem",
                "createdDateTime": "2019-06-10T06:37:43Z",
                "lastModifiedDateTime": "2019-06-10T06:37:43Z",
                "name": "web_part_test_long Notebook",
                "webUrl": "https://contoso.sharepoint.com/sites/contoso-team/contoso-designs.docx",
                "lastModifiedBy": {
                  "user": {
                    "displayName": "Richard Mayer"
                  }
                },
                "fileSystemInfo": {
                  "createdDateTime": "2019-06-10T06:37:43Z",
                  "lastModifiedDateTime": "2019-06-10T06:37:43Z"
                }
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## Search external files (well-known types)

The [file share connector](/MicrosoftSearch/file-share-connector) is available in Microsoft Search by default. You can use it to index files available on a file share. You can use the query API to query all external files.

<!-- markdownlint-disable MD024 -->
### Example

The following example returns all configured external files for the tenant, and sorts the results by relevance.

#### Request

```HTTP
POST /search/query
Content-Type: application/json
```

```json
{
  "requests": [
    {
      "entityTypes": [
        "microsoft.graph.externalFile"
      ],
      "query": {
        "query_string": {
          "query": "contoso"
        }
      },
      "from": 0,
      "size": 25
    }
  ]
}
```

#### Response

```json
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#search",
  "value": [
    {
      "searchTerms": [
        "contoso"
      ],
      "hitsContainers": [
        {
          "total": 4,
          "moreResultsAvailable": true,
          // Hits represent the search results
          "hits": [
            {
              "_id": "FsHvoeTuRRVLnuEZLEVBfSQAAWTp",
              "_score": 1,
              "_sortField": "Relevance",
              "_source": {
                "@odata.type": "#microsoft.graph.externalFile",
                "id": "FsHvoeTuRRVLnuEZLEVBfSQAAWTp",
                "extension": "pptx",
                "name": "Contoso-Overview.pptx",
                "lastModifiedTime": "2018-05-09T04:01:14Z",
                "modifiedBy": "Baala Vedantam",
                "title": "Contoso Overview 2018",
                "url": "file://fileshare01/External Presentations/Contoso-Overview.pptx"
              }
            },
            {
              //Another searchHit
            }
          ]
        }
      ]
    }
  ]
}
```

## Search all files (including externalFile instances)

You can search all the files in a tenant, including files stored in [driveItems](/graph/api/resources/driveitem?view=graph-rest-beta) and external files, by specifying two entity types in the search request.

The response includes **driveItem** and **externalItem** instances in the `_sources` field of each [searchHit](/graph/api/resources/searchhit?view=graph-rest-beta) object.

### Example

The following example returns all configured **externalFile** and **driveItem** objects in the tenant that satisfy the search terms. It sorts the results by relevance.

### Request

```HTTP
POST https://graph.microsoft.com/beta/search/query
Content-Type: application/json
```

```json
{
  "requests": [
    {
      "entityTypes": [
        "microsoft.graph.driveItem",
        "microsoft.graph.externalFile"
      ],
      "query": {
        "query_string": {
          "query": "contoso"
        }
      },
      "from": 0,
      "size": 25
    }
  ]
}
```

## Known limitations

You cannot scope a query to a particular connection ID.

## Next steps

- [Use the Microsoft Search API to query data](/graph/api/resources/search-api-overview?view=graph-rest-beta)

