---
title: Säkerhetskopiera dina data till Azure med CommVault
titleSuffix: Azure Storage
description: Ger en översikt över faktorer att överväga och steg att följa för att använda Azure som ett lagrings mål och en återställnings plats för fullständig säkerhets kopiering och återställning av CommVault
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: ce321574ce2878f51864f55bf5618df2c96d1068
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589896"
---
# <a name="backup-to-azure-with-commvault"></a>Säkerhetskopiera till Azure med CommVault

Den här artikeln hjälper dig att integrera en CommVault-infrastruktur med Azure Blob Storage. Det innehåller krav, överväganden, implementering och drifts vägledning. Den här artikeln handlar om att använda Azure som ett säkerhets kopierings mål på en annan plats och en återställnings plats om en katastrof inträffar, vilket förhindrar normal drift på den primära platsen.

> [!NOTE]
> CommVault erbjuder en mindre RTO-lösning (Recovery Time mål), CommVault Live Sync. Med den här lösningen kan du ha en virtuell dator för vänte läge som kan hjälpa dig att återställa snabbare i händelse av en katastrof i en Azure-produktions miljö. Dessa funktioner är utanför omfånget för det här dokumentet.

## <a name="reference-architecture"></a>Referensarkitektur

Följande diagram innehåller en referens arkitektur för lokala Azure-distributioner och Azure-distributioner.

![Referens arkitektur för CommVault till Azure](../media/commvault-diagram.png)

Din befintliga CommVault-distribution kan enkelt integreras med Azure genom att lägga till ett Azure Storage-konto eller flera konton som ett moln lagrings mål. Med CommVault kan du också återställa säkerhets kopior från lokala platser i Azure med en återställnings-på-begäran-webbplats i Azure.

## <a name="commvault-interoperability-matrix"></a>CommVault Interoperabilitets mat ris

| Arbetsbelastning | GPv2 och Blob Storage | Support på låg frekvent nivå | Arkiv nivå stöd | Stöd för Data Box-enhet-familjen |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokala virtuella datorer/data | v-11,5 | v-11,5 | v-11.10 | v-11.10 |
| Virtuella Azure-datorer | v-11,5 | v-11,5 | v-11,5 | Ej tillämpligt |
| Azure-blobb | v-11.6 | v-11.6 | v-11.6 | Ej tillämpligt |
| Azure Files | v-11.6 | v-11.6 | v-11.6 | Ej tillämpligt |

## <a name="before-you-begin"></a>Innan du börjar

En lite första planering hjälper dig att använda Azure som en säkerhets kopierings mål och återställnings plats.

### <a name="get-started-with-azure"></a>Komma igång med Azure

