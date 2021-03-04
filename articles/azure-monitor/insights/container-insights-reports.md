---
title: Rapporter i behållar insikter
description: Beskriver rapporter som är tillgängliga för analys av data som samlas in av behållar insikter.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: ca74521a08d4edaa498e00e6452d8f69912e4bb9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032807"
---
# <a name="reports-in-container-insights"></a>Rapporter i behållar insikter
Rapporter i behållar insikter rekommenderas som färdiga [Azure-arbetsböcker](../visualize/workbooks-overview.md). I den här artikeln beskrivs de olika rapporter som är tillgängliga och hur du kommer åt dem.

## <a name="viewing-reports"></a>Visa rapporter
Från **Azure Monitor** -menyn i Azure Portal väljer du **behållare**. Välj **insikter** i avsnittet **övervakning** , Välj ett visst kluster och välj sedan sidan **rapporter** . 

[![Sidan rapporter](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Skapa en anpassad arbetsbok
Om du vill skapa en anpassad arbets bok baserat på någon av dessa arbets böcker markerar du List rutan **Visa arbets böcker** och **går sedan till AKS-galleriet** längst ned i list rutan. Se [Azure Monitor-arbetsböcker](../visualize/workbooks-overview.md) för mer information om arbets böcker och använda mallar för arbets böcker.

[![Galleri för AKS](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Node-arbetsböcker

- **Disk kapacitet**: interaktiva disk användnings diagram för varje disk som visas för noden i en behållare i följande perspektiv:

    - Disk procents användning för alla diskar.
    - Ledigt disk utrymme för alla diskar.
    - Ett rutnät som visar varje nods disk, dess procent andel av använt utrymme, trenden i procent av använt utrymme, ledigt disk utrymme (GiB) och trenden för ledigt disk utrymme (GiB). När en rad väljs i tabellen visas procent andelen använt utrymme och ledigt disk utrymme (GiB) under raden.

- **Disk-i/o**: interaktiva disk användnings diagram för varje disk som visas för noden i en behållare i följande perspektiv:

    - Disk-I/O sammanfattas över alla diskar med lästa byte/s, skrivna byte/s och Läs-och skriv byte/s-trender.
    - Åtta prestanda diagram visar nyckeltal för att hjälpa till att mäta och identifiera disk-I/O-Flask halsar.

- **GPU**: diagram över interaktiva GPU-användning för varje GPU-medveten Kubernetes-klusternod.

## <a name="resource-monitoring-workbooks"></a>Arbets böcker för resurs övervakning

- **Distributioner**: status för dina distributioner & horisontell Pod autoskalning (hPa), inklusive anpassade hPa. 
  
- **Arbets belastnings information**: interaktiva diagram som visar prestanda statistik för arbets belastningar för ett namn område. Innehåller flera flikar:

  - Översikt över processor-och minnes användning av POD.
  - POD/container status som visar omstarts trend för POD, containerns restart-trend och container status för poddar.
  - Kubernetes-händelser som visar en sammanfattning av händelser för kontrollanten.

- **Kubelet**: innehåller två rutnät som visar drift statistik över viktiga noder:

    - Översikt per Node-rutnät sammanfattar total åtgärd, totalt antal fel och lyckade åtgärder efter procent och trend för varje nod.
    - Översikt per åtgärds typ sammanfattar för varje åtgärd, totalt antal fel och lyckade åtgärder efter procent och trend.
## <a name="billing-workbooks"></a>Fakturerings arbets böcker

- **Data användning**: hjälper dig att visualisera data källan utan att behöva bygga egna bibliotek med frågor från vad vi delar i vår dokumentation. I den här arbets boken finns det diagram med vilka du kan visa fakturerbara data från sådana perspektiv som:

  - Totalt antal fakturerbara data som matats in i GB efter lösning
  - Fakturerbara data som matats in av behållar loggar (program loggar)
  - Fakturerbara container loggar inmatade data per Kubernetes-namnrymd
  - Fakturerbara container loggar data som matats in åtskiljda av kluster namn
  - Fakturerbara behållar logg data inmatat av logsource-post
  - Fakturerbara diagnostikdata som matats in av loggar för den diagnostiska huvud noden

## <a name="networking-workbooks"></a>Nätverks arbets böcker

- **NPM konfiguration**: övervakning av nätverkskonfigurationer som kon figurer ATS via Network Policy Manager (NPM).

  - Sammanfattnings information om övergripande konfigurations komplexitet.
  - Princip-, regel-och uppsättnings antal över tid, vilket ger inblick i relationen mellan de tre och lägger till en tids dimension för att felsöka en konfiguration.
  - Antal poster i alla IPSets och varje IPSet.
  - Sämsta och genomsnittliga fall prestanda per nod för att lägga till komponenter i nätverks konfigurationen.

- **Nätverk**: interaktiva diagram för nätverks användning för varje nods nätverkskort och ett rutnät presenterar nyckeltalet för att mäta prestanda för nätverkskorten.



## <a name="next-steps"></a>Nästa steg

- Se [Azure Monitor-arbetsböcker](../visualize/workbooks-overview.md) för information om arbets böcker i Azure Monitor.
