---
title: Aktivera zon-till-zon-haveriberedskap för Azure Virtual Machines
description: I den här artikeln beskrivs när och hur du använder haveriberedskap från zon till zon för virtuella Azure-datorer.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 786d877328b1ab3d0f03a75604b7345dba14aa9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713416"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Aktivera haveriberedskap för virtuella Azure-datorer mellan tillgänglighetszoner

Den här artikeln beskriver hur du replikerar, redundansar och återställning efter fel för virtuella Azure-datorer från en tillgänglighetszon till en annan inom samma Azure-region.

>[!NOTE]
>
>- Stödet för haveriberedskap från zon till zon är för närvarande begränsat till följande regioner: Sydostasien, Japan, östra, Australien, östra, JIO Indien, västra, Storbritannien, södra, Europa, västra, Europa, norra, USA, centrala, USA, östra, USA, östra 2 och USA, västra 2.  
>- Site Recovery inte flytta eller lagra kunddata från den region där de distribueras när kunden använder zon-till-zon-haveriberedskap. Kunder kan välja ett Recovery Services-valv från en annan region om de väljer det. Recovery Services-valvet innehåller metadata men inga faktiska kunddata.

Site Recovery-tjänsten bidrar till din strategi för affärskontinuering och haveriberedskap genom att hålla dina affärsappar igång och köra dem under planerade och oplanerade avbrott. Det är det rekommenderade haveriberedskapsalternativet för att hålla dina program igång om det finns regionala avbrott.

Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon har ett eller flera datacenter. 

Om du vill flytta virtuella datorer till en tillgänglighetszon i en annan region kan du [läsa den här artikeln.](../resource-mover/move-region-availability-zone.md)

## <a name="using-availability-zones-for-disaster-recovery"></a>Använda Tillgänglighetszoner för haveriberedskap 

Normalt används Tillgänglighetszoner för att distribuera virtuella datorer i en konfiguration med hög tillgänglighet. De kan vara för nära varandra för att fungera som en haveriberedskapslösning vid naturkatastrofer.

I vissa fall kan dock Tillgänglighetszoner användas för haveriberedskap:

- Många kunder som hade en strategi för haveriberedskap som var värd för lokala program ville ibland efterlikna den här strategin när de migrerade program till Azure. Dessa kunder är medveten om att en strategi för haveriberedskap i stor skala inte fungerar i händelse av en storskalig fysisk katastrof och accepterar den här risken. För sådana kunder kan haveriberedskap från zon till zon användas som ett haveriberedskapsalternativ.

- Många andra kunder har komplicerad nätverksinfrastruktur och vill inte återskapa den i en sekundär region på grund av den tillhörande kostnaden och komplexiteten. Haveriberedskap från zon till zon minskar komplexiteten eftersom redundanta nätverkskoncept används Tillgänglighetszoner att göra konfigurationen mycket enklare. Sådana kunder föredrar enkelhet och kan även använda Tillgänglighetszoner för haveriberedskap.

- I vissa regioner som inte har en länkad region inom samma juridiska jurisdiktion (till exempel Sydostasien) kan haveriberedskap från zon till zon fungera som en haveriberedskapslösning eftersom det bidrar till att säkerställa juridisk efterlevnad, eftersom dina program och data inte flyttas över nationella gränser. 

- Haveriberedskap från zon till zon innebär replikering av data över kortare avstånd jämfört med Azure till Azure-haveriberedskap, och därför kan svarstiderna bli kortare och därmed lägre återställningszoner ( RPO).

Även om dessa är starka fördelar, finns det en risk att haveriberedskap mellan zoner inte uppfyller återhämtningskraven i händelse av naturkatastrofer i hela regionen.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Nätverk för haveriberedskap mellan zoner

Som nämnts ovan minskar haveriberedskap mellan zoner komplexiteten eftersom redundanta nätverksbegrepp används i flera Tillgänglighetszoner att göra konfigurationen mycket enklare. Beteendet för nätverkskomponenter i haveriberedskapsscenariot zon-till-zon beskrivs nedan: 

- Virtual Network: Du kan använda samma virtuella nätverk som källnätverket för faktiska redundanser. Använd ett annat virtuellt nätverk än det virtuella källnätverket för redundanstest.

- Undernät: Redundans till samma undernät stöds.

- Privat IP-adress: Om du använder statiska IP-adresser kan du använda samma IP-adresser i målzonen om du väljer att konfigurera dem på ett sådant sätt.

- Accelererat nätverk: På liknande sätt som med Azure-haveriberedskap kan du aktivera accelererat nätverk om VM-SKU:n stöder det.

- Offentlig IP-adress: Du kan koppla en tidigare skapad offentlig IP-adress i samma region till den virtuella måldatorn. Grundläggande offentliga IP-adresser stöder inte scenarier relaterade till tillgänglighetszoner.

- Lastbalanserare: Standardlastbalanserare är en regional resurs och därför kan den virtuella måldatorn kopplas till backend-poolen för samma lastbalanserare. En ny lastbalanserare krävs inte.

