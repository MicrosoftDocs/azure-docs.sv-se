---
title: Stöd för Network File System 3,0 i Azure Blob Storage (för hands version) | Microsoft Docs
description: Blob Storage stöder nu NFS-protokollet (Network File System) 3,0. Det här stödet gör att Linux-klienter kan montera en behållare i Blob Storage från en virtuell Azure-dator (VM) eller en dator som kör lokalt.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 57dec4187ea42476ff1c0a993b751a7389da5639
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490360"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>NFS (Network File System) 3,0 protokoll stöd i Azure Blob Storage (för hands version)

Blob Storage stöder nu NFS-protokollet (Network File System) 3,0. Det här stödet ger kompatibilitet med Linux-filsystem vid objekt lagrings skala och priser och gör att Linux-klienter kan montera en behållare i Blob Storage från en virtuell Azure-dator (VM) eller en lokal dator. 

> [!NOTE]
> NFS 3,0 protokoll stöd i Azure Blob Storage finns i offentlig för hands version. Det stöder GPV2-lagrings konton med standard-nivå prestanda i följande regioner: östra Australien, Korea, centrala, USA, östra och södra centrala USA. För hands versionen stöder också Block-Blob med Premium prestanda nivå i alla offentliga regioner.

Det är alltid en utmaning att köra storskaliga äldre arbets belastningar, till exempel HPC (data behandling med höga prestanda) i molnet. En orsak är att programmen ofta använder traditionella fil protokoll som NFS eller SMB (Server Message Block) för att få åtkomst till data. Interna moln lagrings tjänster fokuserar också på objekt lagring som har ett platt namn område och omfattande metadata i stället för fil system som tillhandahåller ett hierarkiskt namn område och effektiva metadata-åtgärder. 

Blob Storage stöder nu ett hierarkiskt namn område och när det kombineras med NFS 3,0-protokoll stöd gör Azure det mycket enklare att köra äldre program i storskalig lagring av moln objekt. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>Program och arbets belastningar som passar för den här funktionen

NFS 3,0-protokoll funktionen passar bäst för bearbetning av stora data flöden, storskaliga Läs intensiva arbets belastningar, till exempel medie bearbetning, risk simulering och genomik. Du bör överväga att använda den här funktionen för andra typer av arbets belastningar som använder flera läsare och många trådar, vilket kräver hög bandbredd. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3,0 och hierarkiskt namn område

NFS 3,0 protokoll stöd kräver att blobbar organiseras i på ett hierarkiskt namn område. Du kan aktivera ett hierarkiskt namn område när du skapar ett lagrings konto. Möjligheten att använda ett hierarkiskt namn område introducerades av Azure Data Lake Storage Gen2. Den ordnar objekt (filer) i en hierarki med kataloger och under kataloger på samma sätt som fil systemet på datorn är ordnat.  Det hierarkiska namn området skalas linjärt och försämrar inte data kapaciteten eller prestandan. Olika protokoll sträcker sig från det hierarkiska namn området. NFS 3,0-protokollet är ett av de här tillgängliga protokollen.   

