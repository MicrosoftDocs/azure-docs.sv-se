---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 035399924216434de85865102db8838ea3fa15a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85570128"
---
## <a name="create-a-project-zip-file"></a>Skapa en ZIP-fil av projektet

>[!NOTE]
> Om du har laddat ned filerna i en ZIP-fil extraherar du filerna först. Om du till exempel har hämtat en ZIP-fil från GitHub kan du inte distribuera filen i befintligt skick. GitHub lägger till ytterligare kapslade kataloger som inte fungerar med App Service. 
>

I ett lokalt terminalfönster navigerar du till rot katalogen för ditt Apps-projekt. 

Den här katalogen ska innehålla post filen till din webbapp, till exempel _index.html_, _index. php_ och _app.js_. Den kan också innehålla paket hanterings filer som _project.jspå_, _composer.jspå_, _package.jspå_, _bower.jspå_ och _requirements.txt_.

Om du inte vill att App Service ska köra distributions automatisering, kör du alla Bygg aktiviteter (till exempel,,,, `npm` `bower` `gulp` `composer` och `pip` ) och kontrollerar att du har alla filer du behöver för att köra appen. Det här steget krävs om du vill [köra paketet direkt](../articles/app-service/deploy-run-package.md).

Skapa ett ZIP-arkiv med allt i projektet. För `dotnet` -projekt är den här mappen mappen utdata i `dotnet publish` kommandot. Följande kommando använder standardverktyget i terminalen:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

