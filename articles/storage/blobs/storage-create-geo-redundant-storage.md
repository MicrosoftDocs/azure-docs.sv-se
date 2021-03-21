---
title: Självstudie – Bygg ett program med hög tillgänglighet med Blob Storage
titleSuffix: Azure Storage
description: Använd Read-Access geo-Zone-redundant (RA-GZRS) lagring för att göra dina program data hög tillgängliga.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc, devx-track-python, devx-track-js, devx-track-csharp
ms.subservice: blobs
ms.openlocfilehash: 0d597f0742cfc43f1c7fb38568b2a2bbda352beb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049346"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Självstudie: Bygg ett program med hög tillgänglighet med Blob Storage

Den här självstudien ingår i en serie. I den lär du dig att ge programdata hög tillgänglighet i Azure.

När du har slutfört den här självstudien har du ett konsol program som laddar upp och hämtar en BLOB från ett [Read-Access geo-Zone-redundant](../common/storage-redundancy.md) (ra-GZRS) lagrings konto.

GEO-redundans i Azure Storage replikerar transaktioner asynkront från en primär region till en sekundär region som är hundratals mil bort. Replikeringsprocessen garanterar att data i den sekundära regionen blir konsekventa. Konsol programmet använder [krets brytar](/azure/architecture/patterns/circuit-breaker) mönstret för att avgöra vilken slut punkt som ska användas för att ansluta till, vilket automatiskt växlar mellan slut punkter som felen och återställningar simuleras.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Skapa ett lagringskonto
> * Ange anslutningssträngen
> * Kör konsolprogrammet

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

* Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med arbets belastningen **Azure Development** .

  ![Azure-utveckling (under Web & Cloud (Webb och moln))](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python-v12"></a>[Python-V12](#tab/python)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

* Installera [python](https://www.python.org/downloads/)
* Ladda ned och installera [Azure Storage SDK för Python](https://github.com/Azure/azure-storage-python)

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

* Installera [Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Ett lagringskonto tillhandahåller en unik namnrymd där du kan lagra och få åtkomst till dina Azure-lagringdataobjekt.

Följ de här stegen om du vill skapa ett lagrings konto med Read-Access geo-Zone-redundant (RA-GZRS):

1. Välj knappen **skapa en resurs** i Azure Portal.
2. Välj **lagrings konto – BLOB, fil, tabell, kö** på den **nya** sidan.
4. Fyll i formuläret för lagringskontot med följande information (se bilden nedan) och välj **Skapa**:

   | Inställning       | Exempelvärde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Prenumeration** | *Min prenumeration* | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.azure.com/Subscriptions). |
   | **ResourceGroup** | *myResourceGroup* | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
   | **Namn** | *mystorageaccount* | Ett unikt namn för ditt lagrings konto. |
   | **Plats** | *East US* | Välj en plats. |
   | **Prestanda** | *Standard* | Standard prestanda är ett lämpligt alternativ för exempel scenariot. |
   | **Typ av konto** | *StorageV2* | Vi rekommenderar att du använder ett lagrings konto för generell användning v2. Mer information om typer av Azure Storage-konton finns i [Översikt över lagrings konto](../common/storage-account-overview.md). |
   | **Replikering**| *Skrivskyddad geozonredundant lagring (RA-GZRS)* | Den primära regionen är zon-redundant och replikeras till en sekundär region, med Läs behörighet till den sekundära regionen aktive rad. |
   | **Åtkomstnivå**| *Frekvent* | Använd frekvent nivå för data som används ofta. |

    ![skapa lagringskonto](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Ladda ned exemplet

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

[Ladda ned exempelprojektet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) och extrahera (packa upp) filen storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Du kan också använda [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. Exempelprojektet innehåller ett konsolprogram.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

[Ladda ned exempelprojektet](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) och extrahera (packa upp) filen storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Du kan också använda [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. Exempelprojektet innehåller ett grundläggande Python-program.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

[Hämta exempelprojektet](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) och packa upp filen. Du kan också använda [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. Exempelprojektet innehåller ett grundläggande Node.js-program.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Konfigurera exemplet

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Du kan lagra den här anslutningssträngen inom en miljövariabel på den lokala dator där programmet körs. Följ något av exemplen nedan beroende på operativsystemet för att skapa miljövariabeln.

Navigera till ditt lagringskonto i Azure Portal. Välj **Åtkomstnycklar** under **Inställningar** i ditt lagringskonto. Kopiera **anslutningssträngen** från den primära eller sekundära nyckeln. Kör något av följande kommandon baserade på operativ systemet och Ersätt \<yourconnectionstring\> med den faktiska anslutnings strängen. Kommandot sparar en miljövariabel till den lokala datorn. I Windows är miljövariabeln inte tillgänglig förrän du har läst in **kommandotolken** eller gränssnittet på nytt.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

I programmet måste du ange autentiseringsuppgifterna för ditt lagrings konto. Du kan lagra informationen i miljövariabler på den lokala datorn som kör programmet. Följ något av exemplen nedan, beroende på vilket operativ system som används för att skapa miljövariablerna.

Navigera till ditt lagringskonto i Azure Portal. Välj **Åtkomstnycklar** under **Inställningar** i ditt lagringskonto. Klistra in **lagrings kontots namn** och **nyckel** värden i följande kommandon och Ersätt \<youraccountname\> \<youraccountkey\> plats hållarna och. Det här kommandot sparar miljövariablerna på den lokala datorn. I Windows är miljövariabeln inte tillgänglig förrän du har läst in **kommandotolken** eller gränssnittet på nytt.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

Om du vill köra det här exemplet måste du lägga till dina autentiseringsuppgifter för ditt lagrings konto i `.env.example` filen och sedan byta namn på den till `.env` .

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Du kan hitta den här informationen i Azure Portal genom att gå till ditt lagrings konto och välja **åtkomst nycklar** i avsnittet **Inställningar** .

Installera de nödvändiga beroendena. Det gör du genom att öppna en kommando tolk, navigera till mappen exempel och sedan ange `npm install` .

---

## <a name="run-the-console-application"></a>Kör konsolprogrammet

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

I Visual Studio trycker du på **F5** eller väljer **Start** för att starta felsökning av programmet. Visual Studio återställer automatiskt NuGet-paket som saknas (om konfigurerat). Gå till avsnittet om hur du [installerar och ominstallerar paket med paketåterställning](/nuget/consume-packages/package-restore#package-restore-overview) om du vill veta mer.

Ett konsolfönster öppnas och programmet körs. Programmet överför bilden **HelloWorld.png** från lösningen till lagringskontot. Programmet kontrollerar att avbildningen har repliker ATS till den sekundära RA-GZRS-slutpunkten. Sedan börjar programmet ladda ned bilden upp till 999 gånger. Varje läsning representeras av en **P** eller en **s**. Där **P** representerar den primära slut punkten och **S** representerar den sekundära slut punkten.

![Konsolprogrammet körs](media/storage-create-geo-redundant-storage/figure3.png)

I exempelkoden används aktiviteten `RunCircuitBreakerAsync` i filen `Program.cs` för att ladda ned en bild från lagringskontot med hjälp av metoden [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). Innan nedladdningen definieras en [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) . Åtgärdskontexten definierar de händelsehanterare som utlöses när en nedladdning slutförs eller om en nedladdning misslyckas och ett nytt försök görs.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

Om du vill köra programmet i en terminal eller kommandotolk går du till katalogen **circuitbreaker.py** och skriver `python circuitbreaker.py`. Programmet överför bilden **HelloWorld.png** från lösningen till lagringskontot. Programmet kontrollerar att avbildningen har repliker ATS till den sekundära RA-GZRS-slutpunkten. Sedan börjar programmet ladda ned bilden upp till 999 gånger. Varje läsning representeras av en **P** eller en **s**. Där **P** representerar den primära slut punkten och **S** representerar den sekundära slut punkten.

![Konsolprogrammet körs](media/storage-create-geo-redundant-storage/figure3.png)

I exempelkoden används metoden `run_circuit_breaker` i filen `circuitbreaker.py` för att ladda ned en bild från lagringskontot med hjälp av metoden [get_blob_to_path](/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-).

Återförsöksfunktionen för lagringsobjektet har angetts till en linjär återförsöksprincip. Återförsöksfunktionen bestämmer om det ska göras ett nytt försök med begäran och anger antalet sekunder som ska förflyta innan det görs ett nytt försök. Ange true för värdet **retry\_to\_secondary** om det ska göras ett nytt försök till den sekundära slutpunkten om den första begäran till den primära slutpunkten misslyckas. I exempelprogrammet definieras en anpassad återförsöksprincip i funktionen `retry_callback` för lagringsobjektet.

Innan nedladdningen definieras tjänst objekt [retry_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) och [response_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) -funktionen. Funktionerna definierar de händelsehanterare som utlöses när en nedladdning slutförs eller om en nedladdning misslyckas och ett nytt försök görs.

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

Kör exemplet genom att öppna en kommando tolk, navigera till mappen exempel och sedan ange `node index.js` .

Exemplet skapar en behållare i ditt Blob Storage-konto, laddar upp **HelloWorld.png** i behållaren och kontrollerar sedan upprepade gånger om behållaren och avbildningen har repliker ATS till den sekundära regionen. Efter replikeringen tillfrågas du om du vill ange **D** eller **Q** (följt av retur) för att ladda ned eller avsluta. Resultatet bör se ut ungefär så här:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

### <a name="retry-event-handler"></a>Händelsehanterare för nytt försök

Händelsehanteraren `OperationContextRetrying` anropas när nedladdningen av bilden misslyckas och den är inställd på att göra nya försök. Om det maximala antalet återförsök som har definierats i programmet nås ändras [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) för begäran till `SecondaryOnly`. Den här inställningen tvingar programmet att försöka ladda ned bilden från den sekundära slutpunkten. Med den här konfigurationen tar det kortare tid att begära bilden eftersom det inte görs oändliga försök att hämta den från den primära slutpunkten.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Händelsehanterare för slutförd begäran

Händelsehanteraren `OperationContextRequestCompleted` anropas när nedladdningen av bilden slutförs. Om programmet använder den sekundära slutpunkten fortsätter programmet att använda den här slutpunkten upp till 20 gånger. Efter 20 gånger återställer programmet [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) till `PrimaryThenSecondary` och försöker på nytt med den primära slutpunkten. Om en begäran lyckas fortsätter programmet att läsa från den primära slutpunkten.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

### <a name="retry-event-handler"></a>Händelsehanterare för nytt försök

Händelsehanteraren `retry_callback` anropas när nedladdningen av bilden misslyckas och den är inställd på att göra nya försök. Om det maximala antalet återförsök som har definierats i programmet nås ändras [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) för begäran till `SECONDARY`. Den här inställningen tvingar programmet att försöka ladda ned bilden från den sekundära slutpunkten. Med den här konfigurationen tar det kortare tid att begära bilden eftersom det inte görs oändliga försök att hämta den från den primära slutpunkten.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Händelsehanterare för slutförd begäran

Händelsehanteraren `response_callback` anropas när nedladdningen av bilden slutförs. Om programmet använder den sekundära slutpunkten fortsätter programmet att använda den här slutpunkten upp till 20 gånger. Efter 20 gånger återställer programmet [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) till `PRIMARY` och försöker på nytt med den primära slutpunkten. Om en begäran lyckas fortsätter programmet att läsa från den primära slutpunkten.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

Med Node.js v10 SDK är återanrops hanterare onödig. I stället skapar exemplet en pipeline som kon figurer ATS med alternativ för återförsök och en sekundär slut punkt. Detta gör att programmet automatiskt växlar till den sekundära pipelinen om det inte går att komma åt dina data via den primära pipelinen.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Nästa steg

I del ett av serien lärde du dig att göra ett program högt tillgängligt med RA-GZRS lagrings konton.

Gå vidare till del två i serien och lär dig hur du simulerar ett fel och tvingar ditt program att använda den sekundära RA-GZRS-slutpunkten.

> [!div class="nextstepaction"]
> [Simulera ett problem med att läsa från den primära regionen](simulate-primary-region-failure.md)