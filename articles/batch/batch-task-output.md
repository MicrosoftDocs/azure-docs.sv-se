---
title: Spara jobb-och uppgifts utdata i ett data lager
description: Lär dig mer om olika alternativ för att spara utdata från batch-uppgifter och-jobb. Du kan spara data till Azure Storage eller till ett annat data lager.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ebe0b6d57225eff9f3f1251d5e491c95e9b7ffc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85965118"
---
# <a name="persist-job-and-task-output"></a>Bevara jobb- och uppgiftsutdata

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Några vanliga exempel på Uppgiftsutdata är:

- Filer som skapas när aktiviteten bearbetar indata.
- Loggfiler som är associerade med uppgifts körning.

I den här artikeln beskrivs olika alternativ för att spara Uppgiftsutdata.

## <a name="options-for-persisting-output"></a>Alternativ för att spara utdata

Beroende på ditt scenario finns det några olika metoder som du kan vidta för att spara Uppgiftsutdata:

- [Använd batch-tjänstens API](batch-task-output-files.md).  
- [Använd biblioteket med kommando fils konventioner för .net](batch-task-output-file-conventions.md).  
- Implementera standard konventionerna för batch-filen i ditt program.
- Implementera en anpassad lösning för fil förflyttning.

I följande avsnitt beskrivs varje metod och allmänna design överväganden för att spara utdata.

### <a name="use-the-batch-service-api"></a>Använda batch-tjänstens API

Batch-tjänsten har stöd för att ange utdatafiler i Azure Storage för uppgifts data när du [lägger till en aktivitet i ett jobb](/rest/api/batchservice/add-a-task-to-a-job) eller [lägger till en samling aktiviteter i ett jobb](/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Mer information om att spara Uppgiftsutdata med batch-tjänstens API finns i [Spara uppgifts data till Azure Storage med batch-tjänstens API](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Använd biblioteket med kommando fils konventioner för .NET

Batch definierar en valfri uppsättning konventioner för namngivning av Uppgiftsutdata i Azure Storage. Standard konventionerna för [batch-filen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) beskriver dessa konventioner. Standard för fil konventioner bestämmer namnen på mål behållaren och blob-sökvägen i Azure Storage för en specifik utdatafil baserat på namnet på jobbet och uppgiften.

Det är upp till dig om du bestämmer dig för att använda fil konventions standarden för att namnge dina utdatafiler. Du kan också namnge mål behållaren och bloben som du vill. Om du använder fil konventions standarden för namngivning av utdatafiler är utdatafilerna tillgängliga för visning i [Azure Portal][portal].

Utvecklare som skapar batch-lösningar med C# och .NET kan använda [fil konventions biblioteket för .net][nuget_package] för att spara uppgifts data till ett Azure Storage-konto, enligt [satserna i satsen för fil konventioner som standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions). Biblioteket fil konventioner hanterar flyttning av utdatafiler till Azure Storage och namngivning av mål behållare och blobbar på ett välkänt sätt.

Mer information om att spara Uppgiftsutdata med fil konventions biblioteket för .NET finns i [Spara jobb-och uppgifts data till Azure Storage med bibliotek för batch-Filkonventioner för .net](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementera standard konventionerna för batch-filen

Om du använder ett annat språk än .NET kan du implementera [standard konventionerna för batch-filen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) i ditt eget program.

Du kanske vill implementera namngivnings standarden för fil konventioner själv när du vill ha ett beskrivande namn schema, eller när du vill visa Uppgiftsutdata i Azure Portal.

### <a name="implement-a-custom-file-movement-solution"></a>Implementera en lösning för anpassad fil förflyttning

Du kan också implementera din egen fullständiga lösning för fil förflyttning. Använd den här metoden när:

- Du vill spara uppgifts data till ett annat data lager än Azure Storage. Om du vill ladda upp filer till ett data lager som Azure SQL eller Azure DataLake kan du skapa ett anpassat skript eller en körbar fil som du kan överföra till den platsen. Sedan kan du anropa det på kommando raden när du har kört den primära körbara filen. På en Windows-nod kan du till exempel anropa följande två kommandon: `doMyWork.exe && uploadMyFilesToSql.exe`
- Du vill utföra en kontroll punkt eller tidig uppladdning av de första resultaten.
- Du vill ha detaljerad kontroll över fel hanteringen. Du kanske till exempel vill implementera din egen lösning om du vill använda aktivitets beroende åtgärder för att utföra vissa uppladdnings åtgärder baserat på specifika slut koder för aktiviteter. Mer information om aktivitets beroende åtgärder finns i [skapa aktivitets beroenden för att köra uppgifter som är beroende av andra uppgifter](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Design överväganden för att spara utdata

När du designar din batch-lösning bör du tänka på följande faktorer som rör jobb-och Uppgiftsutdata.

- **Livs längd för Compute-noden**: Compute-noder är ofta tillfälliga, särskilt i autoskalning-aktiverade pooler. Utdata från en aktivitet som körs på en nod är bara tillgänglig när noden finns och endast inom den kvarhållna filloggning som du har angett för aktiviteten. Om en aktivitet producerar utdata som kan behövas när uppgiften har slutförts, måste aktiviteten Ladda upp utdatafilerna till ett varaktigt lager, till exempel Azure Storage.

- **Utgående lagring**: Azure Storage rekommenderas som ett data lager för Uppgiftsutdata, men du kan använda alla varaktiga lagrings enheter. Skrivning av Uppgiftsutdata till Azure Storage integreras i batch-tjänstens API. Om du använder en annan typ av varaktig lagring måste du skriva program logiken för att spara Uppgiftsutdata.

- **Hämtning av utdata**: du kan hämta Uppgiftsutdata direkt från Compute-noderna i poolen, eller från Azure Storage eller ett annat data lager om du har sparat Uppgiftsutdata. Om du vill hämta en aktivitets utdata direkt från en Compute-nod behöver du fil namnet och dess utmatnings plats på noden. Om du sparar Uppgiftsutdata till Azure Storage måste du använda den fullständiga sökvägen till filen i Azure Storage för att ladda ned utdatafilerna med Azure Storage SDK.

- **Visa utdata**: när du navigerar till en batch-aktivitet i Azure Portal och väljer **filer på noden** visas alla filer som är associerade med uppgiften, inte bara de utdatafiler som du är intresse rad av. Filerna på Compute-noder är bara tillgängliga medan noden finns och endast inom den kvarhållna fil lagrings tid som du har angett för aktiviteten. Om du vill visa Uppgiftsutdata som du har sparat för att Azure Storage kan du använda Azure Portal eller ett Azure Storage-klient program som [Azure Storage Explorer][storage_explorer]. Om du vill visa utdata i Azure Storage med portalen eller något annat verktyg måste du känna till filens plats och navigera till den direkt.

## <a name="next-steps"></a>Nästa steg

- Utforska genom att använda de nya funktionerna i batch-tjänstens API för att spara uppgifts data i [Spara uppgifts data till Azure Storage med batch-tjänstens API](batch-task-output-files.md).
- Lär dig mer om att använda bibliotek för batch-filkonventioner för .NET i [beständiga jobb-och uppgifts data till Azure Storage med bibliotek för batch-fil konventioner för .net](batch-task-output-file-conventions.md).
- Se [PersistOutputs][github_persistoutputs] -exempelprojektet på GitHub, som visar hur du använder både batch-klientprogrammet för .net och fil konventions biblioteket för .net för att spara Uppgiftsutdata till varaktig lagring.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
