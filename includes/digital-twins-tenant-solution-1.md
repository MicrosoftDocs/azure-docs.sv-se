---
author: baanders
description: inkludera fil som beskriver en tokenlösning till begränsningen mellan klientorganisationen med Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589365"
---
Ett sätt att göra detta är med följande CLI-kommando, där är ID:t för den Azure AD-klientorganisation som `<home-tenant-ID>` innehåller Azure Digital Twins instansen:

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

När identiteten har begärt detta får den en token utfärdad för Azure AD-resursen, som har ett matchande *https://digitaltwins.azure.net* klient-ID-anspråk till Azure Digital Twins instansen. Om du använder denna token i API-begäranden eller `Azure.Identity` med din kod bör den federerade identiteten få åtkomst Azure Digital Twins resursen.