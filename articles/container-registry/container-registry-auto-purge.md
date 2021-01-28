---
title: Ta bort taggar och manifest
description: Använd ett rensnings kommando om du vill ta bort flera taggar och manifest från ett Azure Container Registry baserat på ålder och ett tag filter och eventuellt schemalägga rensnings åtgärder.
ms.topic: article
ms.date: 01/27/2021
ms.openlocfilehash: ab1a925092784effd07431d75e4ec1535c53ed33
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927268"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Rensa avbildningar automatiskt från ett Azure Container Registry

När du använder ett Azure Container Registry som en del av ett arbets flöde för utveckling kan registret snabbt fylla i med bilder eller andra artefakter som inte behövs efter en kort period. Du kanske vill ta bort alla Taggar som är äldre än en viss varaktighet eller matcha ett angivet namn filter. Om du vill ta bort flera artefakter snabbt, introducerar den här artikeln `acr purge` kommandot som du kan köra som en aktivitet på begäran eller [schemalagd](container-registry-tasks-scheduled.md) ACR. 

`acr purge`Kommandot är för närvarande distribuerat i en offentlig behållar avbildning ( `mcr.microsoft.com/acr/acr-cli:0.4` ) som byggts från käll koden i [ACR-CLI-](https://github.com/Azure/acr-cli) lagrings platsen i GitHub.

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra ACR-uppgifts exemplen i den här artikeln. Om du vill använda det lokalt, krävs version 2.0.76 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install]. 

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

> [!WARNING]
> Använd `acr purge` kommandot med varning--borttagna bilddata kan inte återställas. Om du har system som hämtar bilder av manifest sammandrag (i stället för avbildnings namn) bör du inte rensa otaggade bilder. Om du tar bort otaggade bilder så förhindras dessa system från att hämta avbildningarna från registret. Överväg att använda ett unikt taggnings schema i stället för att hämta ett *unikt taggnings* schema, en [rekommenderad metod](container-registry-image-tag-version.md).

