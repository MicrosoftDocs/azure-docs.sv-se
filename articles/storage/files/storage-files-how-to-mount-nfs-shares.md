---
title: Montera en Azure NFS-filresurs – Azure Files
description: Lär dig hur du monterar en nätverksfilsystemresurs.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4369619cd83dffe36cf156f523a951e1360438db
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717085"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Montera en NFS-filresurs

[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Linux-distributioner med hjälp Server Message Block SMB (Network File System) eller NFS-protokollet (Network File System). Den här artikeln fokuserar på montering med NFS. Mer information om montering med SMB finns i [Använda Azure Files med Linux](storage-how-to-use-files-linux.md). Mer information om vart och ett av de tillgängliga protokollen finns i [Azure-filresursprotokoll.](storage-files-compare-protocols.md)

## <a name="limitations"></a>Begränsningar

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Skapa en NFS-resurs](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > NFS-resurser kan bara nås från betrodda nätverk. Anslutningar till din NFS-resurs måste komma från någon av följande källor:

- Använd någon av följande nätverkslösningar:
    - Skapa [antingen en privat slutpunkt](storage-files-networking-endpoints.md#create-a-private-endpoint) (rekommenderas) eller begränsa [åtkomsten till din offentliga slutpunkt.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Konfigurera en punkt-till-plats-VPN (P2S) i Linux för användning med Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Konfigurera ett plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).
    - Konfigurera [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Inaktivera säker överföring

1. Logga in på den Azure Portal åtkomst till lagringskontot som innehåller den NFS-resurs som du skapade.
1. Välj **Konfiguration**.
1. Välj **Inaktiverad** för **Säker överföring krävs.**
1. Välj **Spara**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Skärmbild av skärmen för konfiguration av lagringskonto med säker överföring inaktiverad.":::

## <a name="mount-an-nfs-share"></a>Montera en NFS-resurs

1. När filresursen har skapats väljer du resursen och **anslut från Linux.**
1. Ange monteringssökvägen som du vill använda och kopiera sedan skriptet.
1. Anslut till klienten och använd det tillhandahållna monteringsskriptet.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Skärmbild av anslutningsbladet för filresurs.":::

Nu har du monterat din NFS-resurs.

### <a name="validate-connectivity"></a>Verifiera anslutningen

Om monteringen misslyckades är det möjligt att den privata slutpunkten inte har ställts in korrekt eller är otillgänglig. Mer information om hur du bekräftar anslutningen finns i [avsnittet Verifiera](storage-files-networking-endpoints.md#verify-connectivity) anslutningar i artikeln om nätverksslutpunkter.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Files artikeln Planera [för en Azure Files distribution](storage-files-planning.md).
- Om du får problem kan du gå till [Felsöka Azure NFS-filresurser.](storage-troubleshooting-files-nfs.md)