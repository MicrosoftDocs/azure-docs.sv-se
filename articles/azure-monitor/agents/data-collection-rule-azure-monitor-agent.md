---
title: Konfigurera data insamling för Azure Monitor agenten (för hands version)
description: Beskriver hur du skapar en data insamlings regel för att samla in data från virtuella datorer med hjälp av Azure Monitor-agenten.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 2a91062a701ca1b07f47f381a04cdf06c57c5746
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721536"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Konfigurera data insamling för Azure Monitor agenten (för hands version)

Data insamlings regler (DCR) definiera data som kommer till Azure Monitor och ange var de ska skickas. Den här artikeln beskriver hur du skapar en data insamlings regel för att samla in data från virtuella datorer med hjälp av Azure Monitor agenten.

En fullständig beskrivning av regler för data insamling finns [i data insamlings regler i Azure Monitor (för hands version)](data-collection-rule-overview.md).

> [!NOTE]
> Den här artikeln beskriver hur du konfigurerar data för virtuella datorer med Azure Monitor-agenten som för närvarande är en för hands version. Se [Översikt över Azure Monitor agenter](agents-overview.md) för en beskrivning av agenter som är allmänt tillgängliga och hur de används för att samla in data.

## <a name="data-collection-rule-associations"></a>Regel associationer för data insamling

Om du vill tillämpa en DCR på en virtuell dator skapar du en Association för den virtuella datorn. En virtuell dator kan ha en association till flera DCRs och en DCR kan ha flera virtuella datorer kopplade till den. På så sätt kan du definiera en uppsättning DCRs, var och en matcha ett visst krav och tillämpa dem på enbart de virtuella datorer där de gäller. 

Överväg till exempel en miljö med en uppsättning virtuella datorer som kör ett affärs program och andra som kör SQL Server. Du kan ha en standard regel för data insamling som gäller för alla virtuella datorer och separata data insamlings regler som samlar in data specifikt för affärs programmet och för SQL Server. Associationerna för de virtuella datorerna till data insamlings reglerna ser ut ungefär som i följande diagram.

