---
title: Rensa taggar och manifest
description: Använd ett rensningskommando för att ta bort flera taggar och manifest från ett Azure-containerregister baserat på ålder och ett taggfilter och om du vill schemalägga rensningsåtgärder.
ms.topic: article
ms.date: 02/19/2021
ms.openlocfilehash: 562d1940459cb1594b7cd9aca2af280b05a4e419
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784199"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Rensa avbildningar automatiskt från ett Azure-containerregister

När du använder ett Azure-containerregister som en del av ett utvecklingsarbetsflöde kan registret snabbt fyllas med avbildningar eller andra artefakter som inte behövs efter en kort period. Du kanske vill ta bort alla taggar som är äldre än en viss varaktighet eller matcha ett angivet namnfilter. Om du vill ta bort flera artefakter snabbt introducerar den här artikeln kommandot som du kan köra som `acr purge` en ACR-uppgift på begäran eller som [en schemalagd](container-registry-tasks-scheduled.md) ACR-uppgift. 

Kommandot distribueras för närvarande i en offentlig containeravbildning ( ), som skapats från källkoden på `acr purge` `mcr.microsoft.com/acr/acr-cli:0.4` lagringsplatsen [acr-cli](https://github.com/Azure/acr-cli) i GitHub.

Du kan använda Azure Cloud Shell lokal installation av Azure CLI för att köra ACR-uppgiftsexempel i den här artikeln. Om du vill använda den lokalt krävs version 2.0.76 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install]. 

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

> [!WARNING]
> Använd kommandot `acr purge` med försiktighet – borttagna bilddata kan inte återställas. Om du har system som hämtar avbildningar enligt manifestet digest (till skillnad från avbildningens namn) bör du inte rensa otaggade avbildningar. Om du tar bort otaggade avbildningar förhindras dessa system från att hämta avbildningarna från registret. I stället för att hämta efter manifest bör du överväga *att införa ett unikt taggningsschema,* vilket är en [rekommenderad metod.](container-registry-image-tag-version.md)

