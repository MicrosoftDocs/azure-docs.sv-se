---
title: Azure Files Azure NetApp Files | Microsoft Docs
description: Jämförelse av Azure Files och Azure NetApp Files.
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/19/2021
ms.author: jeffpatt
ms.openlocfilehash: 381fe00e835f0e359a2347372cbc2544a2b211ca
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364234"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Azure Files och Azure NetApp Files jämförelse

Den här artikeln innehåller en jämförelse av Azure Files och Azure NetApp Files. 

De flesta arbetsbelastningar som kräver molnfillagring fungerar bra på antingen Azure Files eller Azure NetApp Files. Läs informationen i den här artikeln för att avgöra vilken arbetsbelastning som passar bäst. Mer information finns i dokumentationen [om Azure Files](https://docs.microsoft.com/azure/storage/files/) [och Azure NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/)

## <a name="features"></a>Funktioner

| Kategori | Azure Files | Azure NetApp Files |
|---------|-------------------------|---------|
| Beskrivning | [Azure Files](https://azure.microsoft.com/services/storage/files/) är en fullständigt hanterad tjänst i företagsklass med hög tillgänglighet som är optimerad för slumpmässig åtkomst till arbetsbelastningar med datauppdateringar på plats.<br><br> Azure Files bygger på samma Azure-lagringsplattform som andra tjänster som Azure Blobs. | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) är en fullständigt hanterad, hög tillgänglig NAS-tjänst i företagsklass som kan hantera de mest krävande, högpresterande arbetsbelastningar med korta svarstider som kräver avancerade datahanteringsfunktioner. Det gör det möjligt att migrera arbetsbelastningar, som anses vara "icke-migreringsbara" utan.<br><br>  ANF bygger på NetApps bare metal med ONTAP-lagringsoperativsystemet som körs i Azure-datacentret för en konsekvent Azure-upplevelse och en lokal prestanda. |
| Protokoll | Premium<br><ul><li>SMB 2.1, 3.0</li><li>NFS 4.1 (förhandsversion)</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2.1, 3.0</li><li>REST</li></ul><br> Mer information finns i tillgängliga [filresursprotokoll.](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols) | Alla nivåer<br><ul><li>SMB 1, 2.x, 3.x</li><li>NFS 3.0, 4.1</li><li>Dubbel protokollåtkomst (NFSv3/SMB)</li></ul><br> Mer information finns i skapa [NFS-,](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) [SMB-](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)eller [dual-protocol-volymer.](https://docs.microsoft.com/azure/azure-netapp-files/create-volumes-dual-protocol) |
| Regionstillgänglighet | Premium<br><ul><li>Över 30 regioner</li></ul><br>Standard<br><ul><li>Alla regioner</li></ul><br> Mer information finns i [Produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=storage). | Alla nivåer<br><ul><li>Över 25 regioner</li></ul><br> Mer information finns i [Produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=storage). |
| Redundans | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> Mer information finns i [redundans](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy). | Alla nivåer<br><ul><li>Inbyggd lokal HÖG</li><li>[Replikering mellan regioner](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction)</li></ul> |
| Service-Level (SLA)<br><br> Observera att serviceavtal för Azure Files och Azure NetApp Files beräknas på olika sätt. | [Serviceavtal för Azure Files](https://azure.microsoft.com/support/legal/sla/storage/) | [Serviceavtal för Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Identity-Based autentisering och auktorisering | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Observera att identifieringsbaserad autentisering endast stöds när du använder SMB-protokollet. Mer information finns i Vanliga [frågor och svar.](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> NFS/SMB dubbla protokoll<ul><li>ADDS/LDAP-integrering</li></ul><br>NFSv3/NFSv4.1<ul><li>ADDS/LDAP-integrering (kommer)</li><li>Utökade NFS-grupper (kommer)</li></ul><br> Mer information finns i Vanliga [frågor och svar.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs) |
| Kryptering | SMB<br><ul><li>Kryptering i vila (AES 256) med kund- eller Microsoft-hanterade nycklar</li><li>Kerberos-kryptering med AES 256 eller RC4-HMAC</li><li>Kryptering under överföring</li></ul><br>NFS<br><ul><li>Kryptering i vila (AES 256) med kund- eller Microsoft-hanterade nycklar</li></ul><br>REST<br><ul><li>Kryptering i vila (AES 256) med kund- eller Microsoft-hanterade nycklar</li><li>Kryptering under överföring</li></ul><br> Mer information finns i [säkerhet.](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols#security) | Alla protokoll<br><ul><li>Kryptering i vila (AES 256) med Microsoft-hanterade nycklar </li></ul><br>NFS 4.1<ul><li>Kryptering under överföring med Kerberos med AES 256</li></ul><br> Mer information finns i Vanliga frågor [och svar om säkerhet.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#security-faqs) |
| Åtkomstalternativ | <ul><li>Internet</li><li>Säker VNet-åtkomst</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure File Sync</li></ul><br> Mer information finns i [nätverksöverväganden](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview). | <ul><li>Säker VNet-åtkomst</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[Global filcache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-overview)</li></ul><br> Mer information finns i [nätverksöverväganden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies). |
| Dataskydd  | <ul><li>Inkrementella ögonblicksbilder</li><li>Självåterställning av fil-/kataloganvändare</li><li>Återställa till ny plats</li><li>Återställning på plats</li><li>Mjuk borttagning på resursnivå</li><li>Azure Backup integrering</li></ul><br> Mer information finns [i Azure Files förbättrar dataskyddsfunktionerna.](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/) | <ul><li>Ögonblicksbilder (255/volym)</li><li>Självåterställning av fil-/kataloganvändare</li><li>Återställa till ny volym</li><li>Återställning på plats</li><li>[Replikering mellan regioner](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction) (offentlig förhandsversion)</li></ul><br> Mer information finns i Så [här Azure NetApp Files ögonblicksbilder fungerar](https://docs.microsoft.com/azure/azure-netapp-files/snapshots-introduction). |
| Migreringsverktyg  | <ul><li>Azure Data Box</li><li>Azure File Sync</li><li>Storage Migration Service</li><li>AzCopy</li><li>Robocopy</li></ul><br> Mer information finns i Migrera [till Azure-filresurser.](https://docs.microsoft.com/azure/storage/files/storage-files-migration-overview) | <ul><li>[Global filcache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>Storage Migration Service</li><li>AzCopy</li><li>Robocopy</li><li>Programbaserad (till exempel HSR, Data Guard, AOAG)</li></ul> |
| Nivåer | <ul><li>Premium</li><li>Transaktionsoptimerad</li><li>Frekvent</li><li>Lågfrekvent</li></ul><br> Mer information finns i [lagringsnivåer.](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers) | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> Alla nivåer ger lägsta svarstid under ms.<br><br> Mer information finns i [Servicenivåer och](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) [prestandaöverväganden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-considerations) |
| Priser | [Azure Files prissättning](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp Files prissättning](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Skalbarhet och prestanda

| Kategori | Azure Files | Azure NetApp Files |
|---------|---------|---------|
| Minsta resurs-/volymstorlek | Premium<br><ul><li>100 GiB</li></ul><br>Standard<br><ul><li>1 GiB</li></ul> | Alla nivåer<br><ul><li>100 GiB (minsta kapacitetspoolstorlek: 4 TiB)</li></ul> |
| Maximal resurs-/volymstorlek | Premium<br><ul><li>100 TiB</li></ul><br>Standard<br><ul><li>100 TiB</li></ul> | Alla nivåer<br><ul><li>100 TiB (kapacitetspoolgräns på 500 TiB)</li></ul><br>Upp till 12,5 PiB per Azure NetApp-konto. |
| Maximal resurs-/volym-IOPS | Premium<br><ul><li>Upp till 100 000</li></ul><br>Standard<br><ul><li>Upp till 10 000</li></ul> | Ultra och Premium<br><ul><li>Upp till 450 000 </li></ul><br>Standard<br><ul><li>Upp till 320 000</li></ul> |
| Maximalt dataflöde för resurs/volym | Premium<br><ul><li>Upp till 10 GiB/s</li></ul><br>Standard<br><ul><li>Upp till 300 MiB/s</li></ul> | Ultra och Premium<br><ul><li>Upp till 4,5 GiB/s</li></ul><br>Standard<br><ul><li>Upp till 3,2GiB/s</li></ul> |
| Maximal filstorlek | Premium<br><ul><li>4 TiB</li></ul><br>Standard<br><ul><li>1 TiB</li></ul> | Alla nivåer<br><ul><li>16 TiB</li></ul> |
| Maximalt antal IOPS per fil | Premium<br><ul><li>Upp till 8 000</li></ul><br>Standard<br><ul><li>1 000</li></ul> | Alla nivåer<br><ul><li>Upp till volymgräns</li></ul> |
| Maximalt dataflöde per fil | Premium<br><ul><li>300 MiB/s (upp till 1 GiB/s med SMB multichannel)</li></ul><br>Standard<br><ul><li>60 MiB/s</li></ul> | Alla nivåer<br><ul><li>Upp till volymgräns</li></ul> |
| SMB Multichannel | Ja[(förhandsversion)](https://docs.microsoft.com/azure/storage/files/storage-files-smb-multichannel-performance) | Ja |
| Svarstid | Minsta svarstid på en millisekund (2 ms till 3 ms för liten I/O) | Minsta svarstid på mindre än millisekunder (<1 ms för slumpmässig I/O)<br><br>Läs mer i [prestandatesterna.](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux) |

Mer information om skalbarhets- och prestandamål finns [i Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) och [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits).

## <a name="next-steps"></a>Nästa steg
* [Dokumentation om Azure Files](https://docs.microsoft.com/azure/storage/files/)
* [Azure NetApp Files dokumentation](https://docs.microsoft.com/azure/azure-netapp-files/)
