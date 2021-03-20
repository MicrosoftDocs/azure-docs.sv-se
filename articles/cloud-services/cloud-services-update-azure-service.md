---
title: Så här uppdaterar du en moln tjänst (klassisk) | Microsoft Docs
description: Lär dig hur du uppdaterar moln tjänster i Azure. Lär dig hur en uppdatering av en moln tjänst fortsätter för att säkerställa tillgängligheten.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5d85003ca7b4307c308914484502ae03269f66ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741119"
---
# <a name="how-to-update-an-azure-cloud-service-classic"></a>Så här uppdaterar du en Azure Cloud Service (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Att uppdatera en moln tjänst, inklusive både dess roller och gäst operativ system, är en process i tre steg. Först måste binärfilerna och konfigurationsfilerna för den nya moln tjänsten eller operativ system versionen överföras. Därefter reserverar Azure beräknings-och nätverks resurser för moln tjänsten baserat på kraven i den nya moln tjänst versionen. Slutligen utför Azure en löpande uppgradering för att stegvis uppdatera klienten till den nya versionen eller gäst operativ systemet, samtidigt som du behåller din tillgänglighet. I den här artikeln beskrivs information om det här sista steget – den löpande uppgraderingen.

## <a name="update-an-azure-service"></a>Uppdatera en Azure-tjänst
Azure ordnar roll instanserna i logiska grupperingar som kallas uppgraderings domäner (UD). Uppgraderings domäner (UD) är logiska uppsättningar av roll instanser som uppdateras som en grupp.  Azure uppdaterar en moln tjänst en UD i taget, vilket gör att instanser i andra UDs kan fortsätta betjäna trafiken.

Standard antalet uppgraderings domäner är 5. Du kan ange ett annat antal uppgraderings domäner genom att inkludera attributet upgradeDomainCount i tjänstens definitions fil (. csdef). Mer information om attributet upgradeDomainCount finns i [definitions schema för Azure-Cloud Services (. csdef-fil)](./schema-csdef-file.md).