Om du vill ta bort enstaka bildtaggar eller manifest med hjälp av Azure CLI-kommandon, se [ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Använda kommandot Rensa

`acr purge`Container kommandot tar bort bilder efter tagg i en lagrings plats som matchar ett namn filter och som är äldre än en angiven varaktighet. Som standard tas endast tagg referenser bort, inte underliggande [manifest](container-registry-concepts.md#manifest) och lager data. Kommandot har ett alternativ för att även ta bort manifest. 

> [!NOTE]
> `acr purge` tar inte bort en bildtagg eller lagrings plats där `write-enabled` attributet är inställt på `false` . Mer information finns i [låsa en behållar avbildning i ett Azure Container Registry](container-registry-image-lock.md).

`acr purge` är utformad för att köras som ett container-kommando i en [ACR-uppgift](container-registry-tasks-overview.md), så att den autentiseras automatiskt med det register där aktiviteten körs och utför åtgärder där. I uppgifts exemplen i den här artikeln används `acr purge` kommando [Ali Aset](container-registry-tasks-reference-yaml.md#aliases) i stället för ett fullständigt kvalificerat behållar avbildnings kommando.

Ange minst följande när du kör `acr purge` :

* `--filter` – En lagrings plats och ett *reguljärt uttryck* för att filtrera Taggar i lagrings platsen. Exempel: `--filter "hello-world:.*"` matchar alla Taggar i `hello-world` databasen och `--filter "hello-world:^1.*"` matchar taggar som börjar med `1` . Skicka flera `--filter` Parametrar för att rensa flera databaser.
* `--ago` – En [varaktighets sträng](https://golang.org/pkg/time/) för go-format för att ange en varaktighet utöver vilken avbildningar tas bort. Varaktigheten består av en sekvens med ett eller flera decimal tal, var och en med ett enhets suffix. Giltiga tidsenheter är "d" för dagar, "h" för timmar och "m" för minuter. Väljer till exempel `--ago 2d3h6m` Alla filtrerade bilder senast ändrad över 2 dagar, 3 timmar och 6 minuter sedan och `--ago 1.5h` väljer bilder som senast ändrades för 1,5 timmar sedan.

`acr purge` stöder flera valfria parametrar. Följande två används i exempel i den här artikeln:

* `--untagged` -Anger att manifest som inte har tillhör ande Taggar (*otaggade manifest*) tas bort.
* `--dry-run` -Anger att inga data tas bort, men utdata är desamma som om kommandot körs utan den här flaggan. Den här parametern är användbar för att testa ett rensnings kommando för att kontrol lera att den inte oavsiktligt tar bort data som du tänker behålla.
* `--keep` -Anger att det senaste x-antalet för borttagna Taggar behålls.
* `--concurrency` -Anger att x-rensnings aktiviteter bearbetas samtidigt. Ett standardvärde kommer att användas om den här parametern inte anges.

Kör om du vill ha fler parametrar `acr purge --help` . 

`acr purge` stöder andra funktioner i ACR tasks-kommandon, inklusive [körning av variabler](container-registry-tasks-reference-yaml.md#run-variables) och körnings [loggar för uppgifter](container-registry-tasks-logs.md) som strömmas och som också sparas för senare hämtning.

### <a name="run-in-an-on-demand-task"></a>Kör i en aktivitet på begäran

I följande exempel används kommandot [AZ ACR Run][az-acr-run] för att köra `acr purge` kommandot på begäran. Det här exemplet tar bort alla bildtaggar och manifest i `hello-world` databasen i *registret* som ändrades för mer än 1 dag sedan. Container kommandot skickas med en miljö variabel. Aktiviteten körs utan någon käll kontext.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Kör i en schemalagd aktivitet

I följande exempel används kommandot [AZ ACR Task Create][az-acr-task-create] för att skapa en daglig [schemalagd ACR-aktivitet](container-registry-tasks-scheduled.md). Borttagning av taggar har ändrats mer än 7 dagar sedan i `hello-world` lagrings platsen. Container kommandot skickas med en miljö variabel. Aktiviteten körs utan någon käll kontext.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Kör kommandot [AZ ACR Task show][az-acr-task-show] för att se att timer-utlösaren har kon figurer ATS.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Ta bort ett stort antal taggar och manifest

Det kan ta flera minuter eller längre att rensa ett stort antal taggar och manifest. För att tömma tusentals Taggar och manifest kan kommandot behöva köras längre än standard tiden för timeout på 600 sekunder för en aktivitet på begäran, eller 3600 sekunder för en schemalagd aktivitet. Om tids gränsen överskrids tas endast en delmängd av taggar och manifest bort. För att säkerställa att en storskalig tömning är klar skickar du `--timeout` parametern för att öka värdet. 

Följande på begäran-aktivitet anger till exempel en tids gräns på 3600 sekunder (1 timme):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Exempel: schemalagd rensning av flera databaser i ett register

Det här exemplet visar hur `acr purge` du regelbundet rensar flera databaser i ett register. Du kan till exempel ha en utvecklings pipeline som skickar avbildningar till-och- `samples/devimage1` `samples/devimage2` databaserna. Du kan regelbundet importera utvecklings avbildningar till ett produktions lager för dina distributioner, så att du inte längre behöver utvecklings avbildningarna. I varje vecka rensar du `samples/devimage1` och `samples/devimage2` -databaserna, som förberedelse för den kommande veckans arbete.

### <a name="preview-the-purge"></a>Förhandsgranska rensningen

Innan du tar bort data rekommenderar vi att du kör en aktivitet på begäran med hjälp av `--dry-run` parametern. Med det här alternativet kan du se taggar och manifest som kommandot tar bort, utan att ta bort några data. 

I följande exempel väljer filtret i varje databas alla Taggar. `--ago 0d`Parametern matchar avbildningar av alla åldrar i databaserna som matchar filtren. Ändra urvalskriterierna efter behov för ditt scenario. `--untagged`Parametern anger att manifest ska tas bort förutom taggar. Container-kommandot skickas till kommandot [AZ ACR Run][az-acr-run] med en miljö variabel.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Granska kommandots utdata för att se de taggar och manifest som matchar urvals parametrarna. Eftersom kommandot körs med `--dry-run` raderas inga data.

Exempel på utdata:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Schemalägg rensningen

När du har verifierat den torra körningen skapar du en schemalagd aktivitet som automatiserar rensningen. I följande exempel schemaläggs en veckovis aktivitet på söndag vid 1:00 UTC för att köra föregående rensnings kommando:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Kör kommandot [AZ ACR Task show][az-acr-task-show] för att se att timer-utlösaren har kon figurer ATS.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra alternativ för att [ta bort bilddata](container-registry-delete.md) i Azure Container Registry.

Mer information om avbildnings lagring finns [i behållar avbildnings lagring i Azure Container Registry](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

