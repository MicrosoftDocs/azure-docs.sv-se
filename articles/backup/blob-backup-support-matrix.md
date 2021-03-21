---
title: Support mat ris för Azure blobs-säkerhetskopiering
description: Innehåller en sammanfattning av support inställningar och begränsningar vid säkerhets kopiering av Azure-blobbar (i för hands version)
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: ade43350bbe3fa1bcf58f47e93b948db3a5b21bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746505"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Support mat ris för Azure blobs-säkerhetskopiering (i för hands version)

Den här artikeln sammanfattar regionala tillgänglighet, scenarier som stöds och begränsningar i drift säkerhets kopiering av blobbar.

## <a name="supported-regions"></a>Regioner som stöds

Den operativa säkerhets kopieringen för blobbar är för närvarande tillgänglig i följande regioner: Australien, östra, södra Brasilien, centrala Kanada, centrala Indien, centrala USA, Asien, östra, östra USA, östra USA 2, Tyskland, västra centrala, Östra Japan, västra Japan, centrala Korea, södra centrala Schweiz, norra Asien, sydöstra USA, södra centrala USA, Förenade Arabemiraten, norra, Storbritannien, södra, Storbritannien, västra, västra centrala USA, Västeuropa , Västra USA, västra USA 2

## <a name="limitations"></a>Begränsningar

Den operativa säkerhets kopieringen av blobbar använder BLOB-återställning, BLOB-versioner, mjuk borttagning för blobbar, ändra feed för blobbar och ta bort lås för att tillhandahålla en lokal lösning för säkerhets kopiering. Så begränsningar som gäller för dessa funktioner gäller även för drift säkerhets kopiering.

**Scenarier som stöds:** Den operativa säkerhets kopieringen har stöd för block-blobar i standard-General-Purpose v2-lagrings konton. ADLS Gen2-konton stöds därför inte. Dessutom återställs inte alla sid-blobar, tillägg av blobbar och Premium-blobbar i ditt lagrings konto och endast block-blobbar återställs.

**Andra begränsningar:**

- Om du har tagit bort en behållare under kvarhållningsperioden, återställs inte behållaren med återställnings åtgärden vid tidpunkten. Om du försöker återställa ett intervall med blobbar som innehåller blobbar i en borttagen behållare, Miss lyckas återställnings åtgärden vid tidpunkten. Mer information om att skydda behållare från att tas bort finns i [mjuk borttagning för behållare (för hands version)](https://docs.microsoft.com/azure/storage/blobs/soft-delete-container-overview).
- Om en BLOB har flyttats mellan frekventa och låg frekventa nivåer under perioden mellan den aktuella tidpunkten och återställnings punkten, återställs blobben till den tidigare nivån. Det finns inte stöd för att återställa block blobbar i Arkiv nivån. Om en blob till exempel har flyttats till Arkiv nivån två dagar sedan, och en återställnings åtgärd återställer till en punkt tre dagar sedan, återställs inte blobben till den frekventa nivån. För att återställa en arkiverad BLOB, flytta först bort den från Arkiv lag rings nivån. Mer information finns i [rehydratisera BLOB-data från Arkiv](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration)lag rings nivån.
- Ett block som har överförts via funktionen för att [blockera eller](https://docs.microsoft.com/rest/api/storageservices/put-block) [blockera från URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url), men som inte har allokerats via [list block](https://docs.microsoft.com/rest/api/storageservices/put-block-list), är inte en del av en blob och återställs därför inte som en del av en återställnings åtgärd.
- Det går inte att återställa en blob med ett aktivt lån. Om en blob med ett aktivt lån ingår i det intervall med blobbar som ska återställas, Miss kommer återställnings åtgärden automatiskt. Bryt eventuella aktiva lån innan du påbörjar återställnings åtgärden.
- Ögonblicks bilder skapas eller tas inte bort som en del av en återställnings åtgärd. Endast bas-bloben återställs till sitt tidigare tillstånd.

## <a name="next-steps"></a>Nästa steg

- [Översikt över drift säkerhets kopiering för Azure-blobar (i för hands version)](blob-backup-overview.md)
