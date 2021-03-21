---
title: Snabb start – kör ditt första batch-jobb med Azure CLI
description: Den här snabb starten visar hur du skapar ett batch-konto och kör ett batch-jobb med Azure CLI.
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 297af47b6280381646e654eaededfe8b71a5d874
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106690"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Snabbstart: Kör ditt första Batch-jobb med Azure CLI

Kom igång med Azure Batch med hjälp av Azure CLI för att skapa ett batch-konto, en pool med datornoder (virtuella datorer) och ett jobb som kör aktiviteter i poolen. Varje exempelaktivitet kör ett grundläggande kommando på en av noderna i poolen.

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. När du har slutfört den här snabbstarten kommer du att förstå huvudbegreppen för Batch-tjänsten och vara redo att testa Batch med mer realistiska arbetsbelastningar i större skala.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här snabb starten kräver version 2.0.20 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet *QuickstartBatch-RG* på *eastus2* -platsen.

```azurecli-interactive
az group create \
    --name QuickstartBatch-rg \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Du kan länka ett Azure Storage-konto till Batch-kontot. Även om det inte krävs för den här snabbstarten, är lagringskontot användbart för att distribuera program och lagra indata och utdata för de flesta verkliga arbetsbelastningarna. Skapa ett lagringskonto i resursgruppen med kommandot [az storage account create](/cli/azure/storage/account#az-storage-account-create).

```azurecli-interactive
az storage account create \
    --resource-group QuickstartBatch-rg \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto

Skapa ett Batch-konto med kommandot [az batch account create](/cli/azure/batch/account#az-batch-account-create). Du behöver ett konto för att skapa beräkningsresurser (pooler med datornoder) och Batch-jobb.

I följande exempel skapas ett batch-konto med namnet *mybatchaccount* i *QuickstartBatch-RG* och länkar det lagrings konto som du har skapat.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group QuickstartBatch-rg \
    --location eastus2
```

Du måste autentisera med Batch för att skapa och hantera beräkningspooler och jobb. Logga in på kontot med kommandot [az batch account login](/cli/azure/batch/account#az-batch-account-login). När du har loggat in använder dina `az batch`-kommandon den här kontokontexten.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group QuickstartBatch-rg \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Skapa en pool med beräkningsnoder

Nu när du har ett Batch-konto kan du skapa en exempelpool med Linux-beräkningsnoder med hjälp av kommandot [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). I följande exempel skapas en pool med namnet *mypool* av 2 noder i storlek *Standard_A1_v2* som kör Ubuntu 16.04 LTS. Föreslagen nodstorlek erbjuder en bra balans mellan prestanda och kostnad för det här snabba exemplet.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

Batch skapar poolen omedelbart, men det tar några minuter att allokera och starta beräkningsnoderna. Under denna tid är poolen i tillståndet `resizing`. Om du vill se status för poolen kör du kommandot [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show). Det här kommandot visar alla egenskaper för poolen och du kan fråga efter specifika egenskaper. Följande kommando hämtar allokeringstillståndet för poolen:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Fortsätt med följande steg för att skapa ett jobb och aktiviteter medan pooltillståndet ändras. Poolen är redo att köra aktiviteter när allokeringstillståndet är `steady` och alla noderna körs.

## <a name="create-a-job"></a>Skapa ett jobb

Nu när du har en pool ska du skapa ett jobb att köra på den. Ett Batch-jobb är en logisk grupp för en eller flera aktiviteter. Ett jobb omfattar inställningar som är gemensamma för aktiviteter, till exempel prioritet och vilken pool som aktiviteterna ska köras på. Skapa ett Batch-jobb med hjälp av kommandot [az batch job create](/cli/azure/batch/job#az-batch-job-create). Följande exempel skapar jobbet *myjob* på poolen *mypool*. Från början har jobbet inga uppgifter.

```azurecli-interactive
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Skapa uppgifter

Använd nu kommandot [az batch task create](/cli/azure/batch/task#az-batch-task-create) för att skapa några aktiviteter att köra i jobbet. I det här exemplet skapar du fyra identiska aktiviteter. Varje aktivitet kör en `command-line` för att visa Batch-miljövariablerna på en beräkningsnod och väntar sedan i 90 sekunder. När du använder Batch är det på den här kommandoraden som anger du din app eller ditt skript. Batch tillhandahåller ett antal sätt att distribuera appar och skript till beräkningsnoder.

Följande Bash-skript skapar 4 parallella aktiviteter(*mytask1* till *mytask4*).

```azurecli-interactive
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

Kommandoutdata visar inställningarna för alla aktiviteter. Batch distribuerar aktiviteterna till beräkningsnoderna.

## <a name="view-task-status"></a>Visa status för aktivitet

När du har skapat en aktivitet köar Batch den så att den körs på poolen. Aktiviteten körs när en nod är tillgänglig att köra den.

Använd kommandot [az batch task show](/cli/azure/batch/task#az-batch-task-show) för att visa status för Batch-aktiviteterna. I följande exempel visas information om *mytask1* som körs på en av noderna i poolen.

```azurecli-interactive
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

Kommandoutdata innehåller många detaljer, men observera `exitCode` på kommandoraden och `nodeId`. En `exitCode` på 0 anger att kommandoradsaktiviteten har slutförts. `nodeId` anger ID för den poolnod som aktiviteten kördes på.

## <a name="view-task-output"></a>Visa aktivitetens utdata

Om du vill visa en lista över filer som skapas av en aktivitet på en beräkningsnod ska du använda kommandot [az batch task file list](/cli/azure/batch/task). Följande kommando visar filer som skapas av *mytask1*:

```azurecli-interactive
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

De utdata som genereras liknar följande:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Om du vill hämta en av utdatafilerna till en lokal katalog ska du använda kommandot [az batch task file download](/cli/azure/batch/task). I det här exemplet finns aktivitetens utdata i `stdout.txt`.

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

Du kan visa innehållet i `stdout.txt` i en textredigerare. Innehållet visar Azure Batch-miljövariabler som ställts in på noden. När du skapar dina egna Batch-jobb kan du referera till dessa miljövariabler i aktivitetens kommandorader och i de appar och skript som körs av kommandoraderna. Exempel:

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med Batch-självstudier och -exempel ska du använda Batch-kontot och det länkade lagringskontot som skapats i denna snabbstart. Själva Batch-kontot kostar ingenting.

Du debiteras för pooler medan noderna körs, även om inga jobb har schemalagts. När du inte längre behöver en pool tar du bort den med kommandot [az batch pool delete](/cli/azure/batch/pool#az-batch-pool-delete). När du tar bort poolen raderas alla aktivitetsutdata på noderna.

```azurecli-interactive
az batch pool delete --pool-id mypool
```

När den inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, Batch-kontot, poolerna och alla relaterade resurser. Ta bort resurserna på följande sätt:

```azurecli-interactive
az group delete --name QuickstartBatch-rg
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Batch-konto, en Batch-pool och ett Batch-jobb. Jobbet körde exempelaktiviteter och du visade utdata som skapats på en av noderna. Nu när du förstår nyckelbegreppen för Batch-tjänsten är du redo att testa Batch med mer realistiska arbetsbelastningar i större skala. Om du vill veta mer om Azure Batch ska du fortsätta med Azure Batch-självstudierna.

> [!div class="nextstepaction"]
> [Azure Batch-självstudier](./tutorial-parallel-dotnet.md)
