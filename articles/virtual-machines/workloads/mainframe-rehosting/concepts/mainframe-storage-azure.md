---
title: Flytta stordator lagring till Azure Storage
description: Massivt skalbara Azure Storage-resurser kan hjälpa stordatorbaserade organisationer att migrera och modernisera IBM Z14-program.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 27f625e7fced5ff710a6fa0e8b2045a8c5434258
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954368"
---
# <a name="move-mainframe-storage-to-azure"></a>Flytta stordator lagring till Azure

Om du vill köra stordator belastningar på Microsoft Azure måste du veta hur din stordators funktioner jämför med Azure. De enorma skalbara lagrings resurserna kan hjälpa organisationer att börja modernisera utan att behöva överge de program de använder.

Azure ger stordatorer som liknar funktioner och lagrings kapacitet som är jämförbara med IBM Z14-baserade system (den mest aktuella modellen som skrivs). Den här artikeln beskriver hur du får jämförbara resultat på Azure.

## <a name="mainframe-storage-at-a-glance"></a>En snabbt ITT på stordator lagring

IBM-stordatoren karaktäriserar lagring på två sätt. Den första är en lagrings enhet med direkt åtkomst (DASD). Det andra är sekventiell lagring. För att hantera lagring tillhandahåller stordatorer DFSMS (data Facility Storage Management Subsystem). Den hanterar data åtkomst till de olika lagrings enheterna.

[Dasd](https://en.wikipedia.org/wiki/Direct-access_storage_device) refererar till en separat enhet för sekundär lagring (inte i minnet) som gör det möjligt att använda en unik adress för direkt åtkomst till data. Från början gjorde termen DASD till snurrande diskar, Magnet fat eller data celler. Men nu kan termen också gälla för lagrings enheter med solid state (SSD), San (Storage Area Network), Network Attached Storage (NAS) och optiska enheter. I det här dokumentet avser DASD snurrande diskar, San och SSD.

I motsats till DASD-lagring, refererar sekventiell lagring på en stordator till enheter som band enheter där data nås från en start punkt och sedan läses eller skrivs på en linje.

Lagrings enheter är vanligt vis kopplade via en fiber anslutning (FICON) eller nås direkt på stordatorens IO-buss med [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), en IBM-teknik för snabb kommunikation mellan partitioner på en server med en hypervisor.

De flesta stordator system är separata lagrings utrymmen i två typer:

- *Online-lagring* (även kallad frekvent lagring) behövs för dagliga åtgärder. DASD-lagring används vanligt vis för det här ändamålet. Sekventiell lagring, t. ex. dagliga band säkerhets kopieringar (logiska eller fysiska), kan också användas för detta ändamål.

- *Arkiv lag* ring (kallas även kall lagring) är inte garanterat att monteras vid en viss tidpunkt. I stället är den monterad och används vid behov. Arkiv lag ring är ofta implementerat med sekventiella band säkerhets kopior (logiska eller fysiska) för lagring.

## <a name="mainframe-versus-io-latency-and-iops"></a>Stordator jämfört med i/o-latens och IOPS

Stordatorer används ofta för program som kräver hög prestanda i/o-svars tid. De kan göra detta med hjälp av FICON-anslutningar till IO-enheter och HiperSockets. När HiperSockets används för att ansluta program och enheter direkt till en stordators IO-kanal kan svars tiden i mikrosekunderna uppnås.

## <a name="azure-storage-at-a-glance"></a>En snabbt ITT på Azure Storage

Azure Infrastructure-as-a-Service ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) alternativ för lagring ger jämförbar stordator kapacitet.

Microsoft erbjuder petabyte lagring för program som finns i Azure och du har flera lagrings alternativ. Detta sträcker sig från SSD-lagring för hög prestanda till blob-lagring med låg kostnad för Mass lagring och arkiv. Dessutom tillhandahåller Azure ett alternativ för dataredundans för lagring – något som tar mer ansträngning att konfigurera i en stordator miljö.

