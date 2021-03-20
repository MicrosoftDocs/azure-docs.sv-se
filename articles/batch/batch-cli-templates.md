---
title: Köra jobb från slut punkt till slut punkt med hjälp av mallar
description: Med endast CLI-kommandon kan du skapa en pool, ladda upp indata, skapa jobb och associerade uppgifter och hämta utdata som resulterade.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 845a32c2feda5a5a3b8d44d237c62db94cae1779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91848729"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Använd Azure Batch CLI-mallar och fil överföring

Med ett batch-tillägg till Azure CLI går det att köra batch-jobb utan att skriva kod.

Skapa och Använd filer med JSON-mallar med Azure CLI för att skapa batch-pooler, jobb och uppgifter. Använd CLI-tilläggsbegäranden för att enkelt ladda upp indatafiler till det lagrings konto som är associerat med batch-kontot och hämta utdatafiler.

> [!NOTE]
> JSON-filer har inte stöd för samma funktioner som [Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md). De är avsedda att formateras som RAW REST-brödtext. CLI-tillägget ändrar inte några befintliga kommandon, men det har ett liknande mall-alternativ som lägger till delvis Azure Resource Manager mall-funktionalitet. Se [Azure Batch CLI-tillägg för Windows, Mac och Linux](https://github.com/Azure/azure-batch-cli-extensions).

## <a name="overview"></a>Översikt

Ett tillägg till Azure CLI gör att batch kan användas från slut punkt till slut punkt av användare som inte är utvecklare. Med endast CLI-kommandon kan du skapa en pool, ladda upp indata, skapa jobb och associerade uppgifter och hämta utdata som resulterade. Ingen ytterligare kod krävs. Kör CLI-kommandona direkt eller integrera dem i skript.

Batch-mallar bygger på det befintliga batch-stödet i [Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) för JSON-filer för att ange egenskaps värden när du skapar pooler, jobb, uppgifter och andra objekt. Med batch-mallar lägger du till följande funktioner:

-   Parametrar kan definieras. När mallen används, anges bara parameter värden för att skapa objektet, med andra objekt egenskaps värden som anges i mal Lav bildbrödet. En användare som förstår batch och program som ska köras av batch kan skapa mallar, ange pool-, jobb-och aktivitets egenskaps värden. En användare som är mindre bekant med batch och/eller program behöver bara ange värden för de definierade parametrarna.

-   Jobb aktivitets fabriker skapar en eller flera uppgifter som är associerade med ett jobb, vilket gör att det inte behövs många aktivitets definitioner för att skapa och förenkla jobb sändningen avsevärt.


Jobb använder vanligt vis indatafiler för indata och skapar filer för utdata. Ett lagrings konto är associerat som standard med varje batch-konto. Överför filer till och från det här lagrings kontot med hjälp av CLI, utan kodning och inga autentiseringsuppgifter för lagring.

Till exempel är [ffmpeg](https://ffmpeg.org/) ett populärt program som bearbetar ljud-och videofiler. Här följer steg med Azure Batch CLI för att anropa ffmpeg till Omkoda-källfiler för olika lösningar.

-   Skapa en pool-mall. Användaren som skapar mallen vet hur man anropar ffmpeg-programmet och dess krav. de anger rätt operativ system, VM-storlek, hur ffmpeg installeras (från ett programpaket eller med en paket hanterare, till exempel) och andra egenskaps värden för poolen. Parametrar skapas så när mallen används måste endast pool-ID och antal virtuella datorer anges.

-   Skapa en jobbmall. Användaren som skapar mallen vet hur ffmpeg måste anropas till Omkoda-källans video till en annan upplösning och anger aktivitetens kommando rad. de vet också att det finns en mapp med källfiler för källan, med en uppgift som krävs per indatafil.

-   En slutanvändare med en uppsättning videofiler att Omkoda skapar först en pool med hjälp av pool-mallen och anger bara pool-ID och antalet virtuella datorer som krävs. De kan sedan överföra källfilerna till Omkoda. Ett jobb kan sedan skickas med hjälp av jobb mal len och anger bara pool-ID och plats för de källfiler som laddats upp. Batch-jobbet skapas med en aktivitet per indatafil som genereras. Slutligen kan de kodade utdatafilerna hämtas.

## <a name="installation"></a>Installation

Installera Azure Batch CLI-tillägget genom att först [Installera Azure cli 2,0](/cli/azure/install-azure-cli)eller köra Azure cli i [Azure Cloud Shell](../cloud-shell/overview.md).

Installera den senaste versionen av batch-tillägget med hjälp av följande Azure CLI-kommando:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Mer information om batch CLI-tillägget och ytterligare installations alternativ finns i GitHub- [lagrings platsen](https://github.com/Azure/azure-batch-cli-extensions).


Om du vill använda CLI-filtillägg måste du ha ett Azure Batch konto och, för de kommandon som överför filer till och från lagring, ett länkat lagrings konto.

Information om hur du loggar in på ett batch-konto med Azure CLI finns i [Hantera batch-resurser med Azure CLI](batch-cli-get-started.md).

## <a name="templates"></a>Mallar

Azure Batch mallar liknar Azure Resource Manager mallar, i funktionalitet och syntax. De är JSON-filer som innehåller objekt egenskaps namn och-värden, men lägger till följande huvud begrepp:

-   **Parametrar**

    -   Tillåt att egenskaps värden anges i avsnittet brödtext, med bara parameter värden som behöver anges när mallen används. Den fullständiga definitionen för en pool kan till exempel placeras i bröd texten och endast en parameter som definierats för `poolId` . det måste därför anges en sträng för pool-ID för att skapa en pool.

    -   Mal Lav bildbrödet kan redige ras av någon med kunskaper om batch och de program som ska köras av batch; endast värden för de författar definierade parametrarna måste anges när mallen används. En användare utan indjupgående batch och/eller program kunskaper kan därför använda mallarna.

-   **Variabler**

    -   Tillåt att enkla eller komplexa parameter värden anges på ett ställe och används på en eller flera platser i mal linne texten. Variabler kan förenkla och minska storleken på mallen, samt göra den mer hanterbar genom att ha en plats för att ändra egenskaper.

-   **Konstruktioner på högre nivå**

    -   Vissa konstruktioner på högre nivå är tillgängliga i mallen som ännu inte är tillgängliga i batch-API: erna. En aktivitets fabrik kan till exempel definieras i en jobbmall som skapar flera aktiviteter för jobbet med hjälp av en gemensam aktivitets definition. Dessa konstruktioner gör att du inte behöver koda för att dynamiskt skapa flera JSON-filer, till exempel en fil per aktivitet, samt skapa skriptfiler för att installera program via en paket hanterare.

    -   Nu kan dessa konstruktioner läggas till i batch-tjänsten och vara tillgängliga i batch-API: erna, UIs, osv.

### <a name="pool-templates"></a>Mallar för pool

Mallar för pooler stöder standardmallens funktioner för parametrar och variabler. De stöder även följande konstruktion på högre nivå:

-   **Paket referenser**

    -   Om du vill kan program vara kopieras till pool-noder med hjälp av paket hanterare. Paket hanteraren och paket-ID har angetts. Genom att deklarera ett eller flera paket kan du undvika att skapa ett skript som hämtar de nödvändiga paketen, installera skriptet och köra skriptet på varje nod i poolen.

Följande är ett exempel på en mall som skapar en pool med virtuella Linux-datorer med ffmpeg installerat. Om du vill använda den anger du bara en pool-ID-sträng och antalet virtuella datorer i poolen:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "taskSlotsPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Om mallfilen heter _pool-ffmpeg.jspå_, anropar du mallen enligt följande:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

CLI uppmanas att ange värden för `poolId` `nodeCount` parametrarna och. Du kan också ange parametrarna i en JSON-fil. Exempel:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Om JSON-filen för parametrar hette *pool-parameters.jspå*, så anropa sedan mallen enligt följande:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Jobbmallar

Jobbmallar stöder standardmallens funktioner för parametrar och variabler. De stöder även följande konstruktion på högre nivå:

-   **Aktivitets fabrik**

    -   Skapar flera aktiviteter för ett jobb från en aktivitets definition. Tre typer av aktivitets fabriker stöds – parameter rensning, aktivitet per fil och aktivitets insamling.

Följande är ett exempel på en mall som skapar ett jobb för att Omkoda MP4-videofiler med ffmpeg till en av två lägre upplösning. Den skapar en aktivitet per käll video fil. Se [fil grupper och fil överföring](#file-groups-and-file-transfer) för mer information om fil grupper för jobb indata och utdata.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": {
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Om mallfilen heter _job-ffmpeg.jspå_, anropar du mallen enligt följande:

```azurecli
az batch job create --template job-ffmpeg.json
```

Som tidigare uppmanas CLI att ange värden för parametrarna. Du kan också ange parametrarna i en JSON-fil.

### <a name="use-templates-in-batch-explorer"></a>Använda mallar i Batch Explorer

Du kan ladda upp en batch CLI-mall till [batch Explorer](https://github.com/Azure/BatchExplorer) Desktop-programmet (tidigare kallat BatchLabs) för att skapa en batch-pool eller ett jobb. Du kan också välja från fördefinierade pooler och jobbmallar i Batch Explorer galleriet.

Ladda upp en mall:

1. I batch Explorer väljer du **Galleri**  >  **lokala mallar**.

2. Välj eller dra och släpp en lokal pool eller jobbmall.

3. Välj **Använd den här mallen** och följ anvisningarna på skärmen.

## <a name="file-groups-and-file-transfer"></a>Fil grupper och fil överföring

De flesta jobb och uppgifter kräver indatafiler och producerar utdatafiler. Normalt överförs indatafiler och utdatafiler, antingen från klienten till noden eller från noden till klienten. Azure Batch CLI-tillägget är en sammanfattning av fil överföringen och använder det lagrings konto som du kan associera med varje batch-konto.

En fil grupp motsvarar en behållare som skapas i Azure Storage-kontot. Fil gruppen kan ha undermappar.

Batch CLI-tillägget innehåller kommandon för att ladda upp filer från klienten till en angiven fil grupp och hämta filer från den angivna fil gruppen till en klient.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Pool-och jobbmallar gör att filer som lagras i fil grupper kan anges för kopiering till pooler eller av pooler tillbaka till en fil grupp. I jobb mal len tidigare angavs till exempel fil gruppen *ffmpeg-indata* för aktivitets fabriken som platsen för de källfiler som är kopierade till noden för kodning. Fil gruppen *ffmpeg-output* är den plats där de kodade utdatafilerna kopieras från noden som kör varje aktivitet.

## <a name="summary"></a>Sammanfattning

Stöd för mall-och fil överföring har för närvarande lagts till i Azure CLI. Målet är att expandera mål gruppen som kan använda batch för användare som inte behöver utveckla kod med hjälp av batch-API: er, t. ex. forskare och IT-användare. Utan kodning kan användare med kunskaper om Azure, batch och de program som ska köras med batch skapa mallar för att skapa pooler och jobb. Med mallparametrar kan användare utan detaljerad kunskap om batch och program använda mallarna.

Prova batch-tillägget för Azure CLI och ge oss feedback eller förslag, antingen i kommentarerna för den här artikeln eller via batch-gruppen [lagrings platsen](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Nästa steg

- Detaljerad dokumentation om installation och användning, exempel och käll kod finns i [Azure GitHub-lagrings platsen](https://github.com/Azure/azure-batch-cli-extensions).

- Lär dig mer om hur du använder [batch Explorer](https://github.com/Azure/BatchExplorer) för att skapa och hantera batch-resurser.
