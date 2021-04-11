---
title: Jämförelse av Azure Files och Azure NetApp Files | Microsoft Docs
description: Jämförelse av Azure Files och Azure NetApp Files.
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/19/2021
ms.author: jeffpatt
ms.openlocfilehash: 652d362a11e80a488c9278dfeff38e715acee784
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641943"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Jämförelse av Azure Files och Azure NetApp Files

Den här artikeln innehåller en jämförelse av Azure Files och Azure NetApp Files. 

De flesta arbets belastningar som kräver moln fil lagring fungerar bra på antingen Azure Files eller Azure NetApp Files. Läs informationen i den här artikeln för att avgöra vilken som passar din arbets belastning bäst. Mer information finns i dokumentationen för [Azure Files](https://docs.microsoft.com/azure/storage/files/) och [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) .

## <a name="features"></a>Funktioner

| Kategori | Azure Files | Azure NetApp Files |
|---------|-------------------------|---------|
| Beskrivning | [Azure Files](https://azure.microsoft.com/services/storage/files/) är en fullständigt hanterad tjänst med hög tillgänglighet och är optimerad för slumpmässiga åtkomst arbets belastningar med data uppdateringar på plats.<br><br> Azure Files bygger på samma plattform för Azure Storage som andra tjänster som Azure-blobbar. | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) är en fullständigt HANTERAd NAS-tjänst i företags klass som kan hantera de mest krävande arbets belastningarna med hög prestanda och låg latens som kräver avancerade data hanterings funktioner. Det möjliggör migrering av arbets belastningar som betraktas som "går migrera" utan.<br><br>  ANF bygger på NetApp Bare Metal med ONTAP Storage OS som körs i Azure-datacentret för en enhetlig Azure-upplevelse och en lokal som prestanda. |
| Protokoll | Premium<br><ul><li>SMB 2,1, 3,0</li><li>NFS 4,1 (förhands granskning)</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2,1, 3,0</li><li>REST</li></ul><br> Läs mer i [tillgängliga fil resurs protokoll](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols). | Alla nivåer<br><ul><li>SMB 1, 2. x, 3. x</li><li>NFS 3,0, 4,1</li><li>Åtkomst till dubbla protokoll (NFSv3/SMB)</li></ul><br> Mer information finns i så här skapar du [NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)-, [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)-eller [Dual-Protocol-](https://docs.microsoft.com/azure/azure-netapp-files/create-volumes-dual-protocol) volymer. |
| Tillgänglighet för regioner | Premium<br><ul><li>30 + regioner</li></ul><br>Standard<br><ul><li>Alla regioner</li></ul><br> Mer information finns i [Produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=storage). | Alla nivåer<br><ul><li>25 + regioner</li></ul><br> Mer information finns i [Produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=storage). |
| Redundans | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> Mer information finns i [redundans](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy). | Alla nivåer<br><ul><li>Inbyggt lokalt HA</li><li>[Replikering mellan regioner](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction)</li></ul> |
| Service-Level avtal (SLA)<br><br> Observera att service avtal för Azure Files och Azure NetApp Files beräknas på olika sätt. | [SLA för Azure Files](https://azure.microsoft.com/support/legal/sla/storage/) | [SLA för Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Identity-Based autentisering och auktorisering | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Observera att identifierad autentisering endast stöds när du använder SMB-protokollet. Mer information finns i [vanliga frågor och svar](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control). | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> NFS/SMB Dual Protocol<ul><li>Lägger till/LDAP-integrering</li></ul><br>NFSv3/NFSv 4.1<ul><li>Lägger till/LDAP-integrering (kommer)</li><li>Utökad NFS-grupp (kommer)</li></ul><br> Mer information finns i [vanliga frågor och svar](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs). |
| Kryptering | SMB<br><ul><li>Kryptering i vila (AES 256) med kund-eller Microsoft-hanterade nycklar</li><li>Kerberos-kryptering med AES 256 eller RC4-HMAC</li><li>Kryptering under överföring</li></ul><br>NFS<br><ul><li>Kryptering i vila (AES 256) med kund-eller Microsoft-hanterade nycklar</li></ul><br>REST<br><ul><li>Kryptering i vila (AES 256) med kund-eller Microsoft-hanterade nycklar</li><li>Kryptering under överföring</li></ul><br> Mer information finns i [säkerhet](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols#security). | Alla protokoll<br><ul><li>Kryptering i vila (AES 256) med Microsoft-hanterade nycklar </li></ul><br>NFS 4,1<ul><li>Kryptering under överföring med hjälp av Kerberos med AES 256</li></ul><br> Mer information finns i [vanliga frågor och svar om säkerhet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#security-faqs). |
| Åtkomstalternativ | <ul><li>Internet</li><li>Säker VNet-åtkomst</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure File Sync</li></ul><br> Mer information finns i [nätverks överväganden](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview). | <ul><li>Säker VNet-åtkomst</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[Global filcache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-overview)</li></ul><br> Mer information finns i [nätverks överväganden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies). |
| Dataskydd  | <ul><li>Stegvisa ögonblicks bilder</li><li>Själv återställning av fil-/katalog användare</li><li>Återställ till ny plats</li><li>Återställning på plats</li><li>Mjuk borttagning på resurs nivå</li><li>Azure Backup-integrering</li></ul><br> Mer information finns i [Azure Files förbättrar funktionerna för data skydd](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/). | <ul><li>Ögonblicks bilder (255/volym)</li><li>Själv återställning av fil-/katalog användare</li><li>Återställ till ny volym</li><li>Återställning på plats</li><li>[Replikering mellan regioner](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction) (offentlig för hands version)</li></ul><br> Mer information finns i [så här fungerar Azure NetApp Files-ögonblicksbilder](https://docs.microsoft.com/azure/azure-netapp-files/snapshots-introduction). |
| Migreringsverktyg  | <ul><li>Azure Data Box</li><li>Azure File Sync</li><li>Tjänsten för lagringsmigrering</li><li>AzCopy</li><li>Robocopy</li></ul><br> Mer information finns i [migrera till Azure-filresurser](https://docs.microsoft.com/azure/storage/files/storage-files-migration-overview). | <ul><li>[Global filcache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>Tjänsten för lagringsmigrering</li><li>AzCopy</li><li>Robocopy</li><li>Program-baserad (till exempel HSR, data Guard, AOAG)</li></ul> |
| Nivåer | <ul><li>Premium</li><li>Transaktion optimerad</li><li>Frekvent</li><li>Lågfrekvent</li></ul><br> Mer information finns i [lagrings nivåer](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers). | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> Alla nivåer ger lägsta svars tid under MS.<br><br> Mer information finns i [service nivåer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) och [prestanda överväganden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-considerations). |
| Priser | [Azure Files priser](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp Files priser](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Skalbarhet och prestanda

| Kategori | Azure Files | Azure NetApp Files |
|---------|---------|---------|
| Minsta resurs-/volym storlek | Premium<br><ul><li>100 GiB</li></ul><br>Standard<br><ul><li>1 GiB</li></ul> | Alla nivåer<br><ul><li>100 GiB (minsta kapacitet för pool: 4 TiB)</li></ul> |
| Maximal resurs-/volym storlek | Premium<br><ul><li>100 TiB</li></ul><br>Standard<br><ul><li>100 TiB</li></ul> | Alla nivåer<br><ul><li>100 TiB (500-TiB kapacitets pool)</li></ul><br>Upp till 12,5 PiB per Azure NetApp-konto. |
| Maximal resurs/volym-IOPS | Premium<br><ul><li>Upp till 100 000</li></ul><br>Standard<br><ul><li>Upp till 10 000</li></ul> | Ultra och Premium<br><ul><li>Upp till 450k </li></ul><br>Standard<br><ul><li>Upp till 320k</li></ul> |
| Maximalt data flöde för resurs/volym | Premium<br><ul><li>Upp till 10 GiB/s</li></ul><br>Standard<br><ul><li>Upp till 300 MiB/s</li></ul> | Ultra och Premium<br><ul><li>Upp till 4,5 GiB/s</li></ul><br>Standard<br><ul><li>Upp till 3,2 GiB/s</li></ul> |
| Maximal filstorlek | Premium<br><ul><li>4 TiB</li></ul><br>Standard<br><ul><li>1 TiB</li></ul> | Alla nivåer<br><ul><li>16 TiB</li></ul> |
| Maximalt antal IOPS per fil | Premium<br><ul><li>Upp till 8 000</li></ul><br>Standard<br><ul><li>1 000</li></ul> | Alla nivåer<br><ul><li>Upp till volym gräns</li></ul> |
| Maximalt data flöde per fil | Premium<br><ul><li>300 MiB/s (upp till 1 GiB/s med SMB Multichannel)</li></ul><br>Standard<br><ul><li>60 MiB/s</li></ul> | Alla nivåer<br><ul><li>Upp till volym gräns</li></ul> |
| SMB Multichannel | Ja (för[hands version](https://docs.microsoft.com/azure/storage/files/storage-files-smb-multichannel-performance)) | Ja |
| Svarstid | Minsta svars tid i millisekunder (2ms till 3ms för små IO-svar) | Lägsta svars tid under millisekunder (<1ms för slumpmässig IO)<br><br>Mer information finns i [prestandatest](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux). |

Mer information om skalbarhets-och prestanda mål finns i [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) och [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits).

## <a name="next-steps"></a>Nästa steg
* [Dokumentation om Azure Files](https://docs.microsoft.com/azure/storage/files/)
* [Azure NetApp Files dokumentation](https://docs.microsoft.com/azure/azure-netapp-files/)
