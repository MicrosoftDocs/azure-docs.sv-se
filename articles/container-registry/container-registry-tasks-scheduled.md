---
title: Självstudie – Schemalägga en ACR-uppgift
description: I den här självstudien lär du dig att köra en Azure Container Registry uppgift enligt ett definierat schema genom att ange en eller flera timerutlösare
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: fa80bcbd318266a86c5bec08c9ee60fc0d22a10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780883"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Självstudie: Köra en ACR-uppgift enligt ett definierat schema

Den här självstudien visar hur du kör [en ACR-uppgift](container-registry-tasks-overview.md) enligt ett schema. Schemalägg en aktivitet genom att konfigurera en eller flera *timerutlösare*. Timerutlösare kan användas fristående eller i kombination med andra uppgiftsutlösare.

I den här självstudien lär du dig mer om schemaläggning av aktiviteter och:

> [!div class="checklist"]
> * Skapa en uppgift med en timerutlösare
> * Hantera timerutlösare

Schemaläggning av en uppgift är användbart för scenarier som följande:

* Kör en containerarbetsbelastning för schemalagda underhållsåtgärder. Du kan till exempel köra en containerapp för att ta bort avbildningar som inte behöver tas bort från registret.
* Kör en uppsättning tester på en produktionsavbildning under arbetsdagen som en del av din live-platsövervakning.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Om schemaläggning av en uppgift