När du utför en uppdatering på plats av en eller flera roller i tjänsten uppdaterar Azure uppsättningar roll instanser enligt den uppgraderings domän som de tillhör. Azure uppdaterar alla instanser i en specifik uppgraderings domän – stoppar dem, uppdaterar dem och aktiverar dem igen. sedan flyttas de vidare till nästa domän. Genom att bara stoppa instanserna som körs i den aktuella uppgraderings domänen, ser Azure till att en uppdatering sker med minsta möjliga påverkan på tjänsten som körs. Mer information finns i [hur uppdateringen fortsätter](#howanupgradeproceeds) senare i den här artikeln.

> [!NOTE]
> Termerna **Uppdatera** och **Uppgradera** skiljer sig något åt i sammanhanget Azure, men de kan användas utbytbart för processer och beskrivningar av funktionerna i det här dokumentet.
>
>

Tjänsten måste definiera minst två instanser av en roll för att rollen ska uppdateras på plats utan drift avbrott. Om tjänsten endast består av en instans av en roll kommer tjänsten att vara otillgänglig tills uppdateringen på plats har avslut ATS.

I det här avsnittet beskrivs följande information om Azure-uppdateringar:

* [Tillåtna tjänst ändringar under en uppdatering](#AllowedChanges)
* [Så här fortsätter uppgraderingen](#howanupgradeproceeds)
* [Återställa en uppdatering](#RollbackofanUpdate)
* [Initiera flera Mutations åtgärder i en pågående distribution](#multiplemutatingoperations)
* [Distribution av roller över uppgraderings domäner](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Tillåtna tjänst ändringar under en uppdatering
I följande tabell visas de tillåtna ändringarna av en tjänst under en uppdatering:

| Ändringar som tillåts vara värd för, tjänster och roller | Uppdatering på plats | Mellanlagrad (VIP-växling) | Ta bort och distribuera om |
| --- | --- | --- | --- |
| Operativsystemversion |Ja |Ja |Ja |
| .NET-förtroende nivå |Ja |Ja |Ja |
| Storlek<sup>1</sup> för virtuell dator |Ja<sup>2</sup> |Ja |Ja |
| Inställningar för lokal lagring |Öka bara<sup>2</sup> |Ja |Ja |
| Lägga till eller ta bort roller i en tjänst |Ja |Ja |Ja |
| Antal instanser av en viss roll |Ja |Ja |Ja |
| Antal eller typ av slut punkter för en tjänst |Ja<sup>2</sup> |Inga |Ja |
| Namn och värden för konfigurations inställningar |Ja |Ja |Ja |
| Värden (men inte namn) för konfigurations inställningar |Ja |Ja |Ja |
| Lägg till nya certifikat |Ja |Ja |Ja |
| Ändra befintliga certifikat |Ja |Ja |Ja |
| Distribuera ny kod |Ja |Ja |Ja |

<sup>1</sup> storlek begränsas till den delmängd av storlekarna som är tillgängliga för moln tjänsten.

<sup>2</sup> kräver Azure SDK 1,5 eller senare versioner.

> [!WARNING]
> Om du ändrar storleken på den virtuella datorn kommer lokala data att förstöras.
>
>

Följande objekt stöds inte under en uppdatering:

* Ändra namnet på en roll. Ta bort och Lägg sedan till rollen med det nya namnet.
* Ändring av antal uppgraderings domäner.
* Minska storleken på de lokala resurserna.

Om du gör andra uppdateringar av tjänstens definition, till exempel minska storleken på den lokala resursen, måste du utföra en uppdatering av VIP-växling i stället. Mer information finns i [Växla distribution](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Så här fortsätter uppgraderingen
Du kan välja om du vill uppdatera alla roller i tjänsten eller en enskild roll i tjänsten. I båda fallen stoppas alla instanser av varje roll som uppgraderas och tillhör den första uppgraderings domänen, uppgraderas och tas online igen. När de är online är instanserna i den andra uppgraderings domänen stoppade, uppgraderade och online igen. En moln tjänst kan ha högst en uppgradering aktiv i taget. Uppgraderingen utförs alltid mot den senaste versionen av moln tjänsten.

Följande diagram illustrerar hur uppgraderingen fortsätter om du uppgraderar alla roller i tjänsten:

![Uppgradera tjänst](media/cloud-services-update-azure-service/IC345879.png "Uppgradera tjänst")

I nästa diagram visas hur uppdateringen fortsätter om du bara uppgraderar en enda roll:

![Uppgradera roll](media/cloud-services-update-azure-service/IC345880.png "Uppgradera roll")  

Under en automatisk uppdatering utvärderar Azure Fabric Controller med jämna mellanrum hälso tillståndet för moln tjänsten för att fastställa när det är säkert att gå vidare till nästa UD. Den här hälso utvärderingen utförs per roll och betraktar bara instanser i den senaste versionen (d.v.s. instanser från UDs som redan har gick). Den verifierar att ett minsta antal roll instanser, för varje roll, har uppnått ett fungerande Terminal-tillstånd.

### <a name="role-instance-start-timeout"></a>Start-timeout för roll instans
Infrastruktur styrenheten kommer att vänta 30 minuter för varje roll instans för att komma igång. Om tids gränsen uppnås fortsätter infrastruktur styrenheten att gå vidare till nästa roll instans.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Påverkan på enhets data under uppgraderingar av moln tjänsten

När du uppgraderar en tjänst från en enda instans till flera instanser kommer tjänsten att stängas av medan uppgraderingen utförs på grund av Azure uppgraderingar-tjänster. Service nivå avtalet som garanterar tjänst tillgänglighet gäller bara för tjänster som har distribuerats med fler än en instans. I följande lista beskrivs hur data på varje enhet påverkas av varje uppgraderings scenario för Azure-tjänsten:

|Scenario|C-enhet|D-enhet|E-enhet|
|--------|-------|-------|-------|
|Omstart av virtuell dator|Behåll|Behåll|Behåll|
|Starta om portalen|Behåll|Behåll|Förstörs|
|Avbildning av Portal|Behåll|Förstörs|Förstörs|
|In-Place uppgradering|Behåll|Behåll|Förstörs|
|Node-migrering|Förstörs|Förstörs|Förstörs|

Observera att i listan ovan representerar enheten E:-enheten rollens rot enhet och bör inte vara hårdkodad. Använd i stället miljövariabeln **% RoleRoot%** för att representera enheten.

Om du vill minimera stillestånds tiden när du uppgraderar en instans av en instans distribuerar du en ny tjänst för flera instanser till den fristående servern och utför en VIP-växling.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Återställa en uppdatering
Azure ger flexibilitet vid hantering av tjänster under en uppdatering genom att låta dig initiera ytterligare åtgärder på en tjänst, efter att den inledande uppdateringsbegäran accepteras av Azure Fabric-kontrollanten. En återställning kan bara utföras när en uppdatering (konfigurations ändring) eller en uppgradering har statusen **pågår** i distributionen. En uppdatering eller uppgradering anses vara pågående så länge det finns minst en instans av tjänsten som ännu inte har uppdaterats till den nya versionen. Om du vill testa om en återställning tillåts kontrollerar du värdet för flaggan RollbackAllowed, som returneras av [Hämta distribution](/previous-versions/azure/reference/ee460804(v=azure.100)) och [hämtar egenskaper för moln tjänst egenskaper](/previous-versions/azure/reference/ee460806(v=azure.100)) , har angetts till sant.

> [!NOTE]
> Det är bara klokt att anropa rollback vid en uppdatering eller uppgradering på plats, eftersom det innebär att virtuella växlings uppdateringar **i** VIP innebär att ersätta en hel aktiv instans av tjänsten med en annan.
>
>

Återställningen av en pågående uppdatering har följande effekter i distributionen:

* Alla roll instanser som inte har uppdaterats eller uppgraderats till den nya versionen uppdateras eller uppgraderas inte, eftersom dessa instanser redan kör mål versionen av tjänsten.
* Alla roll instanser som redan har uppdaterats eller uppgraderats till den nya versionen av Service Pack- \* filen (. cspkg) eller tjänst konfigurations \* filen (. cscfg)-filen (eller båda filerna) återställs till före uppgraderings versionen av dessa filer.

Den här funktionen tillhandahålls av följande funktioner:

* [Återställnings-eller uppgraderings](/previous-versions/azure/reference/hh403977(v=azure.100)) åtgärden, som kan anropas på en konfigurations uppdatering (utlöses genom att anropa [ändrings distributions konfigurationen](/previous-versions/azure/reference/ee460809(v=azure.100))) eller en uppgradering (utlöses genom att anropa [uppgraderings distribution](/previous-versions/azure/reference/ee460793(v=azure.100))) så länge det finns minst en instans i tjänsten som ännu inte har uppdaterats till den nya versionen.
* Elementet Locked och RollbackAllowed, som returneras som en del av svars texten för åtgärderna [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) och [Get Cloud Service Properties](/previous-versions/azure/reference/ee460806(v=azure.100)) :

  1. Med det låsta elementet kan du identifiera när en motsvarande åtgärd kan anropas för en specifik distribution.
  2. Med RollbackAllowed-elementet kan du identifiera när [återställnings-eller uppgraderings](/previous-versions/azure/reference/hh403977(v=azure.100)) åtgärden kan anropas för en specifik distribution.

  Du behöver inte kontrol lera både låsta och RollbackAllowed-element för att kunna utföra en återställning. Det räcker för att bekräfta att RollbackAllowed har angetts till true. Dessa element returneras bara om dessa metoder anropas med hjälp av begär ande huvudet inställt på "x-MS-version: 2011-10-01" eller en senare version. Mer information om versions rubriker finns i [Service Management-versioner](/previous-versions/azure/gg592580(v=azure.100)).

Det finns vissa situationer där en återställning av en uppdatering eller uppgradering inte stöds:

* Reducering av lokala resurser – om uppdateringen ökar de lokala resurserna för en roll kan inte Azure-plattformen återställas.
* Kvot begränsningar – om uppdateringen var en skalnings åtgärd kanske du inte längre har tillräckligt med beräknings kvoter för att slutföra återställnings åtgärden. Varje Azure-prenumeration har en associerad kvot som anger det maximala antalet kärnor som kan utnyttjas av alla värdbaserade tjänster som tillhör den prenumerationen. Om du utför en återställning av en specifik uppdatering skulle din prenumeration prioriteras, vilket innebär att ingen återställning aktive ras.
* Villkor för tävling – om den första uppdateringen har slutförts går det inte att återställa.

Ett exempel på när återställningen av en uppdatering kan vara användbart är om du använder [uppgraderings distributionen](/previous-versions/azure/reference/ee460793(v=azure.100)) i manuellt läge för att styra den hastighet som en större uppgradering på plats till den Azure-värdbaserade tjänsten har distribuerats.

Under distributionen av uppgraderingen ska du anropa [uppgraderings distributionen](/previous-versions/azure/reference/ee460793(v=azure.100)) i manuellt läge och börja gå igenom uppgraderings domäner. Om du vid en viss tidpunkt övervakar uppgraderingen, Observera att vissa roll instanser i de första uppgraderings domäner som du undersöker inte svarar, kan du anropa [återställnings uppdaterings-eller uppgraderings](/previous-versions/azure/reference/hh403977(v=azure.100)) åtgärden för distributionen, vilket innebär att det inte finns några instanser som ännu inte har uppgraderats och återställnings instanser som har uppgraderats till det tidigare tjänst paketet och konfigurationen.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Initiera flera Mutations åtgärder i en pågående distribution
I vissa fall kanske du vill initiera flera samtidiga åtgärder i en pågående distribution. Du kan till exempel utföra en tjänst uppdatering och, medan uppdateringen distribueras över tjänsten, du vill göra vissa ändringar, t. ex. återställa uppdateringen, tillämpa en annan uppdatering eller till och med ta bort distributionen. Ett fall där detta kan vara nödvändigt är om en tjänst uppgradering innehåller fel söknings kod som gör att en uppgraderad roll instans upprepade gånger kraschar. I det här fallet kommer Azure Fabric Controller inte att kunna göra framsteg med att tillämpa den uppgraderingen eftersom det inte finns tillräckligt många instanser i den uppgraderade domänen. Det här läget kallas en *fast distribution*. Du kan ångra distributionen genom att återställa uppdateringen eller tillämpa en uppdaterad uppdatering ovanpå den misslyckade.

När den första begäran om att uppdatera eller uppgradera tjänsten har tagits emot av Azure Fabric-styrenheten kan du starta efterföljande åtgärder. Det vill säga att du inte behöver vänta på att den första åtgärden ska slutföras innan du kan starta en annan Mutations åtgärd.

Att initiera en andra uppdaterings åtgärd medan den första uppdateringen pågår kommer att fungera på samma sätt som återställnings åtgärden. Om den andra uppdateringen är i automatiskt läge uppgraderas den första uppgraderings domänen omedelbart, vilket kan leda till att instanser från flera uppgraderings domäner är offline vid samma tidpunkt.

Följande åtgärder är följande: [ändra distributions konfiguration](/previous-versions/azure/reference/ee460809(v=azure.100)), [uppgraderings](/previous-versions/azure/reference/ee460793(v=azure.100))distribution, [status för uppdaterings distribution](/previous-versions/azure/reference/ee460808(v=azure.100)), [ta bort distribution](/previous-versions/azure/reference/ee460815(v=azure.100))och [Återställ uppdatering eller uppgradering](/previous-versions/azure/reference/hh403977(v=azure.100)).

Två åtgärder, [Hämta distribution](/previous-versions/azure/reference/ee460804(v=azure.100)) och [Hämta moln tjänst egenskaper](/previous-versions/azure/reference/ee460806(v=azure.100)), returnera den låsta flagga som kan undersökas för att avgöra om en motsvarande åtgärd kan anropas för en specifik distribution.

För att kunna anropa versionen av dessa metoder som returnerar den låsta flaggan, måste du ange begärans huvud till "x-MS-version: 2011-10-01" eller en senare. Mer information om versions rubriker finns i [Service Management-versioner](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribution av roller över uppgraderings domäner
Azure distribuerar instanser av en roll jämnt över ett angivet antal uppgraderings domäner, som kan konfigureras som en del av tjänst definitions filen (. csdef). Det maximala antalet uppgraderings domäner är 20 och standardvärdet är 5. Mer information om hur du ändrar tjänst definitions filen finns i [schema för Azure Service definition (. csdef-fil)](cloud-services-model-and-package.md#csdef).

Om din roll exempelvis har tio instanser, innehåller varje uppgraderings domän som standard två instanser. Om din roll har 14 instanser innehåller fyra av uppgraderings domänerna tre instanser och en femte domän innehåller två.

Uppgraderings domäner identifieras med ett nollbaserat index: den första uppgraderings domänen har ID 0 och den andra uppgraderings domänen har ID 1 och så vidare.

Följande diagram illustrerar hur en tjänst som innehåller två roller distribueras när tjänsten definierar två uppgraderings domäner. Tjänsten kör åtta instanser av webb rollen och nio instanser av arbets rollen.

![Distribution av uppgraderings domäner](media/cloud-services-update-azure-service/IC345533.png "Distribution av uppgraderings domäner")

> [!NOTE]
> Observera att Azure styr hur instanser allokeras mellan uppgraderings domäner. Det går inte att ange vilka instanser som tilldelas till vilken domän.
>
>

## <a name="next-steps"></a>Nästa steg
[Så här hanterar du Cloud Services](cloud-services-how-to-manage-portal.md)  
[Övervaka Cloud Services](cloud-services-how-to-monitor.md)  
[Så här konfigurerar du Cloud Services](cloud-services-how-to-configure-portal.md)