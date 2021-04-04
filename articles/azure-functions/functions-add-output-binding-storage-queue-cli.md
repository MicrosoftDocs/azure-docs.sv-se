---
title: Ansluta Azure Functions till Azure Storage med hjälp av kommando rads verktyg
description: Lär dig hur du ansluter Azure Functions till en Azure Storage kö genom att lägga till en utgående bindning till ditt kommando rads projekt.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 6d0d559579e841553e04c26bf4dcc78934de3e95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94920210"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Ansluta Azure Functions till Azure Storage med hjälp av kommando rads verktyg

I den här artikeln integrerar du en Azure Storage kö med den funktion och det lagrings konto som du skapade i föregående snabb starts artikel. Du uppnår denna integrering genom att använda en *utgående bindning* som skriver data från en http-begäran till ett meddelande i kön. Om du slutför den här artikeln debiteras inga ytterligare kostnader utöver de få USD cent i föregående snabb start. Mer information om bindningar finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

::: zone pivot="programming-language-csharp"  
Innan du börjar måste du slutföra artikeln, [snabb start: skapa ett Azure Functions-projekt från kommando raden](create-first-function-cli-csharp.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa Function-appen och relaterade resurser i Azure.  
::: zone-end  
::: zone pivot="programming-language-javascript"  
Innan du börjar måste du slutföra artikeln, [snabb start: skapa ett Azure Functions-projekt från kommando raden](create-first-function-cli-node.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa Function-appen och relaterade resurser i Azure.  
::: zone-end   
::: zone pivot="programming-language-java"  
Innan du börjar måste du slutföra artikeln, [snabb start: skapa ett Azure Functions-projekt från kommando raden](create-first-function-cli-java.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa Function-appen och relaterade resurser i Azure.  
::: zone-end   
::: zone pivot="programming-language-typescript"  
Innan du börjar måste du slutföra artikeln, [snabb start: skapa ett Azure Functions-projekt från kommando raden](create-first-function-cli-typescript.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa Function-appen och relaterade resurser i Azure.  
::: zone-end   
::: zone pivot="programming-language-python"  
Innan du börjar måste du slutföra artikeln, [snabb start: skapa ett Azure Functions-projekt från kommando raden](create-first-function-cli-python.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa Function-appen och relaterade resurser i Azure.  
::: zone-end   
::: zone pivot="programming-language-powershell"  
Innan du börjar måste du slutföra artikeln, [snabb start: skapa ett Azure Functions-projekt från kommando raden](create-first-function-cli-powershell.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa Function-appen och relaterade resurser i Azure.  
::: zone-end   

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Mer information om bindningar finns i [Azure Functions utlösare och bindning av koncept](functions-triggers-bindings.md) och utdata från [kö](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Lägg till kod för att använda utgående bindning

När du har definierat kökvoter kan du nu uppdatera din funktion för att ta emot `msg` Utdataparametern och skriva meddelanden till kön.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Observera att du *inte* behöver skriva någon kod för autentisering, få en Queue-referens eller skriva data. Alla dessa integrations aktiviteter hanteras bekvämt i Azure Functions Runtime-och Queue-bindning.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visa meddelandet i Azure Storage kön

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Distribuera om projektet till Azure

Nu när du har verifierat lokalt att funktionen skrev ett meddelande till Azure Storage kön kan du distribuera om projektet för att uppdatera slut punkten som körs på Azure.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
I mappen *LocalFunctionsProj* använder du [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) kommandot för att distribuera om projektet och ersätter `<APP_NAME>` det med namnet på din app.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

I den lokala projektmappen använder du följande maven-kommando för att publicera projektet igen:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Verifiera i Azure

1. Som i föregående snabb start använder du en webbläsare eller en sväng för att testa den omdistribuerade funktionen.

    # <a name="browser"></a>[Webbläsare](#tab/browser)
    
    Kopiera den fullständiga **anrops-URL: en** som visas i utdata från kommandot Publicera till ett webbläsarens Adress fält, som lägger till Frågeparametern `&name=Functions` . Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

    ![Resultatet av funktionen körs på Azure i en webbläsare](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Kör [`curl`](https://curl.haxx.se/) med **anrops-URL: en** och Lägg till parametern `&name=Functions` . Kommandots utdata ska vara texten, "Hello Functions".
    
    ![Resultatet av funktionen körs på Azure med hjälp av sväng](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Granska lagrings kön igen, enligt beskrivningen i föregående avsnitt, för att kontrol lera att den innehåller det nya meddelandet som skrivits till kön.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagrings kö. Nu kan du lära dig mer om att utveckla funktioner från kommando raden med hjälp av kärn verktyg och Azure CLI:

+ [Arbeta med Azure Functions Core Tools](functions-run-local.md)  

+ [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)

::: zone pivot="programming-language-csharp"  
+ [Exempel på kompletta funktions projekt i C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referens för Azure Functions C#-utvecklare](functions-dotnet-class-library.md)  

[previous-quickstart]: create-first-function-cli-csharp.md

::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exempel på kompletta funktions projekt i Java Script](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions JavaScript-guide för utvecklare](functions-reference-node.md)  

[previous-quickstart]: create-first-function-cli-javascript.md
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exempel på kompletta funktions projekt i typescript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Guide för Azure Functions TypeScript-utvecklare](functions-reference-node.md#typescript)  

[previous-quickstart]: create-first-function-cli-typescript.md
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exempel på kompletta funktions projekt i python](/samples/browse/?products=azure-functions&languages=python).

+ [Guide för Azure Functions python-utvecklare](functions-reference-python.md)  

[previous-quickstart]: create-first-function-cli-python.md
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exempel på kompletta funktions projekt i PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Functions PowerShell-guide för utvecklare](functions-reference-powershell.md) 

[previous-quickstart]: create-first-function-cli-powershell.md
::: zone-end
