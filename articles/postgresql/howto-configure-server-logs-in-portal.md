---
title: Hantera loggar – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du konfigurerar och kommer åt Server loggar (. log-filer) i Azure Database for PostgreSQL-enskild server från Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3b52cea1d440506caf5b8244c9643719edd8755c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999254"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Konfigurera och få åtkomst till Azure Database for PostgreSQL-enstaka server loggar från Azure Portal

Du kan konfigurera, lista och hämta [Azure Database for PostgreSQL loggar](concepts-server-logs.md) från Azure Portal.

## <a name="prerequisites"></a>Förutsättningar
Anvisningarna i den här artikeln kräver att du har [Azure Database for postgresql server](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till frågans loggar och fel loggar. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj din Azure Database for PostgreSQL-server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Skärm bild av alternativ för Server loggar":::

4. Om du vill se server parametrarna väljer du **Klicka här för att aktivera loggar och konfigurera logg parametrar**.

5. Ändra parametrarna som du behöver justera. Alla ändringar du gör i den här sessionen är markerade i lila.

   När du har ändrat parametrarna väljer du **Spara**. Du kan också ignorera ändringarna. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Skärm bild av alternativ för Server parametrar":::

På sidan **Server parametrar** kan du gå tillbaka till listan över loggar genom att stänga sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
När loggningen har påbörjats kan du Visa en lista över tillgängliga loggar och hämta enskilda loggfiler. 

1. Öppna Azure-portalen.

2. Välj din Azure Database for PostgreSQL-server.

3. Under avsnittet **övervakning** på sid panelen väljer du **Server loggar**. Sidan visar en lista över loggfilerna.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Skärm bild av sidan Server loggar med lista över loggar markerade":::

   > [!TIP]
   > Namngivnings konventionen för loggen är **postgresql-åååå-mm-dd_hh0000. log**. Datum och tid som används i fil namnet är den tidpunkt då loggen utfärdades. Loggfilerna roterar varje timme eller 100 MB, beroende på vilket som kommer först.

4. Om det behövs kan du använda sökrutan för att snabbt begränsa till en specifik logg baserat på datum och tid. Sökningen har samma namn som loggen.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-search.png" alt-text="Skärm bild av sidan Server loggar med sökruta och resultat markerade":::

5. Om du vill hämta enskilda loggfiler väljer du nedåtpilen bredvid varje loggfil i tabell raden.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/6-download.png" alt-text="Skärm bild av sidan Server loggar med nedåtpil ikon markerad":::

## <a name="next-steps"></a>Nästa steg
- Se [åtkomst Server loggar i CLI](howto-configure-server-logs-using-cli.md) för att lära dig hur du hämtar loggar program mässigt.
- Läs mer om [Server loggar](concepts-server-logs.md) i Azure Database for PostgreSQL. 
- Mer information om parameter definitioner och PostgreSQL-loggning finns i PostgreSQL-dokumentationen om [fel rapportering och loggning](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