> [!div class="mx-imgBorder"]
> ![hierarkiskt namn område](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>Data som lagras som block-blobbar

Om du aktiverar stöd för NFS 3,0-protokoll kommer alla data i ditt lagrings konto att lagras som block-blobbar. Block-blobbar är optimerade för att effektivt bearbeta stora mängder Läs tung data. Block-blobar består av block. Varje block identifieras av ett block-ID. En Block-Blob kan innehålla upp till 50 000 block. Varje block i en Block-Blob kan ha olika storlek, upp till den högsta tillåtna storleken för den tjänst version som ditt konto använder.

När programmet gör en begäran med hjälp av NFS 3,0-protokollet, översätts denna begäran till en kombination av Block-Blob-åtgärder. Till exempel översätts NFS 3,0 Read RPC-begäranden (Remote Procedure Call) till [Hämta BLOB](/rest/api/storageservices/get-blob) -åtgärd. NFS 3,0 Skriv RPC-begäranden översätts till en kombination av [listorna](/rest/api/storageservices/put-block-list) [Hämta block](/rest/api/storageservices/get-block-list), [placera block](/rest/api/storageservices/put-block)och placera block.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Allmänt arbets flöde: montera en lagrings konto behållare

Linux-klienterna kan montera en behållare i Blob Storage från en virtuell Azure-dator (VM) eller en lokal dator. För att montera en lagrings konto behållare måste du göra detta.

1. Registrera NFS 3,0-protokoll funktion med din prenumeration.

2. Kontrol lera att funktionen har registrerats.

3. Skapa ett Azure-Virtual Network (VNet).

4. Konfigurera nätverks säkerhet.

5. Skapa och konfigurera lagrings konto som bara accepterar trafik från det virtuella nätverket.

6. Skapa en behållare i lagrings kontot.

7. Montera behållaren.

Steg-för-steg-anvisningar finns i [montera Blob Storage med hjälp av Network File System (NFS) 3,0-protokollet (för hands version)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> Det är viktigt att slutföra dessa uppgifter i rätt ordning. Du kan inte montera behållare som du skapar innan du aktiverar NFS 3,0-protokollet på ditt konto. När du har aktiverat NFS 3,0-protokollet på ditt konto kan du också inaktivera det.

## <a name="network-security"></a>Nätverkssäkerhet

Ditt lagrings konto måste finnas i ett VNet. Ett VNet gör det möjligt för klienter att ansluta på ett säkert sätt till ditt lagrings konto. Det enda sättet att skydda data i ditt konto är genom att använda ett VNet och andra nätverks säkerhets inställningar. Andra verktyg som används för att skydda data, inklusive konto nyckel auktorisering, Azure Active Directory (AD) säkerhet och åtkomst kontrol listor (ACL) stöds inte ännu i konton som har stöd för NFS 3,0-protokollet aktiverat. 

Mer information finns i [nätverks säkerhets rekommendationer för Blob Storage](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Nätverks anslutningar som stöds

En klient kan ansluta via en offentlig eller [privat slut punkt](../common/storage-private-endpoints.md)och kan ansluta från någon av följande nätverks platser:

- Det virtuella nätverk som du konfigurerar för ditt lagrings konto. 

  I den här artikeln kommer vi att se detta VNet som *primärt VNet*. Läs mer i [bevilja åtkomst från ett virtuellt nätverk](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Ett peer-kopplat VNet som finns i samma region som det primära virtuella nätverket.

  Du måste konfigurera ditt lagrings konto för att tillåta åtkomst till detta peered VNet. Läs mer i [bevilja åtkomst från ett virtuellt nätverk](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Ett lokalt nätverk som är anslutet till ditt primära VNet med hjälp av [VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) eller en [ExpressRoute-Gateway](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md). 

  Mer information finns i [Konfigurera åtkomst från lokala nätverk](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Ett lokalt nätverk som är anslutet till ett peer-nätverk.

  Detta kan göras med hjälp av [VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) eller en [ExpressRoute-Gateway](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) tillsammans med [Gateway-överföring](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit). 

> [!IMPORTANT]
> Om du ansluter från ett lokalt nätverk kontrollerar du att klienten tillåter utgående kommunikation via portarna 111 och 2048. NFS 3,0-protokollet använder dessa portar.

## <a name="azure-storage-features-not-yet-supported"></a>Azure Storage funktioner som ännu inte stöds

Följande Azure Storage funktioner stöds inte när du aktiverar NFS 3,0-protokollet på ditt konto. 

- Azure Active Directory (AD) säkerhet

- POSIX-liknande åtkomst kontrol listor (ACL: er)

- Möjligheten att aktivera NFS 3,0-stöd för befintliga lagrings konton

- Möjligheten att inaktivera NFS 3,0-stöd i ett lagrings konto (när du har aktiverat det)

- Möjlighet att skriva till blobbar med hjälp av REST-API: er eller SDK: er. 
  
## <a name="nfs-30-features-not-yet-supported"></a>NFS 3,0-funktioner stöds ännu inte

Följande NFS 3,0-funktioner stöds inte ännu.

- NFS 3,0 över UDP. Endast NFS 3,0 över TCP stöds.

- Låsa filer med Network Lock Manager (NLM). Monterings kommandon måste innehålla `-o nolock` parametern.

- Montera under kataloger. Du kan bara montera rot katalogen (container).

- Visar monteringar (t. ex. genom att använda kommandot `showmount -a` )

- Lista över exporter (till exempel: genom att använda kommandot `showmount -e` )

- Hård länk

- Exportera en behållare som skrivskyddad

## <a name="nfs-30-clients-not-yet-supported"></a>NFS 3,0-klienter stöds ännu inte

Följande NFS 3,0-klienter stöds inte ännu.

- Windows-klient för NFS

## <a name="pricing"></a>Priser

Under för hands versionen debiteras de data som lagras i ditt lagrings konto med samma kapacitets taxa som Blob Storage-avgifter per GB per månad. 

En transaktion debiteras inte under för hands versionen. Prissättningen för transaktioner kan ändras och kommer att fastställas när den är allmänt tillgänglig.

## <a name="next-steps"></a>Nästa steg

- För att komma igång, se [montera Blob Storage med hjälp av Network File System (NFS) 3,0-protokollet (för hands version)](network-file-system-protocol-support-how-to.md).

- Information om hur du optimerar prestanda finns [i Network File System (NFS) 3,0 prestanda överväganden i Azure Blob Storage (för hands version)](network-file-system-protocol-support-performance.md).
