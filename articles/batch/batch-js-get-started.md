---
title: Använd Azure Batch klient bibliotek för Java Script
description: Lär dig grunderna i Azure Batch och bygga en enkel lösning med hjälp av Java Script.
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 1f53c793e4c676df319d46f30595ac330846dfa5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231485"
---
# <a name="get-started-with-batch-sdk-for-javascript"></a>Kom igång med batch SDK för Java Script

Lär dig grunderna för att skapa en batch-klient i Java Script med hjälp av [Azure Batch JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/). Vi tar en stegvis metod för att förstå ett scenario för ett batch-program och sedan konfigurera det med hjälp av Java Script.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har kunskaper om Java Script och hur du bekantar dig med Linux. Den förutsätter också att du har ett Azure-konto med behörighet att skapa batch- och lagringstjänster.

Vi rekommenderar att du läser [Azure Batch, teknisk översikt](batch-technical-overview.md) innan du går igenom stegen som beskrivs i den här artikeln.

## <a name="understand-the-scenario"></a>Förstå scenariot

Här har vi ett enkelt skript skrivet i python som laddar ned alla CSV-filer från en Azure Blob Storage-behållare och konverterar dem till JSON. Om du vill bearbeta flera Storage- kontocontainrar parallellt med varandra kan vi distribuera skriptet som ett Azure Batch-jobb.

## <a name="azure-batch-architecture"></a>Azure Batch arkitektur

Följande diagram illustrerar hur vi kan skala python-skriptet med Azure Batch och en-klient.

![Diagram som visar scenario arkitektur.](./media/batch-js-get-started/batch-scenario.png)

JavaScript-exemplet distribuerar ett batch-jobb med en förberedande aktivitet (förklaras i detalj senare) och en uppsättning aktiviteter beroende på antalet behållare i lagrings kontot. Du kan ladda ned skripten från GitHub-lagringsplatsen.

- [Exempelkod](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/sample.js)
- [Förberedande aktivitet – kommandoskript](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/startup_prereq.sh)
- [Processor för konvertering från CSV-format (Python) till JSON-format](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/processcsv.py)

> [!TIP]
> JavaScript-exemplet i den angivna länken innehåller ingen specifik kod som ska distribueras som en Azure Function-app. Se följande länkar för att få anvisningar om hur du skapar en sådan.
> - [Skapa funktionsappen](../azure-functions/functions-get-started.md)
> - [Skapa timerutlösare](../azure-functions/functions-bindings-timer.md)

## <a name="build-the-application"></a>Skapa programmet

Nu ska vi följa stegen i processen för att skapa JavaScript-klienten:

### <a name="step-1-install-azure-batch-sdk"></a>Steg 1: Installera Azure Batch SDK

Du kan installera Azure Batch SDK för Java Script med kommandot NPM install.

`npm install azure-batch`

Detta kommando installerar den senaste versionen av Azure-Batch JavaScript SDK.

>[!Tip]
> Om du använder en Azure-funktionsapp går du till Kudu-konsolen på Azure-funktionens inställningsflik för att köra installationskommandot npm. I det här fallet installerar du Azure Batch SDK för Java Script.

### <a name="step-2-create-an-azure-batch-account"></a>Steg 2: Skapa ett Azure Batch-konto

Du kan skapa den från [Azure Portal](batch-account-create-portal.md) eller från kommando raden ([PowerShell](batch-powershell-cmdlets-get-started.md)  / [Azure CLI](/cli/azure)).

Nedan beskrivs kommandon som kan användas för att skapa ett sådant med hjälp av Azure CLI.

Skapa en resursgrupp. Hoppa över det här steget om du redan har en på den plats där du vill skapa ett Batch-konto:

`az group create -n "<resource-group-name>" -l "<location>"`

Skapa ett Azure Batch-konto.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Varje Batch-konto har motsvarande åtkomstnycklar. Dessa nycklar behövs för att skapa fler resurser i Azure Batch-kontot. Om du arbetar med en produktionsmiljö är det en bra idé att lagra nycklarna i Azure Key Vault. Du kan sedan skapa ett huvudnamn för tjänsten för programmet. Med den här tjänstens huvudnamn kan programmet skapa en OAuth-token för att komma åt åtkomstnycklarna i Key Vault.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Kopiera och lagra nyckeln som ska användas i efterföljande steg.

### <a name="step-3-create-an-azure-batch-service-client"></a>Steg 3: Skapa en Azure Batch-tjänsteklient

Följande kodfragment importerar först Azure-batchens JavaScript-modul och skapar sedan en batch-tjänst-klient. Du måste först skapa ett SharedKeyCredentials-objekt med hjälp av den nyckel för Batch-kontot som kopierades i det föregående steget.

```javascript
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

URI:en för Azure Batch återfinns på översiktsfliken i Azure Portal. Formatet ser ut så här:

`https://accountname.location.batch.azure.com`

Se skärmbilden:

![URI för Azure Batch](./media/batch-js-get-started/batch-uri.png)

### <a name="step-4-create-an-azure-batch-pool"></a>Steg 4: Skapa en Azure Batch-pool

En Azure Batch-pool består av flera virtuella datorer (även kallade batchnoder). Azure Batch-tjänsten distribuerar aktiviteterna på noderna och hanterar dem. Följande konfigurationsparametrar kan definieras för din pool.

- Typ av virtuell datoravbildning
- Storlek på de virtuella datornoderna
- Antal virtuella datornoder

