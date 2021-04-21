---
title: Kom igång med Azure CLI för Batch
description: Få en snabb introduktion till Batch-kommandon i Azure CLI för att hantera Azure Batch-tjänstens resurser
ms.topic: how-to
ms.date: 07/24/2018
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: befe2f6fe67ce7c2c4bb61153344103ee2aa70cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768441"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Hantera Batch-resurser med Azure CLI

Azure CLI är Azures kommandoradsmiljö för att hantera Azure-resurser. Den kan användas i Mac OS, Linux och Windows. Azure CLI är optimerat för att hantera och administrera Azure-resurser från kommandoraden. Du kan använda Azure CLI till att hantera Azure Batch-konton och hantera resurser, som pooler, jobb och aktiviteter. Med Azure CLI kan du skripta många av de uppgifter som du utför med Batch-API:erna, Azure Portal och Batch PowerShell-cmdletarna.

Den här artikeln innehåller en översikt över hur du använder [Azure CLI version 2.0](/cli/azure) med Batch. I [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli) ges en översikt över hur du använder CLI:t med Azure.

## <a name="set-up-the-azure-cli"></a>Konfigurera Azure CLI

Du kan köra senaste Azure CLI i [Azure Cloud Shell](../cloud-shell/overview.md). När du ska installera Azure CLI lokalt följer du stegen som beskrivs i [Installera Azure CLI](/cli/azure/install-azure-cli).

> [!TIP]
> Vi rekommenderar att du uppdaterar Azure CLI-installationen ofta för att dra nytta av tjänstuppdateringar och förbättringar.
> 
> 

## <a name="command-help"></a>Kommandohjälp

Du kan visa hjälptext för alla kommandon i Azure CLI genom att lägga till `-h` efter kommandot. Utelämna andra alternativ. Exempel:

* Om du vill få hjälp med `az`-kommandot anger du: `az -h`
* Om du vill hämta en lista över alla Batch-kommandon i CLI använder du: `az batch -h`
* Om du vill ha hjälp med att skapa ett Batch-konto anger du: `az batch account create -h`

Om du är tveksam ska du använda kommandoradsalternativet `-h` för att få hjälp med samtliga Azure CLI-kommandon.



Dessutom kan du läsa mer om [Azure CLI-kommandon för Batch](/cli/azure/batch) i referensdokumentationen för Azure CLI. 

## <a name="log-in-and-authenticate"></a>Logga in och autentisera

När du ska använda Azure CLI med Batch måste du logga in och autentisera. Det gör du i två enkla steg:

1. **Logga in på Azure.** När du loggar in på Azure får du tillgång till Azure Resource Manager-kommandon, inklusive kommandon för [Batch Management-tjänsten](batch-management-dotnet.md).  
2. **Logga in på Batch-kontot**. När du loggar in på ditt Batch-konto får du tillgång till kommandon för Batch-tjänsten.   

### <a name="log-in-to-azure"></a>Logga in på Azure

Det finns några olika sätt att logga in på Azure, läs mer i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli):

1. [Logga in interaktivt](/cli/azure/authenticate-azure-cli). Logga in interaktivt när du själv kör Azure CLI-kommandon från kommandoraden.
2. [Logga in med tjänstens huvudnamn](/cli/azure/authenticate-azure-cli). Logga in med ett huvudnamn för tjänsten när du använder Azure CLI-kommandon från ett skript eller ett program.

