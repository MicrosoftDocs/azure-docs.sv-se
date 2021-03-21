---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001221"
---
Om du behöver konfigurera en HTTP-proxy för att göra utgående begär Anden använder du följande två argument:

| Name | Datatyp | Beskrivning |
|--|--|--|
|HTTPS_PROXY|sträng|Proxyservern som ska användas, till exempel `https://proxy:8888`<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|sträng|Alla autentiseringsuppgifter som krävs för att autentisera mot proxyservern, till exempel username: Password.|
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
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
