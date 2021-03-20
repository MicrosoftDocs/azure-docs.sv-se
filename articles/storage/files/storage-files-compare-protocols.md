---
title: Tillgängliga Azure Files protokoll – NFS och SMB
description: Läs om tillgängliga protokoll innan du skapar en Azure-filresurs, inklusive SMB (Server Message Block) och NFS (Network File System).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 0ed41cc01fcf5aa35b3d2195518b8e2bb0f3b9c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588247"
---
# <a name="azure-file-share-protocols"></a>Azure File Share-protokoll

Azure Files erbjuder två protokoll för att ansluta och montera dina Azure-filresurser. [SMB-protokoll (Server Message Block](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) ) och [NFS-protokoll (Network File System](https://en.wikipedia.org/wiki/Network_File_System) ) (för hands version). Azure Files stöder för närvarande inte åtkomst till flera protokoll, så en resurs kan bara vara en NFS-resurs eller en SMB-resurs. På grund av detta rekommenderar vi att du bestämmer vilket protokoll som passar bäst för dina behov innan du skapar Azure-filresurser.

## <a name="differences-at-a-glance"></a>Skillnader i en överskådlighet

|Funktion  |NFS (för hands version)  |SMB  |
|---------|---------|---------|
|Åtkomst protokoll     |NFS 4,1         |SMB 2,1, SMB 3,0         |
|Rekommenderat operativ system     |Linux-kernel version 4.3 +         |Windows 2008 R2 +, Linux kernel-version 4.11 +         |
|[Tillgängliga nivåer](storage-files-planning.md#storage-tiers)     |Premium Storage         |Premium Storage, transaktion optimerad, frekvent, låg frekvent         |
|Fakturerings modell         |[Betala för etablerad kapacitet](./understanding-billing.md#provisioned-model)         |[Betala för etablerad kapacitet för Premium-nivån](./understanding-billing.md#provisioned-model), [betala per användning för standard nivån](./understanding-billing.md#pay-as-you-go-model)         |
|[Redundans](storage-files-planning.md#redundancy)     |LRS, ZRS         |LRS, ZRS, GRS         |
|Autentisering     |Endast värdbaserad autentisering        |Identitets baserad autentisering, användarbaserad autentisering         |
|Behörigheter     |Behörigheter för UNIX-typ         |NTFS-typ behörigheter         |
|Semantiska fil system     |POSIX-kompatibel         |Inte POSIX-kompatibel         |
|Skift läges känslighet     |Skiftlägeskänsligt         |Inte Skift läges känslig         |
|Stöd för hårda länkar     |Stöds         |Stöds inte         |
|Stöd för symboliska länkar     |Stöds         |Stöds inte         |
|Ta bort eller ändra öppna filer     |Stöds         |Stöds inte         |
|Låsning     |Meddelande hanteraren för byte intervall som är ansvarig för Nätverks Lås         |Stöds         |
|Säker lista över offentliga IP-adresser | Stöds inte | Stöds|
|Protokoll-interop| Stöds inte | Filerst|

## <a name="nfs-shares-preview"></a>NFS-resurser (förhands granskning)

Att montera Azure-filresurser med NFS 4,1 är för närvarande en för hands version. Den erbjuder en närmare integrering med Linux. Detta är ett fullständigt POSIX-kompatibelt erbjudande som är en standard för alla varianter av UNIX och andra * nix-baserade operativ system. Den här fil lagrings tjänsten i företags klass skalas upp för att uppfylla dina lagrings behov och kan nås samtidigt av tusentals beräknings instanser.

### <a name="limitations"></a>Begränsningar

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Passar bäst

NFS med Azure Files är perfekt för:

- Arbets belastningar som kräver POSIX-kompatibla fil resurser, SKIFT läges känslighet eller UNIX-stil behörigheter (UID/GID).
- Linux-inriktade arbets belastningar som inte kräver Windows-åtkomst.

### <a name="security"></a>Säkerhet

Alla Azure Files data är krypterade i vila. För kryptering under överföring tillhandahåller Azure ett krypterings lager för alla data som överförs mellan Azure-datacenter med hjälp av [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). Det finns kryptering när data överförs mellan Azure-datacenter. Till skillnad från Azure Files att använda SMB-protokollet erbjuder inte fil resurser som använder NFS-protokollet användar baserad autentisering. Autentisering för NFS-resurser baseras på de konfigurerade nätverks säkerhets reglerna. På grund av detta måste du använda antingen tjänstens slut punkter eller privata slut punkter för att säkerställa att endast säkra anslutningar upprättas till din NFS-resurs. Om du vill komma åt resurser lokalt måste du konfigurera en VPN-eller ExpressRoute, förutom en privat slut punkt. Begär Anden som inte härstammar från följande källor kommer att avvisas:

- [En privat slut punkt](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [VPN för punkt-till-plats (P2S)](../../vpn-gateway/point-to-site-about.md)
    - [Plats-till-plats](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [En begränsad offentlig slut punkt](storage-files-networking-overview.md#storage-account-firewall-settings)

Mer information om tillgängliga nätverks alternativ finns [Azure Files nätverks överväganden](storage-files-networking-overview.md).

## <a name="smb-shares"></a>SMB-resurser

Azure-filresurser som är monterade med SMB erbjuder fler Azure Files funktioner och har inga Azure Files funktions begränsningar eftersom den är allmänt tillgänglig.

### <a name="features"></a>Funktioner

- Azure File Sync
- Identitets baserad autentisering
- Azure Backup support
- Ögonblicksbilder
- Mjuk borttagning
- Kryptering under överföring och kryptering vid vila

### <a name="best-suited"></a>Passar bäst

SMB med Azure Files är perfekt för:

- Produktions miljöer
- Kunder som kräver någon av funktionerna som anges i [funktioner](#features)

## <a name="next-steps"></a>Nästa steg

- [Skapa en NFS-filresurs](storage-files-how-to-create-nfs-shares.md)
- [Skapa en SMB-filresurs](storage-how-to-create-file-share.md)