I den här artikeln beskrivs hur du loggar in på Azure interaktivt. Skriv [az login](/cli/azure/reference-index#az_login) på kommandoraden:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

Kommandot `az login` returnerar en token som du kan använda för autentisering, vilket visas här. Följ anvisningarna för att öppna en webbsida och skicka denna token till Azure:

![Logga in på Azure](./media/batch-cli-get-started/az-login.png)

I exemplen i avsnittet Exempel på kommandoskript visas också hur du startar Azure CLI-sessionen genom att logga in på Azure interaktivt. När du har loggat in kan du anropa kommandon för att arbeta med Batch Management-resurser, som Batch-konton, nycklar, programpaket och kvoter.  

### <a name="log-in-to-your-batch-account"></a>Logga in på Batch-kontot

Om du vill använda Azure CLI till att hantera Batch-resurser, som pooler, jobb och aktiviteter, måste du logga in på Batch-kontot och autentisera. Du loggar in på Batch-tjänsten med kommandot [az batch account login](/cli/azure/batch/account#az_batch_account_login). 

Det finns två alternativ för att autentisera mot Batch-kontot:

- **Med hjälp Azure Active Directory -autentisering (Azure AD)** 

    Autentisering med Azure AD är standard när du använder Azure CLI med Batch, och rekommenderas i de flesta situationer. 
    
    När du loggar in på Azure interaktivt enligt beskrivningen i föregående avsnitt cachelagras dina autentiseringsuppgifter så att Azure CLI kan logga in dig på Batch-kontot med samma autentiseringsuppgifter. Om du loggar in på Azure med hjälp av ett huvudnamn för tjänsten används också dessa autentiseringsuppgifter till att logga in på ditt Batch-konto.

    En fördel med Azure AD är att det erbjuder rollbaserad åtkomstkontroll i Azure (Azure RBAC). Med Azure RBAC beror en användares åtkomst på deras tilldelade roll, i stället för om de har kontonycklarna eller inte. I stället för att hantera kontonycklar kan du hantera Azure-roller och låta Azure AD hantera åtkomst och autentisering.  

     Du loggar in på Batch-kontot med hjälp av Azure AD med kommandot [az batch account login](/cli/azure/batch/account#az_batch_account_login): 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Med hjälp av autentisering med delad nyckel**

    Vid [autentisering med delad nyckel](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) används dina åtkomstnycklar för kontot till att autentisera Azure CLI-kommandon för Batch-tjänsten.

    Om du skapar Azure CLI-skript för att automatisera anrop av Batch-kommandon kan du använda antingen autentisering med delad nyckel eller ett huvudnamn för tjänsten i Azure AD. I vissa situationer kan det vara enklare att använda autentisering med delad nyckel än att skapa ett huvudnamn för tjänsten.  

    När du ska logga in med hjälp av autentisering med delad nyckel ska du ta med alternativet `--shared-key-auth` på kommandoraden:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

I exemplen i avsnittet Exempel på kommandoskript visas hur du loggar in på ditt Batch-konto med Azure CLI, både med Azure AD och med delad nyckel.

## <a name="use-azure-batch-cli-extension-commands"></a>Använda Azure Batch CLI-tilläggskommandon

Genom att installera Azure Batch CLI-tillägget kan du använda Azure CLI för att köra batchjobb från slutpunkt till slutpunkt utan att skriva kod. Med batchkommandon som stöds av tillägget kan du använda JSON-mallar för att skapa pooler, jobb och aktiviteter med Azure CLI. Du kan även använda CLI-tilläggskommandona för att ladda upp jobbindatafiler till Azure Storage-kontot som är kopplat till Batch-kontot och ladda ned jobbutdatafiler därifrån. Mer information finns i [Använda Azure Batch CLI-mallar och filöverföring](batch-cli-templates.md).

## <a name="script-examples"></a>Skriptexempel

Se [CLI-skriptexemplen](./scripts/batch-cli-sample-create-account.md) för att utföra vanliga uppgifter med Batch. De här exemplen omfattar många av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera konton, pooler, jobb och uppgifter med Batch.

## <a name="json-files-for-resource-creation"></a>JSON-filer för resursskapande

När du skapar Batch-resurser som pooler och jobb kan ange du en JSON-fil som innehåller den nya resursens konfiguration i stället för att ange dess parametrar som kommandoradsalternativ. Exempel:

```azurecli
az batch pool create my_batch_pool.json
```

Du kan skapa de flesta Batch-resurser enbart med kommandoradsalternativ, men vissa funktioner kräver att du anger en JSON-formaterad fil som innehåller information om resursen. Du måste till exempel använda en JSON-fil om du vill ange resursfiler för en startuppgift.

Du kan läsa vilken JSON-syntax som krävs för att skapa en resurs i dokumentationen [Batch REST API reference][rest_api] (REST API-referens för Batch). Varje ämne om att *lägga till resurstyper* i REST API-referensen innehåller exempel på JSON-skript för att skapa den aktuella resursen. Du kan använda dessa JSON-exempelskript som en mall för JSON-filer att använda med Azure CLI. Om du till exempel vill se JSON-syntaxen för att skapa en pool läser du [Add a pool to an account][rest_add_pool] (Lägga till en pool för ett konto).

Ett exempelskript som anger en JSON-fil finns i [Köra ett jobb och aktiviteter med Batch](./scripts/batch-cli-sample-run-job.md).

> [!NOTE]
> Om du anger en JSON-fil när du skapar en resurs ignoreras alla andra parametrar du anger på kommandoraden för den resursen.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Effektiva frågor för Batch-resurser

Varje Batch-resurstyp stöder ett `list`-kommando som frågar Batch-kontot och visar en lista över resurser av den typen. Du kan till exempel ange poolerna i ditt konto och aktiviteterna i ett jobb:

```azurecli
az batch pool list
az batch task list --job-id job001
```

När du ställer frågor mot Batch-tjänsten med en `list`-åtgärd kan du ange en OData-sats som begränsar mängden data som returneras. Eftersom all filtrering utförs på serversidan överförs endast de data du begär. Använd dessa satser för att spara bandbredd (och därmed tid) när du utför liståtgärder.

I följande tabell beskrivs de OData-satser som stöds av Batch-tjänsten:

| Sats | Description |
|---|---|
| `--select-clause [select-clause]` | Returnerar en delmängd av egenskaperna för varje entitet. |
| `--filter-clause [filter-clause]` | Returnerar endast de enheter som matchar det angivna OData-uttrycket. |
| `--expand-clause [expand-clause]` | Hämtar entitetsinformationen i ett enda underliggande REST-anrop. expand-satsen har för närvarande bara stöd för egenskapen `stats`. |

Ett exempelskript som visar hur du använder en OData-sats finns i [Köra ett jobb och aktiviteter med Batch](./scripts/batch-cli-sample-run-job.md).

Mer information om effektiva listfrågor med OData-satser finns i [Query the Azure Batch service efficiently](batch-efficient-list-queries.md) (Fråga Azure Batch-tjänsten effektivt).

## <a name="troubleshooting-tips"></a>Felsökningstips

Följande tips kan vara till hjälp när du felsöker problem med Azure CLI:

* Använd `-h` för att hämta **hjälptext** för alla CLI-kommandon
* Använd `-v` och `-vv` till att visa **utförliga** utdata från kommandon. När du tar med flaggan `-vv` visas faktiska REST-begäranden och -svar i Azure CLI. Växlarna är användbara för att visa fullständiga utdata vid fel.
* Du kan visa **-kommandoutdata som JSON-** med `--json`-alternativet. Till exempel visar `az batch pool show pool001 --json` pool001:s egenskaper i JSON-format. Du kan sedan kopiera och ändra dessa utdata och använda dem i en `--json-file` (se JSON-filer tidigare i den här artikeln).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->

## <a name="next-steps"></a>Nästa steg

* Se [Azure CLI-dokumentationen.](/cli/azure)
* Lär dig mer [om Batch-tjänstarbetsflödet och primära](batch-service-workflow-features.md) resurser som pooler, noder, jobb och aktiviteter.
* Lär dig mer om att använda Batch-mallar för att skapa pooler, jobb och uppgifter utan att skriva kod [i Använd Azure Batch CLI-mallar och filöverföring.](batch-cli-templates.md)

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: /rest/api/batchservice/
[rest_add_pool]: /rest/api/batchservice/pool/add
