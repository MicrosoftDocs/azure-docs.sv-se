---
title: ta med fil
description: inkludera fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "78255803"
---
## <a name="robots933456-in-logs"></a>robots933456 i loggar

Följande meddelande kan visas i containerloggarna:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Du kan ignorera det här meddelandet. `/robots933456.txt` är en dummysökväg för URL:en som App Service använder till att kontrollera om containern kan hantera begäranden. Ett 404-svar innebär helt enkelt att sökvägen inte finns, men det låter App Service veta att containern är felfri och redo att svara på begäranden.

