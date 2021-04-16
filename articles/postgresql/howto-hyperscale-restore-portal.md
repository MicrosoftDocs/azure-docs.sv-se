---
title: Återställ – Hyperskala (Citus) – Azure Database for PostgreSQL – Azure Portal
description: Den här artikeln beskriver hur du utför återställningsåtgärder i Azure Database for PostgreSQL – Hyperskala (Citus) genom Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/14/2021
ms.openlocfilehash: 99e507fed35e5b74aa44bc2713550f7cf9d7b2e5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518889"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Återställning till tidpunkt av en Hyperskala (Citus) servergrupp

Den här artikeln innehåller stegvisa [](concepts-hyperscale-backup.md#restore) procedurer för att utföra återställningar till en tidpunkt för en Hyperskala (Citus)-servergrupp med hjälp av säkerhetskopior. Du kan återställa antingen till den tidigaste säkerhetskopian eller till en anpassad återställningspunkt inom kvarhållningsperioden.

## <a name="restoring-to-the-earliest-restore-point"></a>Återställa till den tidigaste återställningspunkten

Följ dessa steg för att återställa Hyperskala (Citus)-servergruppen till den tidigaste befintliga säkerhetskopian.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den servergrupp som du vill återställa.

2.  Klicka **på** Översikt i den vänstra panelen och klicka på **Återställ.**

    > [!IMPORTANT]
    > Om knappen **Återställ** inte finns för servergruppen ännu öppnar du en Azure-supportbegäran.

3.  Återställningssidan uppmanar dig att välja mellan den **tidigaste** och en **anpassad** återställningspunkt och visar det tidigaste datumet.

4.  Välj **Tidigaste återställningspunkt.**

5.  Ange ett nytt servergruppnamn i **fältet Återställ till ny** server. De andra fälten (prenumeration, resursgrupp och plats) visas men kan inte redigeras.

6.  Klicka på **OK**.

7.  Ett meddelande visas om att återställningsåtgärden har initierats.

Följ slutligen aktiviteterna [efter återställningen.](#post-restore-tasks)

## <a name="restoring-to-a-custom-restore-point"></a>Återställa till en anpassad återställningspunkt

Följ dessa steg för att återställa Hyperskala (Citus) servergrupp till ett datum och en tidpunkt som du väljer.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den servergrupp som du vill återställa.

2.  Klicka **på** Översikt i den vänstra panelen och klicka på **Återställ**

    > [!IMPORTANT]
    > Om knappen **Återställ** inte finns för servergruppen ännu öppnar du en Azure-supportbegäran.

3.  Återställningssidan uppmanar dig att välja mellan den **tidigaste** och en **anpassad** återställningspunkt och visar det tidigaste datumet.

4.  Välj **Anpassad återställningspunkt.**

5.  Välj datum och tid för **Återställningspunkt (UTC)** och ange ett nytt servergruppnamn i **fältet Återställ till ny** server. De andra fälten (prenumeration, resursgrupp och plats) visas men kan inte redigeras.
 
6.  Klicka på **OK**.

7.  Ett meddelande visas om att återställningsåtgärden har initierats.

Följ slutligen aktiviteterna [efter återställningen.](#post-restore-tasks)

## <a name="post-restore-tasks"></a>Post-restore tasks

Efter en återställning bör du göra följande för att få igång dina användare och program igen:

* Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klientprogram till den nya servern
* Se till att det finns en lämplig brandvägg på servernivå som användarna kan ansluta till. Dessa regler kopieras inte från den ursprungliga servergruppen.
* Justera PostgreSQL-serverparametrarna efter behov. Parametrarna kopieras inte från den ursprungliga servergruppen.
* Se till att rätt inloggningar och behörigheter på databasnivå finns på plats.
* Konfigurera aviseringar efter behov.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [säkerhetskopiering och återställning](concepts-hyperscale-backup.md) i Hyperskala (Citus).
* Ange [föreslagna aviseringar](./howto-hyperscale-alert-on-metric.md#suggested-alerts) Hyperskala (Citus) servergrupper.
