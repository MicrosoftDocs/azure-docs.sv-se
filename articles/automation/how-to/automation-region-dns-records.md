---
title: Azure datacenter DNS-poster som används av Azure Automation | Microsoft Docs
description: Den här artikeln innehåller de DNS-poster som krävs av Azure Automation funktioner vid begränsning av kommunikation till en speciell Azure-region som är värd för Automation-kontot.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: 8630afa7410aad81a7a3c61540fc74702fc6481c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575989"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>DNS-poster för Azure-regioner som används av Azure Automation

Tjänsten [Azure Automation](../automation-intro.md) använder ett antal DNS-poster för att ansluta till tjänsten. Om du har ett Automation-konto som har definierats för en viss region kan du begränsa kommunikationen till det regionala data centret. Du kan behöva känna till dessa poster för att tillåta att följande automatiserings funktioner fungerar när den finns bakom en brand vägg:

* Hybrid Runbook Worker
* Statuskonfiguration
* Webhooks

>[!NOTE]
>Linux Hybrid Runbook Worker registreringen Miss kommer med de nya posterna om den inte är version 1.6.10.2 eller högre. Du måste uppgradera till en nyare version av [Log Analytics-agenten för Linux](../../azure-monitor/agents/agent-linux.md) för att datorn ska kunna ta emot en uppdaterad version av arbets rollen och använda dessa nya poster. Befintliga datorer fortsätter att fungera utan problem.  

## <a name="dns-records-per-region"></a>DNS-poster per region

Följande tabell innehåller DNS-posten för varje region.

>[!NOTE]
>Även om listan över automatiserings-DNS-poster som anges här har dragits tillbaka, är de fortfarande funktionella, så att du kan migrera till de nya poster som visas under [stöd för privat länk](#support-for-private-link) och förhindra problem med automatiserings processerna.

| **Region** | **DNS-post** |
| --- | --- |
| Australien, centrala |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australien, östra |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sydöstra Australien |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Kanada, centrala |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| USA, östra 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Japan, östra |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europa, norra |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| USA, södra centrala |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Storbritannien, södra | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov, Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| USA, västra centrala | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa, västra |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| USA, västra 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Stöd för privat länk

För att stödja [privat länk](../../private-link/private-link-overview.md) i Azure Automation har DNS-posterna för alla data Center som stöds uppdaterats. I stället för landsspecifika URL: er är URL: erna ett Automation-konto.

| **Region** | **DNS-post** |
| --- | --- |
| USA, västra centrala |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| USA, västra |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| USA, västra 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| Central US |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| USA, södra centrala |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| USA, norra centrala |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| East US |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| USA, östra 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Kanada, centrala |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| Europa, västra |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Europa, norra |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Sydostasien |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Asien, östra |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| Indien, centrala |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Japan, östra |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Sydkorea, centrala |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Sydöstra Australien |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Australien, östra |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Australien, centrala |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| Storbritannien, södra |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| Frankrike, centrala |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Sydafrika, norra |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Brasilien, södra |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| Kina, norra |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| Kina, norra 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| Kina, östra 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| US Gov, Virginia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov, Texas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov, Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

Ersätt `<accountId>` i DNS-posten med GUID som representerar ditt Automation-konto-ID från värde- **URL**: en. Du kan hämta det ID som krävs från **nycklar** under **konto inställningar** i Azure Portal.

![Primär nyckel sida för Automation-konto](./media/automation-region-dns-records/automation-account-keys.png)

Kopiera värdet efter *konton/* från **URL** -fältet – `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`

> [!NOTE]
> Alla webhook-och agentservice DNS-poster har uppdaterats till den nya format-DNS-posten för att stödja privat länk. För JRDS DNS-poster stöds både gamla och nya DNS-poster för formatmall. Om du inte använder en privat länk ser du de gamla DNS-posterna, medan de som använder privat länk kommer att se ny stil för DNS-poster.

Vi rekommenderar att du använder de adresser som anges när du definierar [undantag](../automation-runbook-execution.md#exceptions). Om du vill ha en lista över regions-IP-adresser i stället för region namn kan du hämta JSON-filen från Microsoft Download Center för följande moln miljöer:

* [Azure IP-intervall och service märken – Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
* [Azure IP-intervall och service märken – Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
* [Azure IP-intervall och service märken – Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
* [Azure IP-intervall och service märken – Azure Kina Vianet 21](https://www.microsoft.com/download/details.aspx?id=57062)

IP-adress filen visar de IP-adressintervall som används i Microsoft Azure Data Center. Den innehåller beräknings-, SQL-och lagrings intervall och återspeglar för närvarande distribuerade intervall och eventuella kommande ändringar i IP-intervallen. Nya intervall som visas i filen används inte i Data Center i minst en vecka.

Det är en bra idé att ladda ned den nya IP-adressen varje vecka. Uppdatera sedan webbplatsen för att identifiera tjänster som körs i Azure på rätt sätt.

> [!NOTE]
> Om du använder Azure ExpressRoute måste du komma ihåg att IP-adressutrymmet används för att uppdatera Border Gateway Protocol-annonsering (BGP) för Azure-utrymmet under den första veckan i varje månad.

## <a name="next-steps"></a>Nästa steg

* Information om hur du felsöker dina hybrid Runbook Worker finns i [felsöka hybrid Runbook Worker problem](../troubleshoot/hybrid-runbook-worker.md#general).

* Information om hur du felsöker problem med tillstånds konfiguration finns i [Felsöka problem med tillstånds konfiguration](../troubleshoot/desired-state-configuration.md).