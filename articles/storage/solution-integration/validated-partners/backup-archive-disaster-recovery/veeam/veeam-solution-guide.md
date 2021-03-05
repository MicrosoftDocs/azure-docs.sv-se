---
title: Säkerhetskopiera dina data till Azure med Veeam
titleSuffix: Azure Blob Storage Docs
description: Webb sidan ger en översikt över faktorer att överväga och steg att följa för att utnyttja Azure som ett lagrings mål och en återställnings plats för Veeam säkerhets kopiering och återställning
keywords: Veeam,, säkerhets kopiering till molnet, säkerhets kopiering, säkerhets kopiering till Azure, haveri beredskap, affärs kontinuitet
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: 6d4b005a3f9c79ff14f5ba4053dc651c1ede56e0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124439"
---
# <a name="backup-to-azure-with-veeam"></a>Säkerhetskopiera till Azure med Veeam

Den här artikeln innehåller en guide för att integrera en Veeam-infrastruktur med Azure Blob Storage. Den innehåller krav, Azure Storage principer, implementering och operativa rikt linjer. Den här artikeln handlar bara om att använda Azure som säkerhets kopierings mål på plats och en återställnings plats i händelse av en katastrof, vilket förhindrar normal drift på den primära platsen. Veeam erbjuder också en lägre RTO-lösning, Veeam replikering, vilket innebär att en virtuell dator i vänte läge kan starta och återställa snabbare i händelse av katastrof och skydd av resurser i en Azure-produktions miljö. Veeam dedikerar också verktyg för att säkerhetskopiera Azure-och Office 365-resurser. Dessa funktioner är utanför omfånget för det här dokumentet. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Referens arkitektur för lokal till Azure och In-Azure distributioner

![Referens arkitektur för Veeam till Azure](../media/veeam-architecture.png)

Din befintliga Veeam-distribution kan enkelt integreras med Azure genom att lägga till ett Azure Storage konto eller flera konton som en lagrings plats för moln säkerhets kopior. Med Veeam kan du också återställa säkerhets kopior från lokala platser i Azure med en återställnings-på-begäran-webbplats i Azure.

## <a name="veeam-interoperability-matrix"></a>Veeam för interoperabilitet
| Arbetsbelastning | GPv2 och Blob Storage | Support på låg frekvent nivå | Arkiv nivå stöd | Stöd för Data Box-enhet-familjen |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokala virtuella datorer/data | v10a | v10a | NA | 10a |
| Virtuella Azure-datorer | v10a | v10a | NA | 10a |
| Azure-blobb | v10a | v10a | NA | 10a |
| Azure Files | v10a | v10a | NA | 10a | 

> [!Note]
Veeam-säkerhetskopiering och replikering stöder REST API endast för Azure Data Box, så Azure Data Box Disk stöds därför inte. Stöd för Arkiv nivån i Azure Blob Storage förväntas i Veeam v11.

## <a name="before-you-begin"></a>Innan du börjar

En lite direkt planering ser till att du ansluter till många av de många nöjda kunder som använder Azure som en extern säkerhets kopierings mål och återställnings plats.

### <a name="are-you-new-to-azure"></a>Är du nybörjare på Azure?

