---
title: Återställ en Azure Database for MySQL flexibel server med Azure Portal.
description: Den här artikeln beskriver hur du utför återställningsåtgärder i Azure Database for MySQL flexibel server via Azure Portal
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502053"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Återställning till tidpunkt av en Azure Database for MySQL – flexibel server (förhandsversion) med hjälp av Azure Portal


> [!IMPORTANT]
> Azure Database for MySQL – flexibel server är för närvarande i offentlig förhandsversion.

Den här artikeln innehåller stegvisa instruktioner för att utföra återställningar till en annan tidpunkt på en flexibel server med hjälp av säkerhetskopior.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här i guiden behöver du:

-   Du måste ha en Azure Database for MySQL flexibel server.

## <a name="restore-to-the-latest-restore-point"></a>Återställa till den senaste återställningspunkten

Följ de här stegen för att återställa din flexibla server med hjälp av en tidigaste befintlig säkerhetskopia.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill återställa säkerhetskopian från.

2.  Klicka **på** Översikt i den vänstra panelen.

3.  Klicka på Återställ på **översiktssidan.**

4.  Sidan Återställ visas med ett alternativ för att välja mellan **Senaste återställningspunkt och** Anpassad återställningspunkt.

5.  Välj **Senaste återställningspunkt.**

6.  Ange ett nytt servernamn i **fältet Återställ till ny** server.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Tidigaste återställningstid":::

8.  Klicka på **OK**.

9.  Ett meddelande visas om att återställningsåtgärden har initierats.

## <a name="restoring-to-a-custom-restore-point"></a>Återställa till en anpassad återställningspunkt

Följ de här stegen för att återställa din flexibla server med hjälp av en tidigaste befintlig säkerhetskopia.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill återställa säkerhetskopian från.

2.  Klicka på Återställ på **översiktssidan.**

3.  Sidan Återställ visas med ett alternativ för att välja mellan Tidigaste återställningspunkt och Anpassad återställningspunkt.

4.  Välj **Anpassad återställningspunkt.**

5.  Välj datum och tid.

6.  Ange ett nytt servernamn i **fältet Återställ till ny** server.

6.  Ange ett nytt servernamn i **fältet Återställ till ny** server.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="visa översikt":::

7.  Klicka på **OK**.

8.  Ett meddelande visas om att återställningsåtgärden har initierats.


## <a name="perform-post-restore-tasks"></a>Utföra åtgärder efter återställning
När återställningen är klar bör du utföra följande uppgifter för att få igång dina användare och program igen:

- Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klientprogram till den nya servern.
- Se till att rätt VNet-regler finns på plats för att användarna ska kunna ansluta. Dessa regler kopieras inte över från den ursprungliga servern.
- Se till att rätt inloggningar och behörigheter på databasnivå finns på plats.
- Konfigurera aviseringar efter behov för den nyligen återställda servern.


## <a name="next-steps"></a>Nästa steg
Läs mer om [affärskontinuhet](concepts-business-continuity.md)