Om du vill ta bort enskilda bildtaggar eller manifest med hjälp av Azure CLI-kommandon, se [Ta bort containeravbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Använd rensningskommandot

`acr purge`Containerkommandot tar bort avbildningar efter tagg i en lagringsplats som matchar ett namnfilter och som är äldre än en angiven varaktighet. Som standard tas endast taggreferenser bort, inte underliggande [manifest och](container-registry-concepts.md#manifest) lagerdata. Kommandot har ett alternativ för att även ta bort manifest. 

> [!NOTE]
> `acr purge` tar inte bort en avbildningstagg eller lagringsplats `write-enabled` där attributet är inställt på `false` . Mer information finns i [Låsa en containeravbildning i ett Azure-containerregister.](container-registry-image-lock.md)

`acr purge` är utformat för att köras som ett containerkommando i en [ACR-uppgift,](container-registry-tasks-overview.md)så att den autentiseras automatiskt med registret där aktiviteten körs och utför åtgärder där. I uppgiftsexempel i den här artikeln används `acr purge` [kommandoaliaset](container-registry-tasks-reference-yaml.md#aliases) i stället för ett fullständigt kvalificerat kommando för containeravbildningen.

Ange minst följande när du kör `acr purge` :

* `--filter` – En lagringsplats och ett *reguljärt uttryck* för att filtrera taggar i lagringsplatsen. Exempel: `--filter "hello-world:.*"` matchar alla taggar på `hello-world` lagringsplatsen och matchar `--filter "hello-world:^1.*"` taggar som börjar med `1` . Skicka flera `--filter` parametrar för att rensa flera lagringsplatsen.
* `--ago` – En varaktighetssträng [i](https://golang.org/pkg/time/) Go-format för att ange en varaktighet utöver vilken bilder tas bort. Varaktigheten består av en sekvens med ett eller flera decimaltal, vart och ett med ett enhetssuffix. Giltiga tidsenheter omfattar "d" för dagar, "h" i timmar och "m" i minuter. Till exempel väljer alla filtrerade avbildningar som senast ändrades för mer än 2 dagar, 3 timmar och 6 minuter sedan och väljer bilder som senast ändrades för mer än `--ago 2d3h6m` `--ago 1.5h` 1,5 timmar sedan.

`acr purge` stöder flera valfria parametrar. Följande två används i exemplen i den här artikeln:

* `--untagged` - Anger att manifest som inte har associerade taggar (*otaggade manifest*) tas bort.
* `--dry-run` - Anger att inga data tas bort, men utdata är samma som om kommandot körs utan den här flaggan. Den här parametern är användbar för att testa ett rensningskommando för att se till att den inte oavsiktligt tar bort data som du tänker bevara.
* `--keep` - Anger att de senaste x antalet taggar som ska tas bort behålls.
* `--concurrency` - Anger ett antal rensningsuppgifter som ska bearbetas samtidigt. Ett standardvärde används om den här parametern inte anges.

Kör för ytterligare `acr purge --help` parametrar. 

`acr purge`har stöd för andra funktioner ACR-uppgifter kommandon, inklusive körningsvariabler och aktivitetskörningsloggar som [strömmas](container-registry-tasks-reference-yaml.md#run-variables) och även sparas för senare hämtning. [](container-registry-tasks-logs.md)

### <a name="run-in-an-on-demand-task"></a>Köra i en aktivitet på begäran

I följande exempel används [kommandot az acr run][az-acr-run] för att köra kommandot på `acr purge` begäran. Det här exemplet tar bort alla bildtaggar och manifest i lagringsplatsen `hello-world` *i myregistry* som ändrades för mer än en dag sedan. Containerkommandot skickas med hjälp av en miljövariabel. Aktiviteten körs utan källkontext.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Köra i en schemalagd aktivitet

I följande exempel används kommandot [az acr task create för][az-acr-task-create] att skapa en daglig schemalagd [ACR-uppgift](container-registry-tasks-scheduled.md). Aktiviteten tar bort taggar som ändrades för mer än 7 dagar sedan i `hello-world` lagringsplatsen. Containerkommandot skickas med hjälp av en miljövariabel. Aktiviteten körs utan källkontext.

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

Kör kommandot [az acr task show för][az-acr-task-show] att se att timerutlösaren har konfigurerats.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Rensa ett stort antal taggar och manifest

Det kan ta flera minuter eller längre att rensa ett stort antal taggar och manifest. Om du vill rensa tusentals taggar och manifest kan kommandot behöva köras längre än standardtiden på 600 sekunder för en aktivitet på begäran, eller 3 600 sekunder för en schemalagd aktivitet. Om tidsgränsen överskrids tas endast en delmängd av taggar och manifest bort. För att säkerställa att en storskalig rensning är klar skickar du `--timeout` parametern för att öka värdet. 

Följande aktivitet på begäran anger till exempel en tidsgräns på 3 600 sekunder (1 timme):

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Exempel: Schemalagd rensning av flera lagringsplatsen i ett register

Det här exemplet går igenom `acr purge` hur du använder för att regelbundet rensa flera lagringsplatsen i ett register. Du kan till exempel ha en utvecklingspipeline som push-lar avbildningar `samples/devimage1` `samples/devimage2` till lagringsplatsen och . Du importerar regelbundet utvecklingsavbildningar till en produktionsdatabas för dina distributioner, så du behöver inte längre utvecklingsavbildningarna. Varje vecka rensar du lagringsplatsen och inför den `samples/devimage1` `samples/devimage2` kommande veckans arbete.

### <a name="preview-the-purge"></a>Förhandsgranska rensningen

Innan du tar bort data rekommenderar vi att du kör en rensningsaktivitet på begäran med `--dry-run` parametern . Med det här alternativet kan du se de taggar och manifest som kommandot rensar, utan att ta bort några data. 

I följande exempel väljer filtret på varje lagringsplats alla taggar. Parametern `--ago 0d` matchar bilder av alla åldrar i lagringsplatsen som matchar filtren. Ändra urvalskriterierna efter behov för ditt scenario. Parametern `--untagged` anger att manifest ska tas bort utöver taggar. Containerkommandot skickas till kommandot [az acr run med][az-acr-run] hjälp av en miljövariabel.

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

Granska kommandoutdata för att se de taggar och manifest som matchar urvalsparametrarna. Eftersom kommandot körs med `--dry-run` tas inga data bort.

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

### <a name="schedule-the-purge"></a>Schemalägga rensningen

När du har verifierat körningen skapar du en schemalagd uppgift för att automatisera rensningen. I följande exempel schemaläggs en veckovis aktivitet på söndag kl. 13:00 UTC för att köra föregående rensningskommando:

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

Kör kommandot [az acr task show för][az-acr-task-show] att se att timerutlösaren har konfigurerats.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra alternativ [för att ta bort bilddata](container-registry-delete.md) i Azure Container Registry.

Mer information om avbildningslagring finns i [Containeravbildningslagring i Azure Container Registry](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
