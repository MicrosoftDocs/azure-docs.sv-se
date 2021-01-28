---
title: Självstudie – Utlös container grupp efter Azure Function
description: Skapa en HTTP-utlöst, Server lös PowerShell-funktion för att automatisera skapandet av Azure Container instances
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e7315796f2f7f89800b58f5fa607e69cd7ae3447
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935419"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Självstudie: Använd en HTTP-utlöst Azure-funktion för att skapa en behållar grupp

[Azure Functions](../azure-functions/functions-overview.md) är en server lös beräknings tjänst som kan köra skript eller kod som svar på en rad olika händelser, till exempel en http-begäran, en timer eller ett meddelande i en Azure Storage kö.

I den här självstudien skapar du en Azure-funktion som tar en HTTP-förfrågan och utlöser distributionen av en [behållar grupp](container-instances-container-groups.md). Det här exemplet visar grunderna i hur du använder Azure Functions för att automatiskt skapa resurser i Azure Container Instances. Ändra eller utöka exemplet för mer komplexa scenarier eller andra händelse utlösare. 

Lär dig att:

> [!div class="checklist"]
> * Använd Visual Studio Code med [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för att skapa en grundläggande http-utlöst PowerShell-funktion.
> * Aktivera en identitet i Function-appen och ge den behörighet att skapa Azure-resurser.
> * Ändra och publicera om PowerShell-funktionen för att automatisera distributionen av en container grupp med en behållare.
> * Verifiera den HTTP-utlösta distributionen av containern.

## <a name="prerequisites"></a>Förutsättningar

Se [skapa din första funktion i Azure med Visual Studio Code](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell#configure-your-environment) för krav för att installera och använda Visual Studio code med Azure Functions tillägget på ditt operativ system.

Ytterligare steg i den här artikeln använder Azure PowerShell. Om du behöver installera eller uppgradera kan du läsa [installera Azure PowerShell][azure-powershell-install] och [Logga in på Azure](/powershell/azure/get-started-azureps#sign-in-to-azure).

## <a name="create-a-basic-powershell-function"></a>Skapa en grundläggande PowerShell-funktion

Följ stegen i [skapa din första PowerShell-funktion i Azure](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell) för att skapa en PowerShell-funktion med hjälp av http-utlösaren. Använd standard namnet **HttpTrigger** i Azure function. Som du ser i snabb starten, testa funktionen lokalt och publicera projektet till en Function-app i Azure. Det här exemplet är en grundläggande HTTP-utlöst funktion som returnerar en text sträng. I senare steg i den här artikeln ändrar du funktionen för att skapa en behållar grupp.

I den här artikeln förutsätter vi att du publicerar projektet med namnet *myfunctionapp* i en Azure-resurs grupp automatiskt med namnet enligt namnet på Function-appen (även *myfunctionapp*). Ersätt namnet på din unika Function-app och resurs grupps namnet i senare steg.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Aktivera en Azure-hanterad identitet i Function-appen

Följande kommandon aktiverar en systemtilldelad [hanterad identitet](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) i din Function-app. PowerShell-värden som kör appen kan automatiskt autentisera till Azure med hjälp av den här identiteten, vilket gör det möjligt att vidta åtgärder på Azure-tjänster som identiteten beviljas åtkomst till. I den här självstudien beviljar du hanterade identitets behörigheter för att skapa resurser i Function-appens resurs grupp. 

[Lägg till en identitet](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1) i Function-appen:

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

Tilldela identiteten som deltagar rollen är begränsad till resurs gruppen:

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>Ändra funktionen HttpTrigger

Ändra PowerShell-koden för funktionen **HttpTrigger** för att skapa en behållar grupp. `run.ps1`Leta upp följande kodblock i filen för funktionen. Den här koden visar ett namn värde, om ett sådant skickas som en frågesträng i funktions webb adressen:

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

Ersätt den här koden med följande exempel block. Om ett namn-värde skickas i frågesträngen används det för att namnge och skapa en behållar grupp med cmdleten [New-AzContainerGroup][new-azcontainergroup] . Se till att ersätta resurs grupps namnet *myfunctionapp* med namnet på resurs gruppen för din Function-app:

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

I det här exemplet skapas en behållar grupp som består av en enda behållar instans som kör `alpine` avbildningen. Behållaren kör ett enda `echo` kommando och avslutas sedan. I ett verkligt exempel kan du utlösa skapandet av en eller flera behållar grupper för att köra ett batch-jobb.
 
## <a name="test-function-app-locally"></a>Testa Function-appen lokalt

Se till att funktionen körs lokalt innan du publicerar om Function-Appaketet till Azure. När det körs lokalt skapar funktionen inte Azure-resurser. Du kan dock testa funktions flödet med och utan att skicka ett namn-värde i en frågesträng. Fel sökning av funktionen finns i [Felsöka PowerShell-Azure Functions lokalt](../azure-functions/functions-debug-powershell-local.md).

## <a name="republish-azure-function-app"></a>Publicera Azure Function-appen igen

När du har kontrollerat att funktionen körs lokalt kan du publicera projektet på den befintliga Function-appen i Azure.

1. I Visual Studio Code öppnar du kommandopaletten. Sök efter och välj `Azure Functions: Deploy to Function App...` .
1. Välj den aktuella arbetsmappen till zip och distribuera.
1. Välj prenumerationen och sedan namnet på den befintliga Function-appen (*myfunctionapp*). Bekräfta att du vill skriva över den tidigare distributionen.

Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har uppdaterat.

## <a name="run-the-function-in-azure"></a>Kör funktionen i Azure

När distributionen har slutförts hämtar du funktions-URL: en. Använd till exempel avsnittet **Azure: Functions** i Visual Studio Code för att kopiera **HttpTrigger** -funktionens URL eller hämta funktions webb adressen i [Azure Portal](../azure-functions/functions-get-started.md).

Funktions webb adressen har formatet:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>Kör funktion utan att skicka ett namn

Som första test kör du `curl` kommandot och skickar funktions webb adressen utan att lägga till en `name` frågesträng. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

Funktionen returnerar status koden 200 och texten `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response` :

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Kör funktionen och skicka namnet på en behållar grupp

Kör nu `curl` kommandot och Lägg till namnet på en behållar grupp (*mycontainergroup*) som en frågesträng `?name=mycontainergroup` :

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

Funktionen returnerar status koden 200 och utlöser skapandet av behållar gruppen:

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

Kontrol lera att behållaren kördes med kommandot [Get-AzContainerInstanceLog][get-azcontainerinstancelog] :

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

Exempel på utdata:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna som du skapade i den här självstudien kan du köra kommandot [AZ Group Delete] [AZ-Group-Delete] för att ta bort resurs gruppen och alla resurser den innehåller. Det här kommandot tar bort den Function-app som du har skapat, samt den behållare som körs och alla relaterade resurser.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Azure-funktion som tar en HTTP-förfrågan och utlöser distributionen av en behållar grupp. Du har lärt dig att:

> [!div class="checklist"]
> * Använd Visual Studio Code med Azure Functions-tillägget för att skapa en grundläggande HTTP-utlöst PowerShell-funktion.
> * Aktivera en identitet i Function-appen och ge den behörighet att skapa Azure-resurser.
> * Ändra PowerShell-funktions koden för att automatisera distribution av en container grupp med en behållare.
> * Verifiera den HTTP-utlösta distributionen av containern.

Ett detaljerat exempel för att starta och övervaka ett behållar jobb finns i avsnittet om [händelse drivna serverbaserade behållare för blogg inlägg med PowerShell Azure Functions och Azure Container instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) och tillhör ande [kod exempel](https://github.com/anthonychu/functions-powershell-run-aci).

I [Azure Functions-dokumentationen](../azure-functions/index.yml) finns detaljerad information om hur du skapar Azure Functions och publicerar ett Functions-projekt. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
