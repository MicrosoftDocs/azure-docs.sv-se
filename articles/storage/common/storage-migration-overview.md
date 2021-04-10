---
title: Guide för Azure Storage migrering
description: Översikts guide för lagringsmigrering beskriver grundläggande rikt linjer för lagringsmigrering
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f6f00075c7c66679281d776f9472ec4a1a590d76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231652"
---
# <a name="azure-storage-migration-overview"></a>Översikt över Azure Storage migrering

Den här artikeln fokuserar på lagringsmigrering till Azure och ger vägledning om följande scenarier för migrering av lagring:

- Migrering av ostrukturerade data, till exempel filer och objekt
- Migrering av blockbaserade enheter, till exempel diskar och San-nätverk (Storage Area Network)

## <a name="migration-of-unstructured-data"></a>Migrering av ostrukturerade data

Migrering av ostrukturerade data innehåller följande scenarier:

- Filmigrering från nätverksansluten lagring (NAS) till något av Azure File-erbjudanden:
  - [Azure Files](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [oberoende program varu leverantörer (ISV)-lösningar](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).
- Migrering av objekt från objekt lagrings lösningar till Azure Object Storage-plattformen:
  - [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>Faser för migrering

En fullständig migrering består av flera olika faser: identifiering, utvärdering och migrering.

![Diagram som visar identifierings-, utvärderings-och migrerings faserna för migreringen](./media/storage-migration-overview/migration-phases.png)

#### <a name="discovery-phase"></a>Identifierings fas

I identifierings fasen bestämmer du alla källor som måste migreras som SMB-resurser, NFS-exporter eller objekt namn rymder. Du kan göra den här fasen manuellt eller använda automatiserade verktyg.

#### <a name="assessment-phase"></a>Utvärderings fas

Utvärderings fasen är viktig för att förstå tillgängliga alternativ för migreringen. För att minska risken för migreringen och för att undvika vanliga fall GRO par följer du dessa tre steg:

| Steg i utvärderings fasen                     | Alternativ                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Välj en mål lagrings tjänst**            | – Azure Blob Storage och Data Lake Storage<br>-Azure Files<br>-Azure NetApp Files<br>– ISV-lösningar |
| **Välj en metod för migrering**                  | – Online<br>– Offline<br> – Kombination av båda                                  |
| **Välj det bästa verktyget för migrering för jobbet** | – Kommersiella verktyg (Azure och ISV)<br> – Öppen källkod                             


Det finns flera kommersiella (ISV) verktyg som kan hjälpa dig med utvärderings fasen. Se [jämförelse matrisen](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

##### <a name="choose-a-target-storage-service"></a>Välj en mål lagrings tjänst

Att välja en mål lagrings tjänst beror på vilket program eller vilka användare som har åtkomst till data. Det korrekta valet beror på både tekniska och ekonomiska aspekter. Genomför först en teknisk utvärdering för att utvärdera möjliga mål och fastställa vilka tjänster som uppfyller kraven. Gör sedan en finansiell utvärdering för att fastställa det bästa valet.

Om du vill hjälpa till att välja mål lagrings tjänst för migreringen ska du utvärdera följande aspekter av varje tjänst:

- Protokollstöd
- Prestanda egenskaper
- Begränsningar för mål lagrings tjänsten

Följande diagram är ett förenklat besluts träd som hjälper dig att hjälpa dig med den rekommenderade Azure File Service. Om interna Azure-tjänster inte uppfyller kraven kommer en rad [oberoende program varu leverantör (ISV)-lösningar](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview) .

När du har slutfört den tekniska utvärderingen och valt rätt mål gör du en kostnads bedömning för att fastställa det mest kostnads effektiva alternativet.

![Grundläggande besluts träd för att välja rätt fil tjänst](./media/storage-migration-overview/files-decision-tree.png)

För att hålla besluts trädet enkelt, är gränserna för mål lagrings tjänsten inte inkluderade i diagrammet. Om du vill veta mer om aktuella gränser och avgöra om du behöver ändra dina val utifrån dem, se:

- [Lagrings konto gränser](/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
- [Blob Storage gränser](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-blob-storage-limits)
- [Skalbarhet för Azure Files och prestandamål](/azure/storage/files/storage-files-scale-targets)
- [Azure NetApp Files resurs gränser](/azure/azure-netapp-files/azure-netapp-files-resource-limits)

Om någon av gränserna utgör en blockering för att använda en tjänst, stöder Azure flera lagrings leverantörer som erbjuder sina lösningar på Azure Marketplace. Information om verifierade ISV-partner som tillhandahåller fil tjänster finns [Azure Storage partner för primär och sekundär lagring](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).

##### <a name="select-the-migration-method"></a>Välj metod för migrering

Det finns två grundläggande metoder för migrering för lagringsmigrering.

- **Online**. Online-metoden använder nätverket för migrering av data. Antingen det offentliga Internet-eller [Azure-ExpressRoute](/azure/expressroute/expressroute-introduction) kan användas. Om tjänsten inte har en offentlig slut punkt måste du använda ett VPN med offentligt Internet.
- **Anslutningen.** Metoden offline använder en av de [Azure Data Box](https://azure.microsoft.com/services/databox/) enheterna.

Beslutet att använda en online-metod kontra en offline-metod beror på den tillgängliga nätverks bandbredden. Online-metoden rekommenderas i fall där det finns tillräckligt med nätverks bandbredd för att utföra en migrering inom den tids linje som behövs.

Det är möjligt att använda en kombination av båda metoderna, offline-metod för den första Mass migreringen och en online-metod för stegvis migrering av ändringar. Att använda båda metoderna samtidigt kräver en hög samordnings nivå och rekommenderas inte av den anledningen. Om du väljer att använda båda metoderna för att isolera de data uppsättningar som migreras online från de data uppsättningar som migreras offline.

Mer information om olika metoder och rikt linjer för migrering finns i [Välj en Azure-lösning för data överföring](/azure/storage/common/storage-choose-data-transfer-solution) och [migrera till Azure-filresurser](/azure/storage/files/storage-files-migration-overview).

##### <a name="choose-the-best-migration-tool-for-the-job"></a>Välj det bästa verktyget för migrering för jobbet

Det finns olika Migreringsverktyg som du kan använda för att utföra migreringen. Vissa är öppen källkod som AzCopy, Robocopy, xcopy och rsync medan andra är kommersiella. En lista över tillgängliga kommersiella verktyg och en jämförelse mellan dem finns på vår [jämförelse mat ris](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

Verktyg med öppen källkod passar bra för storskaliga migreringar. För migrering från Windows-filservrar till Azure Files rekommenderar Microsoft att du börjar med Azure Files inbyggda funktioner och använder [Azure File Sync](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-file-sync). För mer komplexa migreringar som består av olika källor, stora kapaciteter eller särskilda krav som begränsning eller detaljerad rapportering med gransknings funktioner, är kommersiella verktyg det bästa valet. De här verktygen gör migreringen enklare och minskar risken markant. De flesta kommersiella verktyg kan också utföra identifieringen, vilket ger en värdefull inblandning av utvärderingen.

#### <a name="migration-phase"></a>Migrerings fas

Migrations fasen är det sista steget för migrering som utför data flytt och migrering. Vanligt vis körs du genom migreringen flera gånger för att uppnå en enklare växling. Fasen migrering består av följande steg:

1. **Första migreringen.** Steget första migreringen migrerar alla data från källan till målet. Det här steget migrerar mängden data som behöver migreras.
2. **Omsynkronisering.** En omsynkronisering migrerar alla data som har ändrats efter det första steget i migreringen. Du kan upprepa det här steget flera gånger om det finns många ändringar. Målet med att köra flera omsynkroniseringar är att minska den tid det tar för det sista steget. Du kan hoppa över det här steget för inaktiva data och för data som inte har några ändringar (t. ex. säkerhets kopiering eller Arkiv av data).
3. **Slutlig växling**. Steget slutlig omgång växlar den aktiva användningen av data från källan till målet och drar tillbaka källan.

Varaktigheten för migreringen för ostrukturerade data beror på flera aspekter. Utanför den valda metoden är de mest kritiska faktorerna Total storlek för data-och fil storleks distribution. Större data uppsättningar, desto längre tid för migreringen. Ju mindre den genomsnittliga fil storleken är, den längre tiden för migreringen. Om du har ett stort antal små filer bör du arkivera dem i större filer (t. ex. i en. tar-eller. zip-fil), om det är tillämpligt, för att minska den totala migrerings tiden.

## <a name="migration-of-block-based-devices"></a>Migrering av blockbaserade enheter

Migrering av blockbaserade enheter utförs vanligt vis som en del av den virtuella datorn eller fysisk värd migrering. Det är en vanlig felbegrepp att fördröja block lagrings beslut tills efter migreringen. Att fatta dessa beslut i förväg med lämpliga överväganden för arbets belastnings krav leder till en smidig migrering till molnet.

Information om hur du kan använda arbets belastningar för att migrera och gå till finns i [Azure-disklagring-dokumentationen](/azure/virtual-machines/disks-types)och resurserna på [sidan disklagring produkt](https://azure.microsoft.com/services/storage/disks/#resources). Du kan lära dig om vilka diskar som uppfyller dina krav och de senaste funktionerna, till exempel [disk bursting](/azure/virtual-machines/disk-bursting). Information om hur du migrerar de virtuella datorerna tillsammans med de underliggande blockbaserade enheterna finns i [Azure Migrate](/azure/migrate/) -dokumentationen.

## <a name="see-also"></a>Se även

- [Välja en Azure-lösning för dataöverföring](/azure/storage/common/storage-choose-data-transfer-solution)
- [Jämförelse av verktyg för kommersiell migrering](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Migrera till Azure-filresurser](/azure/storage/files/storage-files-migration-overview)
- [Migrera till Data Lake Storage med WANdisco LiveData-plattformen för Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Kopiera eller flytta data till Azure Storage med AzCopy](https://aka.ms/azcopy)
- [Migrera stora data uppsättningar till Azure Blob Storage med AzReplicate](https://github.com/Azure/AzReplicate/tree/master/)