- Nätverkssäkerhetsgrupp: Du kan använda samma nätverkssäkerhetsgrupper som tillämpas på den virtuella källdatorn.

## <a name="pre-requisites"></a>Förutsättningar

Innan du distribuerar zon till zonåterställning för dina virtuella datorer är det viktigt att se till att andra funktioner som är aktiverade på den virtuella datorn är kompatibla med haveriberedskap från zon till zon.

|Funktion  | Support-instruktion  |
|---------|---------|
|Klassiska virtuella datorer   |     Stöds inte    |
|Virtuella ARM-datorer    |    Stöds    |
|Azure Disk Encryption v1 (dubbla pass, med Azure Active Directory (Azure AD))     |     Stöds   |
|Azure Disk Encryption v2 (enkel passning utan Azure AD)    |    Stöds    |
|Ohanterade diskar    |    Stöds inte    |
|Hanterade diskar    |    Stöds    |
|Kundhanterade nycklar    |    Stöds    |
|Närhetsplaceringsgrupper    |    Stöds    |
|Samverkan mellan säkerhetskopiering    |    Säkerhetskopiering och återställning på filnivå stöds. Säkerhetskopiering och återställning på disk- och VM-nivå och stöds inte.    |
|Lägg till/ta bort hot    |    Diskar kan läggas till efter aktivering av zon-till-zon-replikering. Borttagning av diskar efter aktivering av zon-till-zonreplikering stöds inte.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Konfigurera haveriberedskap Site Recovery zon till zon

### <a name="log-in"></a>Logga in

Logga in på Azure Portal.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Aktivera replikering för den zonindebaserade virtuella Azure-datorn

1. På Azure Portal väljer du Virtuella datorer eller söker efter och väljer Virtuella datorer på valfri sida. Välj den virtuella dator som du vill replikera. För haveriberedskap mellan zoner måste den här virtuella datorn redan finnas i en tillgänglighetszon.

2. I Åtgärder väljer du Haveriberedskap.

3. Som du ser nedan går du till fliken Grundläggande inställningar och väljer Ja för Haveriberedskap mellan Tillgänglighetszoner?"

    ![Sidan Grundläggande inställningar](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Om du godkänner alla standardvärden klickar du på Granska + starta replikering följt av Starta replikering.

5. Om du vill göra ändringar i replikeringsinställningarna klickar du på Nästa: Avancerade inställningar.

6. Ändra inställningarna från standardinställningarna där det är lämpligt. För användare av haveriberedskap från Azure till Azure kan den här sidan verka bekant. Mer information om alternativen som visas på det här bladet finns [här](./azure-to-azure-tutorial-enable-replication.md)

    ![Sidan Avancerade inställningar](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Klicka på Nästa: Granska + Starta replikering och sedan Starta replikering.

## <a name="faqs"></a>Vanliga frågor och svar

**1. Hur fungerar prissättningen för haveriberedskap mellan zoner?**
Prissättningen för haveriberedskap mellan zoner är identisk med prissättningen för Haveriberedskap mellan Azure och Azure. Du hittar mer information på prissättningssidan [här](https://azure.microsoft.com/pricing/details/site-recovery/) och [här.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Observera att de avgifter för utgående data som du skulle se i haveriberedskap mellan zoner skulle vara lägre än haveriberedskap från region till region.

**2. Vad är serviceavtalet för RTO och RPO?**
Serviceavtalet för RTO är detsamma som för Site Recovery övergripande. Vi utlovar RTO på upp till 2 timmar. Det finns inget definierat serviceavtal för RPO.

**3. Garanteras kapaciteten i den sekundära zonen?**
Teamet Site Recovery Azure-kapacitetshantering planerar för tillräcklig infrastrukturkapacitet. När du startar en redundans hjälper teamen också till att se till att vm-instanser som skyddas av Site Recovery distribueras till målzonen.

**4. Vilka operativsystem stöds?**
Haveriberedskap från zon till zon stöder samma operativsystem som Azure till Azure-haveriberedskap. Se supportmatrisen [här.](./azure-to-azure-support-matrix.md)

**5. Kan käll- och målresursgrupperna vara desamma?**
Nej, du måste växla över till en annan resursgrupp.

## <a name="next-steps"></a>Nästa steg

De steg som måste följas för att köra ett haveriberedskapsgransknings-, redundans-, återskydds- och återställning efter fel är samma som stegen i scenariot för Haveriberedskap i Azure till Azure.

Om du vill utföra ett haveriberedskapsgranskningssteg följer du stegen som beskrivs [här.](./azure-to-azure-tutorial-dr-drill.md)

Om du vill utföra en redundans och återaktivera skyddet av virtuella datorer i den sekundära zonen följer du stegen som beskrivs [här](./azure-to-azure-tutorial-failover-failback.md).

Om du vill växla tillbaka till den primära zonen följer du stegen som beskrivs [här.](./azure-to-azure-tutorial-failback.md)