![Diagrammet visar virtuella datorer som är värdar för affärs program och SQL Server kopplade till data insamlings regler med namnet Central-i t-standard och LOB-app för affärs program och Central-i-t-standard och s q l för SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Skapa regel och Association i Azure Portal

Du kan använda Azure Portal för att skapa en data insamlings regel och koppla virtuella datorer i din prenumeration till den regeln. Den Azure Monitor agenten installeras automatiskt och en hanterad identitet skapas för virtuella datorer som inte redan har den installerad.

> [!IMPORTANT]
> Det finns för närvarande ett känt problem där data insamlings regeln skapar en hanterad identitet på en virtuell dator som redan har en tilldelad hanterad identitet. den tilldelade identiteten är inaktive rad.

I **Azure Monitor** -menyn i Azure Portal väljer du **data insamlings regler** i avsnittet **Inställningar** . Klicka på **Lägg** till för att lägga till en ny data insamlings regel och tilldelning.

[![Data insamlings regler](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Klicka på **Lägg till** för att skapa en ny regel och en uppsättning associationer. Ange ett **regel namn** och ange en **prenumeration** och en **resurs grupp**. Detta anger var DCR ska skapas. De virtuella datorerna och deras associationer kan finnas i alla prenumerationer eller resurs grupper i klienten.

[![Grunder för data insamlings regel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

På fliken **Virtual Machines (virtuella datorer** ) lägger du till virtuella datorer som ska använda regeln för data insamling. Både virtuella Azure-datorer och Azure Arc-aktiverade servrar i din miljö bör listas. Azure Monitor Agent installeras på virtuella datorer som inte redan har den installerad.

[![Data insamlings regel för virtuella datorer](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

På fliken **samla in och leverera** klickar du på **Lägg till data källa** för att lägga till en data källa och mål uppsättning. Välj en **typ av data källa** så visas motsvarande information som ska väljas. För prestanda räknare kan du välja från en fördefinierad uppsättning objekt och deras samplings frekvens. För händelser kan du välja bland en uppsättning loggar eller anläggningar och allvarlighets grad. 

[![Grundläggande data Källa](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Om du vill ange andra loggar och prestanda räknare från de [data källor som stöds](azure-monitor-agent-overview.md#data-sources-and-destinations) eller filtrera händelser med hjälp av XPath-frågor väljer du **anpassad**. Du kan sedan ange en [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) för alla angivna värden som ska samlas in. Se exempel på [DCR](data-collection-rule-overview.md#sample-data-collection-rule) .

[![Anpassad data Källa](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

På fliken **mål** lägger du till en eller flera mål för data källan. Windows-händelse och syslog-datakällor kan bara skicka till Azure Monitor loggar. Prestanda räknare kan skicka till både Azure Monitor mått och Azure Monitor loggar.

[![Mål](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Klicka på **Lägg till data källa** och **granska sedan + skapa** för att granska informationen för data insamlings regeln och associationen med uppsättningen med virtuella datorer. Skapa genom att klicka på **skapa** .

> [!NOTE]
> När data insamlings regeln och associationerna har skapats kan det ta upp till 5 minuter innan data skickas till målen.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Begränsa data insamling med anpassade XPath-frågor
Eftersom du debiteras för data som samlas in i en Log Analytics arbets yta bör du endast samla in de data som du behöver. Med hjälp av grundläggande konfiguration i Azure Portal har du bara begränsad möjlighet att filtrera händelser som ska samlas in. För program-och system loggar är detta alla loggar med en viss allvarlighets grad. För säkerhets loggar är detta alla lyckade eller misslyckade gransknings loggar.

Om du vill ange ytterligare filter måste du använda Anpassad konfiguration och ange en XPath som filtrerar bort händelser som du inte vill. XPath-poster skrivs i formuläret `LogName!XPathQuery` . Du kanske till exempel bara vill returnera händelser från program händelse loggen med händelse-ID 1035. XPathQuery för dessa händelser är `*[System[EventID=1035]]` . Eftersom du vill hämta händelserna från program händelse loggen skulle XPath vara `Application!*[System[EventID=1035]]`

Se [xpath 1,0-begränsningar](/windows/win32/wes/consuming-events#xpath-10-limitations) för en lista över begränsningar i XPath som stöds av Windows händelse logg.

> [!TIP]
> Använd PowerShell-cmdleten `Get-WinEvent` med `FilterXPath` parametern för att testa giltigheten för en XPathQuery. Följande skript visar ett exempel.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Om händelser returneras är frågan giltig.
> - Om du får meddelandet det *gick inte att hitta några händelser som matchar de angivna urvalskriterierna.* frågan kan vara giltig, men det finns inga matchande händelser på den lokala datorn.
> - Om du får meddelandet *den angivna frågan är ogiltig* är frågesyntaxen ogiltig. 

I följande tabell visas exempel för att filtrera händelser med hjälp av en anpassad XPath.

| Beskrivning |  XPath |
|:---|:---|
| Samla endast in system händelser med händelse-ID = 4648 |  `System!*[System[EventID=4648]]`
| Samla endast in system händelser med händelse-ID = 4648 och process namnet consent.exe |  `System!*[System[(EventID=4648) and (EventData[@Name='ProcessName']='C:\Windows\System32\consent.exe')]]`
| Samla in alla kritiska händelser, fel, varnings-och informations händelser från system händelse loggen utom händelse-ID = 6 (driv rutin inläst) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Samla in alla lyckade och misslyckade säkerhets händelser förutom händelse-ID 4624 (lyckad inloggning) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Skapa regel och Association med REST API

Följ stegen nedan för att skapa en data insamlings regel och associationer med hjälp av REST API.

1. Skapa DCR-filen manuellt med JSON-formatet som visas i [exempel-DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Skapa regeln med hjälp av [REST API](/rest/api/monitor/datacollectionrules/create#examples).

3. Skapa en koppling för varje virtuell dator till data insamlings regeln med hjälp av [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Skapa Association med Resource Manager-mall

Du kan inte skapa en data insamlings regel med hjälp av en Resource Manager-mall, men du kan skapa en koppling mellan en virtuell Azure-dator eller Azure Arc-aktiverad server med hjälp av en Resource Manager-mall. Se exempel [på Resource Manager-mallar för data insamlings regler i Azure Monitor](./resource-manager-data-collection-rules.md) för exempel-mallar.



## <a name="next-steps"></a>Nästa steg

- Läs mer om den [Azure Monitor agenten](azure-monitor-agent-overview.md).
- Lär dig mer om [data insamlings regler](data-collection-rule-overview.md).
