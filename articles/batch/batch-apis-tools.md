---
title: 'API: er och verktyg för utvecklare'
description: Läs mer om API:er och verktyg som är tillgängliga för utveckling av lösningar med Azure Batch-tjänsten.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: 502eb08631223215933b75dca882c12c02d17bd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89146410"
---
# <a name="overview-of-batch-apis-and-tools"></a>Översikt över Batch-API:er och verktyg

Bearbetning av parallella arbetsbelastningar med Azure Batch görs normalt programmatiskt med någon av Batch-API:erna. Ditt klientprogram eller -tjänst kan använda Batch-API:erna för att kommunicera med Batch-tjänsten. Med Batch-API:erna kan du skapa och hantera pooler för beräkningsnoder, antingen virtuella datorer eller molntjänster. Sedan kan du schemalägga jobb och uppgifter som ska köras på dessa noder.

Du kan effektivt bearbeta storskaliga arbetsbelastningar för din organisation eller tillhandahålla en tjänstportal för dina kunder så att de kan köra jobb och aktiviteter (på begäran eller enligt ett schema) på en eller flera hundra tusen noder. Du kan också använda Azure Batch som en del av ett större arbetsflöde, med verktyg som [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Mer information om de funktioner och arbets flöden som används i Azure Batch finns i [batch-tjänstens arbets flöde och resurser](batch-service-workflow-features.md).

## <a name="azure-accounts-for-batch-development"></a>Azure-konton för Batch-utveckling

När du utvecklar Batch-lösningar använder du följande konton i Azure-prenumerationen:

- **Batch-konto** – Azure Batch resurser, inklusive pooler, Compute-noder, jobb och aktiviteter, är associerade med ett Azure [Batch-konto](accounts.md). När ditt program gör en begäran mot batchtjänsten, autentiserar det begäran med hjälp av Azure Batch-kontonamnet, URL:en för kontot och antingen en åtkomstnyckel eller en Azure Active Directory-token. Du kan [skapa ett batch-konto](batch-account-create-portal.md) i Azure Portal eller program mässigt.
- **Storage-konto** – Batch innehåller inbyggt stöd för att arbeta med filer i [Azure Storage](../storage/index.yml). Nästan alla Batch-scenarier använder Azure Blob Storage, dels för att mellanlagra programmen som dina aktiviteter kör och de data som de bearbetar, dels för att lagra de utdata som de genererar. Varje batch-konto är vanligt vis kopplat till ett motsvarande lagrings konto.

## <a name="service-level-and-management-level-apis"></a>API: er på tjänst nivå och hanterings nivå

Azure Batch har två uppsättnings-API: er, en för Service nivån och en för hanterings nivån. Namnet är ofta snarlikt, men de returnerar olika resultat.

Endast åtgärder från hanterings-API: erna spåras i aktivitets loggen. API: er för service nivå kringgår Azure Resource Management Layer (management.azure.com) och loggas inte.

[Batch-tjänstens API för att ta bort en pool](/rest/api/batchservice/pool/delete) är till exempel direkt riktad mot batch-kontot:`DELETE {batchUrl}/pools/{poolId}`

[Batch Management-API: et för att ta bort en pool](/rest/api/batchmanagement/pool/delete) riktas mot Management.Azure.com-skiktet:`DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>API:er för Batch-tjänst

Dina program och tjänster kan skicka direkta REST-API-anrop eller använda ett eller flera av följande klientbibliotek för att köra och hantera dina Azure Batch-arbetsbelastningar.

| API | API-referens | Ladda ned | Självstudie | Kodexempel | Mer information |
| --- | --- | --- | --- | --- | --- |
| **REST för Batch** |[Azure REST API-dokument](/rest/api/batchservice/) |Ej tillämpligt |- |- | [Versioner som stöds](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **.NET för Batch** |[Azure SDK för .NET – dokument](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[Självstudie](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [Viktig information](https://aka.ms/batch-net-dataplane-changelog) |
| **Python för Batch** |[Azure SDK för python – dok.](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[Självstudie](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [Viktigt](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[Azure SDK för Java Script – dokument](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[Självstudie](batch-nodejs-get-started.md) |- | [Viktigt](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Azure SDK för Java-dokumentation](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [Viktigt](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>API:er för Batch Management

Azure Resource Manager-API:erna för Batch ger programmatisk åtkomst till Batch-konton. Med dessa API:er kan du programmässigt hantera Batch-konton, kvoter, programpaket och andra resurser i Microsoft.Batch-providern.  

| API | API-referens | Ladda ned | Självstudie | Kodexempel |
| --- | --- | --- | --- | --- |
| **Batch Management REST** |[Azure REST API-dokument](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[Azure SDK för .NET – dokument](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [Självstudie](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Batch Management Python** |[Azure SDK för python – dok.](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Batch Management Node.js** |[Azure SDK för Java Script – dokument](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Batch Management Java** |[Azure SDK för Java-dokumentation](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Batch kommandoradsverktyg

Dessa kommandoradsverktyg innehåller samma funktioner som Batch-tjänsten och API:erna för Batch Management: 

- [PowerShell-cmdlets för Batch ](/powershell/module/az.batch/): Du kan använda Azure Batch-cmdlets i [Azure PowerShell](/powershell/azure/)-modulen om du vill hantera Batch-resurser med PowerShell.
- [Azure CLI](/cli/azure): Azure CLI är en plattformsoberoende verktygsuppsättning som tillhandahåller shell-kommandon för att interagera med många Azure-tjänster, inklusive Batch-tjänsten och Batchhanteringstjänsten. Mer information om hur du använder Azure CLI med Batch finns i [Hantera Batch-resurser med Azure CLI](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Andra verktyg för programutveckling

Dessa ytterligare verktyg kan vara till hjälp för att skapa och felsöka batch-program och-tjänster.

- [Azure Portal](https://portal.azure.com/): Du kan skapa, övervaka och ta bort Batch-pooler, jobb och uppgifter i Azure Portal. Du kan visa statusinformation för dessa och andra resurser när du kör jobben, och till och med hämta filer från datornoderna i dina pooler. Du kan till exempel ladda ned en misslyckad uppgifts `stderr.txt` vid felsökning. Du kan också hämta fjärrskrivbordsfiler (RDP) som du kan använda för att logga in till beräkningsnoder.
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): Batch Explorer (hette förut BatchLabs) är ett kostnadsfritt, fristående klientverktyg med omfattande funktioner för att skapa, felsöka och övervaka Azure Batch-program. Hämta ett [installationspaketet](https://azure.github.io/BatchExplorer/) för Mac, Linux eller Windows.
- [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard): batch-Shipyard är ett verktyg som hjälper dig att etablera, köra och övervaka behållar baserad BATCHBEARBETNING och HPC-arbetsbelastningar på Azure Batch.
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/): trots att det inte är ett Azure Batch verktyg är Storage Explorer ett annat värdefullt verktyg som du kan använda när du utvecklar och felsöker dina batch-lösningar.

## <a name="additional-resources"></a>Ytterligare resurser

- Information om loggning av händelser från batch-programmet finns i [batch-mått, aviseringar och loggar för diagnostisk utvärdering och övervakning](batch-diagnostics.md).
- Referens information om händelser som aktive ras av batch-tjänsten finns i [batch Analytics](batch-analytics.md).
- Information om miljövariabler för Compute-noder finns i [Azure Batch körnings miljövariabler](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- [Komma igång med Azure Batch-biblioteket för .NET](tutorial-parallel-dotnet.md) innehåller information om hur du använder C# och Batch .NET-biblioteket för att köra en enkel arbetsbelastning med ett vanligt Batch-arbetsflöde. En [Python-version](tutorial-parallel-python.md) och en [Node.js-självstudie](batch-nodejs-get-started.md) finns också tillgängliga.
- Hämta [kodexemplet på GitHub](https://github.com/Azure-Samples/azure-batch-samples) om du vill se hur både C# och Python kan användas med Batch för att schemalägga och bearbeta exempelarbetsbelastningar.
