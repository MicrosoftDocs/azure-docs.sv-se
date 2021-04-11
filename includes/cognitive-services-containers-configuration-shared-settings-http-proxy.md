---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630126"
---
Om du behöver konfigurera en HTTP-proxy för att göra utgående begär Anden använder du följande två argument:

| Name | Datatyp | Beskrivning |
|--|--|--|
|HTTP_PROXY|sträng|Proxyservern som ska användas, till exempel `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|sträng|Alla autentiseringsuppgifter som krävs för att autentisera mot proxyservern, till exempel `username:password` . Det här värdet **måste vara i gemener**. |
|`<proxy-user>`|sträng|Användaren för proxyservern.|
|`<proxy-password>`|sträng|Lösen ordet som är associerat med `<proxy-user>` för proxyservern.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