Microsoft erbjuder ett ramverk som följer för att komma igång med Azure. [Cloud adoption Framework](/azure/architecture/cloud-adoption/) (CAF) är en detaljerad metod för digital transformering i företag och omfattande vägledning för att planera ett moln införande i produktions klass. CAF innehåller en steg-för-steg- [Guide för Azure-installationen](/azure/cloud-adoption-framework/ready/azure-setup-guide/) som hjälper dig att komma igång snabbt och säkert. Du kan hitta en interaktiv version i [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Du hittar exempel arkitekturer, metod tips för att distribuera program och kostnads fria utbildnings resurser som du kan använda för att få information om sökvägen till Azures expertis.

### <a name="consider-the-network-between-your-location-and-azure"></a>Överväg nätverket mellan din plats och Azure

Oavsett om du använder moln resurser för att köra produktion, testning och utveckling, eller som en säkerhets kopierings plats och återställnings plats, är det viktigt att förstå dina bandbredds behov för inledande dirigerad säkerhets kopiering och för pågående dagliga överföringar.

Azure Data Box är ett sätt att överföra din första säkerhets kopierings bas linje till Azure utan att kräva mer bandbredd. Detta är användbart om bas linje överföringen är beräknad för att ta längre tid än vad du kan tolerera. Du kan använda Dataöverföring uppskattningen när du skapar ett lagrings konto för att beräkna den tid som krävs för att överföra den första säkerhets kopian.

![Visar uppskattningen Azure Storage data överförings uppskattning i portalen.](../media/az-storage-transfer.png)

Kom ihåg att du behöver tillräckligt med nätverks kapacitet för att stödja dagliga data överföringar i fönstret för överföring av begärda data (säkerhets kopierings fönster) utan att påverka produktions program. Det här avsnittet beskriver de verktyg och tekniker som är tillgängliga för att bedöma dina nätverks behov.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Ta reda på hur mycket bandbredd du behöver

Använd följande resurser för att avgöra hur mycket bandbredd du behöver:

- Rapporter från program varan för säkerhets kopiering.
- CommVault tillhandahåller standard rapporter för att fastställa [ändrings takten](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) och den [totala storleken för säkerhets kopierings uppsättningen](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) för den inledande bas linje överföringen till Azure.
- Säkerhetskopiera program vara – oberoende utvärderings-och rapporterings verktyg som:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Fastställa den outnyttjade Internet bandbredden

Det är viktigt att känna till hur mycket normalt outnyttjad bandbredd (eller *utrymme*) som du har tillgängligt på en dag till dags basis. Detta hjälper dig att bedöma om du kan uppfylla dina mål för:

- Start tid att ladda upp när du inte använder Azure Data Box för seeding offline
- slutföra dagliga säkerhets kopieringar baserat på ändrings frekvensen som identifierades tidigare och säkerhets kopierings fönstret

Använd följande metoder för att identifiera bandbredds utrymme som dina säkerhets kopieringar till Azure är kostnads fria att använda.

- Om du är en befintlig Azure ExpressRoute-kund kan du se din [krets användning](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) i Azure Portal.
- Kontakta din Internet leverantör. De bör kunna dela rapporter som visar din befintliga dagliga och månatliga användning.
- Det finns flera verktyg som kan mäta användningen genom att övervaka din nätverks trafik på router-/växel nivån. Dessa omfattar:
  - [SolarWinds Bandwidth Analyzer-paket](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco-nätverks assistent](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp guld](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Välj rätt lagrings alternativ

När du använder Azure som ett säkerhets kopierings mål använder du [Azure Blob Storage](../../../../blobs/storage-blobs-introduction.md). Blob Storage är Microsofts lösning för objekt lagring. Blob Storage är optimerat för att lagra enorma mängder ostrukturerade data, som är data som inte följer någon data modell eller definition. Dessutom är Azure Storage tåligt, hög tillgängligt, säkert och skalbart. Du kan välja rätt lagring för din arbets belastning för att ge den [återhämtnings nivå](../../../../common/storage-redundancy.md) som passar din interna service avtal. Blob Storage är en tjänst för betalning per användning. Du [debiteras månads vis](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) för den mängd data som lagras, till gång till dessa data och i händelse av låg frekvent lagrings nivå, minst en kvarhållningsperiod som krävs. De återhämtnings-och nivåers alternativ som gäller för säkerhetskopierade data sammanfattas i följande tabeller.

**Återhämtnings alternativ för Blob Storage:**

|  |Lokalt redundant  |Zon – redundant  |Geo-redundant  |Redundant geo-zon  |
|---------|---------|---------|---------|---------|
|**Gällande antal kopior**     | 3         | 3         | 6         | 6 |
|**antal tillgänglighets zoner**     | 1         | 3         | 2         | 4 |
|**antal regioner**     | 1         | 1         | 2         | 2 |
|**Manuell redundans till sekundär region**     | Saknas         | Saknas         | Ja         | Ja |

**Blob Storage-nivåer:**

|  | Frekvent nivå   |Låg frekvent nivå   | Arkiv lag ring |
| ----------- | ----------- | -----------  | -----------  |
| **Tillgänglighet** | 99,9 %         | 99 %         | Offline      |
| **Användnings kostnader** | Högre kostnader för lagring, lägre åtkomst och transaktionskostnader | Lägre kostnader för lagring, högre åtkomst och transaktionskostnader | Lägsta kostnader för lagring, högsta åtkomst och transaktionskostnader |
| **Minsta data lagring krävs**| Ej tillämpligt | 30 dagar | 180 dagar |
| **Svars tid (tid till första byte)** | Millisekunder | Millisekunder | Tider |

#### <a name="sample-backup-to-azure-cost-model"></a>Exempel på säkerhets kopiering till Azures kostnads modell

Med betala per användning kan du avskräckande till kunder som är nya i molnet. Även om du bara betalar för den kapacitet som används betalar du även för transaktioner (läsning och skrivning) och utgångs punkt [för data](https://azure.microsoft.com/pricing/details/bandwidth/) som läses tillbaka till din lokala miljö när [Azure Express Route Direct Local eller Express Route unlimiting data plan](https://azure.microsoft.com/pricing/details/expressroute/) används där data som utgående från Azure ingår. Du kan använda [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att utföra "Vad händer om"-analys. Du kan basera analysen på list prissättning eller på [Azure Storage reserverade kapacitets priser](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), vilket kan leverera upp till 38% besparingar. Här är ett exempel på prissättning för att modellera månads kostnaden för säkerhets kopiering till Azure. Detta är bara ett exempel. *Din prissättning kan variera på grund av aktiviteter som inte har registrerats här.*

|Kostnads faktor  |Månatlig kostnad  |
|---------|---------|
|100 TB säkerhets kopierings data i cool Storage     |$1556,48         |
|2 TB nya data som skrivs per dag x 30 dagar     |$39 i transaktioner          |
|Månatlig uppskattad summa     |$1595,48         |
|---------|---------|
|En tids återställning på 5 TB till lokal över offentligt Internet   | $491,26         |

> [!NOTE]
> Den här uppskattningen genererades i pris Kalkylatorn för Azure med hjälp av amerikanska priser för "betala per användning" och baseras på CommVault-standardvärdet på 32 MB under segment storlek som genererar 65 536-begäranden (Skriv transaktioner) per dag. Det här exemplet kanske inte gäller för dina krav.

## <a name="implementation-guidance"></a>Implementerings vägledning

Det här avsnittet innehåller en kort guide för hur du lägger till Azure Storage i en lokal CommVault-distribution. Detaljerad vägledning och överväganden för överväganden finns i den [offentliga arkitektur guiden för CommVault för Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Öppna Azure Portal och Sök efter **lagrings konton**. Du kan också klicka på ikonen för standard **lagrings konton** .

    ![Visar hur du lägger till ett lagrings konto i Azure Portal.](../media/azure-portal.png)
  
    ![Visar var du har skrivit in Storage i sökrutan för Azure Portal.](../media/locate-storage-account.png)

2. Välj **skapa** för att lägga till ett konto. Välj eller skapa en resurs grupp, ange ett unikt namn, Välj region, Välj **standard** prestanda, lämna alltid konto typ som **Storage v2**, Välj den replikeringsprincip som uppfyller dina service avtal och standard nivån som säkerhets kopierings programmet kommer att gälla. Ett Azure Storage-konto gör frekventa, svala och Arkiv nivåer tillgängliga i ett enda konto och CommVault-principer gör att du kan använda flera nivåer för att effektivt hantera data livs cykeln.

    ![Visar inställningar för lagrings konto i portalen](../media/account-create-1.png)

3. Behåll standard nätverks alternativen nu och gå vidare till **data skydd**. Här kan du välja att aktivera mjuk borttagning, vilket gör att du kan återställa en säkerhets kopia som tagits bort av misstag inom den definierade kvarhållningsperioden och ger skydd mot oavsiktlig eller skadlig borttagning.

    ![Visar inställningarna för data skydd i portalen.](../media/account-create-2.png)

4. Sedan rekommenderar vi standardinställningarna från den **avancerade** skärmen för säkerhets kopiering till Azures användnings fall.

    ![Visar fliken Avancerade inställningar i portalen.](../media/account-create-3.png)

5. Lägg till taggar för organisation om du använder taggning och skapa ditt konto.

6. Två snabba steg är nu obligatoriska innan du kan lägga till kontot i din CommVault-miljö. Navigera till det konto som du skapade i Azure Portal och välj **behållare** under menyn **BLOB service** . Lägg till en behållare och välj ett meningsfullt namn. Navigera sedan till objektet **åtkomst nycklar** under **Inställningar** och kopiera **lagrings kontots namn** och en av de två åtkomst nycklarna. Du behöver behållar namn, konto namn och åtkomst nyckel i nästa steg.

    ![Visar skapande av behållare i portalen.](../media/container.png)

    ![Visar åtkomst nyckel inställningar i portalen.](../media/access-key.png)

7. (*Valfritt*) Du kan lägga till ytterligare säkerhets lager i din distribution.

    1. Konfigurera rollbaserad åtkomst för att begränsa vem som kan göra ändringar i ditt lagrings konto. Mer information finns i [inbyggda roller för hanterings åtgärder](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Begränsa åtkomsten till kontot till vissa nätverks segment med [inställningarna för lagrings brand väggen](../../../../common/storage-network-security.md) för att förhindra åtkomst försök utanför företagets nätverk.

        ![Visar inställningar för lagrings brand väggen i portalen.](../media/storage-firewall.png)

    1. Ange ett [borttagnings lås](../../../../../azure-resource-manager/management/lock-resources.md) för kontot för att förhindra oavsiktlig borttagning av lagrings kontot.

        ![Visar inställning av ett borttagnings lås i portalen.](../media/resource-lock.png)

    1. Konfigurera ytterligare [rekommenderade säkerhets metoder](../../../../../storage/blobs/security-recommendations.md).

1. I CommVault-kommando centret navigerar du till **Hantera**  ->  hanterare för **säkerhets**  ->  **referenser**. Välj ett **moln konto**, **leverantörs typ** **Microsoft Azure Storage**, Välj **MediaAgent**, som överför data till och från Azure, Lägg till lagrings kontots namn och åtkomst nyckel.

    ![Visar tillägg av autentiseringsuppgifter i CommVault Command Center.](../media/commvault-credential.png)

9. Gå sedan till **lagrings**  ->  **moln** i CommVault Command Center. Välj att **lägga till**. Ange ett eget namn på lagrings kontot och välj sedan **Microsoft Azure Storage** i listan **typ** . Välj en medie agent server som ska användas för att överföra säkerhets kopior till Azure Storage. Lägg till den behållare som du skapade, Välj den lagrings nivå som ska användas i Azure Storage-kontot och välj de autentiseringsuppgifter som skapades i steg #8. Slutligen väljer du om du vill överföra deduplicerade säkerhets kopior eller inte och en plats för Deduplicerings databasen.

     ![Skärm bild av CommVaults Lägg till moln användar gränssnitt. I den nedrullningsbara menyn Arkiv är * * Arkiv * * markerat.](../media/commvault-add-storage.png)

10. Lägg slutligen till din nya Azure Storage-resurs till en befintlig eller ny plan i CommVault Command Center via **Hantera**  ->  **planer** som ett säkerhets kopierings mål.

    ![Skärm bild av användar gränssnittet för CommVault Command Center. I det vänstra navigerings fältet under * * Manage * *, * * Plans * * är markerat.](../media/commvault-plan.png)

11. *(Valfritt)* Om du planerar att använda Azure som en återställnings plats eller CommVault för att migrera servrar och program till Azure, är det en bra idé att distribuera en VSA-proxy i Azure. Du hittar detaljerade instruktioner [här](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).

## <a name="operational-guidance"></a>Operationell vägledning

### <a name="azure-alerts-and-performance-monitoring"></a>Azure-aviseringar och prestanda övervakning

Vi rekommenderar att du övervakar både dina Azure-resurser och CommVaults möjlighet att utnyttja dem på samma sätt som med lagrings mål som du förlitar dig på för att lagra dina säkerhets kopior. En kombination av Azure Monitor och CommVault Command Center Monitoring hjälper dig att hålla din miljö felfri.

#### <a name="azure-portal"></a>Azure Portal

Azure tillhandahåller en robust övervaknings lösning i form av [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Du kan [konfigurera Azure Monitor](../../../../blobs/monitor-blob-storage.md) att spåra Azure Storage kapacitet, transaktioner, tillgänglighet, autentisering med mera. Du kan hitta en fullständig referens till mått som samlas in [här](../../../../blobs/monitor-blob-storage-reference.md). Några användbara mått för att spåra är BlobCapacity – för att se till att du är kvar under den maximala [kapacitets gränsen för lagrings kontot](../../../../common/scalability-targets-standard-account.md), ingress och utgångs läge, för att spåra mängden data som skrivs till och läses från ditt Azure Storage-konto och SuccessE2ELatency – för att spåra tur och retur-tiden för förfrågningar till och från Azure Storage och din MediaAgent.

Du kan också [Skapa logg aviseringar](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) för att spåra Azure Storage tjänstens hälsa och se [Azures status instrument panel](https://status.azure.com/status) när som helst.

#### <a name="commvault-command-center"></a>CommVault Command Center

- [Skapa en avisering för moln lagrings pooler](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Information om var du kan visa prestanda rapporter, slut för ande av jobb och påbörja grundläggande fel sökning finns i [instrument paneler](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Så här öppnar du support ärenden

När du behöver hjälp med din säkerhets kopiering till Azure-lösning bör du öppna ett ärende med både CommVault och Azure. Detta hjälper våra support organisationer att samar beta, om det behövs.

#### <a name="to-open-a-case-with-commvault"></a>Öppna ett ärende med CommVault

På [webbplatsen för CommVault-support](https://www.commvault.com/support)loggar du in och öppnar ett ärende.

Information om de support kontrakts alternativ som är tillgängliga finns i [alternativ för CommVault-support](https://ma.commvault.com/support)

Du kan också anropa i för att öppna ett ärende eller få åtkomst till CommVault-support via e-post:

- Avgiftsfritt: + 1 877-780-3077
- [Worldwide support-nummer](https://ma.commvault.com/Support/TelephoneSupport)
- [Support för e-post för CommVault](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Öppna ett ärende med Azure

I [Azure Portal](https://portal.azure.com) Sök efter **support** i Sök fältet längst upp. Välj **Hjälp + Support**  ->  **ny supportbegäran**.

> [!NOTE]
> När du öppnar ett ärende är det viktigt att du behöver hjälp med Azure Storage eller Azure-nätverk. Ange inte Azure Backup. Azure Backup är namnet på en Azure-tjänst och ditt ärende kommer att dirigeras felaktigt.

### <a name="links-to-relevant-commvault-documentation"></a>Länkar till relevant CommVault-dokumentation

Mer information finns i följande CommVault-dokumentation:

- [Användar handbok för CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Arkitektur guide för CommVault Azure](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

### <a name="marketplace-offerings"></a>Marketplace-erbjudanden

CommVault har gjort det enkelt att distribuera sin lösning i Azure för att skydda Azure Virtual Machines och många andra Azure-tjänster. Läs mer i följande referenser:

- [Distribuera CommVault via Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [CommVault för Azure-datablad](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [CommVaults lista över Azure-funktioner och Azure-tjänster som stöds](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Använda CommVault för att skydda SAP HANA i Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Nästa steg

Se dessa ytterligare CommVault-resurser för information om specialiserade användnings scenarier.

- [Använd CommVault för att migrera dina servrar och program till Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Skydda SAP i Azure med CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Skydda Office365 med CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)