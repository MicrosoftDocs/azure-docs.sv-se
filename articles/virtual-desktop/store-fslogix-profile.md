---
title: Lagring FSLogix Profile container Windows Virtual Desktop – Azure
description: Alternativ för att lagra din Windows FSLogix-profil för virtuella skriv bord på Azure Storage.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ec166c1df9727052d4980f5d5758ece8c499880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526610"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Lagrings alternativ för FSLogix profil behållare i Windows Virtual Desktop

Azure erbjuder flera lagrings lösningar som du kan använda för att lagra din FSLogix-profil behållare. I den här artikeln jämförs de lagrings lösningar som Azure erbjuder för Windows Virtual Desktop-FSLogix användar profil behållare. Vi rekommenderar att du lagrar FSLogix profil behållare på Azure Files för de flesta av våra kunder.

Windows Virtual Desktop erbjuder FSLogix profil behållare som den rekommenderade användar profil lösningen. FSLogix är utformat för att roama profiler i fjärrdatormiljöer, till exempel Windows Virtual Desktop. Vid inloggning är den här behållaren dynamiskt kopplad till dator miljön med hjälp av en inbyggd virtuell hård disk som stöds och en virtuell hård disk för Hyper-V (VHDX). Användarprofilen blir tillgänglig omedelbart och visas i systemet precis som en intern användarprofil.

I följande tabeller jämförs lagrings lösningarna Azure Storage erbjudanden för användar profiler i Windows Virtual Desktop-FSLogix profil behållare.

## <a name="azure-platform-details"></a>Information om Azure-plattformen

|Funktioner|Azure Files|Azure NetApp Files|Lagringsutrymmen direkt|
|--------|-----------|------------------|---------------------|
|Användningsfall|Generellt syfte|Ultra Performance eller migration från NetApp lokalt|Plattformsoberoende|
|Plattforms tjänst|Ja, Azure – inbyggd lösning|Ja, Azure – inbyggd lösning|Ingen, egen hantering|
|Regional tillgänglighet|Alla regioner|[Välj regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Alla regioner|
|Redundans|Lokalt redundant/zon-redundant/Geo-redundant/geo-Zone-redundant|Lokalt redundant|Lokalt redundant/zon-redundant/Geo-redundant|
|Nivåer och prestanda| Standard (transaktion optimerad)<br>Premium<br>Upp till max 100 000 IOPS per resurs med 10 Gbit/s per resurs vid cirka 3 MS svars tid|Standard<br>Premium<br>Ultra<br>Upp till 320k (16K) IOPS med 4,5 Gbit/s per volym vid ungefär 1 MS svars tid|Standard HDD: gränser för upp till 500 IOPS per disk<br>Standard SSD: upp till 4 000 IOPS-gränser per disk<br>Premium SSD: upp till 20 000 IOPS per disk<br>Vi rekommenderar Premium diskar för Lagringsdirigering|
|Kapacitet|100 TiB per resurs, upp till 5 PiB per allmänt syftes konto |100 TiB per volym, upp till 12,5 PiB per prenumeration|Maximalt 32 TiB per disk|
|Nödvändig infrastruktur|Minsta resurs storlek 1 GiB|Pool för minsta kapacitet 4 TiB, minsta volym storlek 100 GiB|Två virtuella datorer på Azure IaaS (+ Cloud vittne) eller minst tre virtuella datorer utan och kostnader för diskar|
|Protokoll|SMB 3.0/2.1, NFSv 4.1 (för hands version), REST|NFSv3, NFSv 4.1 (för hands version), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Information om Azure-hantering

|Funktioner|Azure Files|Azure NetApp Files|Lagringsutrymmen direkt|
|--------|-----------|------------------|---------------------|
|Access|Moln, lokalt och hybrid (Azure File Sync)|Moln, lokalt (via ExpressRoute)|Moln, lokalt|
|Backup|Integrering av Azure Backup-ögonblicksbild|Azure NetApp Files ögonblicks bilder|Integrering av Azure Backup-ögonblicksbild|
|Säkerhet och efterlevnad|[Alla Azure-certifikat som stöds](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO slutförd|[Alla Azure-certifikat som stöds](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory-integrering|[Interna Active Directory och Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services och ursprunglig Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Interna Active Directory eller Azure Active Directory Domain Services endast stöd|

När du har valt din lagrings metod kan du titta på [priser för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-desktop/) för information om våra prissättnings planer.

## <a name="next-steps"></a>Nästa steg

Mer information om FSLogix profil behållare, användar profil diskar och andra tekniker för användar profiler finns i tabellen i [FSLogix profil behållare och Azure Files](fslogix-containers-azure-files.md).

Kom igång med någon av de här självstudierna om du är redo att skapa dina egna FSLogix-profil behållare:

- [Komma igång med FSLogix profil behållare på Azure Files i det virtuella Windows-skrivbordet](create-file-share.md)
- [Skapa en FSLogix profil behållare för en värd-pool med Azure NetApp-filer](create-fslogix-profile-container.md)
- Anvisningarna i [distribuera en skalbar fil server med två noder Lagringsdirigering för UPD-lagring i Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) gäller också när du använder en FSLogix profil behållare i stället för en användar profil disk

Du kan också börja från början och konfigurera din egen Windows-lösning för virtuella skriv bord genom att [skapa en klient i det virtuella Windows-skrivbordet](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
