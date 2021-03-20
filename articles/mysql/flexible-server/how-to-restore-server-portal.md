---
title: Återställa-Azure Portal-Azure Database for MySQL – flexibel Server
description: I den här artikeln beskrivs hur du utför återställnings åtgärder i Azure Database for MySQL via Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 062d53fcb122ebacd004d7dca5e11f5a883354cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241964"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Återställning vid olika tidpunkter för en Azure Database for MySQL-flexibel Server (för hands version)


> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här artikeln innehåller steg-för-steg-anvisningar för att utföra återställningar av tidpunkter i flexibel server med hjälp av säkerhets kopiering.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

-   Du måste ha en Azure Database for MySQL flexibel Server.

## <a name="restore-to-the-latest-restore-point"></a>Återställ till den senaste återställnings punkten

Följ de här stegen för att återställa din flexibla server med en tidigare befintlig säkerhets kopia.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill återställa säkerhets kopian från.

2.  Klicka på **Översikt** i den vänstra panelen.

3.  På sidan Översikt klickar du på **Återställ**.

    Placeholder

4.  Sidan Återställ visas med ett alternativ för att välja mellan den **senaste** återställnings punkten och den anpassade återställnings punkten.

5.  Välj **senaste återställnings punkt**.


6.  Ange ett nytt Server namn i fältet **Återställ till ny server** .

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Tidigaste återställnings tid":::

8.  Klicka på **OK**.

9.  Ett meddelande visas om att återställnings åtgärden har startats.

## <a name="restoring-to-a-custom-restore-point"></a>Återställa till en anpassad återställnings punkt

Följ de här stegen för att återställa din flexibla server med en tidigare befintlig säkerhets kopia.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill återställa säkerhets kopian från.

2.  På sidan Översikt klickar du på **Återställ**.

    Placeholder

3.  Sidan Återställ visas med ett alternativ för att välja mellan den tidigaste återställnings punkten och den anpassade återställnings punkten.

4.  Välj en **anpassad återställnings punkt**.

5.  Välj datum och tid.

6.  Ange ett nytt Server namn i fältet **Återställ till ny server** .

6.  Ange ett nytt Server namn i fältet **Återställ till ny server** . 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="Visa översikt":::
 
7.  Klicka på **OK**.

8.  Ett meddelande visas om att återställnings åtgärden har startats.

## <a name="next-steps"></a>Nästa steg

Platshållare
