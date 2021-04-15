---
title: Konfigurera datainsamling för Azure Monitor (förhandsversion)
description: Beskriver hur du skapar en regel för datainsamling för att samla in data från virtuella datorer med hjälp Azure Monitor agenten.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 884f048ac099cfc6b799fe266172a0eecef3db6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478377"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Konfigurera datainsamling för Azure Monitor (förhandsversion)

Regler för datainsamling (DCR) definierar data som kommer Azure Monitor och anger var de ska skickas. Den här artikeln beskriver hur du skapar en regel för datainsamling för att samla in data från virtuella datorer med hjälp Azure Monitor agenten.

En fullständig beskrivning av datainsamlingsregler finns i [Datainsamlingsregler i Azure Monitor (förhandsversion)](data-collection-rule-overview.md).

> [!NOTE]
> Den här artikeln beskriver hur du konfigurerar data för virtuella datorer med Azure Monitor agent som för närvarande är i förhandsversion. Se [Översikt över Azure Monitor för en](agents-overview.md) beskrivning av agenter som är allmänt tillgängliga och hur du använder dem för att samla in data.

## <a name="data-collection-rule-associations"></a>Regelassociationer för datainsamling

Om du vill tillämpa en DCR på en virtuell dator skapar du en association för den virtuella datorn. En virtuell dator kan ha en koppling till flera domänkontrollanter och en DCR kan ha flera associerade virtuella datorer. På så sätt kan du definiera en uppsättning domänkontrollanter som var och en matchar ett visst krav och endast tillämpa dem på de virtuella datorer där de är tillämpliga. 

Tänk dig till exempel en miljö med en uppsättning virtuella datorer som kör ett affärsprogram och andra som kör SQL Server. Du kan ha en standardregel för datainsamling som gäller för alla virtuella datorer och separata datainsamlingsregler som samlar in data specifikt för verksamhetsapplikationen och för SQL Server. Associationerna för de virtuella datorerna till datainsamlingsreglerna skulle se ut ungefär som i följande diagram.

