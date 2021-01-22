---
title: Distribuera Horisont på Azure VMware-lösning
description: Lär dig hur du distribuerar VMware-Horisont på Azure VMware-lösningen.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 2cf6fc5cb7662188650365cb019774d6c778d405
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684883"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Distribuera Horisont på Azure VMware-lösning 

>[!NOTE]
>Det här dokumentet fokuserar på en horisont produkt i VMware, tidigare kallat Horisont 7. Horisont är en annan lösning än Horisont molnet på Azure, även om det finns några delade komponenter. Viktiga fördelar med Azure VMware-lösningen är både en smidigare storleks metod och integrering av VMware Cloud Foundation Management i Azure Portal.

[VMware-horisont](https://www.vmware.com/products/horizon.html)®, en plattform för virtuella skriv bord och program, som körs i Data Center och ger enkel och centraliserad hantering. Den levererar virtuella skriv bord och program på valfri enhet, var som helst. Med Horisont kan du skapa och Broker-anslutningar till virtuella Windows-och Linux-datorer, värdbaserade program för fjärr skrivbords servrar, Station ära datorer och fysiska datorer.

Här fokuserar vi särskilt på att distribuera en horisont på Azure VMware-lösningen. Allmän information om VMware-Horisont finns i dokumentationen om Horisont produktion:

* [Vad är VMware-Horisont?](https://www.vmware.com/products/horizon.html)

* [Läs mer om VMware-Horisont](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Referens arkitektur för Horisont](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Det finns nu två VDI-lösningar (Virtual Desktop Infrastructure) på Azure-plattformen med Horisont introduktion på Azure VMware-lösningen. I följande diagram sammanfattas viktiga skillnader på hög nivå.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Horisont för Azure VMware-lösning och Horisont moln i Azure" border="false":::

Horisont 2006 och senare versioner på fri släps linjen på Horisont 8 stöder både lokal distribution och distribution av Azure VMware-lösningar. Det finns några Horisont funktioner som stöds lokalt men inte på Azure VMware-lösningar. Det finns också stöd för ytterligare produkter i Horisont eko systemet. Mer information finns i [funktions paritet och interoperabilitet](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Distribuera Horisont i ett hybrid moln

Du kan distribuera en horisont i en hybrid moln miljö när du använder Pod-arkitektur (Horisont Cloud Architecture) för att koppla samman lokala data och Azure-datacenter. CPA skalar upp din distribution, skapar ett hybrid moln och ger redundans för verksamhets kontinuitet och haveri beredskap.  Mer information finns i [expandera befintliga miljöer för Horisont 7](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA är inte en utsträckt distribution. varje horisont-pod är distinkt och alla anslutnings servrar som hör till var och en av de enskilda poddar måste finnas på en enda plats och köras på samma broadcast-domän från ett nätverks perspektiv.

Precis som lokalt eller privat data Center kan horisonten distribueras i ett privat moln i Azure VMware-lösningen. Vi diskuterar viktiga skillnader i att distribuera Horisont lokalt och på Azure VMware-lösningen i följande avsnitt.

Azures privata moln är konceptuellt samma som VMware-SDDC, en term som vanligt vis används i en horisont dokumentation. Resten av det här dokumentet använder villkoren i Azures privata moln och VMware SDDC utbytbara.

Det krävs en horisont för att ansluta till Azure VMware-lösningen för att hantera prenumerations licenser. Cloud Connector kan distribueras i Azure Virtual Network bredvid Horisont anslutnings servrar.

>[!IMPORTANT]
>Stöd för Horisont kontroll plan för Horisont i Azure VMware-lösningen är ännu inte tillgängligt. Se till att ladda ned VHD-versionen av Horisont Cloud Connector.

## <a name="vcenter-cloud-admin-role"></a>Administratörs roll för vCenter-moln

Eftersom Azure VMware-lösningen är en SDDC-tjänst och Azure hanterar livs cykeln för SDDC på Azure VMware-lösningen, begränsas vCenter-behörighets modellen på Azure VMware-lösningen av design.

Kunder måste använda moln administratörs rollen, som har en begränsad uppsättning vCenter-behörigheter. Horisont produkten ändrades till att fungera med moln administratörs rollen på Azure VMware-lösningen, särskilt:

* Direkt klonings etableringen ändrades till att köras på Azure VMware-lösningen.

* En speciell virtuellt San-princip (VMware_Horizon) skapades på Azure VMware-lösningen för att fungera med horisont, som måste vara tillgänglig och användas i SDDCs som distribueras för Horisont.

* vSphere Content-based Read cache (CBRC), även kallat View Storage Accelerator, inaktive ras när du kör på Azure VMware-lösningen.

>[!IMPORTANT]
>CBRC får inte vara aktive rad igen.

>[!NOTE]
>Azure VMware-lösningen konfigurerar automatiskt vissa Horisont inställningar så länge du distribuerar Horisont 2006 (aka Horisont 8) och senare i grenen för Horisont 8 och väljer alternativet **Azure** i installations servern för Horisont anslutnings Server.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Horisont för distributions arkitektur för Azure VMware-lösningen

En typisk arkitektur design för Horisont använder en POD och en block strategi. Ett block är en enda vCenter, medan flera block skapar en pod. En horisont-pod är en organisations enhet som fastställs av Horisont skalbarhets gränser. Varje horisont Pod har en separat hanterings Portal, och därför är det en standard utformning att minimera antalet poddar.

Varje moln har ett eget nätverks anslutnings schema. I kombination med VMware SDDC Network/NSX Edge visar Azure VMware-lösningen nätverks anslutning unika krav för att distribuera en annan horisont än den lokala.

Varje privat Azure-moln och SDDC kan hantera 4 000-datorer eller programsessioner, förutsatt att:

* Arbets belastnings trafiken justeras med LoginVSI uppgifts Worker-profil.

* Endast protokoll trafik beaktas, inga användar data.

* NSX Edge har kon figurer ATS för att vara stor.

>[!NOTE]
>Din arbets belastnings profil och dina behov kan skilja sig åt, och därför kan resultatet variera beroende på ditt användnings fall. Användar data volymer kan sänka skalnings gränserna i arbets Belastningens kontext. Ändra storlek och planera distributionen därefter. Mer information finns i rikt linjerna för storleks rikt linjer i avsnittet [storlek på Azure VMware-lösningar som är värdar för Horisont distributioner](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

Med tanke på Azures privata moln och SDDC Max gräns, rekommenderar vi en distributions arkitektur där Horisont anslutnings servrarna och VMware Unified Access gateways (UAGs) körs i Azure-Virtual Network. Det aktiverar effektivt varje Azure-privat moln och SDDC till ett block. I sin tur maximerar du skalbarheten för Horisont som körs på Azure VMware-lösningen.

Anslutningen från Azure Virtual Network till Azures privata moln/SDDCs ska konfigureras med ExpressRoute FastPath. I följande diagram visas en grundläggande Horisont Pod-distribution.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Typisk Horisont Pod-distribution med snabb sökväg för ExpressPath" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Nätverks anslutning för att skala Horisont på Azure VMware-lösning

I det här avsnittet beskrivs nätverks arkitekturen på hög nivå med några vanliga distributions exempel som hjälper dig att skala horisonten på Azure VMware-lösningen. Fokus är specifikt för kritiska nätverks element. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Pod för enkel Horisont på Azure VMware-lösning

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Pod för enkel Horisont på Azure VMware-lösning" border="false":::

En enda Horisont pod är det bästa scenariot för vanlig distribution eftersom du bara distribuerar en horisont Pod i regionen USA, östra.  Eftersom varje privat moln och SDDC beräknas för att hantera 4 000 Desktop-sessioner, distribuerar du den maximala Horisont storleken för pod.  Du kan planera distributionen av upp till tre privata moln/SDDCs.

Med de virtuella datorerna för Horisont infrastruktur som distribueras i Azure Virtual Network kan du uppnå 12 000-sessioner per Horisont-pod. Anslutningen mellan varje privat moln och SDDC till Azure-Virtual Network är ExpressRoute snabb väg.  Ingen öst-väst-trafik mellan privata moln krävs. 

Viktiga antaganden för det här grundläggande distributions exemplet är:

* Du har inte en lokal Horisont-Pod som du vill ansluta till den nya Pod med hjälp av Cloud Pod Architecture (CPA).

* Slutanvändarna ansluter till sina virtuella datorer via Internet (vs. ansluter via ett lokalt Data Center).

Du ansluter din AD-domänkontrollant i Azure Virtual Network med din lokala AD via VPN eller ExpressRoute-kretsen.

En variant av det grundläggande exemplet kan vara att stödja anslutning för lokala resurser. Användare kan till exempel komma åt skriv bord och generera trafik för virtuella Skriv bords program eller ansluta till en lokal Horisont-Pod med hjälp av CPA.

Diagrammet visar hur du stöder anslutning för lokala resurser. För att ansluta till företagets nätverk till Azure-Virtual Network behöver du en ExpressRoute-krets.  Du måste också ansluta företagets nätverk till var och en av de privata moln-och SDDCs med hjälp av ExpressRoute Global Reach.  Den tillåter anslutning från SDDC till ExpressRoute-kretsen och lokala resurser. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Anslut företags nätverket till ett Azure-Virtual Network" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Flera Horisont poddar på Azure VMware-lösning i flera regioner

Ett annat scenario är skalnings Horisont över flera poddar.  I det här scenariot kan du distribuera två Horisont poddar i två olika regioner och federera dem med hjälp av CPA. Det liknar nätverks konfigurationen i föregående exempel, men med vissa ytterligare kors regionala länkar. 

Du ansluter Azure-Virtual Network i varje region till de privata molnen/SDDCs i den andra regionen. Den tillåter Horisont anslutnings servrar som ingår i CPA-federationen att ansluta till alla skriv bord under hantering. Genom att lägga till ytterligare privata moln/SDDCs i den här konfigurationen kan du skala till 24 000 sessioner övergripande. 

Samma principer gäller om du distribuerar två Horisont poddar i samma region.  Se till att distribuera den andra Horisont Pod i en *separat Azure-Virtual Network*. Precis som i det enkla Pod-exemplet kan du ansluta företagets nätverk och lokala Pod till exemplet på flera Pod/regioner med ExpressRoute och Global Reach. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" Flera Horisont poddar på Azure VMware-lösning i flera regioner" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Storlek på Azure VMware-lösningar värdar för Horisont distributioner 

Metoden för att ändra storlek på en värd som körs i Azure VMware-lösningen är enklare än en horisont lokalt.  Det beror på att Azure VMware-lösningens värd är standardiserad.  Exakt värd storlek hjälper till att fastställa antalet värdar som behövs för att stödja dina VDI-krav.  Det är centralt för att fastställa kostnad per skriv bord.

### <a name="sizing-tables"></a>Storleks tabeller

Specifika vCPU/vRAM-krav för Horisont av virtuella skriv bord är beroende av kundens specifika arbets belastnings profil.   Arbeta med ditt team för MSFT och VMware för att hjälpa dig att fastställa dina vCPU/vRAM-krav för dina virtuella skriv bord. 

| vCPU per virtuell dator | vRAM per virtuell dator (GB) | Instans | 100 virtuella datorer | 200 virtuella datorer | 300 virtuella datorer | 400 virtuella datorer | 500 virtuella datorer | 600 virtuella datorer | 700 virtuella datorer | 800 virtuella datorer | 900 virtuella datorer | 1000 virtuella datorer | 2000 virtuella datorer | 3000 virtuella datorer | 4000 virtuella datorer | 5000 virtuella datorer | 6000 virtuella datorer | 6400 virtuella datorer |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3.5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3.5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3.5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3.5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Data för Horisont storlek

Det här behöver du samla in för din planerade arbets belastning:

* Antal samtidiga skriv bord

* Nödvändig vCPU per stationär dator

* Krävd vRAM per stationär dator

* Lagrings utrymme som krävs per skriv bord

I allmänhet är VDI-distributioner antingen processor-eller RAM-begränsade, vilket avgör värd storleken. Låt oss ta med följande exempel för en LoginVSI kunskap Worker-typ för arbets belastningen, verifierad med prestanda testning:

* 2 000 samtidiga Skriv bords distribution

* 2vCPU per stationär dator.

* 4 GB vRAM per stationär dator.

* 50 GB lagrings utrymme per stationär dator

I det här exemplet är det totala antalet värdar på 18 som ger en virtuell dator per värd på 111.

>[!IMPORTANT]
>Kund arbets belastningar varierar från det här exemplet på en LoginVSI kunskaps arbetare. Som en del av att planera distributionen arbetar du med din VMware EUC-SEs för dina behov. Se till att köra dina egna prestandatester med den faktiska, planerade arbets belastningen innan du slutför storleks ändringen av värden och justera därefter.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Horisont för Azure VMware Solution Licensing 

Det finns fyra komponenter för den totala kostnaden för att köra en horisont på Azure VMware-lösningen. 

### <a name="azure-vmware-solution-capacity-cost"></a>Kapacitets kostnad för Azure VMware-lösningen

Mer information om priser finns på pris sidan för [Azure VMware-lösningen](https://azure.microsoft.com/pricing/details/azure-vmware/)

### <a name="horizon-licensing-cost"></a>Kostnad för Horisont licens

Det finns två tillgängliga licenser för användning med Azure VMware-lösningen, som kan vara antingen samtidig användare (CCU) eller namngiven användare (ny):

* Prenumerations licens för Horisont

* Licens för Universal-prenumeration för Horisont

Om du bara distribuerar Horisont på Azure VMware-lösningen för en förutsebar framtid använder du prenumerations licensen för Horisont eftersom det är en lägre kostnad.

Om du har distribuerat på Azure VMware-lösningen och lokalt, som med en katastrof återställnings väska, väljer du den universella prenumerations licensen för Horisont. Den innehåller en vSphere-licens för lokal distribution, så den har en högre kostnad.

Arbeta med din VMware EUC-säljteam för att fastställa Horisont licens kostnaden utifrån dina behov.

### <a name="azure-instance-types"></a>Azure instance types

För att förstå de storlekar på virtuella Azure-datorer som krävs för Horisont infrastrukturen, se VMwares rikt linjer som du hittar [här](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution).

## <a name="next-steps"></a>Nästa steg
Läs mer om VMware-Horisont på Azure VMware-lösningen i [vanliga frågor och svar om VMware-Horisont](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf).
