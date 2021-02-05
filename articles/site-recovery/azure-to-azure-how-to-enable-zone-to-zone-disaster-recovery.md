---
title: Aktivera återställning av en zon till en zon för Azure Virtual Machines
description: Den här artikeln beskriver när och hur du använder Zone för att återställa haveri beredskap för virtuella Azure-datorer.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: d207a5629647ef3582f1e0490ef6ccc407f935f7
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584846"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Aktivera haveri beredskap för virtuella Azure-datorer mellan tillgänglighets zoner

Den här artikeln beskriver hur du replikerar, redundansväxlas och återställer virtuella Azure-datorer från en tillgänglighets zon till en annan inom samma Azure-region.

>[!NOTE]
>
>- Stöd för haveri beredskap för zoner till zoner är för närvarande begränsat till nio regioner: Sydostasien, Östra Japan, östra Australien, Storbritannien, södra, Västeuropa, norra Europa, centrala USA, östra USA och västra USA 2.  
>- Site Recovery flyttar eller lagrar inte kund information från den region där den distribueras när kunden använder zoner för haveri beredskap i zoner. Kunder kan välja ett Recovery Services valv från en annan region om de så vill. Recovery Services valvet innehåller metadata men inga faktiska kund uppgifter.

Site Recovery Service bidrar till din strategi för affärs kontinuitet och haveri beredskap genom att hålla dina företags program igång, under planerade och oplanerade avbrott. Det är det rekommenderade alternativet för haveri beredskap för att hålla programmen igång om det finns regionala avbrott.

Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon har ett eller flera data Center. 

[Läs den här artikeln](../resource-mover/move-region-availability-zone.md)om du vill flytta virtuella datorer till en tillgänglighets zon i en annan region.

## <a name="using-availability-zones-for-disaster-recovery"></a>Använda Tillgänglighetszoner för haveri beredskap 

Normalt används Tillgänglighetszoner för att distribuera virtuella datorer i en konfiguration med hög tillgänglighet. De kan vara för nära varandra för att fungera som en katastrof återställnings lösning i händelse av natur katastrofer.

I vissa fall kan dock Tillgänglighetszoner utnyttjas för haveri beredskap:

- Många kunder som hade en strategi för haveri beredskap vid en tunnelbane samtidigt som de är värd för program på plats kan ibland se till att efterlikna den här strategin när de migrerar program till Azure. Dessa kunder bekräftar att en strategi för haveri beredskap för tunnel Bane inte kan fungera i händelse av en storskalig fysisk katastrof och accepterar denna risk. För sådana kunder kan zon-till-zon haveri beredskap användas som en katastrof återställnings möjlighet.

- Många andra kunder har komplicerad nätverks infrastruktur och vill inte återskapa den i en sekundär region på grund av den associerade kostnaden och komplexiteten. Zon-till-zon haveri beredskap minskar komplexiteten eftersom det utnyttjar redundanta nätverks koncept i Tillgänglighetszoner att göra konfigurationen mycket enklare. Sådana kunder föredrar enkelhet och kan också använda Tillgänglighetszoner för haveri beredskap.

- I vissa regioner som inte har någon kopplad region inom samma juridiska jurisdiktion (t. ex. Sydostasien), kan zon-till-zon haveri beredskap fungera som den inloggade lösningen för haveri beredskap, eftersom program och data inte flyttas över nationella gränser. 

- Zon till zon haveri beredskap innebär att data replikeras över kortare avstånd jämfört med Azure till Azure Disaster Recovery och därför kan du se lägre latens och därmed sänka den.

Även om dessa är starka fördelar, finns det en möjlighet att återställa haveri beredskap i zoner för att lösa återhämtnings krav i händelse av natur katastrofer i hela regionen.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Nätverk för zon till haveri beredskap för zoner

Som nämnts ovan minskar haveri beredskap för zoner till zoner komplexiteten eftersom det utnyttjar redundanta nätverks koncept i Tillgänglighetszoner att göra konfigurationen mycket enklare. Nätverks komponenternas beteende i zonen till zon katastrof återställnings scenario beskrivs nedan: 

- Virtual Network: du kan använda samma virtuella nätverk som käll nätverket för faktiska redundans. Använd ett annat virtuellt nätverk till det virtuella käll nätverket för redundanstest.

- Undernät: redundans till samma undernät stöds.

- Privat IP-adress: om du använder statiska IP-adresser kan du använda samma IP-adresser i mål zonen om du väljer att konfigurera dem på ett sådant sätt.

- Accelererat nätverk: som liknar Azure till Azure haveri beredskap kan du aktivera accelererat nätverk om VM-SKU: n stöder det.

- Offentlig IP-adress: du kan koppla en tidigare skapad offentlig IP-adress i samma region till den virtuella mål datorn. Grundläggande offentliga IP-adresser stöder inte relaterade scenarier för tillgänglighets zoner.

