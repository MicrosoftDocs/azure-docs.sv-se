---
title: Hämta loggar för containerinstanser & händelser
description: Lär dig hur du hämtar containerloggar och händelser i Azure Container Instances för att felsöka problem med containrar
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f5eb8c878164846ed2f1daf1cb7e5014e0c62c55
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764053"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Hämta loggar och händelser för containers i Azure Container Instances

När du har en container som fungerar felaktigt i Azure Container Instances börjar du med att visa dess loggar med [az container logs][az-container-logs]och strömma standardfelet och standardfelet med az container [attach][az-container-attach]. Du kan också visa loggar och händelser för containerinstanser i Azure Portal eller skicka logg- och händelsedata för containergrupper för [att Azure Monitor loggar](container-instances-log-analytics.md).

## <a name="view-logs"></a>Visa loggar

Om du vill visa loggar från programkoden i en container kan du använda [kommandot az container logs.][az-container-logs]

Följande är loggutdata från den uppgiftsbaserade exempelcontainern i Ange kommandoraden i en [containerinstans](container-instances-start-command.md#azure-cli-example)efter att ha angett en ogiltig URL med hjälp av en kommandorads åsidosättning:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Ansluta utdataströmmar

Kommandot [az container attach][az-container-attach] ger diagnostisk information vid start av containern. När containern har startats strömmas STDOUT och STDERR till den lokala konsolen.

Här är till exempel utdata från den uppgiftsbaserade containern i Ange kommandoraden i en [containerinstans](container-instances-start-command.md#azure-cli-example), efter att ha angett en giltig URL för en stor textfil som ska bearbetas:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Hämta diagnostikhändelser

Om det inte går att distribuera containern granskar du den diagnostikinformation som tillhandahålls av Azure Container Instances-resursprovidern. Om du vill visa händelser för din container kör du [kommandot az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Utdata innehåller kärnegenskaperna för din container, tillsammans med distributionshändelser (visas trunkerat här):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Nästa steg
Lär dig hur [du felsöker vanliga container- och distributionsproblem](container-instances-troubleshooting.md) för Azure Container Instances.

Lär dig hur du skickar logg- och händelsedata för containergrupper till [Azure Monitor loggar](container-instances-log-analytics.md).

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
