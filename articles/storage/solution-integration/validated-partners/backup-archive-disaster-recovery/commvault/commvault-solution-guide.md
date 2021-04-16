---
title: Backa dina data till Azure med Commvault
titleSuffix: Azure Storage
description: Innehåller en översikt över faktorer att tänka på och steg att följa för att använda Azure som lagringsmål och återställningsplats för fullständig säkerhetskopiering och återställning av Commvault
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: fa60b6f002e49babc1e1f014bcb941e7953a43a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484786"
---
# <a name="backup-to-azure-with-commvault"></a>Säkerhetskopiera till Azure med Commvault

Den här artikeln hjälper dig att integrera en Commvault-infrastruktur med Azure Blob Storage. Den innehåller förutsättningar, överväganden, implementering och driftvägledning. Den här artikeln beskriver hur du använder Azure som ett säkerhetskopieringsmål på annan plats och en återställningsplats om en katastrof inträffar, vilket förhindrar normal drift på din primära plats.

> [!NOTE]
> Commvault erbjuder en lösning med lägre mål för återställningstid (RTO), Commvault Live Sync. Med den här lösningen kan du ha en virtuell dator i vänteläge som kan hjälpa dig att återställa snabbare i händelse av ett haveri i en Azure-produktionsmiljö. Dessa funktioner ligger utanför omfånget för det här dokumentet.

## <a name="reference-architecture"></a>Referensarkitektur

Följande diagram innehåller en referensarkitektur för distributioner lokalt till Azure och i Azure.

![Referensarkitektur för Commvault till Azure](../media/commvault-diagram.png)

Din befintliga Commvault-distribution kan enkelt integreras med Azure genom att lägga till ett Azure Storage-konto eller flera konton som ett molnlagringsmål. Med Commvault kan du även återställa säkerhetskopior lokalt i Azure, vilket ger dig en webbplats för återställning på begäran i Azure.

## <a name="commvault-interoperability-matrix"></a>Samverkansmatris för Commvault

| Arbetsbelastning | GPv2 och Blob Storage | Stöd för den coola nivån | Stöd för arkivnivå | Data Box-enhet supportfamilj |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Lokala virtuella datorer/data | v11.5 | v11.5 | v11.10 | v11.10 |
| Virtuella Azure-datorer | v11.5 | v11.5 | v11.5 | Ej tillämpligt |
| Azure-blobb | v11.6 | v11.6 | v11.6 | Ej tillämpligt |
| Azure Files | v11.6 | v11.6 | v11.6 | Ej tillämpligt |

## <a name="before-you-begin"></a>Innan du börjar

Lite startplanering hjälper dig att använda Azure som mål för säkerhetskopiering och återställning på annan plats.

### <a name="get-started-with-azure"></a>Komma igång med Azure

