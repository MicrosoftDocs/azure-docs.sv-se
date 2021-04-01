---
title: Återställa-Azure Portal-Azure Database for PostgreSQL – flexibel Server
description: I den här artikeln beskrivs hur du utför återställnings åtgärder i Azure Database for PostgreSQL via Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90941460"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Återställning av en flexibel Server vid en viss tidpunkt

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Den här artikeln innehåller steg-för-steg-anvisningar för att utföra återställningar av tidpunkter i flexibel server med hjälp av säkerhets kopiering. Du kan utföra antingen till en tidig återställnings punkt eller en anpassad återställnings punkt inom kvarhållningsperioden.

## <a name="pre-requisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

-   Du måste ha en Azure Database for PostgreSQL-flexibel Server. Samma procedur gäller också för flexibel server som kon figurer ATS med zon redundans.

## <a name="restoring-to-the-earliest-restore-point"></a>Återställer till den tidigaste återställnings punkten

Följ de här stegen för att återställa din flexibla server med en tidigare befintlig säkerhets kopia.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill återställa säkerhets kopian från.

2.  Klicka på **Översikt** i den vänstra panelen och klicka på **Återställ**
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Återställa översikt":::

3.  Sidan Återställ visas med ett alternativ för att välja mellan den tidigaste återställnings punkten och den anpassade återställnings punkten.

4.  Välj **tidigaste återställnings punkt** och ange ett nytt Server namn i fältet **Återställ till ny server** . Den tidigaste tidsstämpeln som du kan återställa till visas. 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="Tidigaste återställnings tid":::

5.  Klicka på **OK**.

6.  Ett meddelande visas om att återställnings åtgärden har startats.

## <a name="restoring-to-a-custom-restore-point"></a>Återställa till en anpassad återställnings punkt

Följ de här stegen för att återställa din flexibla server med en tidigare befintlig säkerhets kopia.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill återställa säkerhets kopian från.

2.  På sidan Översikt klickar du på **Återställ**.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Återställa översikt":::
    
3.  Sidan Återställ visas med ett alternativ för att välja mellan den tidigaste återställnings punkten och den anpassade återställnings punkten.

4.  Välj en **anpassad återställnings punkt**.

5.  Välj datum och tid och ange ett nytt Server namn i fältet **Återställ till ny server** . 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="Anpassad återställnings tid":::
 
6.  Klicka på **OK**.

7.  Ett meddelande visas om att återställnings åtgärden har startats.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [verksamhets kontinuitet](./concepts-business-continuity.md)
-   Lär dig mer om [Zone-redundant hög tillgänglighet](./concepts-high-availability.md)
-   Läs mer om [säkerhets kopiering och återställning](./concepts-backup-restore.md)
