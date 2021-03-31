---
title: Introduktion till paket fångst i Azure Network Watcher | Microsoft Docs
description: Den här sidan ger en översikt över funktionen för att fånga Network Watcher-paket
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1c458508dbf8d98349ec8549af32e3dd48bbd09b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966436"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introduktion till variabel paket fångst i Azure Network Watcher

Med Network Watcher variabel paket fångst kan du skapa sessioner för att skapa paket och spåra trafik till och från en virtuell dator. Med paket fångst kan du diagnostisera nätverks avvikelser både återaktivt och proaktivt. Andra användnings områden innefattar insamling av nätverks statistik, få information om nätverks intrång, för att felsöka klient-server-kommunikation och mycket mer.

Paket fångst är ett tillägg för virtuell dator som har startats via en fjärr anslutning via Network Watcher. Den här funktionen fören klar belastningen på att köra en paket registrering manuellt på önskad virtuell dator, vilket sparar värdefull tid. Paket fångst kan utlösas via portalen, PowerShell, CLI eller REST API. Ett exempel på hur paket fångst kan utlösas är med aviseringar för virtuella datorer. Filter tillhandahålls för att avbilda sessionen så att du kan övervaka den trafik som du vill övervaka. Filter baseras på 5-tuple (protokoll, lokal IP-adress, fjärr-IP-adress, lokal port och Fjärrport) information. Insamlade data lagras på den lokala disken eller i en lagrings-blob. Det finns en gräns på 10 paket avbildnings sessioner per region per prenumeration. Den här gränsen gäller endast för sessioner och gäller inte för de sparade paket avbildnings filerna lokalt på den virtuella datorn eller i ett lagrings konto.

> [!IMPORTANT]
> Paket fångst kräver ett tillägg för virtuell dator `AzureNetworkWatcherExtension` . För att installera tillägget på en virtuell Windows-dator går du till [azure Network Watcher agent-tillägget virtuell dator för Windows](../virtual-machines/extensions/network-watcher-windows.md) och för virtuella Linux-datorer gå till [Azure Network Watcher virtuell dator tillägg för Linux](../virtual-machines/extensions/network-watcher-linux.md).

För att minska den information som du har samlat in till endast den information som du vill ha, är följande alternativ tillgängliga för en paket insamlings session:

**Avbilda konfiguration**

|Egenskap|Beskrivning|
|---|---|
|**Högsta antal byte per paket (byte)** | Antalet byte från varje paket som samlas in, alla byte samlas in om inget anges. Antalet byte från varje paket som samlas in, alla byte samlas in om inget anges. Om du bara behöver IPv4-huvudet – visar 34 här |
|**Maximalt antal byte per session (byte)** | Totalt antal byte i som har fångats upp när värdet har nåtts avslutas sessionen.|
|**Tids gräns (sekunder)** | Anger en tids begränsning i insamlings sessionen för paket. Standardvärdet är 18000 sekunder eller 5 timmar.|

**Filtrering (valfritt)**

|Egenskap|Beskrivning|
|---|---|
|**Protokoll** | Protokollet som ska filtreras efter paket fångsten. De tillgängliga värdena är TCP, UDP och all.|
|**Lokal IP-adress** | Det här värdet filtrerar paket fångsten till paket där den lokala IP-adressen matchar det här filtervärdet.|
|**Lokal port** | Det här värdet filtrerar paket fångsten till paket där den lokala porten matchar det här filtervärdet.|
|**Fjärr-IP-adress** | Det här värdet filtrerar paket insamlingen till paket där fjärr-IP matchar detta filter värde.|
|**Fjärr-IP-adress** | Det här värdet filtrerar paket fångsten till paket där fjärrporten matchar det här filtervärdet.|

### <a name="next-steps"></a>Nästa steg

Lär dig hur du kan hantera paket fångster via portalen genom att besöka [Hantera paket fångst i Azure Portal](network-watcher-packet-capture-manage-portal.md) eller med PowerShell genom att besöka [Hantera paket fångst med PowerShell](network-watcher-packet-capture-manage-powershell.md).

Lär dig hur du skapar proaktiva paket avbildningar baserat på aviseringar för virtuella datorer genom att gå till [skapa en avisering utlöst paket fångst](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png