Azure Storage är tillgängligt som [Azure-diskar](../../../managed-disks-overview.md), [Azure Files](../../../../storage/files/storage-files-introduction.md)och [Azure-blobbar](../../../../storage/blobs/storage-blobs-overview.md) som följande tabell sammanfattar. Läs mer om [när du ska använda dem](../../../../storage/common/storage-introduction.md).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Typ</th><th>Beskrivning</th><th>Använd när du vill:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Tillhandahåller ett SMB-gränssnitt, klient bibliotek och ett <a href="/rest/api/storageservices/file-service-rest-api">rest</a> -gränssnitt som ger åtkomst från var som helst till lagrade filer.
</td>
<td><ul>
<li>Lyft och flytta ett program till molnet när programmet använder inbyggda API: er för fil system för att dela data mellan dem och andra program som körs i Azure.</li>
<li>Lagra utvecklings-och fel söknings verktyg som behöver nås från många virtuella datorer.</li>
</ul>
</td>
</tr>
<tr><td>Azure-blobar
</td>
<td>Tillhandahåller klient bibliotek och ett <a href="/rest/api/storageservices/blob-service-rest-api">rest</a> -gränssnitt som tillåter att ostrukturerade data lagras och används i en enorm skala i block-blobbar. Stöder också <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> för företags lösningar för Big data analys.
</td>
<td><ul>
<li>Stöd för strömning och slumpmässiga åtkomst scenarier i ett program.</li>
<li>Ha åtkomst till program data var som helst.</li>
<li>Skapa ett företags data Lake på Azure och utför stor data analys.</li>
</ul></td>
</tr>
<tr><td>Azure-diskar
</td>
<td>Tillhandahåller klient bibliotek och ett <a href="/rest/api/compute/disks">rest</a> -gränssnitt som gör att data kan lagras permanent och nås från en ansluten virtuell hård disk.
</td>
<td><ul>
<li>Lyft och Shift-program som använder inbyggda API: er för fil system för att läsa och skriva data till beständiga diskar.</li>
<li>Lagra data som inte behöver nås från utsidan av den virtuella dator som disken är ansluten till.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure het (online) och kall lagring (Arkiv)

Lagrings typen för ett specifikt system beror på systemets krav, inklusive lagrings storlek, data flöde och IOPS. För DASD-typ lagring på en stordator använder program på Azure vanligt vis Azure-diskar enhets lagring i stället. För lagring av stordatorer används Blob Storage i Azure.

SSD ger högsta möjliga lagrings prestanda på Azure. Följande alternativ är tillgängliga (när det här dokumentet skrivs):

| Typ         | Storlek           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB till 64 TB  | 1 200 till 160 000 IOPS |
| Premium SSD  | 32 GB till 32 TB | 12 till 15 000 IOPS     |
| Standard SSD | 32 GB till 32 TB | 12 till 2 000 IOPS      |

Blob Storage tillhandahåller störst lagrings volym på Azure. Utöver lagrings storleken erbjuder Azure både hanterade och ohanterade lagrings enheter. Med hanterad lagring sköter Azure hanteringen av de underliggande lagrings kontona. Med ohanterat lagrings utrymme tar användaren ansvar för att konfigurera Azure Storage-konton av lämplig storlek för att uppfylla lagrings kraven.

## <a name="next-steps"></a>Nästa steg

- [Stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Revärdering av stordatorer på Azure Virtual Machines](../overview.md)
- [Flytta stordator beräkning till Azure](mainframe-compute-Azure.md)
- [Bestämma när du ska använda Azure-blobbar, Azure Files eller Azure-diskar](../../../../storage/common/storage-introduction.md)
- [Standard SSD Managed Disks för virtuella Azure-arbetsbelastningar](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>IBM-resurser

- [Parallella Sysplex på IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM-CICS och kopplings funktionen: utöver grunderna](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Skapa nödvändiga användare för en funktion installation av DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt – skapa instans kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 pureScale-klustrad databas lösning](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government moln för stordator program](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft och FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Fler migrerings resurser

- [Guide för att lyfta och flytta Azure Virtual Data Center](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)