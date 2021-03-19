---
title: Säkerhetskopiera dina data till Azure med Veeam
titleSuffix: Azure Storage
description: Innehåller en översikt över faktorer att överväga och steg att följa för att använda Azure som ett lagrings mål och en återställnings plats för säkerhets kopiering och återställning av Veeam
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 0b8bc0defd3314fcff691a049323201732644ff3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589913"
---
# <a name="backup-to-azure-with-veeam"></a>Säkerhetskopiera till Azure med Veeam

Den här artikeln hjälper dig att integrera en Veeam-infrastruktur med Azure Blob Storage. Det innehåller krav, överväganden, implementering och drifts vägledning. Den här artikeln handlar om att använda Azure som ett säkerhets kopierings mål på en annan plats och en återställnings plats om en katastrof inträffar, vilket förhindrar normal drift på den primära platsen.

> [!NOTE]
> Veeam erbjuder också en mindre RTO-lösning (Recovery Time mål), Veeam replikering. Med den här lösningen kan du ha en virtuell dator för vänte läge som kan hjälpa dig att återställa snabbare i händelse av en katastrof i en Azure-produktions miljö. Veeam har också särskilda verktyg för att säkerhetskopiera Azure-och Office 365-resurser. Dessa funktioner är utanför omfånget för det här dokumentet.

## <a name="reference-architecture"></a>Referensarkitektur

Följande diagram innehåller en referens arkitektur för lokala Azure-distributioner och Azure-distributioner.

![Veeam till Azures referens arkitektur diagram.](../media/veeam-architecture.png)

Din befintliga Veeam-distribution kan enkelt integreras med Azure genom att lägga till ett Azure Storage-konto eller flera konton som en lagrings plats för moln säkerhets kopior. Med Veeam kan du också återställa säkerhets kopior från lokala platser i Azure med en återställnings-på-begäran-webbplats i Azure.

## <a name="veeam-interoperability-matrix"></a>Veeam för interoperabilitet

| Arbetsbelastning | GPv2 och Blob Storage | Support på låg frekvent nivå | Arkiv nivå stöd | Stöd för Data Box-enhet-familjen |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokala virtuella datorer/data | v10a | v10a | Ej tillämpligt | 10a<sup>*</sup> |
| Virtuella Azure-datorer | v10a | v10a | Ej tillämpligt | 10a<sup>*</sup> |
| Azure-blobb | v10a | v10a | Ej tillämpligt | 10a<sup>*</sup> |
| Azure Files | v10a | v10a | Ej tillämpligt | 10a<sup>*</sup> |

<sup>*</sup>Veeam-stöd för säkerhets kopiering och replikering REST API bara för Azure Data Box. Därför stöds inte Azure Data Box Disk.

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

Det finns flera utvärderings alternativ för att fastställa ändrings takten och den totala storleken för säkerhets kopierings uppsättningen för den inledande bas linje överföringen till Azure. Här följer några exempel på bedömnings-och rapporterings verktyg:

- [MiTrend](https://mitrend.com/)
- [Apt är](https://www.veritas.com/insights/aptare-it-analytics)
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
|antal **region** s     | 1         | 1         | 2         | 2 |
|**Manuell redundans till sekundär region**     | Saknas         | Saknas         | Ja         | Ja |

**Blob Storage-nivåer:**

|  | Frekvent nivå   |Låg frekvent nivå   | Arkiv lag ring |
| ----------- | ----------- | -----------  | -----------  |
| **Tillgänglighet** | 99,9 %         | 99 %         | Offline      |
| **Användnings kostnader** | Högre kostnader för lagring, lägre åtkomst och transaktionskostnader | Lägre kostnader för lagring, högre åtkomst och transaktionskostnader | Lägsta kostnader för lagring, högsta åtkomst och transaktionskostnader |
| **Minsta data lagring krävs** | NA | 30 dagar | 180 dagar |
| **Svars tid (tid till första byte)** | Millisekunder | Millisekunder | Tider |

#### <a name="sample-backup-to-azure-cost-model"></a>Exempel på säkerhets kopiering till Azures kostnads modell

Med betala per användning kan du avskräckande till kunder som är nya i molnet. Även om du bara betalar för den kapacitet som används betalar du även för transaktioner (läsning och skrivning) och utgångs punkt [för data](https://azure.microsoft.com/pricing/details/bandwidth/) som läses tillbaka till din lokala miljö när [Azure Express Route Direct Local eller Express Route unlimiting data plan](https://azure.microsoft.com/pricing/details/expressroute/) används där data som utgående från Azure ingår. Du kan använda [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att utföra "Vad händer om"-analys. Du kan basera analysen på list prissättning eller på [Azure Storage reserverade kapacitets priser](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), vilket kan leverera upp till 38% besparingar. Här är ett exempel på prissättning för att modellera månads kostnaden för säkerhets kopiering till Azure. Detta är bara ett exempel. *Din prissättning kan variera på grund av aktiviteter som inte har registrerats här.*

|Kostnads faktor  |Månatlig kostnad  |
|---------|---------|
|100 TB säkerhets kopierings data i cool Storage     |$1556,48         |
|2 TB nya data som skrivs per dag x 30 dagar     |$72 i transaktioner          |
|Månatlig uppskattad summa     |$1628,48         |
|---------|---------|
|En tids återställning på 5 TB till lokal över offentligt Internet   | $527,26         |

> [!Note]
> Den här uppskattningen genererades i pris Kalkylatorn för Azure med hjälp av amerikanska priser för att betala per användning och baseras på Veeam standard på 256 KB-segment storlek för WAN-överföringar. Det här exemplet kanske inte gäller för dina krav.

## <a name="implementation-guidance"></a>Implementerings vägledning

Det här avsnittet innehåller en kort guide för hur du lägger till Azure Storage i en lokal Veeam-distribution. Detaljerad vägledning och överväganden för planering finns i [Veeam Cloud Connect backup guide](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Öppna Azure Portal och Sök efter **lagrings konton**. Du kan också klicka på standard tjänst ikonen.

      ![Visar hur du lägger till ett lagrings konto i Azure Portal.](../media/azure-portal.png)

      ![Visar var du har skrivit in Storage i sökrutan för Azure Portal.](../media/locate-storage-account.png)

2. Välj **skapa** för att lägga till ett konto. Välj eller skapa en resurs grupp, ange ett unikt namn, Välj region, Välj **standard** prestanda, lämna alltid konto typ som **Storage v2**, Välj den replikeringsprincip som uppfyller dina service avtal och standard nivån som säkerhets kopierings programmet kommer att gälla. Ett Azure Storage-konto gör frekventa, frekventa och Arkiv nivåer tillgängliga i ett enda konto och Veeam-principer gör att du kan använda flera nivåer för att effektivt hantera data livs cykeln.

    ![Visar inställningar för lagrings konto i portalen](../media/account-create-1.png)

3. Behåll standard nätverks alternativen nu och gå vidare till **data skydd**. Här kan du välja att aktivera mjuk borttagning, vilket gör att du kan återställa en säkerhets kopia som tagits bort av misstag inom den definierade kvarhållningsperioden och ger skydd mot oavsiktlig eller skadlig borttagning.

    ![Visar inställningarna för data skydd i portalen.](../media/account-create-2.png)

4. Sedan rekommenderar vi standardinställningarna från den **avancerade** skärmen för säkerhets kopiering till Azures användnings fall.

    ![Visar fliken Avancerade inställningar i portalen.](../media/account-create-3.png)

5. Lägg till taggar för organisation om du använder taggning och skapa ditt konto.

6. Två snabba steg är nu obligatoriska innan du kan lägga till kontot i Veeam-miljön. Navigera till det konto som du skapade i Azure Portal och välj **behållare** under menyn **BLOB service** . Lägg till en behållare och välj ett meningsfullt namn. Navigera sedan till objektet **åtkomst nycklar** under **Inställningar** och kopiera **lagrings kontots namn** och en av de två åtkomst nycklarna. Du behöver behållar namn, konto namn och åtkomst nyckel i nästa steg.

    ![Visar skapande av behållare i portalen.](../media/container-b.png)

    ![Visar åtkomst nyckel inställningar i portalen.](../media/access-key.png)

    > [!Note]
    > Veeam säkerhets kopiering och replikering erbjuder ytterligare alternativ för att ansluta till Azure. För användning av den här artikeln är det rekommenderade tillvägagångs sättet att använda Microsoft Azure Blob Storage som ett säkerhets kopierings mål.

7. *(Valfritt)* Du kan lägga till fler säkerhets lager i din distribution.

     1. Konfigurera rollbaserad åtkomst för att begränsa vem som kan göra ändringar i ditt lagrings konto. Mer information finns i [inbyggda roller för hanterings åtgärder](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Begränsa åtkomsten till kontot till vissa nätverks segment med [inställningarna för lagrings brand väggen](../../../../common/storage-network-security.md) för att förhindra åtkomst försök utanför företagets nätverk.

        ![Visar inställningar för lagrings brand väggen i portalen.](../media/storage-firewall.png)

     1. Ange ett [borttagnings lås](../../../../../azure-resource-manager/management/lock-resources.md) för kontot för att förhindra oavsiktlig borttagning av lagrings kontot.

        ![Resurs lås](../media/resource-lock.png)

     1. Konfigurera ytterligare [rekommenderade säkerhets metoder](../../../../../storage/blobs/security-recommendations.md).

8. I Veaam för säkerhets kopiering och replikering går du till **säkerhets kopierings infrastruktur** – > högerklickar du i översikts fönstret och väljer **Lägg till säkerhets kopierings lagring** för att öppna konfigurations guiden. I dialog rutan väljer du **objekt lagrings**  ->  **Microsoft Azure Blob Storage**  ->  **Azure-Blob Storage**.

    ![Visar val av objekt lagring i guiden Veeam-lagringsplats.](../media/veeam-repo-a.png)

    ![Visar hur du väljer Microsoft Azure Blob Storage i guiden Veeam-lagringsplats.](../media/veeam-repo-b.png)

    ![Visar val av Azure-Blob Storage i guiden Veeam-lagringsplats.](../media/veeam-repo-c.png)

9. Ange sedan ett namn och en beskrivning av den nya Blob Storage-lagringsplatsen.

    ![Visar ett namn för lagrings platsen i guiden Veeam-lagringsplats.](../media/veeam-repo-d.png)

10. I nästa steg lägger du till autentiseringsuppgifterna för åtkomst till ditt Azure Storage-konto. Välj **Microsoft Azure Storage konto** i Cloud Credential Manager och ange ditt lagrings konto namn och åtkomst nyckel. Välj **Azure Global** i region väljaren och eventuell Gateway-server om tillämpligt.

    ![Visar hur du anger ett konto i guiden Veeam-lagringsplats.](../media/veeam-repo-e.png)

    > [!Note]
    > Om du väljer att inte använda en Veeam Gateway-server måste du se till att alla lagrings platser har direkt åtkomst till Internet.

11. I behållarens register väljer du din Azure Storage behållare och väljer eller skapar en mapp för att lagra dina säkerhets kopior i. Du kan också definiera en mjuk gräns för den övergripande lagrings kapaciteten som ska användas av Veeam, vilket rekommenderas. Granska den information som visas i avsnittet Sammanfattning och slutför konfigurations verktyget. Nu kan du välja den nya lagrings platsen i konfigurationen för säkerhets kopierings jobbet.

    ![Visar hur du anger en behållare i guiden Veeam-lagringsplats.](../media/veeam-repo-f.png)

    ![Visar hur du skapar en mapp i guiden Veeam-lagringsplats.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Operationell vägledning

### <a name="azure-alerts-and-performance-monitoring"></a>Azure-aviseringar och prestanda övervakning

Vi rekommenderar att du övervakar både dina Azure-resurser och Veeam för att utnyttja dem på samma sätt som med ett lagrings mål som du förlitar dig på för att lagra dina säkerhets kopior. En kombination av övervaknings funktionerna Azure Monitor och Veeam (fliken **statistik** i noden **jobb** i hanterings konsolen för Veeam eller flera avancerade alternativ som Veeam) hjälper dig att hålla miljön felfri.

#### <a name="azure-portal"></a>Azure Portal

Azure tillhandahåller en robust övervaknings lösning i form av [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Du kan [konfigurera Azure Monitor](../../../../blobs/monitor-blob-storage.md) att spåra Azure Storage kapacitet, transaktioner, tillgänglighet, autentisering med mera. Du kan hitta en fullständig referens till mått som spåras [här](../../../../blobs/monitor-blob-storage-reference.md). Några användbara mått för att spåra är BlobCapacity – för att se till att du är kvar under den maximala [kapacitets gränsen för lagrings kontot](../../../../common/scalability-targets-standard-account.md), ingress och utgångs läge, för att spåra hur mycket data som skrivs till och läses från ditt Azure Storage-konto och SuccessE2ELatency – för att spåra tur och retur-tiden för förfrågningar till och från Azure Storage och din MediaAgent.

Du kan också [Skapa logg aviseringar](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) för att spåra Azure Storage tjänstens hälsa och se [Azures status instrument panel](https://status.azure.com/status) när som helst.

#### <a name="veeam-reporting"></a>Veeam-rapportering

- [Konfigurera Veeam en rapportering](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeam säkerhets kopiering och replikering av larm](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Så här öppnar du support ärenden

När du behöver hjälp med din säkerhets kopiering till Azure-lösning bör du öppna ett ärende med både Veeam och Azure. Detta hjälper våra support organisationer att samar beta, om det behövs.

#### <a name="to-open-a-case-with-veeam"></a>Öppna ett ärende med Veeam

På [kund support webbplatsen Veeam](https://www.veeam.com/support.html)loggar du in och öppnar ett ärende.

Information om vilka support alternativ som är tillgängliga för dig av Veeam finns i [kund support policyn för Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf).

Du kan också anropa för att öppna ett ärende: [internationella support nummer](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Öppna ett ärende med Azure

I [Azure Portal](https://portal.azure.com) Sök efter **support** i Sök fältet längst upp. Välj **Hjälp + Support**  ->  **ny supportbegäran**.

> [!NOTE]
> När du öppnar ett ärende är det viktigt att du behöver hjälp med Azure Storage eller Azure-nätverk. Ange inte Azure Backup. Azure Backup är namnet på en Azure-tjänst och ditt ärende kommer att dirigeras felaktigt.

### <a name="links-to-relevant-veeam-documentation"></a>Länkar till relevant dokumentation om Veeam

Mer information finns i följande Veeam-dokumentation:

- [Användar handbok för Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Arkitektur guide för Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Marketplace-erbjudanden

Du kan fortsätta att använda Veeam-lösningen som du känner till och litar på för att skydda dina arbets belastningar som körs på Azure. Veeam har gjort det enkelt att distribuera sin lösning i Azure och skydda Azure Virtual Machines och många andra Azure-tjänster.

- [Distribuera Veeam backup & replikering via Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Veeam för Azures säkerhets kopiering och återställning](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Nästa steg

Se följande resurser på Veeam-webbplatsen för information om specialiserade användnings scenarier:

- [Veeam How to Videos](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Tekniska dokument för Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeam Knowledge Base och vanliga frågor och svar](https://www.veeam.com/knowledge-base.html?ad=menu-resources)