![Diagram som visar virtuella datorer som är värdar för affärsprogram och SQL Server som är associerade med regler för datainsamling med namnet central-i-default och lob-app för affärsprogram och central-i-t-default och s q l för SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Skapa regel och association i Azure Portal

Du kan använda Azure Portal för att skapa en datainsamlingsregel och associera virtuella datorer i din prenumeration med den regeln. Agenten Azure Monitor installeras automatiskt och en hanterad identitet skapas för alla virtuella datorer som inte redan har den installerad.

> [!IMPORTANT]
> Det finns för närvarande ett känt problem där den användar tilldelade identiteten inaktiveras om datainsamlingsregeln skapar en hanterad identitet på en virtuell dator som redan har en användar tilldelad hanterad identitet.

I **Azure Monitor** på Azure Portal väljer du **Datainsamlingsregler** i **avsnittet** Inställningar. Klicka **på Lägg** till för att lägga till en ny datainsamlingsregel och tilldelning.

[![Regler för datainsamling](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Klicka **på Lägg** till för att skapa en ny regel och uppsättning associationer. Ange ett **regelnamn** och ange en **prenumeration** och **en resursgrupp.** Detta anger var DCR ska skapas. De virtuella datorerna och deras associationer kan finnas i valfri prenumeration eller resursgrupp i klientorganisationen.

[![Grunderna i datainsamlingsregel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

På fliken **Virtuella datorer** lägger du till virtuella datorer som ska ha datainsamlingsregeln tillämpad. Både virtuella Azure-datorer Azure Arc och aktiverade servrar i din miljö bör visas. Den Azure Monitor Agent installeras på virtuella datorer som inte redan har den installerad.

[![Virtuella datorer med datainsamlingsregel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

På fliken **Samla in och leverera** klickar du på Lägg till **datakälla för** att lägga till en datakälla och en måluppsättning. Välj **typen Datakälla.** Motsvarande information som ska väljas visas. För prestandaräknare kan du välja från en fördefinierad uppsättning objekt och deras samplingsfrekvens. För händelser kan du välja från en uppsättning loggar eller anläggningar och allvarlighetsgraden. 

[![Grundläggande datakälla](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Om du vill ange andra loggar och prestandaräknare från [de datakällor](azure-monitor-agent-overview.md#data-sources-and-destinations) som stöds för närvarande eller filtrera händelser med hjälp av XPath-frågor väljer du **Anpassad.** Du kan sedan ange en [XPath för ](https://www.w3schools.com/xml/xpath_syntax.asp) alla specifika värden som ska samlas in. Exempel [finns i Exempel på DCR.](data-collection-rule-overview.md#sample-data-collection-rule)

[![Anpassad datakälla](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

På fliken **Mål** lägger du till ett eller flera mål för datakällan. Windows-händelse- och Syslog-datakällor kan bara skicka till Azure Monitor Loggar. Prestandaräknare kan skicka till både Azure Monitor mått och Azure Monitor loggar.

[![Mål](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Klicka **på Lägg till** datakälla och granska + **skapa** för att granska informationen om datainsamlingsregeln och associationen med uppsättningen virtuella datorer. Klicka **på Skapa** för att skapa den.

> [!NOTE]
> När datainsamlingsregeln och associationerna har skapats kan det ta upp till 5 minuter innan data skickas till destinationerna.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Begränsa datainsamling med anpassade XPath-frågor
Eftersom du debiteras för alla data som samlas in på en Log Analytics-arbetsyta bör du endast samla in de data som du behöver. Med grundläggande konfiguration i Azure Portal har du bara begränsad möjlighet att filtrera händelser att samla in. För program- och systemloggar är detta alla loggar med en viss allvarlighetsgrad. För säkerhetsloggar är allt detta granskningsframgång eller alla granskningsfelloggar.

Om du vill ange ytterligare filter måste du använda Anpassad konfiguration och ange en XPath som filtrerar bort de händelser som du inte gör. XPath-poster skrivs i formen `LogName!XPathQuery` . Du kanske till exempel bara vill returnera händelser från programhändelseloggen med händelse-ID:t 1035. XPathQuery för dessa händelser är `*[System[EventID=1035]]` . Eftersom du vill hämta händelserna från programhändelseloggen skulle XPath vara `Application!*[System[EventID=1035]]`

En lista över begränsningar i XPath som stöds av Windows-händelseloggen finns i [XPath 1.0-begränsningar.](/windows/win32/wes/consuming-events#xpath-10-limitations)

> [!TIP]
> Använd PowerShell-cmdleten `Get-WinEvent` med `FilterXPath` parametern för att testa giltigheten för en XPathQuery. Följande skript visar ett exempel.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Om händelser returneras är frågan giltig.
> - Om du får meddelandet *Inga händelser hittades* som matchar de angivna urvalskriterierna. , kan frågan vara giltig, men det finns inga matchande händelser på den lokala datorn.
> - Om du får meddelandet Den *angivna frågan är ogiltig är* frågesyntaxen ogiltig. 

I följande tabell visas exempel på filtrering av händelser med hjälp av en anpassad XPath.

| Beskrivning |  Xpath |
|:---|:---|
| Samla endast in systemhändelser med händelse-ID = 4648 |  `System!*[System[EventID=4648]]`
| Samla endast in systemhändelser med händelse-ID = 4648 och processnamnet consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Samla in alla kritiska händelser, fel-, varnings- och informationshändelser från systemhändelseloggen förutom händelse-ID = 6 (drivrutinen har lästs in) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Samla in alla lyckade och misslyckade säkerhetshändelser utom händelse-ID 4624 (lyckad inloggning) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Skapa regel och association med REST API

Följ stegen nedan för att skapa en regel för datainsamling och associationer med hjälp av REST API.

1. Skapa DCR-filen manuellt med JSON-formatet som visas i [Exempel på DCR.](data-collection-rule-overview.md#sample-data-collection-rule)

2. Skapa regeln med hjälp av [REST API](/rest/api/monitor/datacollectionrules/create#examples).

3. Skapa en association för varje virtuell dator till datainsamlingsregeln med hjälp av [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Skapa association med Resource Manager mall

Du kan skapa en association mellan en virtuell Azure-dator Azure Arc en aktiverad server med hjälp av Resource Manager mall. Se [Resource Manager mallexempel för datainsamlingsregler i Azure Monitor](./resource-manager-data-collection-rules.md) för exempelmallar.



## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor Agent](azure-monitor-agent-overview.md).
- Läs mer om [datainsamlingsregler.](data-collection-rule-overview.md)
