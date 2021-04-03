---
title: Självstudie – Schemalägg en ACR-uppgift
description: I den här självstudien får du lära dig hur du kör en Azure Container Registry aktivitet enligt ett definierat schema genom att ange en eller flera timer-utlösare
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 13a4ccac4ea97538583c1c063a6dc61e4d25686a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96030619"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Självstudie: köra en ACR-aktivitet enligt ett definierat schema

Den här självstudien visar hur du kör en [ACR-aktivitet](container-registry-tasks-overview.md) enligt ett schema. Schemalägg en aktivitet genom att ställa in en eller flera *timer-utlösare*. Timer-utlösare kan användas separat eller i kombination med andra aktivitets utlösare.

I den här självstudien får du lära dig om schemaläggning av aktiviteter och:

> [!div class="checklist"]
> * Skapa en uppgift med en timer-utlösare
> * Hantera timer-utlösare

Att schemalägga en aktivitet är användbart för scenarier som följande:

* Kör arbets belastningen container för schemalagda underhålls åtgärder. Du kan till exempel köra en container app för att ta bort onödiga avbildningar från registret.
* Kör en uppsättning tester på en produktions avbildning under arbets dagen som en del av din direktsända webbplats övervakning.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Om schemaläggning av en aktivitet

* **Utlösare med cron-uttryck** – timer-utlösaren för en uppgift använder ett *cron-uttryck*. Uttrycket är en sträng med fem fält som anger minut, timme, dag, månad och veckodag för att utlösa uppgiften. Det finns stöd för frekvens upp till en gång per minut.

  Uttrycket utlöser till exempel `"0 12 * * Mon-Fri"` en aktivitet kl. 12.00 UTC på varje veckodag. Se [informationen](#cron-expressions) längre fram i den här artikeln.
* **Flera timer-utlösare** – det går att lägga till flera timers till en aktivitet, så länge scheman skiljer sig åt.
    * Ange flera timer-utlösare när du skapar uppgiften eller Lägg till dem senare.
    * Du kan också namnge utlösare för enklare hantering, eller så kommer ACR-aktiviteter att tillhandahålla standard namn för utlösare.
    * Om timer-scheman överlappar i taget, utlöser ACR-aktiviteter aktiviteten vid den schemalagda tiden för varje timer.
* **Andra aktivitets utlösare** – i en timer-utlöst uppgift kan du också aktivera utlösare baserat på [käll kods bekräftelse](container-registry-tutorial-build-task.md) eller [bas avbildnings uppdateringar](container-registry-tutorial-base-image-update.md). Precis som andra ACR-uppgifter kan du också köra en schemalagd aktivitet [manuellt][az-acr-task-run] .

## <a name="create-a-task-with-a-timer-trigger"></a>Skapa en uppgift med en timer-utlösare

### <a name="task-command"></a>Uppgifts kommando

Fyll först i följande gränssnitts miljö variabel med ett lämpligt värde för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller i miljövariabeln måste du manuellt ersätta varje värde där det visas i exempel kommandona.

[![Inbäddad start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

När du skapar en uppgift med kommandot [AZ ACR Task Create][az-acr-task-create] kan du också lägga till en timer-utlösare. Lägg till `--schedule` parametern och skicka ett cron-uttryck för timern.

Som ett enkelt exempel utlöses följande aktivitet som kör `hello-world` avbildningen från Microsoft container Registry varje dag kl. 21:00 UTC. Aktiviteten körs utan en käll kods kontext.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Kör kommandot [AZ ACR Task show][az-acr-task-show] för att se att timer-utlösaren har kon figurer ATS. Som standard aktive ras även uppdaterings utlösaren för bas avbildning.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Utlös aktiviteten

Utlös aktiviteten manuellt med [AZ ACR Task-körning][az-acr-task-run] för att kontrol lera att den är korrekt konfigurerad:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Om behållaren körs korrekt, ser utdata ut ungefär så här. Utdata komprimeras för att Visa viktiga steg

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

Efter den schemalagda tiden kör du kommandot [AZ ACR Task List-runs (kör][az-acr-task-list-runs] kommando) för att kontrol lera att timern utlöste uppgiften som förväntat:

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

## <a name="manage-timer-triggers"></a>Hantera timer-utlösare

Använd kommandona [AZ ACR Task timer][az-acr-task-timer] för att hantera timer-utlösare för en ACR-aktivitet.

### <a name="add-or-update-a-timer-trigger"></a>Lägga till eller uppdatera en timer-utlösare

När en uppgift har skapats kan du lägga till en timer-utlösare med hjälp av kommandot [AZ ACR Task timer Add][az-acr-task-timer-add] . I följande exempel läggs ett timer- *timer2* till *timertask* som skapats tidigare. Den här timern utlöser uppgiften varje dag vid 10:30 UTC.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Uppdatera schemat för en befintlig utlösare eller ändra dess status genom att använda kommandot [AZ ACR Task timer Update][az-acr-task-timer-update] . Uppdatera till exempel utlösaren med namnet *timer2* för att utlösa uppgiften vid 11:30 UTC:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Lista över timer-utlösare

Kommandot [AZ ACR Task timer List][az-acr-task-timer-list] visar de timer-utlösare som kon figurer ATS för en uppgift:

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

### <a name="remove-a-timer-trigger"></a>Ta bort en timer-utlösare

Använd kommandot [AZ ACR Task timer Remove][az-acr-task-timer-remove] för att ta bort en timer-utlösare från en aktivitet. I följande exempel tar bort utlösaren *timer2* från *timertask*:

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-uttryck

ACR-aktiviteter använder [NCronTab](https://github.com/atifaziz/NCrontab) -biblioteket för att tolka cron-uttryck. Uttryck som stöds i ACR-aktiviteter har fem obligatoriska fält avgränsade med blank steg:

`{minute} {hour} {day} {month} {day-of-week}`

Tids zonen som används med cron-uttrycken är Coordinated Universal Time (UTC). Timmar är i 24-timmarsformat.

> [!NOTE]
> ACR-aktiviteter har inte stöd `{second}` för `{year}` fältet eller i cron-uttryck. Om du kopierar ett cron-uttryck som används i ett annat system måste du ta bort dessa fält om de används.

Varje fält kan ha en av följande typer av värden:

|Typ  |Exempel  |Utlöses av  |
|---------|---------|---------|
|Ett speciellt värde |<nobr>`"5 * * * *"`</nobr>|varje timme efter 5 minuter efter timmen|
|Alla värden ( `*` )|<nobr>`"* 5 * * *"`</nobr>|varje minut i timmen som börjar 5:00 UTC (60 gånger per dag)|
|Ett intervall ( `-` operator)|<nobr>`"0 1-3 * * *"`</nobr>|3 gånger per dag, med 1:00, 2:00 och 3:00 UTC|
|En uppsättning värden ( `,` operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3 gånger per timme, vid 20 minuter, 30 minuter och 40 minuter efter timmen|
|Ett intervall värde ( `/` operator)|<nobr>`"*/10 * * * *"`</nobr>|6 gånger per timme, vid 10 minuter, 20 minuter och så vidare, efter timmen

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron-exempel

|Exempel|Utlöses av  |
|---------|---------|
|`"*/5 * * * *"`|var femte minut|
|`"0 * * * *"`|en gång överst i varje timme|
|`"0 */2 * * *"`|var två: e timme|
|`"0 9-17 * * *"`|en gång i timmen från 9:00 till 17:00 UTC|
|`"30 9 * * *"`|vid 9:30 UTC varje dag|
|`"30 9 * * 1-5"`|vid 9:30 UTC varje vardag|
|`"30 9 * Jan Mon"`|vid 9:30 UTC varje måndag i januari|

## <a name="clean-up-resources"></a>Rensa resurser

För att ta bort alla resurser som du har skapat i den här själv studie serien, inklusive behållar registret eller register, behållar instansen, nyckel valvet och tjänstens huvud namn, utfärdar du följande kommandon:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar Azure Container Registry uppgifter som automatiskt utlöses av en timer. 

Ett exempel på hur du använder en schemalagd aktivitet för att rensa databaser i ett register finns i [Rensa avbildningar automatiskt från ett Azure Container Registry](container-registry-auto-purge.md).

Exempel på uppgifter som utlöses av käll kods skrivningar eller bas avbildnings uppdateringar finns i andra artiklar i [själv studie serien för ACR uppgifter](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
