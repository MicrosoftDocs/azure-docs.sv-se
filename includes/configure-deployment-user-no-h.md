---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8f1fe6ae38c708c5205f8c1230da05457d6b7010
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764316"
---
FTP och lokal Git kan distribuera till en Azure-webbapp med hjälp av en *distributionsanvändare*. När du har konfigurerat distributionsanvändaren kan du använda den för alla dina Azure-distributioner. Ditt användarnamn och lösenord för distribution på kontonivå skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration. 

Konfigurera distributionsanvändaren genom att köra kommandot [az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) i Azure Cloud Shell. Ersätt \<username> och med ett användarnamn och lösenord för \<password> distributionsanvändaren. 

- Användarnamnet måste vara unikt i Azure och för lokala Git-pushar får det inte innehålla \@ symbolen . 
- Lösenordet måste vara minst åtta tecken långt, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON-utdata visar lösenordet som `null` . Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

Registrera ditt användarnamn och lösenord som ska användas för att distribuera dina webbappar.
