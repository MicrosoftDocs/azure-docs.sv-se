---
title: Designa distributionen av Azure Monitors loggar | Microsoft Docs
description: I den här artikeln beskrivs överväganden och rekommendationer för kunder som förbereder distributionen av en arbets yta i Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: a889275782388781eadffb7cf0a24771bf6e9e4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030842"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Utforma en distribution med Azure Monitor-loggar

Azure Monitor lagrar [loggdata](data-platform-logs.md) i en Log Analytics arbets yta, som är en Azure-resurs och en behållare där data samlas in, aggregeras och fungerar som en administrativ gränser. Även om du kan distribuera en eller flera arbets ytor i din Azure-prenumeration finns det flera saker du bör känna till för att säkerställa att din första distribution följer våra rikt linjer för att ge dig en kostnads effektiv, hanterbar och skalbar distribution som uppfyller organisationens behov.

Data i en arbets yta är indelade i tabeller, där var och en lagrar olika typer av data och har en egen unik uppsättning egenskaper baserade på den resurs som genererar data. De flesta data källor skrivs till sina egna tabeller i en Log Analytics-arbetsyta.

![Exempel på arbets ytans data modell](./media/design-logs-deployment/logs-data-model-01.png)

En Log Analytics arbets yta innehåller:

* En geografisk plats för data lagring.
* Data isolering genom att ge olika användare åtkomst rättigheter efter en av våra rekommenderade design strategier.
* Omfattning för konfiguration av inställningar som [pris nivå](./manage-cost-storage.md#changing-pricing-tier), [kvarhållning](./manage-cost-storage.md#change-the-data-retention-period)och [data capping](./manage-cost-storage.md#manage-your-maximum-daily-data-volume).

Arbets ytor finns i fysiska kluster. Som standard skapar och hanterar systemet dessa kluster. Kunder som inhämtar mer än 4 TB/dag förväntas skapa sina egna dedikerade kluster för sina arbets ytor – den ger bättre kontroll och högre inmatnings takt.

Den här artikeln innehåller en detaljerad översikt över design-och migrerings överväganden, åtkomst kontroll översikt och en förståelse för de design implementeringar vi rekommenderar för din IT-organisation.



## <a name="important-considerations-for-an-access-control-strategy"></a>Viktiga överväganden för en strategi för åtkomst kontroll

Att identifiera hur många arbets ytor du behöver påverkas av ett eller flera av följande krav:

* Du är ett globalt företag och du behöver logga data som lagras i vissa regioner för data suveränitet eller efterföljandekrav.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en arbetsyta i samma region som de Azure-resurser som den hanterar.
* Du hanterar flera avdelningar eller affärs grupper och du vill att var och en ska se sina egna data, men inte data från andra. Det finns inte heller något affärs krav för en konsol IDE rad kors organisation eller en koncern företags grupp.

IT-organisationer idag modelleras efter antingen en centraliserad, decentraliserad eller en mellan hybrid hybrider av båda strukturerna. Därför har följande distributions modeller för arbets ytor ofta använts för att mappa till någon av dessa organisations strukturer:

* **Centraliserad**: alla loggar lagras på en central arbets yta och administreras av ett enda team, med Azure Monitor tillhandahålla differentierad åtkomst per team. I det här scenariot är det enkelt att hantera, söka bland resurser och korsa-korrelerade loggar. Arbets ytan kan växa avsevärt beroende på mängden data som samlas in från flera resurser i din prenumeration, med ytterligare administrations kostnader för att upprätthålla åtkomst kontroll till olika användare. Den här modellen kallas "hubb och eker".
* **Decentraliserad**: varje team har sin egen arbets yta som skapats i en resurs grupp som de äger och hanterar, och loggdata åtskiljs per resurs. I det här scenariot kan arbets ytan vara säker och åtkomst kontrollen är konsekvent med resurs åtkomst, men det är svårt att korsa korrelerande loggar. Användare som behöver en bred vy över många resurser kan inte analysera data på ett meningsfullt sätt.
* **Hybrid**: kraven på säkerhets gransknings efterlevnad försäkrar ytterligare det här scenariot eftersom många organisationer implementerar båda distributions modellerna parallellt. Detta leder ofta till en komplicerad, dyr och hårt hanterad konfiguration med luckor i loggar täckning.

När du använder Log Analyticss agenter för att samla in data måste du förstå följande för att kunna planera agent distributionen:

* Om du vill samla in data från Windows-agenter kan du [Konfigurera varje agent så att den rapporterar till en eller flera arbets ytor](./../agents/agent-windows.md), även om den rapporterar till en System Center Operations Manager hanterings grupp. Windows-agenten kan rapportera upp till fyra arbets ytor.
* Linux-agenten stöder inte Multi-värdar och kan bara rapportera till en enda arbets yta.

Om du använder System Center Operations Manager 2012 R2 eller senare:

* Varje Operations Manager hanterings grupp kan [endast anslutas till en arbets yta](../agents/om-agents.md). 
* Linux-datorer som rapporterar till en hanterings grupp måste konfigureras att rapportera direkt till en Log Analytics-arbetsyta. Om Linux-datorerna redan rapporterar direkt till en arbets yta och du vill övervaka dem med Operations Manager följer du dessa steg för att [rapportera till en Operations Manager hanterings grupp](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group). 
* Du kan installera Log Analytics Windows-agenten på Windows-datorn och låta den rapportera till båda Operations Manager integrerade med en arbets yta och en annan arbets yta.

## <a name="access-control-overview"></a>Översikt över åtkomstkontroll

Med rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du endast bevilja användare och grupper den mängd åtkomst som de behöver för att arbeta med övervaknings data i en arbets yta. På så sätt kan du justera med din IT-organisations drifts modell med en enda arbets yta för att lagra insamlade data aktiverade på alla dina resurser. Till exempel ger du till gång till ditt team som ansvarar för infrastruktur tjänster som finns på Azure Virtual Machines (VM) och därför har de enbart åtkomst till de loggar som genereras av de virtuella datorerna. Det här följer vår nya resurs kontexts logg modell. Basen för den här modellen är för varje logg post som skickas av en Azure-resurs, den kopplas automatiskt till den här resursen. Loggarna vidarebefordras till en central arbets yta som uppfyller omfattning och Azure RBAC baserat på resurserna.

De data som en användare har åtkomst till bestäms av en kombination av faktorer som anges i följande tabell. Var och en beskrivs i avsnitten nedan.

| Faktor | Beskrivning |
|:---|:---|
| [Åtkomstläge](#access-mode) | Metod som användaren använder för att få åtkomst till arbets ytan.  Definierar omfattningen av tillgängliga data och åtkomst kontroll läge som används. |
| [Åtkomstkontrolläge](#access-control-mode) | Inställning på arbets ytan som definierar om behörigheter tillämpas på arbets ytan eller resurs nivån. |
| [Behörigheter](./manage-access.md) | Behörigheter som tillämpas på enskilda eller grupper av användare för arbets ytan eller resursen. Definierar vilka data som användaren kommer att ha åtkomst till. |
| [Tabell nivå Azure RBAC](./manage-access.md#table-level-azure-rbac) | Valfria detaljerade behörigheter som gäller för alla användare oavsett åtkomst läge eller åtkomst kontrol läge. Definierar vilka data typer som en användare kan komma åt. |

## <a name="access-mode"></a>Åtkomstläge

*Åtkomst Läget* avser hur en användare kommer åt en Log Analytics-arbetsyta och definierar omfattningen av de data som de har åtkomst till. 

Användare har två alternativ för att komma åt data:

* **Arbets ytans kontext**: du kan visa alla loggar i arbets ytan du har behörighet till. Frågor i det här läget är begränsade till alla data i alla tabeller i arbets ytan. Detta är det åtkomst läge som används när loggar nås med arbets ytan som omfång, till exempel när du väljer **loggar** på **Azure Monitor** -menyn i Azure Portal.

    ![Log Analytics kontext från arbets ytan](./media/design-logs-deployment/query-from-workspace.png)

* **Resurs kontext**: när du öppnar arbets ytan för en viss resurs, resurs grupp eller prenumeration, t. ex. När du väljer **loggar** från en resurs meny i Azure Portal, kan du Visa loggar för endast resurser i alla tabeller som du har åtkomst till. Frågor i det här läget är begränsade till data som är associerade med den resursen. Det här läget möjliggör också detaljerad Azure RBAC.

    ![Log Analytics kontext från resurs](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Loggarna är bara tillgängliga för resurs kontext frågor om de var korrekt kopplade till den aktuella resursen. För närvarande har följande resurser begränsningar:
    > - Datorer utanför Azure – stöds endast för resurs kontext via Azure- [båge för servrar](../../azure-arc/servers/index.yml)
    > - Service Fabric
    > - Application Insights – stöds endast för resurs kontext när du använder [arbets ytans baserade Application Insights-resurs](../app/create-workspace-resource.md)
    >
    > Du kan testa om loggarna är korrekt kopplade till sin resurs genom att köra en fråga och granska de poster som du är intresse rad av. Om rätt resurs-ID är i egenskapen [_ResourceId](./log-standard-columns.md#_resourceid) är data tillgängliga för resursbaserade frågor.

Azure Monitor anger automatiskt rätt läge beroende på vilken kontext du utför loggs ökningen från. Omfattningen visas alltid i det övre vänstra avsnittet av Log Analytics.

### <a name="comparing-access-modes"></a>Jämför åtkomst lägen

I följande tabell sammanfattas åtkomst lägena:

| Problem | Arbetsytans kontext | Resurskontext |
|:---|:---|:---|
| Vem är varje modell avsedd för? | Central administration. Administratörer som behöver konfigurera data insamling och användare som behöver åtkomst till en mängd olika resurser. Krävs också för användare som behöver åtkomst till loggar för resurser utanför Azure. | Program team. Administratörer av Azure-resurser som övervakas. |
| Vad kräver en användare att visa loggar? | Behörigheter till arbets ytan. Se **behörigheter för arbets ytan** i [Hantera åtkomst med hjälp av arbets ytans behörigheter](./manage-access.md#manage-access-using-workspace-permissions). | Läs åtkomst till resursen. Se **resurs behörigheter** i [Hantera åtkomst med Azure-behörigheter](./manage-access.md#manage-access-using-azure-permissions). Behörigheter kan ärvas (till exempel från resurs gruppen innehåller) eller tilldelas direkt till resursen. Behörighet till loggarna för resursen tilldelas automatiskt. |
| Vad är behörighets omfånget? | Platsen. Användare med åtkomst till arbets ytan kan fråga alla loggar i arbets ytan från tabeller som de har behörighet till. Se [tabell åtkomst kontroll](./manage-access.md#table-level-azure-rbac) | Azure-resurs. Användaren kan söka i loggar efter vissa resurser, resurs grupper eller prenumerationer som de har åtkomst till från en arbets yta, men inte skicka frågor till loggar för andra resurser. |
| Hur kan användare få åtkomst till loggar? | <ul><li>Starta **loggar** från **Azure Monitor** -menyn.</li></ul> <ul><li>Starta **loggar** från **Log Analytics arbets ytor**.</li></ul> <ul><li>Från Azure Monitor [arbets böcker](../visualizations.md#workbooks).</li></ul> | <ul><li>Starta **loggar** från menyn för Azure-resursen</li></ul> <ul><li>Starta **loggar** från **Azure Monitor** -menyn.</li></ul> <ul><li>Starta **loggar** från **Log Analytics arbets ytor**.</li></ul> <ul><li>Från Azure Monitor [arbets böcker](../visualizations.md#workbooks).</li></ul> |

## <a name="access-control-mode"></a>Åtkomstkontrolläge

*Åtkomst kontrol läget* är en inställning på varje arbets yta som definierar hur behörigheter bestäms för arbets ytan.

* **Kräv behörigheter för arbets yta**: det här kontroll läget tillåter inte detaljerad Azure RBAC. För att en användare ska kunna komma åt arbets ytan måste de beviljas behörigheter till arbets ytan eller till vissa tabeller.

    Om en användare har åtkomst till arbets ytan efter arbets ytans kontext läge, har de åtkomst till alla data i alla tabeller som de har beviljats åtkomst till. Om en användare har åtkomst till arbets ytan efter resurs kontext läge, har de åtkomst till data för resursen i alla tabeller som de har beviljats åtkomst till.

    Detta är standardinställningen för alla arbets ytor som skapats före mars 2019.

* **Använd resurs-eller arbets ytans behörigheter**: i det här kontroll läget kan du göra detaljerade Azure RBAC. Användare kan beviljas åtkomst till data som är associerade med resurser som de kan visa genom att tilldela Azure- `read` behörighet. 

    När en användare ansluter till arbets ytan i Sammanhangs läge för arbets ytan gäller arbets ytans behörigheter. När en användare ansluter till arbets ytan i resurs kontext läge, verifieras endast resurs behörigheter och behörigheter för arbets ytan ignoreras. Aktivera Azure RBAC för en användare genom att ta bort dem från arbets ytans behörigheter och tillåta att deras resurs behörigheter identifieras.

    Detta är standardinställningen för alla arbets ytor som skapats efter 2019 mars.

    > [!NOTE]
    > Om en användare bara har resurs behörigheter till arbets ytan kan de bara komma åt arbets ytan med resurs kontext läge förutsatt att arbets ytans åtkomst läge är inställt på att **använda resurs-eller arbets ytans behörigheter**.

Information om hur du ändrar åtkomst kontrol läget i portalen, med PowerShell eller med hjälp av en Resource Manager-mall finns i [Konfigurera åtkomst kontrol läge](./manage-access.md#configure-access-control-mode).

## <a name="scale-and-ingestion-volume-rate-limit"></a>Skalning och inläsning av volym hastighets gräns

Azure Monitor är en hög skalbar data tjänst som hanterar tusentals kunder som skickar petabyte data varje månad i en växande takt. Arbets ytor är inte begränsade i lagrings utrymmet och kan växa till petabyte av data. Det finns inget behov av att dela arbets ytor på grund av skalning.

För att skydda och isolera Azure Monitor kunder och dess server dels infrastruktur finns en standard gräns för inmatnings frekvens som är utformad för att skydda mot toppar och översvämnings situationer. Frekvens gränsen är cirka **6 GB/minut** och har utformats för att aktivera normal inmatning. Mer information om mått för inläsnings volym gränser finns i [Azure Monitor tjänst begränsningar](../service-limits.md#data-ingestion-volume-rate).

Kunder som inhämtar mindre än 4 TB/dag kommer vanligt vis inte att uppfylla dessa gränser. Kunder som inhämtar högre volymer eller som har toppar som en del av sin normala verksamhet, bör överväga att flytta till [dedikerade kluster](./logs-dedicated-clusters.md) där inmatnings takten kan höjas.

När gräns värdet för inmatnings frekvensen aktive ras eller når 80% av tröskelvärdet läggs en händelse till i *Åtgärds* tabellen i din arbets yta. Vi rekommenderar att du övervakar det och skapar en avisering. Se mer information i [volym hastighet för data inmatning](../service-limits.md#data-ingestion-volume-rate).


## <a name="recommendations"></a>Rekommendationer

![Design exempel för resurs kontext](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Det här scenariot täcker en enskild design av arbets ytan i IT-organisationens prenumeration som inte är begränsad av data suveränitet eller reglerande krav eller som måste mappas till de regioner som resurserna distribueras i. Det ger din organisations säkerhets-och IT-administratör möjlighet att utnyttja den förbättrade integreringen med Azure Access Management och säkrare åtkomst kontroll.

Alla resurser, övervaknings lösningar och insikter, till exempel Application Insights och VM-insikter, stöd för infrastruktur och program som underhålls av de olika teamen konfigureras för att vidarebefordra insamlade loggdata till IT-organisationens centraliserade delade arbets yta. Användare i varje team beviljas åtkomst till loggar för resurser som de har fått åtkomst till.

När du har distribuerat din arbets ytas arkitektur kan du framtvinga detta på Azure-resurser med [Azure policy](../../governance/policy/overview.md). Det är ett sätt att definiera principer och se till att dina Azure-resurser är kompatibla så att de skickar alla resurs loggar till en viss arbets yta. Till exempel kan du använda befintliga principer som utvärderar arbets ytans efterlevnad och rapport resultat, eller anpassa för att åtgärda om det inte är kompatibelt, med Azure Virtual Machines eller Virtual Machine Scale Sets.  

## <a name="workspace-consolidation-migration-strategy"></a>Arbets yta konsoliderings strategi för migrering

För kunder som redan har distribuerat flera arbets ytor och som är intresserade av att konsolidera till resurs kontexten, rekommenderar vi att du tar en stegvis metod för att migrera till den rekommenderade åtkomst modellen och du inte försöker uppnå det här snabbt eller aggressivt. Genom att följa en stegvis metod för att planera, migrera, validera och dra tillbaka efter en rimlig tids linje kan du undvika eventuella oplanerade incidenter eller oväntade konsekvenser i moln åtgärderna. Om du inte har någon data lagrings princip för efterlevnad eller av affärs skäl måste du utvärdera hur lång tid det tar att spara data i arbets ytan som du migrerar från under processen. När du konfigurerar om resurser för att rapportera till den delade arbets ytan kan du fortfarande analysera data i den ursprungliga arbets ytan vid behov. När migreringen är klar kan du ta bort den om du är reglerad att behålla data på den ursprungliga arbets ytan innan kvarhållningsperioden slutar att gälla.

När du planerar migreringen till den här modellen bör du tänka på följande:

* Ta reda på vilka bransch regler och interna principer som gäller datakvarhållning som du måste följa.
* Se till att dina program team kan arbeta i de befintliga resurs kontext funktionerna.
* Identifiera den åtkomst som beviljas resurser för dina program team och testa i en utvecklings miljö innan du implementerar i produktion.
* Konfigurera arbets ytan för att aktivera **Använd resurs-eller arbets ytans behörigheter**.
* Ta bort program Teams behörighet att läsa och fråga arbets ytan.
* Aktivera och konfigurera övervaknings lösningar, insikter, till exempel behållar insikter och/eller Azure Monitor for VMs, ditt Automation-konto och hanterings lösningar som Uppdateringshantering, starta/stoppa virtuella datorer osv., som har distribuerats på den ursprungliga arbets ytan.

## <a name="next-steps"></a>Nästa steg

Om du vill implementera säkerhets behörigheter och kontroller som rekommenderas i den här guiden granskar du [Hantera åtkomst till loggar](./manage-access.md).