> [!TIP]
> Storlekar och antalet virtuella noder beror huvudsakligen på antalet aktiviteter som du vill köra parallellt samt själva uppgiften som ska utföras. Vi rekommenderar tester för att bäst kunna avgöra det bästa antalet och perfekta storlekar.

Följande kodfragment skapar konfigurationsparameterobjekten.

```javascript
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!TIP]
> En lista över virtuella datoravbildningar med Linux och deras SKU ID:n finns i [Lista över virtuella datoravbildningar](batch-linux-nodes.md#list-of-virtual-machine-images).

När poolkonfigurationen har definierats kan du skapa Azure Batch-poolen. Batch-poolkommandot skapar virtuella Azure-datornoder och förbereder dem för att kunna ta emot och köra aktiviteter. I alla efterföljande steg ska det finnas ett unikt referens-ID.

Använd följande kodfragment för att skapa en Azure Batch-pool.

```javascript
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

Kontrollera status för den pool som har skapats och försäkra dig om att den är i ”aktivt” tillstånd innan du går vidare med att skicka jobb till poolen i fråga.

```javascript
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Följande är ett exempel på ett resultatobjekt som returnerats av funktionen pool.get.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  taskSlotsPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```

### <a name="step-4-submit-an-azure-batch-job"></a>Steg 4: Skicka ett Azure Batch-jobb

Azure Batch-jobbet består av en logisk grupp av snarlika uppgifter. I vårt exempel är det ”Process csv to JSON” (konvertering från CSV-format till JSON-format). Varje aktivitet här kan bearbeta de CSV-filer som finns i respektive Azure Storage-container.

Dessa uppgifter körs parallellt och distribueras över flera noder, och allt detta samordnas av Azure Batch-tjänsten.

> [!TIP]
> Du kan använda egenskapen [taskSlotsPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) för att ange det maximala antalet uppgifter som kan köras samtidigt på en enda nod.

#### <a name="preparation-task"></a>Förberedande aktivitet

De VM-noder som skapas är tomma Ubuntu-noder. Oftast måste du installera en obligatorisk uppsättning program.
Om du använder Linux-noder har du normalt sett ett kommandoskript som installerar alla obligatoriska program innan de faktiska aktiviteterna körs. Det kan röra sig om vilka körbara filer som helst.

[Kommandoskriptet](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) i det här exemplet installerar Python-pip och Azure Storage SDK för Python.

Du kan ladda upp skriptet på Azure Storage-kontot och generera en SAS-URI för att komma åt skriptet. Den här processen kan också automatiseras med hjälp av Azure Storage JavaScript SDK.

> [!TIP]
> Förberedande aktiviteter för ett jobb kan endast köras på de virtuella datornoder där en viss aktivitet ska köras. Om du vill att de obligatoriska programmen ska installeras på alla noder, oavsett vilka aktiviteter som körs på dem, kan du använda egenskapen [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) när du lägger till poolen. Du kan använda följande definition för förberedande aktiviteter som referens.

En förberedande aktivitet anges vid överföring av Azure Batch-jobbet. Här följer konfigurationsparametrar för den förberedande aktiviteten:

- **ID**: En unik identifierare för den förberedande aktiviteten
- **commandLine**: Den kommandorad som exekverar den körbara filen
- **resourceFiles**: En uppsättning objekt som tillhandahåller detaljerad information om de filer som måste laddas ned innan aktiviteten kan köras.  Här visas alternativen
  - blobSource: SAS-URI för filen.
  - filePath: Lokal sökväg för nedladdning och sparande av filen.
  - fileMode: fileMode har ett oktalt format med standardvärdet 0770 (gäller endast Linux-noder).
- **waitForSuccess**: Om värdet är satt till sant går det inte att köra aktiviteten om den förberedande aktiviteten misslyckas.
- **runElevated**: Sätt värdet till sant om det krävs utökad behörighet för att få köra uppgiften.

Följande kodfragment innehåller ett exempel på skriptkonfigurering för den förberedande aktiviteten:

```javascript
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Om det inte finns några obligatoriska program att installera före aktivitetskörningen kan du hoppa över de förberedande aktiviteterna. Följande kod skapar ett jobb med visningsnamnet ”process csv files” (bearbeta CSV-filer).

 ```javascript
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```

### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>Steg 5: Skicka Azure Batch-aktiviteter för ett jobb

Nu när vi har skapat ett jobb för bearbetning av CSV-filer kan vi börja skapa aktiviteter för jobbet i fråga. Anta att vi har fyra containrar och vill skapa fyra aktiviteter – en för varje container.

Om vi tittar på [Python-skriptet](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py) så godtas två möjliga parametrar:

- container name: Den Storage-behållare som du vill ladda ned filer från
- pattern: En valfri parameter för filnamnsmönster

Anta att vi har fyra containrar – ”con1”, ”con2”, ”con3” och ”con4”. Följande kod visar hur man skickar aktiviteter till Azure Batch-jobbet ”process csv” som vi skapade tidigare.

```javascript
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

Koden lägger till flera aktiviteter i poolen. Varje aktivitet körs på en nod i poolen med virtuella datorer. Om antalet aktiviteter överskrider antalet virtuella datorer i en pool eller egenskapen taskSlotsPerNode, väntar aktiviteterna tills en nod görs tillgänglig. Denna orkestrering hanteras automatiskt av Azure Batch.

Portalen har detaljerade vyer för aktiviteter och jobbstatusar. Du kan också använda listan och hämta funktioner i Azure JavaScript SDK. Detaljerad information kan fås via [dokumentationslänken](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Se batch- [JavaScript-referens](/javascript/api/overview/azure/batch) för att utforska batch-API: et.