Microsoft erbjuder ett ramverk att följa för att komma igång med Azure. Den [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) är en detaljerad metod för företagets digitala omvandling och en omfattande guide för att planera molnintro sig på produktionsklass. CAF innehåller en stegvis konfigurationsguide för [Azure som](/azure/cloud-adoption-framework/ready/azure-setup-guide/) hjälper dig att komma igång snabbt och säkert. Du hittar en interaktiv version i [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Du hittar exempelarkitekturer, specifika metodtips för distribution av program och kostnadsfria utbildningsresurser som hjälper dig att komma i vägen för Azure-expertis.

### <a name="consider-the-network-between-your-location-and-azure"></a>Överväg nätverket mellan din plats och Azure

Oavsett om du använder molnresurser för att köra produktion, testning och utveckling, eller som mål för säkerhetskopiering och återställning, är det viktigt att du förstår bandbreddsbehoven för inledande start av säkerhetskopiering och pågående dagliga överföringar.

Azure Data Box är ett sätt att överföra din första säkerhetskopieringsbaslinje till Azure utan att kräva mer bandbredd. Detta är användbart om baslinjeöverföringen uppskattas ta längre tid än du kan tolerera. Du kan använda dataöverföringsberäknaren när du skapar ett lagringskonto för att uppskatta den tid som krävs för att överföra den första säkerhetskopian.

![Visar Azure Storage för dataöverföring i portalen.](../media/az-storage-transfer.png)

Kom ihåg att du behöver tillräckligt med nätverkskapacitet för att stödja dagliga dataöverföringar inom det nödvändiga överföringsfönstret (säkerhetskopieringsfönstret) utan att påverka produktionsprogram. I det här avsnittet beskrivs de verktyg och tekniker som är tillgängliga för att utvärdera dina nätverksbehov.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Fastställ hur mycket bandbredd du behöver

Använd följande resurser för att avgöra hur mycket bandbredd du behöver:

- Rapporter från din programvara för säkerhetskopiering.
- Commvault tillhandahåller standardrapporter för att fastställa [ändringsfrekvensen](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) och [den totala storleken](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) på uppsättningen säkerhetskopior för den första baslinjeöverföringen till Azure.
- Säkerhetskopiera programoberoende verktyg för utvärdering och rapportering som:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Fastställa outnyttjad Internetbandbredd

Det är viktigt att veta hur mycket normalt outnyttjad bandbredd (eller *utrymme)* som du har tillgänglig från dag till dag. Detta hjälper dig att bedöma om du kan uppfylla dina mål för:

- första gången du laddar upp när du inte använder Azure Data Box offline-seeding
- slutföra dagliga säkerhetskopieringar baserat på ändringstakten som identifierades tidigare och säkerhetskopieringsfönstret

Använd följande metoder för att identifiera bandbreddsutrymme som dina säkerhetskopior till Azure kan använda.

- Om du är en befintlig Azure ExpressRoute kan du visa din [kretsanvändning](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) i Azure Portal.
- Kontakta Internetleverantören. De bör kunna dela rapporter som visar din befintliga dagliga och månatliga användning.
- Det finns flera verktyg som kan mäta användningen genom att övervaka nätverkstrafiken på router-/switchnivå. Dessa omfattar:
  - [Solarwinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Välja rätt lagringsalternativ

När du använder Azure som mål för säkerhetskopiering använder du [Azure Blob Storage](../../../../blobs/storage-blobs-introduction.md). Blob Storage är Microsofts objektlagringslösning. Blob Storage är optimerat för att lagra stora mängder ostrukturerade data, vilket är data som inte följer någon datamodell eller definition. Dessutom är Azure Storage beständig, mycket tillgänglig, säker och skalbar. Du kan välja rätt lagring för din arbetsbelastning för att ge [återhämtningsnivån för att](../../../../common/storage-redundancy.md) uppfylla dina interna serviceavtal. Blob Storage är en tjänst där du betalar per användning. Du debiteras [månadsvis](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) för den mängd data som lagras, åtkomst till dessa data och, i fallet med nivåerna cool och arkiv, en minsta kvarhållningsperiod som krävs. Återhämtnings- och nivåalternativen för säkerhetskopierade data sammanfattas i följande tabeller.

**Återhämtningsalternativ för Blob Storage:**

|  |Lokalt redundant  |Zonredundant  |Geo-redundant  |Geo-zonredundant  |
|---------|---------|---------|---------|---------|
|**Gällande antal kopior**     | 3         | 3         | 6         | 6 |
|**Antal tillgänglighetszoner**     | 1         | 3         | 2         | 4 |
|**Antal regioner**     | 1         | 1         | 2         | 2 |
|**Manuell redundans till sekundär region**     | Saknas         | Saknas         | Ja         | Ja |

**Blob Storage-nivåer:**

|  | Hot-nivå   |Nivån Cool   | Arkivnivå |
| ----------- | ----------- | -----------  | -----------  |
| **Tillgänglighet** | 99,9 %         | 99 %         | Offline      |
| **Användningsavgifter** | Högre lagringskostnader, lägre åtkomst- och transaktionskostnader | Lägre lagringskostnader, högre åtkomst- och transaktionskostnader | Lägsta lagringskostnader, högsta åtkomst- och transaktionskostnader |
| **Minsta datalagring som krävs**| Ej tillämpligt | 30 dagar | 180 dagar |
| **Svarstid (tid till första byte)** | Millisekunder | Millisekunder | Tider |

#### <a name="sample-backup-to-azure-cost-model"></a>Exempel på säkerhetskopiering till Azure-kostnadsmodell

Med betalning per användning kan vara svår för kunder som är nya i molnet. Även om du bara betalar för den kapacitet som används betalar du även för transaktioner (läsa och eller skrivningar) och gå ut för data som läses tillbaka till din lokala miljö när [Azure Express Route](https://azure.microsoft.com/pricing/details/expressroute/) direct local eller Express Route unlimited data plan används där utgående [data](https://azure.microsoft.com/pricing/details/bandwidth/) från Azure ingår. Du kan använda [Azures priskalkylator för](https://azure.microsoft.com/pricing/calculator/) att utföra "vad om"-analys. Du kan basera analysen på listpriser eller [på Azure Storage för](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)reserverad kapacitet, vilket kan ge upp till 38 % besparingar. Här är ett exempel på en prisövning för att modellera månadskostnaden för att backa upp till Azure. Det här är bara ett exempel. *Priserna kan variera på grund av aktiviteter som inte avbildas här.*

|Kostnadsfaktor  |Månadskostnad  |
|---------|---------|
|100 TB säkerhetskopierade data på kall lagring     |1556,48 USD         |
|2 TB nya data som skrivits per dag x 30 dagar     |39 USD i transaktioner          |
|Uppskattad månatlig totalsumma     |1595,48 USD         |
|---------|---------|
|En tidsåterställning på 5 TB till lokal plats via offentligt Internet   | 491,26 USD         |

> [!NOTE]
> Den här uppskattningen genererades i Priskalkylatorn för Azure med betala per användning-priser för USA, östra och baseras på Commvault-standardstorleken på 32 MB under segment som genererar 65 536 PUT-begäranden (skrivtransaktioner) per dag. Det här exemplet kanske inte gäller för dina krav.

## <a name="implementation-guidance"></a>Implementeringsvägledning

Det här avsnittet innehåller en kort guide för hur du lägger Azure Storage till en lokal Commvault-distribution. Detaljerad vägledning och planeringsöverväganden finns i [Commvault Public Cloud Architecture guide for Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Öppna Azure Portal och sök efter **lagringskonton**. Du kan också klicka på standardikonen **lagringskonton.**

    ![Visar hur du lägger till ett lagringskonto i Azure Portal.](../media/azure-portal.png)
  
    ![Visar var du har skrivit lagring i sökrutan för Azure Portal.](../media/locate-storage-account.png)

2. Välj **Skapa för** att lägga till ett konto. Välj eller skapa en resursgrupp, ange ett unikt namn, välj region, välj **Standardprestanda,** lämna alltid typ av konto som **Storage V2,** välj den replikeringsnivå som uppfyller dina serviceavtal och standardnivån som din programvara för säkerhetskopiering ska tillämpas på. Ett Azure Storage-konto gör nivåerna hot, cool och archive tillgängliga inom ett enda konto och Commvault-principer så att du kan använda flera nivåer för att effektivt hantera livscykeln för dina data.

    ![Visar inställningar för lagringskonto i portalen](../media/account-create-1.png)

3. Behåll standardnätverksalternativen för tillfället och gå vidare **till Dataskydd.** Här kan du välja att aktivera mjuk borttagning, vilket gör att du kan återställa en säkerhetskopieringsfil som tagits bort av misstag inom den definierade kvarhållningsperioden och ger skydd mot oavsiktlig eller skadlig borttagning.

    ![Visar dataskyddsinställningarna i portalen.](../media/account-create-2.png)

4. Därefter rekommenderar vi standardinställningarna från skärmen **Avancerat för säkerhetskopiering** till Azure-användningsfall.

    ![Visar fliken Avancerade inställningar i portalen.](../media/account-create-3.png)

5. Lägg till taggar för organisation om du använder taggning och skapar ditt konto.

6. Två snabba steg krävs nu innan du kan lägga till kontot i Commvault-miljön. Gå till det konto som du skapade i Azure Portal och välj **Containrar** under **menyn Blob** Service. Lägg till en container och välj ett beskrivande namn. Gå sedan till objektet **Åtkomstnycklar** under **Inställningar och** kopiera **lagringskontots namn och** en av de två åtkomstnycklarna. Du behöver containernamnet, kontonamnet och åtkomstnyckeln i nästa steg.

    ![Visar hur du skapar containrar i portalen.](../media/container.png)

    ![Visar inställningar för åtkomstnyckel i portalen.](../media/access-key.png)

7. (*Valfritt*) Du kan lägga till ytterligare säkerhetslager i distributionen.

    1. Konfigurera rollbaserad åtkomst för att begränsa vem som kan göra ändringar i ditt lagringskonto. Mer information finns i [Inbyggda roller för hanteringsåtgärder.](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)
    1. Begränsa åtkomsten till kontot till specifika nätverkssegment med [lagringsbrandväggsinställningar](../../../../common/storage-network-security.md) för att förhindra åtkomstförsök utanför företagsnätverket.

        ![Visar inställningar för lagringsbrandväggen i portalen.](../media/storage-firewall.png)

    1. Ange ett [borttagningslås](../../../../../azure-resource-manager/management/lock-resources.md) för kontot för att förhindra oavsiktlig borttagning av lagringskontot.

        ![Visar inställning av ett borttagningslås i portalen.](../media/resource-lock.png)

    1. Konfigurera ytterligare [säkerhetsmetoder.](../../../../../storage/blobs/security-recommendations.md)

1. I Commvault Command Center går du till **Hantera**  ->  **Autentiseringshanteraren**  ->  . Välj **molnkonto,** **leverantörstyp** **för Microsoft Azure Storage**, välj **MediaAgent**, som ska överföra data till och från Azure, lägga till lagringskontots namn och åtkomstnyckel.

    ![Visar tillägg av autentiseringsuppgifter i Commvault Command Center.](../media/commvault-credential.png)

9. Gå sedan till **Lagringsmoln**  ->   i Commvault Command Center. Välj lägg **till**. Ange ett eget namn för lagringskontot och välj **Microsoft Azure Storage** i **listan** Typ. Välj en Media Agent-server som ska användas för att överföra säkerhetskopior till Azure Storage. Lägg till containern som du skapade, välj den lagringsnivå som ska användas i Azure Storage-kontot och välj de autentiseringsuppgifter som skapades i steg #8. Välj slutligen om du vill överföra deduplicerade säkerhetskopior eller inte och en plats för dedupliceringsdatabasen.

     ![Skärmbild av Commvaults Add cloud user interface (Lägg till molngränssnitt). I den nedrullningsna menyn Arkiv har **Arkiv** valts.](../media/commvault-add-storage.png)

10. Slutligen lägger du till din Azure Storage resurs i en befintlig eller ny plan i Commvault Command Center via **Hantera**  ->  **planer** som ett mål för säkerhetskopiering.

    ![Skärmbild av användargränssnittet för Commvault Command Center. Under **Hantera** i det vänstra navigeringsfönstret är **Planer** markerat.](../media/commvault-plan.png)

11. *(Valfritt)* Om du planerar att använda Azure som en återställningsplats eller Commvault för att migrera servrar och program till Azure är det bästa praxis att distribuera en VSA-proxy i Azure. Du hittar detaljerade anvisningar [här.](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)

## <a name="operational-guidance"></a>Verksamhetsvägledning

### <a name="azure-alerts-and-performance-monitoring"></a>Azure-aviseringar och prestandaövervakning

Det är lämpligt att övervaka både dina Azure-resurser och Commvaults möjlighet att utnyttja dem på samma sätt som med alla lagringsmål som du förlitar dig på för att lagra dina säkerhetskopior. En kombination av Azure Monitor och Commvault Command Center-övervakning hjälper dig att hålla din miljö felfri.

#### <a name="azure-portal"></a>Azure Portal

Azure tillhandahåller en robust övervakningslösning i form av [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Du kan [konfigurera Azure Monitor](../../../../blobs/monitor-blob-storage.md) att spåra Azure Storage kapacitet, transaktioner, tillgänglighet, autentisering med mera. Du hittar den fullständiga referensen till mått som samlas in [här.](../../../../blobs/monitor-blob-storage-reference.md) Några användbara mått att spåra är BlobCapacity – för att se till att du håller dig under den högsta gränsen för lagringskontots [kapacitet,](../../../../common/scalability-targets-standard-account.md)Ingress och Egress – för att spåra mängden data som skrivs till och läses från ditt Azure Storage-konto och SuccessE2ELatency – för att spåra tur och retur-tiden för begäranden till och från Azure Storage och din MediaAgent.

Du kan också [skapa logga aviseringar för](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) att spåra Azure Storage tjänstens hälsa och visa [Azure-statusinstrumentpanelen](https://status.azure.com/status) när som helst.

#### <a name="commvault-command-center"></a>Commvault Command Center

- [Skapa en avisering för molnlagringspooler](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Information om var du kan visa prestandarapporter, slutföra jobb och påbörja grundläggande felsökning finns i [Instrumentpaneler.](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm)

### <a name="how-to-open-support-cases"></a>Så här öppnar du supportärenden

När du behöver hjälp med säkerhetskopieringen till Azure-lösningen bör du öppna ett ärende med både Commvault och Azure. Detta hjälper våra supportorganisationer att samarbeta om det behövs.

#### <a name="to-open-a-case-with-commvault"></a>Öppna ett ärende med Commvault

Logga in [på Commvault-supportwebbplatsen](https://www.commvault.com/support)och öppna ett ärende.

Information om vilka supportavtalsalternativ som är tillgängliga finns i [Commvault-supportalternativ](https://ma.commvault.com/support)

Du kan också ringa in för att öppna ett ärende eller kontakta Commvault-supporten via e-post:

- Kostnadsfritt: +1 877-780-3077
- [Supportnummer över hela världen](https://ma.commvault.com/Support/TelephoneSupport)
- [Skicka e-post till Commvault-supporten](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Öppna ett ärende med Azure

I [Azure Portal](https://portal.azure.com) du **efter support** i sökfältet högst upp. Välj **Hjälp + support** Ny  ->  **supportbegäran.**

> [!NOTE]
> När du öppnar ett ärende måste du vara specifik för att du behöver hjälp Azure Storage eller Azure-nätverkstjänster. Ange inte Azure Backup. Azure Backup är namnet på en Azure-tjänst och ditt ärende dirigeras felaktigt.

### <a name="links-to-relevant-commvault-documentation"></a>Länkar till relevant Commvault-dokumentation

Mer information finns i följande Commvault-dokumentation:

- [Användarhandbok för Commvault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Guide för Commvault Azure-arkitektur](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

### <a name="marketplace-offerings"></a>Marketplace-erbjudanden

Commvault har gjort det enkelt att distribuera sin lösning i Azure för att skydda Azure Virtual Machines och många andra Azure-tjänster. Läs mer i följande referenser:

- [Distribuera Commvault via Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Commvault för Azure-datablad](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Commvaults lista över Azure-funktioner och -tjänster som stöds](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Använda Commvault för att skydda SAP HANA i Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Nästa steg

Se dessa ytterligare Commvault-resurser för information om specialiserade användningsscenarier.

- [Använda Commvault för att migrera dina servrar och program till Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Skydda SAP i Azure med Commvault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Skydda Office365 med Commvault](https://www.youtube.com/watch?v=dl3nvAacxZU)