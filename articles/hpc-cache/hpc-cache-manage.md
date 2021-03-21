---
title: Hantera och uppdatera Azure HPC-cache
description: Hantera och uppdatera Azure HPC-cache med hjälp av Azure Portal eller Azure CLI
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: b34beb65bb8c4136887651d8365c937b17718572
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471907"
---
# <a name="manage-your-cache"></a>Hantera din cache

På sidan cache-översikt i Azure Portal visas projekt information, cache-status och grundläggande statistik för cacheminnet. Den har också kontroller för att stoppa eller starta cacheminnet, ta bort cachen, tömma data till långsiktig lagring och uppdatera program vara.

Den här artikeln beskriver också hur du utför dessa grundläggande uppgifter med Azure CLI.

Öppna översikts sidan genom att välja din cache-resurs i Azure Portal. Läs till exempel sidan **alla resurser** och klicka på cache-namnet.

![skärm bild av översikts sidan för Azure HPC-instansen](media/hpc-cache-overview.png)

Knapparna överst på sidan kan hjälpa dig att hantera cachen:

* **Starta** och [**stoppa**](#stop-the-cache) – återupptar eller pausar cache-åtgärd
* [**Flush**](#flush-cached-data) -skriver ändrade data till lagrings mål
* [**Uppgradera**](#upgrade-cache-software) – uppdaterar cache-programvaran
* [**Samla in diagnostik**](#collect-diagnostics) – överför fel söknings information
* **Uppdatera** – Läs in sidan Översikt igen
* [**Ta bort**](#delete-the-cache) – förstör cachen permanent

Läs mer om de här alternativen nedan.

Klicka på bilden nedan om du vill se en [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) som visar hanterings uppgifter för cache.

[![video miniatyr: Azure HPC-cache: hantera (Klicka om du vill besöka video sidan)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Stoppa cachen

Du kan stoppa cacheminnet för att minska kostnaderna under en inaktiv period. Du debiteras inte för drift tid medan cachen stoppas, men du debiteras för cachens allokerade disk lagring. (Mer information finns på sidan med [priser](https://aka.ms/hpc-cache-pricing) .)

Ett stoppat cacheminne svarar inte på klient begär Anden. Du bör demontera klienter innan du stoppar cacheminnet.

### <a name="portal"></a>[Portal](#tab/azure-portal)

**Stopp** knappen pausar en aktiv cache. **Stopp** knappen är tillgänglig när statusen för ett cacheminne är **felfri** eller **försämrad**.

![skärm bild av de översta knapparna med stoppa markerat och ett popup-meddelande som beskriver stopp åtgärden och där vill du fortsätta? med Ja (standard) och inga knappar](media/stop-cache.png)

När du har klickat på Ja för att bekräfta att du vill stoppa cacheminnet tömmer cachen automatiskt innehållet till lagrings målen. Den här processen kan ta lite tid, men den garanterar data konsekvens. Slutligen ändras cachens status till **stoppad**.

Om du vill återaktivera en stoppad cache klickar du på knappen **Starta** . Ingen bekräftelse krävs.

![skärm bild av de översta knapparna med start markerat](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Pausa tillfälligt en cache med kommandot [AZ HPC-cache Stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) . Den här åtgärden är endast giltig när statusen för en cache är **felfri** eller **försämrad**.

Cachen tömmer automatiskt innehållet till lagrings målen innan den stoppas. Den här processen kan ta lite tid, men den garanterar data konsekvens.

När åtgärden har slutförts ändras cachens status till **stoppad**.

Återaktivera en stoppad cache med [AZ HPC-cache start](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start).

När du utfärdar kommandot start eller Stop visar kommando raden status meddelandet "körs" tills åtgärden har slutförts.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

När meddelandet har slutförts uppdateras det till "slutfört" och visar retur koder och annan information.

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

## <a name="flush-cached-data"></a>Rensa cachelagrade data

Knappen **Töm** på översikts sidan visar cacheminnet för att omedelbart skriva alla ändrade data som lagras i cacheminnet till backend-lagrings målen. Cachen sparar regelbundet data till lagrings målen, så det är inte nödvändigt att göra detta manuellt, såvida du inte vill se till att Server delens lagrings system är aktuellt. Du kan till exempel använda **Flush** innan du tar en lagrings ögonblicks bild eller kontrollerar data uppsättningens storlek.

> [!NOTE]
> Under tömnings processen kan cachen inte hantera klient begär Anden. Cache-åtkomst är inaktive rad och återupptas när åtgärden har slutförts.

När du startar rensningen av cachen slutar cachen att acceptera klient begär Anden och cache-statusen på översikts sidan ändras till **tömning**.

Data i cacheminnet sparas till lämpliga lagrings mål. Beroende på hur mycket data som behöver tömmas kan processen ta några minuter eller mer än en timme.

När alla data har sparats på lagrings målen börjar cachen automatiskt ta emot klient begär Anden. Cache-statusen återgår till **felfritt**.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Om du vill tömma cachen klickar du på knappen **Flush** och sedan på **Ja** för att bekräfta åtgärden.

![skärm bild av de översta knapparna med Flush markerat och ett popup-meddelande som beskriver tömnings åtgärden och där du ombeds att fortsätta? med Ja (standard) och inga knappar](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Använd [AZ HPC-cache Flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) för att tvinga cacheminnet att skriva alla ändrade data till lagrings målen.

Exempel:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

När tömningen är klar returneras ett meddelande som lyckats.

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

## <a name="upgrade-cache-software"></a>Uppgradera cache-programvara

Om det finns en ny program varu version blir **uppgraderings** knappen aktiv. Du bör också se ett meddelande längst upp på sidan om uppdatering av program vara.

![skärm bild av den översta raden med knappar med uppgraderings knappen aktive rad](media/hpc-cache-upgrade-button.png)

Klient åtkomst avbryts inte under en program uppgradering, men cache-prestandan går långsamt. Planera för att uppgradera program vara under tider med låg belastning eller under en planerad underhålls period.

Program uppdateringen kan ta flera timmar. Cacheminnen som kon figurer ATS med högre genomflöde tar längre tid att uppgradera än cacheminnen med mindre högsta data flödes värden.

När en program uppgradering är tillgänglig, kommer du att ha en vecka eller så tillämpar du den manuellt. Slutdatumet anges i uppgraderings meddelandet. Om du inte uppgraderar under den tiden tillämpar Azure automatiskt uppdateringen i cacheminnet. Det går inte att konfigurera tids inställningen för automatisk uppgradering. Om du är orolig över prestanda påverkan från cachen bör du uppgradera program varan själv innan tids perioden går ut.

Om cacheminnet stoppas när slutdatumet passerat, uppgraderar cachen automatiskt program varan nästa gång den startas. (Uppdateringen kanske inte startar direkt, men den kommer att starta den första timmen.)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Klicka på **Uppgradera** om du vill starta program uppdateringen. Cachens status ändras till **uppgraderingen** tills åtgärden har slutförts.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

I Azure CLI ingår ny program varu information i slutet av rapporten om cache-status. (Använd [AZ HPC-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) för att kontrol lera.) Leta efter strängen "upgradeStatus" i meddelandet.

Använd [AZ HPC-cache Upgrade-firmware](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) för att tillämpa uppdateringen, om sådan finns.

Om ingen uppdatering är tillgänglig har den här åtgärden ingen påverkan.

I det här exemplet visas cache-status (ingen uppdatering är tillgänglig) och resultatet av kommandot Upgrade-firmware.

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

Knappen **samla in diagnostik** startar manuellt processen för att samla in system information och ladda upp den till Microsoft-tjänsten och support för fel sökning. Cacheminnet samlar automatiskt in och laddar upp samma diagnostikinformation om ett allvarligt cache-problem uppstår.

Använd den här kontrollen om Microsoft-tjänsten och support begär det.

Klicka på **Ja** när du har klickat på knappen för att bekräfta överföringen.

![skärm bild av bekräftelse meddelandet "starta diagnostik-samling". Standard knappen Ja är markerad.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Ta bort cachen

Med knappen **ta bort** förstörs cachen. När du tar bort en cache förstörs alla dess resurser och debiteras inte längre.

De Server dels lagrings volymer som används som lagrings mål påverkas inte när du tar bort cacheminnet. Du kan lägga till dem i en framtida cache senare eller ställa av dem separat.

> [!NOTE]
> Azure HPC cache skriver inte automatiskt över ändrade data från cacheminnet till backend Storage-System innan cachen tas bort.
>
> För att se till att alla data i cacheminnet har skrivits till långsiktig lagring [stoppar du cacheminnet](#stop-the-cache) innan du tar bort det. Se till att den visar statusen **stoppad** innan du tar bort den.

### <a name="portal"></a>[Portal](#tab/azure-portal)

När du har stoppat cacheminnet klickar du på knappen **ta bort** för att ta bort cacheminnet permanent.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Använd Azure CLI-kommandot [AZ HPC-cache Delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) för att ta bort cacheminnet permanent.

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

## <a name="cache-metrics-and-monitoring"></a>Cachelagra mått och övervakning

På sidan Översikt visas diagram för viss grundläggande cache-statistik – cache-genomflöde, åtgärder per sekund och svars tid.

![skärm bild av tre linje diagram som visar ovanstående statistik för ett exempel-cache](media/hpc-cache-overview-stats.png)

Dessa diagram ingår i Azures inbyggda verktyg för övervakning och analys. Det finns ytterligare verktyg och aviseringar på sidorna under **övervaknings** rubriken i portalens marginal List. Läs mer i avsnittet portal i [Azures övervaknings dokumentation](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="view-warnings"></a>Visa varningar

Om cachen hamnar i ett ohälsosamt tillstånd kontrollerar du sidan **varningar** . Den här sidan visar meddelanden från cache-programvaran som kan hjälpa dig att förstå dess tillstånd.

Dessa aviseringar visas inte i aktivitets loggen eftersom de inte styrs av Azure Portal. De är ofta kopplade till anpassade inställningar som du kan ha gjort.

Typer av varningar som du kan se här är:

* Cachen kan inte komma åt sin NTP-server
* Cachen kunde inte hämta användar namns information för utökade grupper
* Anpassade DNS-inställningar har ändrats på ett lagrings mål

![skärm bild av sidan övervaknings > varningar som visar ett meddelande om att det inte gick att hämta användar namn för utökade grupper](media/warnings-page.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azures mått och statistik verktyg](../azure-monitor/index.yml)
* Få [hjälp med Azure HPC-cache](hpc-cache-support-ticket.md)
