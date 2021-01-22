---
title: Åtkomst gransknings loggar – Azure Portal-Azure Database for MariaDB
description: I den här artikeln beskrivs hur du konfigurerar och får åtkomst till gransknings loggarna i Azure Database for MariaDB från Azure Portal.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: ec0791b169f667a2828087c37ea8f01fc3351bf7
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663153"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Konfigurera och få åtkomst till gransknings loggar i Azure Portal

Du kan konfigurera [Azure Database for MariaDB gransknings loggar](concepts-audit-logs.md) och diagnostikinställningar från Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom den här instruktions guiden behöver du:

- [Azure Database for MariaDB Server](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurera gransknings loggning

>[!IMPORTANT]
> Vi rekommenderar att du bara loggar de händelse typer och användare som krävs för gransknings syfte för att säkerställa att serverns prestanda inte påverkas kraftigt.

Aktivera och konfigurera gransknings loggning.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj din Azure Database for MariaDB-Server.

1. Under avsnittet **Inställningar** på sid panelen väljer du **Server parametrar**.
    ![Serverparametrar](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Uppdatera **audit_log_enabled** -parametern till på.
    ![Aktivera gransknings loggar](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Välj de [händelse typer](concepts-audit-logs.md#configure-audit-logging) som ska loggas genom att uppdatera **audit_log_events** -parametern.
    ![Granska logg händelser](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Lägg till eventuella MariaDB-användare som ska undantas från loggning genom att uppdatera **audit_log_exclude_users** -parametern. Ange användare genom att ange sitt MariaDB användar namn.
    ![Exkludera användare i gransknings logg](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. När du har ändrat parametrarna kan du klicka på **Spara**. Eller så kan du **Ignorera** dina ändringar.
    ![Spara](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under avsnittet **övervakning** i sid panelen väljer du **diagnostikinställningar**.

1. Klicka på "+ Lägg till diagnostisk inställning" ![ Lägg till diagnostikinställningar](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Ange ett namn på en diagnostisk inställning.

1. Ange vilka data mottagare som ska skicka gransknings loggarna (lagrings konto, händelsehubben och/eller Log Analytics arbets yta).

1. Välj "MySqlAuditLogs" som logg typ.
![Konfigurera diagnostisk inställning](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. När du har konfigurerat data Sinks att skicka in gransknings loggarna till kan du klicka på **Spara**.
![Spara diagnostisk inställning](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Få åtkomst till gransknings loggarna genom att utforska dem i de data mottagare du konfigurerade. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [gransknings loggar](concepts-audit-logs.md) i Azure Database for MariaDB
- Lär dig hur du konfigurerar gransknings loggar i [Azure CLI](howto-configure-audit-logs-cli.md)