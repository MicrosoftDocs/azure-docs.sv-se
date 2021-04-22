---
title: Hantera och uppdatera Azure HPC Cache
description: Hantera och uppdatera Azure HPC Cache med hjälp av Azure Portal eller Azure CLI
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: a831aa7b2f3b0d438d9db8fefa3d26428fea3680
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862605"
---
# <a name="manage-your-cache"></a>Hantera din cache

Översiktssidan för cachen i Azure Portal visar projektinformation, cachestatus och grundläggande statistik för din cache. Den har också kontroller för att stoppa eller starta cachen, ta bort cachen, tömma data till långsiktig lagring och uppdatera programvara.

Den här artikeln förklarar också hur du utför dessa grundläggande uppgifter med Azure CLI.

Öppna översiktssidan genom att välja din cacheresurs i Azure Portal. Läs till exempel in sidan **Alla resurser** och klicka på cachenamnet.

![skärmbild av Azure HPC Cache instansens översiktssida](media/hpc-cache-overview.png)

Knapparna längst upp på sidan kan hjälpa dig att hantera cacheminnet:

* **Starta** och [**stoppa**](#stop-the-cache) – Återupptar eller pausar cacheåtgärden
* [**Flush**](#flush-cached-data) – Skriver ändrade data till lagringsmål
* [**Uppgradera**](#upgrade-cache-software) – uppdaterar cacheprogramvaran
* [**Samla in**](#collect-diagnostics) diagnostik – Överför felsökningsinformation
* **Uppdatera** – läser in översiktssidan på nytt
* [**Ta**](#delete-the-cache) bort – raderar cachen permanent

Läs mer om dessa alternativ nedan.

Klicka på bilden nedan om du vill titta [på en video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) som visar cachehanteringsuppgifter.

[![videominiatyr: Azure HPC Cache: Hantera (klicka för att besöka videosidan)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Stoppa cachen

Du kan stoppa cacheminnet för att minska kostnaderna under en inaktiv period. Du debiteras inte för drifttid när cachen stoppas, men du debiteras för cachens allokerade disklagring. (Mer information [finns på](https://aka.ms/hpc-cache-pricing) prissättningssidan.)

En stoppad cache svarar inte på klientbegäranden. Du bör demontera klienter innan du stoppar cachen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Knappen **Stoppa** pausar en aktiv cache. Knappen **Stoppa** är tillgänglig när cachens status är **Felfri** eller **Degraderad.**

![skärmbild av de översta knapparna med Stoppa markerat och ett popup-meddelande som beskriver stoppåtgärden och frågar "vill du fortsätta?" med knapparna Ja (standard) och Nej](media/stop-cache.png)

När du har klickat på Ja för att bekräfta att cachen stoppas rensar cacheminnet automatiskt innehållet till lagringsmålen. Den här processen kan ta lite tid, men den säkerställer datakonsekvens. Slutligen ändras cachestatusen till **Stoppad**.

Om du vill återaktivera en stoppad cache klickar du **på knappen** Starta. Ingen bekräftelse krävs.

![skärmbild av de översta knapparna med Start markerat](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Pausa tillfälligt en cache med [kommandot az hpc-cache stop.](/cli/azure/hpc-cache#az_hpc_cache_stop) Den här åtgärden är endast giltig när en caches status är **Felfri** eller **Degraderad.**

Cacheminnet rensar automatiskt innehållet till lagringsmålen innan det stoppas. Den här processen kan ta lite tid, men den säkerställer datakonsekvens.

När åtgärden är klar ändras cachestatusen till **Stoppad**.

Återaktivera en stoppad cache med [az hpc-cache start](/cli/azure/hpc-cache#az_hpc_cache_start).

När du utfärdar start- eller stoppkommandot visar kommandoraden statusmeddelandet "Körs" tills åtgärden har slutförts.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

När det är klart uppdateras meddelandet till "Finished" (Slutfört) och visar returkoder och annan information.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Tömma cachelagrade data

Knappen **Rensa** på översiktssidan uppmanar cachen att omedelbart skriva alla ändrade data som lagras i cacheminnet till backend-lagringsmålen. Cachen sparar regelbundet data till lagringsmålen, så det är inte nödvändigt att göra detta manuellt om du inte vill se till att lagringssystemet för backend-lagring är uppdaterat. Du kan till exempel använda Flush **innan du tar** en ögonblicksbild av lagringen eller kontrollerar datauppsättningens storlek.

> [!NOTE]
> Cachen kan inte hantera klientbegäranden under tömningsprocessen. Cacheåtkomsten pausas och återupptas när åtgärden har avslutats.

När du startar cachelagringsåtgärden slutar cacheminnet att acceptera klientbegäranden och cachestatusen på översiktssidan ändras till **Rensa**.

Data i cacheminnet sparas till lämpliga lagringsmål. Beroende på hur mycket data som behöver rensas kan processen ta några minuter eller över en timme.

När alla data har sparats till lagringsmål börjar cachen automatiskt ta klientbegäranden igen. Cachestatusen återgår till **Felfri**.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Om du vill tömma cacheminnet klickar **du på** knappen Flush (Rensa) och sedan på **Yes (Ja)** för att bekräfta åtgärden.

![skärmbild av de översta knapparna med Flush (Rensa) markerat och ett popup-meddelande som beskriver flush-åtgärden och frågan "vill du fortsätta?". med knapparna Ja (standard) och Nej](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Använd [az hpc-cache flush för](/cli/azure/hpc-cache#az_hpc_cache_flush) att tvinga cachen att skriva alla ändrade data till lagringsmålen.

Exempel:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

När tömman är klar returneras ett meddelande om att det lyckades.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Uppgradera cacheprogramvara

Om en ny programvaruversion är tillgänglig **blir knappen** Uppgradera aktiv. Du bör också se ett meddelande överst på sidan om att uppdatera programvara.

![skärmbild av den översta raden med knappar med knappen Uppgradera aktiverad](media/hpc-cache-upgrade-button.png)

Klientåtkomsten avbryts inte under en programvaruuppgradering, men cacheprestandan blir långsammare. Planera att uppgradera programvara under tider med låg belastning eller under en planerad underhållsperiod.

Programuppdateringen kan ta flera timmar. Cacheminnen som konfigurerats med högre dataflöde tar längre tid att uppgradera än cacheminnen med mindre dataflödesvärden.

När en programuppgradering är tillgänglig har du en vecka på dig att tillämpa den manuellt. Slutdatumet visas i uppgraderingsmeddelandet. Om du inte uppgraderar under den tiden tillämpar Azure automatiskt uppdateringen på ditt cacheminne. Tidsinställningen för den automatiska uppgraderingen kan inte konfigureras. Om du är orolig över cacheprestandapåverkan bör du uppgradera programvaran själv innan tidsperioden upphör att gälla.

Om ditt cacheminne stoppas när slutdatumet passerar uppgraderas programvaran automatiskt nästa gång den startas. (Uppdateringen kanske inte startar omedelbart, men den startar under den första timmen.)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Klicka på **knappen** Uppgradera för att starta programuppdateringen. Cachestatusen ändras till **Uppgradera** tills åtgärden har slutförts.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

I Azure CLI ingår ny programvaruinformation i slutet av cachestatusrapporten. (Använd [az hpc-cache show för](/cli/azure/hpc-cache#az_hpc_cache_show) att kontrollera.) Leta efter strängen "upgradeStatus" i meddelandet.

Använd [az hpc-cache upgrade-firmware](/cli/azure/hpc-cache#az_hpc_cache_upgrade-firmware) för att tillämpa uppdateringen, om sådan finns.

Om ingen uppdatering är tillgänglig har den här åtgärden ingen effekt.

Det här exemplet visar cachestatus (ingen uppdatering är tillgänglig) och resultatet av kommandot upgrade-firmware.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Samla in diagnostik

Knappen **Samla in diagnostik** startar manuellt processen för att samla in systeminformation och ladda upp den till Microsoft-tjänsten och supporten för felsökning. Ditt cacheminne samlar automatiskt in och laddar upp samma diagnostikinformation om ett allvarligt cacheproblem uppstår.

Använd den här kontrollen om Microsoft-tjänsten och supporten begär det.

När du har klickat på knappen **klickar du på Ja** för att bekräfta uppladdningen.

![skärmbild av popup-meddelandet "Starta diagnostiksamlingen". Standardknappen "Ja" är markerad.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Ta bort cachen

Knappen **Ta** bort förstör cachen. När du tar bort en cache raderas alla dess resurser och inga kontoavgifter debiteras längre.

De backend-lagringsvolymer som används som lagringsmål påverkas inte när du tar bort cachen. Du kan lägga till dem i en framtida cache senare eller inaktivera dem separat.

> [!NOTE]
> Azure HPC Cache skriver inte automatiskt ändrade data från cachen till backend-lagringssystemen innan cachen tas bort.
>
> För att säkerställa att alla data i cacheminnet har skrivits till långsiktig lagring stoppar du [cachen innan](#stop-the-cache) du tar bort den. Kontrollera att statusen Stoppad visas **innan du** tar bort den.

### <a name="portal"></a>[Portal](#tab/azure-portal)

När du har stoppat cachen klickar du **på knappen** Ta bort för att permanent ta bort cachen.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Använd Azure CLI-kommandot [az hpc-cache delete för](/cli/azure/hpc-cache#az_hpc_cache_delete) att permanent ta bort cachen.

Exempel:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Cachemått och övervakning

Översiktssidan visar grafer för viss grundläggande cachestatistik – cachegenomflöde, åtgärder per sekund och svarstid.

![skärmbild av treradsdiagram som visar den statistik som nämns ovan för ett exempelcache](media/hpc-cache-overview-stats.png)

Dessa diagram är en del av Azures inbyggda verktyg för övervakning och analys. Ytterligare verktyg och aviseringar är tillgängliga på sidorna under **rubriken** Övervakning i sidofältet i portalen. Läs mer i portalavsnittet i [Azure Monitoring-dokumentationen.](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal)

## <a name="view-warnings"></a>Visa varningar

Om cacheminnet hamnar i ett feltillstånd kontrollerar du **sidan** Varningar. Den här sidan visar meddelanden från cacheprogramvaran som kan hjälpa dig att förstå dess tillstånd.

Dessa meddelanden visas inte i aktivitetsloggen eftersom de inte styrs av Azure Portal. De är ofta associerade med anpassade inställningar som du kan ha gjort.

Här kan du se olika typer av varningar:

* Cachen kan inte nå NTP-servern
* Cachen kunde inte ladda ned information om utökade gruppers användarnamn
* Anpassade DNS-inställningar har ändrats för ett lagringsmål

![skärmbild av sidan Övervakning > varningar som visar ett meddelande om att utökade gruppers användarnamn inte kunde laddas ned](media/warnings-page.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [mått- och statistikverktyg i Azure](../azure-monitor/index.yml)
* Få [hjälp med dina Azure HPC Cache](hpc-cache-support-ticket.md)
