---
title: Åtkomst gransknings loggar – Azure Portal-Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar och får åtkomst till gransknings loggarna i Azure Database for MySQL från Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: a73a3c77310c0e19792758c0586975e14cfaebf8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541665"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Konfigurera och få åtkomst till gransknings loggar för Azure Database for MySQL i Azure Portal

Du kan konfigurera [Azure Database for MySQL gransknings loggar](concepts-audit-logs.md) och diagnostikinställningar från Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom den här instruktions guiden behöver du:

- [Azure Database for MySQL Server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurera gransknings loggning

>[!IMPORTANT]
> Vi rekommenderar att du bara loggar de händelse typer och användare som krävs för gransknings syfte för att säkerställa att serverns prestanda inte påverkas kraftigt.

Aktivera och konfigurera gransknings loggning.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj din Azure Database for MySQL-server.

1. Under avsnittet **Inställningar** på sid panelen väljer du **Server parametrar**.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Serverparametrar":::

1. Uppdatera **audit_log_enabled** -parametern till på.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Aktivera gransknings loggar":::

1. Välj de [händelse typer](concepts-audit-logs.md#configure-audit-logging) som ska loggas genom att uppdatera **audit_log_events** -parametern.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Granska logg händelser":::

1. Lägg till alla MySQL-användare som ska tas med eller undantas från loggning genom att uppdatera **audit_log_exclude_users** och **audit_log_include_users** parametrar. Ange användare genom att ange sitt användar namn för MySQL.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Exkludera användare i gransknings logg":::

1. När du har ändrat parametrarna kan du klicka på **Spara**. Eller så kan du **Ignorera** dina ändringar.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Spara":::

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar

1. Under avsnittet **övervakning** i sid panelen väljer du **diagnostikinställningar**.

1. Klicka på "+ Lägg till diagnostisk inställning" :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Lägg till diagnostikinställningar":::

1. Ange ett namn på en diagnostisk inställning.

1. Ange vilka data mottagare som ska skicka gransknings loggarna (lagrings konto, händelsehubben och/eller Log Analytics arbets yta).

1. Välj "MySqlAuditLogs" som logg typ.
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Konfigurera diagnostisk inställning":::

1. När du har konfigurerat data Sinks att skicka in gransknings loggarna till kan du klicka på **Spara**.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Spara diagnostisk inställning":::

1. Få åtkomst till gransknings loggarna genom att utforska dem i de data mottagare du konfigurerade. Det kan ta upp till 10 minuter innan loggarna visas.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [gransknings loggar](concepts-audit-logs.md) i Azure Database for mysql
- Lär dig hur du konfigurerar gransknings loggar i [Azure CLI](howto-configure-audit-logs-cli.md)