Microsoft erbjuder ett ramverk som följer för att komma igång med Azure. [Cloud adoption Framework](https://docs.microsoft.com/azure/architecture/cloud-adoption/) - \( CAF \) är en detaljerad metod för digital transformering i företag och omfattande vägledning för att planera ett moln införande i en produktions klass. CAF innehåller en steg-för-steg- [Guide för Azure-installationen](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) för de nya i Azure som hjälper dig att komma igång snabbt och säkert och du kan hitta en interaktiv version i [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Du hittar exempel arkitekturer och metod tips för att distribuera program och kostnads fria utbildnings resurser för att ge dig på vägen till Azures expertis.

### <a name="consider-the-network-between-your-location-and-azure"></a>Överväg nätverket mellan din plats och Azure

Oavsett om du använder moln resurser för att köra produktion, testning och utveckling eller som en säkerhets kopierings mål och återställnings plats är det viktigt att förstå dina bandbredds behov för inledande säkerhets kopiering och för pågående dagliga överföringar.

Azure Data Box är ett sätt att överföra din första säkerhets kopierings bas linje till Azure utan att kräva ytterligare bandbredd om bas linje överföringen är beräknad för att ta längre tid än du kan tolerera. Du kan utnyttja Dataöverföring uppskattningen när du skapar ett lagrings konto för att beräkna den tid som krävs för att överföra den första säkerhets kopian.

![Azure Storage Dataöverföring-uppskattning](../media/az-storage-transfer.png)

Kom ihåg att du behöver tillräckligt med nätverks kapacitet för att stödja dagliga data överföringar i fönstret för överföring av begärda data (säkerhets kopierings fönster) utan att påverka produktions program. Det här avsnittet beskriver de verktyg och tekniker som finns tillgängliga för att bedöma dina nätverks behov.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Hur kan du avgöra hur mycket bandbredd du behöver?

Det finns flera utvärderings alternativ för att fastställa ändrings takten och den totala storleken för säkerhets kopierings uppsättningen för den inledande bas linje överföringen till Azure. Här följer några exempel på bedömnings-och rapporterings verktyg som:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>Hur vet jag hur mycket utrymme jag har med min nuvarande Internet anslutning?

Det är viktigt att veta hur mycket utrymme som används, eller vanligt vis ej utnyttjat, bandbredd som du har tillgängligt på en dag till dags basis. Detta gör att du kan utvärdera om du kan uppfylla dina mål för inledande tid att ladda upp, om du inte använder Azure Data Box för seeding offline och för att slutföra dagliga säkerhets kopieringar baserat på ändrings frekvensen som anges ovan och säkerhets kopierings fönstret. Nedan finns metoder som du kan använda för att identifiera bandbredds utrymme som dina säkerhets kopieringar till Azure är kostnads fria att använda.

- Är du en befintlig Azure ExpressRoute-kund? Visa din [krets användning](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) i Azure Portal.
- Du kan kontakta din Internet leverantör. De bör ha rapporter för att dela med dig som illustrerar din befintliga dagliga och månatliga användning.
- Det finns flera verktyg som kan mäta användningen genom att övervaka din nätverks trafik på router-/växel nivån, inklusive:
  - [SolarWinds Bandwidth Analyzer-paket](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco-nätverks assistent](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp guld](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>Välja rätt lagrings alternativ

När du använder Azure som ett säkerhets kopierings mål använder kunderna [azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. Azure Blob Storage är Microsofts lösning för objekt lagring. Blob Storage är optimerat för att lagra enorma mängder ostrukturerade data, som är data som inte följer någon data modell eller definition. Dessutom är Azure Storage tåligt, hög tillgängligt, säkert och skalbart. Microsofts plattform erbjuder flexibilitet för att välja rätt lagring för rätt arbets belastning för att tillhandahålla den [återhämtnings nivå](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) som passar din interna service avtal. Blob Storage är en tjänst för betalning per användning. Du [debiteras månads vis](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) för den mängd data som lagras, till gång till dessa data och-i fallet med låg frekvent lagring och Arkiv lag ring – en minsta begär ande kvarhållningsperiod. De återhämtnings-och nivåers alternativ som gäller för säkerhetskopierade data sammanfattas i tabellerna nedan.

**Azure Blob Storage återhämtnings alternativ:**

|  |Lokalt redundant  |Zonen är redundant  |Geografiskt redundant  |Redundant geo-zon  |
|---------|---------|---------|---------|---------|
|Gällande antal kopior     | 3         | 3         | 6         | 6 |
|antal Tillgänglighetszoner     | 1         | 3         | 2         | 4 |
|antal regioner     | 1         | 1         | 2         | 2 |
|Manuell redundans till sekundär region     | NA         | Ej tillämpligt         | Ja         | Ja |

**Azure Blob Storage-nivåer:**

|  | Frekvent nivå   |Låg frekvent nivå   | Arkiv lag ring |
| ----------- | ----------- | -----------  | -----------  |
| Tillgänglighet | 99,9 %         | 99 %         | Offline      |
| Avgifter för användning | Högre kostnader för lagring, lägre åtkomst och transaktionskostnader | Lägre kostnader för lagring, högre åtkomst och transaktionskostnader | Lägsta kostnader för lagring, högsta åtkomst och transaktionskostnader |
| Minsta data lagring krävs | NA | 30 dagar | 180 dagar |
| Svars tid (tid till första byte) | Millisekunder | Millisekunder | Tider |

#### <a name="sample-backup-to-azure-cost-model"></a>Exempel på säkerhets kopiering till Azures kostnads modell

Konceptet betala per användning kan avskräckande till kunder som är nya för det offentliga molnet. Även om du bara betalar för den kapacitet som används betalar du även för transaktioner (läsning och skrivning) och utgångs punkt [för data](https://azure.microsoft.com/pricing/details/bandwidth/) som läses tillbaka till din lokala miljö när [Azure Express Route Direct Local eller Express Route unlimiting data plan](https://azure.microsoft.com/pricing/details/expressroute/) används där data som utgående från Azure ingår. Du kan utföra vad som händer om du analyserar utifrån List prissättning eller med [Azure Storage reserverade kapacitets priser](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations), vilket kan leverera upp till 38% besparingar i [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/). Här är ett exempel på prissättning för att modellera månads kostnaden för säkerhets kopiering till Azure, detta är bara ett exempel och ***din prissättning kan variera på grund av aktiviteter som inte har registrerats här:***


|Kostnads faktor  |Månatlig kostnad  |
|---------|---------|
|100 TB säkerhets kopierings data i cool Storage     |$1556,48         |
|2 TB nya data som skrivs per dag x 30 dagar     |$72 i transaktioner          |
|Månatlig uppskattad summa     |$1628,48         |
|---------|---------|
|En tids återställning på 5 TB till lokal över offentligt Internet   | $527,26         |

> [!Note]
Den här uppskattningen genererades i pris Kalkylatorn för Azure med hjälp av amerikanska priser för att betala per användning och baseras på Veeam standard på 256 KB segment storlek för WAN-överföringar. Det här exemplet kanske inte gäller för dina krav.

## <a name="implementation-and-operational-guidance"></a>Implementering och operationell vägledning

Det här avsnittet innehåller en kort guide för att lägga till Azure Storage till en lokal Veeam-distribution. Om du är intresse rad av detaljerad vägledning och planerings överväganden rekommenderar vi att du går igenom [Veeam Cloud Connect backup guide](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Öppna Azure Portal och Sök efter "lagrings konton" eller klicka på standard tjänst ikonen.

      ![Azure Portal](../media/azure-portal.png)

      ![Lagrings konton i Azure-portalen](../media/locate-storage-account.png)

2. Välj att lägga till ett konto och välj eller skapa en resurs grupp, ange ett unikt namn, Välj region, välj "standard"-prestanda, lämna alltid konto typ som "Storage v2", Välj den replikeringsprincip som uppfyller dina service avtal och standard nivån som säkerhets kopierings programmet använder. Ett Azure Storage-konto gör frekventa, frekventa och Arkiv nivåer tillgängliga i ett enda konto och Veeam-principer gör att du kan använda flera nivåer för att effektivt hantera data livs cykeln. Gå vidare till nästa steg. 
    
      ![Skapa ett lagrings konto](../media/account-create-1.png)

3. Håll dig med standard nätverks alternativen för tillfället och gå vidare till "data skydd". Här kan du välja att aktivera "mjuk borttagning", vilket gör att du kan återställa en säkerhets kopia av misstag som tagits bort av misstag inom den definierade kvarhållningsperioden och ger skydd mot oavsiktlig eller skadlig borttagning. 
    ![Skapa ett lagrings konto del 2](../media/account-create-2.png)

4. Sedan rekommenderar vi standardinställningarna från den "avancerade" skärmen för säkerhets kopiering till Azures användnings fall.

    ![Skapa ett lagrings konto del 3](../media/account-create-3.png) 

5. Lägg till taggar för organisationen om du utnyttjar taggning och skapa ditt konto. Nu har du petabyte för lagring på begäran när du har ditt förfogande!

6. Två snabba steg är nu obligatoriska innan du kan lägga till kontot i Veeam-miljön. Navigera till det konto som du skapade i Azure-portalen och välj "behållare" under "BLOB service"-menyn på Portal bladet. Lägg till en ny behållare och välj ett meningsfullt namn. Gå sedan till objektet "åtkomst nycklar" under "Inställningar" och kopiera "lagrings kontots namn" och en av de två åtkomst nycklarna. Du behöver behållar namn, konto namn och åtkomst nyckel i nästa steg.

    ![Skapa en behållare](../media/container-b.png)
    
    ![Hämta konto informationen](../media/access-key.png)
    
    > [!Note]
Veeam säkerhets kopiering och replikering erbjuder ytterligare alternativ för att ansluta till Azure. För användnings fallet för den här artikeln är det rekommenderade tillvägagångs sättet att använda Microsoft Azure Blob Storage som ett säkerhets kopierings mål.

7. ***(Valfritt)*** Du kan lägga till ytterligare säkerhets lager i din distribution.

     1. Konfigurera rollbaserad åtkomst för att begränsa vem som kan göra ändringar i ditt lagrings konto. [Läs mer här](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. Begränsa åtkomsten till kontot till vissa nätverks segment med [lagrings brand väggen](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) för att förhindra åtkomst försök utanför företagets nätverk.

    ![Lagrings brand vägg](../media/storage-firewall.png) 

    1. Ange ett [borttagnings lås](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) för kontot för att förhindra oavsiktlig borttagning av lagrings kontot.

    ![Resurs lås ](../media/resource-lock.png) 1.) Konfigurera ytterligare [rekommenderade säkerhets metoder](https://docs.microsoft.com/azure/storage/blobs/security-recommendations). 
8. I Veaam för säkerhets kopiering och replikering navigerar du till säkerhets kopierings infrastruktur--> högerklickar du i översikts fönstret och väljer Lägg till säkerhets kopierings lager för att öppna konfigurations guiden. I dialog rutan väljer du objekt lagring--> Microsoft Azure Blob Storage--> Azure Blob Storage.
    
    ![Veeam-databas-guide skärm a](../media/veeam-repo-a.png)

    ![Guide för Veeam-lagringsplats, skärm b](../media/veeam-repo-b.png)

    ![Guiden Veeam-lagringsplats, skärm c](../media/veeam-repo-c.png)

9. Ange sedan ett namn och en beskrivning av din nya Microsoft Azure Blob-lagringsplats.
    
    ![Veeam för databas i guiden d](../media/veeam-repo-d.png)

10. I nästa steg lägger du till autentiseringsuppgifterna för att få åtkomst till ditt Azure Storage-konto. Välj "Microsoft Azure Storage konto" i moln hanteraren för autentiseringsuppgifter och ange ditt lagrings konto namn och åtkomst nyckel. Välj Azure Global i region väljaren och eventuell Gateway-server om tillämpligt.
    
    ![Guiden Veeam-lagringsplats, skärm e](../media/veeam-repo-e.png)

> [!Note]
Om du väljer att inte använda en Veeam Gateway-server måste du se till att alla lagrings platser har direkt åtkomst till Internet.

11. I behållarens register väljer du din Azure Storage behållare och väljer eller skapar en mapp för att lagra dina säkerhets kopior i. Du kan också definiera en mjuk gräns för den övergripande lagrings kapaciteten som ska användas av Veeam (rekommenderas). Granska den information som visas i avsnittet Sammanfattning och slutför konfigurations verktyget. Den nya lagrings platsen kan sedan väljas i konfigurationen för säkerhets kopierings jobbet.

    ![Skärm f i Veeam-lagringsplatsens guide](../media/veeam-repo-f.png)
    
    ![Guide för Veeam-databas, skärm g](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Azure-aviseringar och prestanda övervakning

Vi rekommenderar att du övervakar både dina Azure-resurser och Veeam för att utnyttja dem på samma sätt som med ett lagrings mål som du förlitar dig på för att lagra dina säkerhets kopior. En kombination av Azure Monitor-och Veeam-övervakningsfunktionerna (dvs. fliken statistik i noden jobb i hanterings konsolen för Veeam eller flera avancerade alternativ som Veeam) hjälper dig att hålla miljön felfri.

#### <a name="microsoft-azure-portal"></a>Microsoft Azure-portalen

Microsoft Azure ger en robust övervaknings lösning i form av [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). Du kan [konfigurera Azure Monitor](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) att spåra Azure Storage kapacitet, transaktioner, tillgänglighet, autentisering med mera. Du kan hitta en fullständig referens till mått som spåras [här](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Några användbara mått för att spåra är BlobCapacity – för att se till att du är kvar under den maximala [kapacitets gränsen för lagrings kontot](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), ingress och utgångs läge för att spåra hur mycket data som skrivs till och läses från ditt Azure Storage-konto och SuccessE2ELatency – för att spåra tur och retur-tiden för förfrågningar till och från Azure Storage och din MediaAgent. 

Du kan också [Skapa logg aviseringar](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) för att spåra Azure Storage tjänstens hälsa och visa [Azures status instrument panel](https://status.azure.com/status) när som helst.

#### <a name="veeam-reporting"></a>Veeam-rapportering

[Konfigurera Veeam en rapportering](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Veeam säkerhets kopiering och replikering av larm](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>Så här öppnar du support ärenden

När du behöver hjälp med säkerhets kopiering till Azure-lösningen rekommenderar vi att du öppnar ett ärende med både Veeam och Azure så att våra support organisationer kan engagera sig kollektivt, om det behövs.

#### <a name="how-to-open-a-case-with-veeam"></a>Så här öppnar du ett ärende med Veeam

Gå till [Veeam kund support Portal](https://www.veeam.com/support.html), logga in och öppna ett ärende.

Om du behöver förstå de support alternativ som är tillgängliga för dig av Veeam, se [Veeam kund support policy](https://www.veeam.com/veeam_software_support_policy_ds.pdf)

Du kan också anropa i för att öppna ett ärende:

[Worldwide support-nummer](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Så här öppnar du ett ärende med support teamet för Azure

I [Azure Portal](https://portal.azure.com) Sök efter "support" i Sök fältet längst upp i portalen och välj "+ ny supportbegäran" 
> [!Note]
När du öppnar ett ärende måste du vara särskilt tvungen att ha hjälp med "Azure Storage" eller "Azure-nätverk" och **inte** "Azure Backup". Azure Backup är en Microsoft Azure inbyggd tjänst och ditt ärende kommer att dirigeras felaktigt.

### <a name="links-to-relevant-veeam-documentation"></a>Länkar till relevant dokumentation om Veeam

Dokumentation om Veeam ger ytterligare information:

[Användar handbok för Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Arkitektur guide för Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>Länk till Marketplace-erbjudande

Du kan även fortsätta att använda Veeam-lösningen du känner till och litar på för att skydda dina arbets belastningar som körs på Azure. Veeam har gjort det enkelt att distribuera sin lösning i Azure och skydda Azure Virtual Machines och många andra Azure-tjänster.

[Distribuera Veeam B&R via Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Azure-datablad](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>Nästa steg

Utforska ytterligare resurser på de här externa webbplatserna för att få information om specialiserade användnings scenarier:

[Veeam How to Videos](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Tekniska dokument för Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Veeam Knowledge Base och vanliga frågor och svar](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