* **Utlösare med cron-uttryck** – Timerutlösaren för en uppgift använder ett *cron-uttryck*. Uttrycket är en sträng med fem fält som anger minut, timme, dag, månad och veckodag för att utlösa uppgiften. Frekvenser på upp till en gång per minut stöds.

  Uttrycket utlöser till exempel `"0 12 * * Mon-Fri"` en uppgift vid 12.00 UTC på varje veckodag. Mer [information finns](#cron-expressions) senare i den här artikeln.
* **Flera timerutlösare** – Det är tillåtet att lägga till flera timers i en uppgift, så länge schemana skiljer sig åt.
    * Ange flera timerutlösare när du skapar uppgiften eller lägg till dem senare.
    * Du kan också namnge utlösarna för enklare hantering eller ACR-uppgifter att ange standardutlösarnamn.
    * Om timerscheman överlappar i taget ACR-uppgifter aktiviteten vid den schemalagda tiden för varje timer.
* **Andra uppgiftsutlösare** – I en timerutlöst aktivitet kan du också aktivera utlösare baserat på källkodsin commit (källkodsin commit) [eller](container-registry-tutorial-build-task.md) [basavbildningsuppdateringar](container-registry-tutorial-base-image-update.md). Precis som med andra ACR-uppgifter kan du [även köra en][az-acr-task-run] schemalagd aktivitet manuellt.

## <a name="create-a-task-with-a-timer-trigger"></a>Skapa en uppgift med en timerutlösare

### <a name="task-command"></a>Aktivitetskommando

Fyll först i följande gränssnittsmiljövariabel med ett värde som är lämpligt för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller i miljövariabeln måste du manuellt ersätta varje värde oavsett var det visas i exempelkommandona.

[![Inbäddad start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

När du skapar en uppgift med kommandot [az acr task create][az-acr-task-create] kan du även lägga till en timerutlösare. Lägg till `--schedule` parametern och skicka ett cron-uttryck för timern.

Som ett enkelt exempel utlöser följande uppgift körning av `hello-world` avbildningen Microsoft Container Registry varje dag kl. 21:00 UTC. Aktiviteten körs utan källkodskontext.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Kör kommandot [az acr task show för][az-acr-task-show] att se att timerutlösaren har konfigurerats. Uppdateringsutlösaren för basavbildningen är också aktiverad som standard.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Utlösa uppgiften

Utlösningsuppgiften manuellt [med az acr task run][az-acr-task-run] för att säkerställa att den är korrekt konfigurerad:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Om containern körs korrekt liknar utdata följande. Utdata komprimeras för att visa viktiga steg

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Efter den schemalagda tiden kör du [kommandot az acr task list-runs][az-acr-task-list-runs] för att kontrollera att timern utlöste uppgiften som förväntat:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

När timern lyckas ser utdata ut ungefär så här:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Hantera timerutlösare

Använd kommandona [az acr task timer][az-acr-task-timer] för att hantera timerutlösare för en ACR-uppgift.

### <a name="add-or-update-a-timer-trigger"></a>Lägga till eller uppdatera en timerutlösare

När en uppgift har skapats kan du lägga till en timerutlösare med hjälp av [kommandot az acr task timer add.][az-acr-task-timer-add] I följande exempel läggs ett timerutlösarnamn *timer2 till* *timertask som* skapades tidigare. Den här timern utlöser uppgiften varje dag kl. 10:30 UTC.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Uppdatera schemat för en befintlig utlösare eller ändra dess status med hjälp av [kommandot az acr task timer update.][az-acr-task-timer-update] Uppdatera till exempel utlösaren med namnet *timer2* för att utlösa uppgiften kl. 11:30 UTC:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Lista timerutlösare

Kommandot [az acr task timer list][az-acr-task-timer-list] visar de timerutlösare som har ställts in för en aktivitet:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
```

Exempel på utdata:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Ta bort en timerutlösare

Använd kommandot [az acr task timer remove för][az-acr-task-timer-remove] att ta bort en timerutlösare från en uppgift. I följande exempel tas *timer2-utlösaren* bort *från timertask*:

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-uttryck

ACR-uppgifter använder [NCronTab-biblioteket för](https://github.com/atifaziz/NCrontab) att tolka Cron-uttryck. Uttryck som stöds ACR-uppgifter fem obligatoriska fält avgränsade med blanksteg:

`{minute} {hour} {day} {month} {day-of-week}`

Tidszonen som används med Cron-uttrycken är Coordinated Universal Time (UTC). Timmarna är i 24-timmarsformat.

> [!NOTE]
> ACR-uppgifter stöder inte fältet `{second}` eller `{year}` i Cron-uttryck. Om du kopierar ett Cron-uttryck som används i ett annat system måste du ta bort fälten om de används.

Varje fält kan ha någon av följande typer av värden:

|Typ  |Exempel  |När den utlöses  |
|---------|---------|---------|
|Ett specifikt värde |<nobr>`"5 * * * *"`</nobr>|varje timme vid 5 minuter efter timmen|
|Alla värden ( `*` )|<nobr>`"* 5 * * *"`</nobr>|varje minut i timmen med början 5:00 UTC (60 gånger per dag)|
|Ett intervall ( `-` operator)|<nobr>`"0 1-3 * * *"`</nobr>|3 gånger per dag, 1:00, 2:00 och 3:00 UTC|
|En uppsättning värden ( `,` operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3 gånger per timme, 20 minuter, 30 minuter och 40 minuter över timmen|
|Ett intervallvärde ( `/` operator)|<nobr>`"*/10 * * * *"`</nobr>|6 gånger per timme, 10 minuter, 20 minuter och så vidare, senaste timmen

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron-exempel

|Exempel|När den utlöses  |
|---------|---------|
|`"*/5 * * * *"`|var femte minut|
|`"0 * * * *"`|en gång överst i varje timme|
|`"0 */2 * * *"`|en gång varannan timme|
|`"0 9-17 * * *"`|en gång i timmen från 9:00 till 17:00 UTC|
|`"30 9 * * *"`|kl. 9:30 UTC varje dag|
|`"30 9 * * 1-5"`|kl. 9:30 UTC varje veckodag|
|`"30 9 * Jan Mon"`|kl. 9:30 UTC varje måndag i januari|

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser som du har skapat i den här självstudieserien, inklusive containerregistret eller registren, containerinstansen, nyckelvalvet och tjänstens huvudnamn, utfärdar du följande kommandon:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur Azure Container Registry skapa aktiviteter som utlöses automatiskt av en timer. 

Ett exempel på hur du använder en schemalagd aktivitet för att rensa lagringsplatsen i ett register finns i Rensa avbildningar [automatiskt från ett Azure-containerregister.](container-registry-auto-purge.md)

Exempel på uppgifter som utlöses av källkods- eller basavbildningsuppdateringar finns i andra artiklar i [ACR-uppgifter självstudieserien](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az_acr_task_timer_add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az_acr_task_timer_remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az_acr_task_timer_list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az_acr_task_timer_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
