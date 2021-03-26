---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs hur du konfigurerar postgres-parametrarna i Azure Database for PostgreSQL via Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: 2e9cecc03c1240d625fa8f6bbdcd633ada978082
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604217"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Konfigurera Server parametrar i Azure Database for PostgreSQL-enskild server via Azure Portal 
Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure Database for PostgreSQL-server via Azure Portal.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visa och redigera parametrar
1. Öppna [Azure-portalen](https://portal.azure.com).

2. Välj din Azure Database for PostgreSQL-server.

3. Under avsnittet **Inställningar** väljer du **Server parametrar**. Sidan visar en lista över parametrar, deras värden och beskrivningar.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Översikts sida för parametrar":::

4. Välj den **nedrullningsbara** knappen för att se möjliga värden för parametrar med uppräknad typ som client_min_messages.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Räkna upp listruta":::

5. Välj eller hovra över knappen **i** (information) för att se möjliga värden för numeriska parametrar som cpu_index_tuple_cost.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="informations knapp":::

6. Om det behövs kan du använda **sökrutan** för att begränsa till en specifik parameter. Sökningen har ett namn och en beskrivning av parametrarna.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Sökresultat":::

7. Ändra de parameter värden som du vill justera. Alla ändringar du gör i en session markeras i lila. När du har ändrat värdena kan du välja **Spara**. Eller så kan du **Ignorera** dina ändringar.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Spara eller ta bort ändringar":::

8. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Återställ alla till standard":::

## <a name="next-steps"></a>Nästa steg
Läs om:
- [Översikt över Server parametrar i Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurera parametrar med Azure CLI](howto-configure-server-parameters-using-cli.md)
