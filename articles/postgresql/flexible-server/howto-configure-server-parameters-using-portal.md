---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for PostgreSQL – flexibel Server
description: Den här artikeln beskriver hur du konfigurerar postgres-parametrarna i Azure Database for PostgreSQL flexibel Server genom Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4f945c2c7fffb143bdb8324a330775fb072b25c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90941444"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Konfigurera Server parametrar i Azure Database for PostgreSQL-flexibel server via Azure Portal 

Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure Database for PostgreSQL flexibel Server genom Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom den här instruktions guiden behöver du:
- [Azure Database for PostgreSQL flexibel Server](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visa och redigera parametrar

1. Öppna [Azure-portalen](https://portal.azure.com).

2. Välj din flexibla Server.

3. Under avsnittet **Inställningar** väljer du **Server parametrar**. Sidan visar en lista över parametrar, deras värden och beskrivningar.
![Översikts sida för parametrar](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Välj den **nedrullningsbara** knappen för att se möjliga värden för parametrar med uppräknad typ som client_min_messages.
![Räkna upp listruta](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Välj eller hovra över knappen **i** (information) för att se möjliga värden för numeriska parametrar som cpu_index_tuple_cost.
![informations knapp](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Om det behövs kan du använda **sökrutan** för att begränsa till en specifik parameter. Sökningen har ett namn och en beskrivning av parametrarna.
![Sökresultat](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Ändra de parameter värden som du vill justera. Alla ändringar du gör i en session markeras i lila. När du har ändrat värdena kan du välja **Spara**. Eller så kan du **Ignorera** dina ändringar.
![Spara eller ta bort ändringar](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
![Återställ alla till standard](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Nästa steg

Läs om:

- [Översikt över Server parametrar i Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurera parametrar med Azure CLI](howto-configure-server-parameters-using-cli.md)
