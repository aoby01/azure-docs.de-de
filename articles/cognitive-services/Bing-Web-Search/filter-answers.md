---
title: Filtering the web answers that Bing returns | Microsoft Docs
description: Shows how to use responseFilter to filter the answers that the Bing Web Search API returns.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.technology: bing-web-search
ms.topic: article
ms.date: 01/12/2017
ms.author: scottwhi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 761f2940b1f2184a9a6702b0e2878ddc1b0b9fe7
ms.contentlocale: de-de
ms.lasthandoff: 05/10/2017

---

# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtering the answers that the search response includes  

When you query the web, Bing returns all content that it thinks is relevant to the search. For example, if the search query is "sailing+dinghies", the response might contain the following answers:

```
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

If you're interested in specific types of content such as images, videos, and news, you can request only those answers by using the [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v5-reference#responsefilter) query parameter. If Bing finds relevant content for the specified answers, Bing returns it. The response filter is a comma-delimited list of answers. The following shows how to use `responseFilter` to request images, videos, and news of sailing dinghies. When you encode the query string, the commas change to %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v5.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

The following shows the response to the previous query. As you can see Bing didn't find relevant video and news results, so the response doesn't include them.

```
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/#Images"
                }
            }]
        }
    }
}
```

Although Bing did not return video and news results in the previous response, it does not mean that video and news content does not exist. It simply means that the page didn't include them. However, if you [page](./paging-webpages.md) through more results, the subsequent pages would likely include them. Also, if called the [Video Search API](../bing-video-search/search-the-web.md) and [News Search API](../bing-news-search/search-the-web.md) endpoints directly, the response would likely contain results. 

You are discouraged from using `responseFilter` to get results from a single API. If you want content from a single Bing API, call that API directly. For example, to receive only images, send a request to the Image Search API endpoint, `https://api.cognitive.microsoft.com/bing/v5.0/images/search` or one of the other [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference.md/endpoints) endpoints. Calling the single API is important not only for performance reasons but because the content-specific APIs offer richer results. For example, you can use filters that are not available to the Web Search API to filter the results.  
  
To get search results from a specific domain, include the [site:](http://msdn.microsoft.com/library/ff795613.aspx) query operator in the query string.  

```
https://api.cognitive.microsoft.com/bing/v5.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE] 
> Depending on the query, if you use the `site:` query operator, there is the chance that the response may contain adult content regardless of the [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v5-reference#safesearch) setting. You should use `site:` only if you are aware of the content on the site and your scenario supports the possibility of adult content. 
  
## <a name="limiting-the-number-of-answers-in-the-response"></a>Limiting the number of answers in the response

> [!NOTE]
> Available in the Bing Web Search v7 Preview only.

Bing includes answers in the response based on ranking. For example, if you query *sailing+dinghies*, Bing returns `webpages`, `images`, `videos`, and `relatedSearches`.

```
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

To limit the number of answers that Bing returns to the top two answers (webpages and images), set the [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) query parameter to 2. 

```  
GET https://api.cognitive.microsoft.com/bing/v5.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

The response includes only `webPages` and `images`.

```
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

If you add the `responseFilter` query parameter to the previous query and set it to webpages and news, the response contains only webpages because news is not ranked.

```
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Promoting answers that are not ranked

> [!NOTE]
> Available in the Bing Web Search v7 Preview only.

If the top ranked answers that Bing returns for a query are webpages, images, videos, and relatedSearches, the response would include those answers. If you set [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) to two (2), Bing returns the top two ranked answers: webpages and images. If you want Bing to include images and videos in the response, specify the [promote](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) query parameter and set it to images and videos. 

```  
GET https://api.cognitive.microsoft.com/bing/v5.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

The following is the response to the above request. Bing returns the top two answers, webpages and images, and promotes videos into the answer.

```
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

If you set `promote` to news, the response doesn't include the news answer because it is not a ranked answer&mdash;you can promote only ranked answers.

The answers that you want to promote do not count against the `answerCount` limit. For example, if the ranked answers are news, images, and videos, and you set `answerCount` to 1 and `promote` to news, the response contains news and images. Or, if the ranked answers are videos, images, and news, the response contains videos and news.

You may use `promote` only if you specify the `answerCount` query parameter.