- Belastningsutjämnare: standard belastnings utjämning är en regional resurs och därför kan den virtuella mål datorn kopplas till backend-poolen för samma belastningsutjämnare. Det krävs ingen ny belastningsutjämnare.

- Nätverks säkerhets grupp: du kan använda samma nätverks säkerhets grupper som tillämpas på den virtuella käll datorn.

## <a name="pre-requisites"></a>Förutsättningar

Innan du distribuerar zonen till zon haveri beredskap för dina virtuella datorer, är det viktigt att se till att andra funktioner som är aktiverade på den virtuella datorn är kompatibla med zon för haveri beredskap för zoner.

|Funktion  | Support instruktion  |
|---------|---------|
|Klassiska virtuella datorer   |     Stöds inte    |
|Virtuella ARM-datorer    |    Stöds    |
|Azure Disk Encryption v1 (dubbla pass, med Azure Active Directory (Azure AD))     |     Stöds   |
|Azure Disk Encryption v2 (enskilt pass, utan Azure AD)    |    Stöds    |
|Ohanterade diskar    |    Stöds inte    |
|Hanterade diskar    |    Stöds    |
|Kundhanterade nycklar    |    Stöds    |
|Närhetsplaceringsgrupper    |    Stöds    |
|Interoperabilitet för säkerhets kopiering    |    Säkerhets kopiering och återställning på fil nivå stöds. Säkerhets kopiering och återställning av diskar och VM-nivåer stöds inte.    |
|Snabb tillägg/ta bort    |    Diskar kan läggas till efter att zonen har Aktiver ATS för zon replikering. Det går inte att ta bort diskar efter aktivering av zon till zon-replikering.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Konfigurera Site Recovery zon till haveri beredskap för zoner

### <a name="log-in"></a>Logga in

Logga in på Azure Portal.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Aktivera replikering för den virtuella Azure-datorn zonindelade

1. Välj virtuella datorer på Azure Portal-menyn, eller Sök efter och välj virtuella datorer på vilken sida som helst. Välj den virtuella dator som du vill replikera. För att zoner ska kunna återställa haveri beredskap måste den här virtuella datorn redan finnas i en tillgänglighets zon.

2. I Åtgärder väljer du Haveriberedskap.

3. Som visas nedan, på fliken grundläggande, väljer du "Ja" för "haveri beredskap mellan Tillgänglighetszoner?"

    ![Sidan grundläggande inställningar](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Om du accepterar alla standardvärden klickar du på granska + starta replikering följt av starta replikering.

5. Om du vill göra ändringar i inställningarna för replikering klickar du på Nästa: avancerade inställningar.

6. Ändra inställningarna från standard där det är lämpligt. För användare av haveri beredskap för Azure till Azure kan den här sidan kännas bekant. Mer information om de alternativ som visas på det här bladet hittar du [här](./azure-to-azure-tutorial-enable-replication.md)

    ![Sidan Avancerade inställningar](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Klicka på Nästa: granska + starta replikering och sedan starta replikering.

## <a name="faqs"></a>Vanliga frågor och svar

**1. Hur fungerar prissättningen för zonen i haveri beredskap för zoner?**
Priser för haveri beredskap för zoner till zoner är identiska med priserna för haveri beredskap i Azure till Azure. Du hittar mer information på [sidan med priser](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) [här](https://azure.microsoft.com/pricing/details/site-recovery/) . Observera att de utgående kostnader som du skulle se i zon till zon haveri beredskap skulle vara lägre än region till region haveri beredskap.

**2. Vad är service avtalet för RTO och återställnings punkt?**
Service avtalet för RTO är detsamma som för Site Recovery övergripande. Vi lovar RTO på upp till 2 timmar. Det finns inget definierat SLA för återställnings punkt.

**3. är kapaciteten garanterad i den sekundära zonen?**
Team planen för Site Recovery team och Azure Capacity Management för tillräcklig infrastruktur kapacitet. När du startar en redundansväxling hjälper teamen också till att se till att de virtuella dator instanser som skyddas av Site Recovery kommer att distribueras till mål zonen.

**4. vilka operativ system stöds?**
Zon till zon haveri beredskap stöder samma operativ system som Azure till Azure Disaster Recovery. Se support mat ris [här](./azure-to-azure-support-matrix.md).

**5. kan käll-och mål resurs grupper vara samma?**
Nej, du måste redundansväxla till en annan resurs grupp.

## <a name="next-steps"></a>Nästa steg

De steg som måste följas för att köra en haveri beredskap, redundansväxla, återskydda och återställnings fel är samma som stegen i Azure till Azure Disaster Recovery-scenariot.

Om du vill utföra en haveri beredskaps granskning följer du stegen som beskrivs [här](./azure-to-azure-tutorial-dr-drill.md).

Om du vill utföra en redundansväxling och skydda virtuella datorer i den sekundära zonen följer du stegen som beskrivs [här](./azure-to-azure-tutorial-failover-failback.md).

För återställning efter fel till den primära zonen följer du stegen som beskrivs [här](./azure-to-azure-tutorial-failback.md).
