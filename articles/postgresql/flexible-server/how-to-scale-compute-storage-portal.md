---
title: Skalnings åtgärder – Azure Portal-Azure Database for PostgreSQL-flexibel Server
description: I den här artikeln beskrivs hur du utför skalnings åtgärder i Azure Database for PostgreSQL via Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90941612"
---
# <a name="scale-operations-in-flexible-server"></a>Skalnings åtgärder i flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Den här artikeln innehåller steg för att utföra skalnings åtgärder för data bearbetning och lagring. Du kommer att kunna ändra beräknings nivåerna mellan burst-, generell användnings-och minnesoptimerade SKU: er, inklusive att välja antalet virtuella kärnor som är lämpligt för att köra programmet. Du kan också skala upp lagringen. Förväntade IOPS visas baserat på beräknings nivån, virtuella kärnor och lagrings kapaciteten. Kostnads uppskattningen visas också baserat på ditt val.

> [!IMPORTANT]
> Det går inte att skala upp lagringen.

## <a name="pre-requisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

-   Du måste ha en Azure Database for PostgreSQL-flexibel Server. Samma procedur gäller också för flexibel server som kon figurer ATS med zon redundans.
> [!IMPORTANT]
> När du har konfigurerat med hög tillgänglighet kan du inte välja en SKU. Under skalnings åtgärden skalas standby först till önskad storlek, den primära servern växlas över och den primära är skalad. 

## <a name="scaling-the-compute-tier-and-size"></a>Skala beräknings nivå och storlek

Följ dessa steg om du vill välja beräknings nivå.
 
1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill återställa säkerhets kopian från.

2.  Klicka på **Compute + Storage**.

3.  En sida med aktuella inställningar visas.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="Compute + Storage-vy":::

4.  Du kan välja beräknings klass mellan burst, generell användning och minnesoptimerade nivåer.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="Visa lista med beräknings nivåer":::


5.  Om du är nöjd med standard virtuella kärnor och minnes storlekar kan du hoppa över nästa steg.

6.  Om du vill ändra antalet virtuella kärnor kan du klicka på list rutan med **beräknings storlek** och klicka på det önskade antalet virtuella kärnor/minne i listan.
    
    - Burst-beräknings nivå: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="burst-beräkning":::

    - Generell beräknings nivå för syfte: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="allmän beräkning":::

    - Optimerad beräknings nivå för minne: minnesoptimerade data :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="bearbetning":::

7.  Klicka på **Spara**. 
8.  Ett bekräftelse meddelande visas. Klicka på **OK** om du vill fortsätta. 
9.  Ett meddelande om skalnings åtgärden pågår.


## <a name="scaling-storage-size"></a>Skala lagrings storlek

Följ de här stegen för att öka lagrings storleken.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill öka lagrings storleken för.
2.  Klicka på **Compute + Storage**.

3.  En sida med aktuella inställningar visas.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="Klicka på Compute + Storage":::
4.  Fält **lagrings storleken i GIB** med ett bild fält visas med aktuell storlek.

5.  Dra fältet till önskad storlek. Motsvarande IOPS-nummer visas. IOPS är beroende av beräknings nivån och storleken. Kostnads informationen visas också. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="lagrings skalning":::

6.  Om du är nöjd med lagrings storleken klickar du på **Spara**. 
7.  Ett bekräftelse meddelande visas. Klicka på **OK** om du vill fortsätta. 
8.  Ett meddelande om skalnings åtgärden pågår.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [verksamhets kontinuitet](./concepts-business-continuity.md)
-   Lär dig mer om [hög tillgänglighet](./concepts-high-availability.md)
-   Läs mer om [säkerhets kopiering och återställning](./concepts-backup-restore.md)
