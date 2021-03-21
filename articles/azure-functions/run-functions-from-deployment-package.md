---
title: Köra Azure Functions från ett paket
description: Kör den Azure Functions körningen av dina funktioner genom att montera en distributions paket fil som innehåller dina projektfiler för Function-appen.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: aad6991d0ddd5c439d03e41adec63837a21db87b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581600"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Köra Azure Functions från en paketfil

I Azure kan du köra dina funktioner direkt från en distributions paket fil i din Function-app. Det andra alternativet är att distribuera dina filer i `d:\home\site\wwwroot` katalogen i din Function-app.

I den här artikeln beskrivs fördelarna med att köra funktioner från ett paket. Den visar också hur du aktiverar den här funktionen i Function-appen.

## <a name="benefits-of-running-from-a-package-file"></a>Fördelar med att köra från en paket fil
  
Det finns flera fördelar med att köra från en paket fil:

+ Minskar risken för fil kopierings låsnings problem.
+ Kan distribueras till en produktions-app (med omstart).
+ Du kan vara säker på vilka filer som körs i din app.
+ Förbättrar prestandan för [Azure Resource Manager distributioner](functions-infrastructure-as-code.md).
+ Kan minska kall start tider, särskilt för JavaScript-funktioner med stora NPM paket träd.

Mer information finns i [det här meddelandet](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Aktivera funktioner för att köra från ett paket

Om du vill att din Function-app ska köras från ett paket lägger du bara till en `WEBSITE_RUN_FROM_PACKAGE` inställning i appens inställningar. `WEBSITE_RUN_FROM_PACKAGE`Inställningen kan ha ett av följande värden:

| Värde  | Beskrivning  |
|---------|---------|
| **`1`**  | Rekommenderas för Function-appar som körs i Windows. Kör från en paket fil i `d:\home\data\SitePackages` mappen i din Function-app. Om du inte [distribuerar med zip-distribution](#integration-with-zip-deployment)kräver det här alternativet att mappen också har en fil med namnet `packagename.txt` . Den här filen innehåller bara namnet på paket filen i mappen utan blank steg. |
|**`<URL>`**  | Platsen för en angiven paketfil som du vill köra. När du använder Blob Storage bör du använda en privat behållare med en [signatur för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att göra det möjligt för functions-körningen att komma åt paketet. Du kan använda [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att ladda upp paketfiler till ditt Blob Storage-konto. När du anger en URL måste du även [Synkronisera utlösare](functions-deployment-technologies.md#trigger-syncing) när du har publicerat ett uppdaterat paket. |

> [!CAUTION]
> När du kör en Function-app i Windows ger det externa URL-alternativet sämre kall start prestanda. När du distribuerar din Function-app till Windows bör du `WEBSITE_RUN_FROM_PACKAGE` ange `1` och publicera med zip-distribution.

Följande visar en Function-app som kon figurer ATS för att köras från en. zip-fil som finns i Azure Blob Storage:

![WEBSITE_RUN_FROM_ZIP app-inställning](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> För närvarande stöds endast. zip-paketfiler.

## <a name="integration-with-zip-deployment"></a>Integrering med zip-distribution

[Zip-distribution][Zip deployment for Azure Functions] är en funktion i Azure App Service som gör att du kan distribuera ditt app-projekt till `wwwroot` katalogen. Projektet paketeras som en. zip-distributions fil. Samma API: er kan användas för att distribuera paketet till `d:\home\data\SitePackages` mappen. Med `WEBSITE_RUN_FROM_PACKAGE` appens inställnings värde `1` kopierar API: erna för zip-distribution ditt paket till `d:\home\data\SitePackages` mappen i stället för att extrahera filerna till `d:\home\site\wwwroot` . Filen skapas också `packagename.txt` . Efter en omstart monteras paketet till `wwwroot` som ett skrivskyddat fil system. Mer information om zip-distribution finns i [zip-distribution för Azure Functions](deployment-zip-push.md).

> [!NOTE]
> När en distribution sker utlöses en omstart av Function-appen. Innan en omstart tillåts alla befintliga funktions körningar att slutföras eller tids gränsen uppnåddes. Mer information finns i [distributions beteenden](functions-deployment-technologies.md#deployment-behaviors).

## <a name="adding-the-website_run_from_package-setting"></a>Lägga till inställningen WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Felsökning

- Kör från paket gör `wwwroot` skrivskyddat så att du får ett fel meddelande när du skriver filer till den här katalogen.
- Tar-och gzip-format stöds inte.
- ZIP-filen kan bara vara 1 GB.
- Den här funktionen skapar inte lokal cache.
- För förbättrade kall start prestanda använder du det lokala zip-alternativet ( `WEBSITE_RUN_FROM_PACKAGE` = 1).
- Kör från paket är inkompatibelt med alternativet för distributions anpassning ( `SCM_DO_BUILD_DURING_DEPLOYMENT=true` ), build-steget kommer att ignoreras under distributionen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
