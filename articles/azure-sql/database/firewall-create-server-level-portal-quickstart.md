---
title: Skapa en brandväggsregel på servernivå
description: Skapa en brand Väggs regel på server nivå
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, sstein
ms.date: 02/11/2019
ms.openlocfilehash: bef1e9bdafd91a51290fe84af074c8cece7cc371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486734"
---
# <a name="quickstart-create-a-server-level-firewall-rule-using-the-azure-portal"></a>Snabb start: skapa en brand Väggs regel på server nivå med hjälp av Azure Portal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här snabb starten visar hur du skapar en [brand Väggs regel på server nivå](firewall-configure.md) i Azure SQL Database att använda Azure Portal för att ansluta till [logiska SQL-servrar](logical-servers.md), enskilda databaser och elastiska pooler och deras databaser. Du måste ha en brandväggsregel för att kunna ansluta från andra Azure-resurser och lokala resurser. Brand Väggs regler på server nivå gäller inte för Azure SQL-hanterad instans.

## <a name="prerequisites"></a>Förutsättningar

I den här snabbstarten används de resurser som skapades i avsnittet [Skapa en enskild databas med Azure-portalen](single-database-create-quickstart.md) som startpunkt.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Skapa en IP-brandväggsregel på servernivå

 SQL Database skapar en brand vägg på server nivå för enskilda databaser och databaser i pooler. Den här brand väggen förhindrar att klient program ansluter till servern eller någon av dess databaser om du inte skapar en IP-brandväggsregel för att öppna brand väggen. För en anslutning från en IP-adress utanför Azure skapar du en brand Väggs regel för en speciell IP-adress eller ett adress intervall som du vill kunna ansluta från. Mer information om regler för IP-brandvägg på server nivå och databas nivå finns i [regler för IP-brandvägg på server nivå och databas nivå](firewall-configure.md).

> [!NOTE]
> Azure SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk kan det hända att utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte din IT-avdelning öppnar port 1433.
> [!IMPORTANT]
> Med en brand Väggs regel på 0.0.0.0 kan alla Azure-tjänster passera genom brand Väggs regeln på server nivå och försöka ansluta till en databas via servern.

Följ de här stegen för att skapa en IP-brandväggsregel på server nivå för klientens IP-adress och aktivera extern anslutning via Azure SQL Database brand väggen enbart för din IP-adress.

1. När [databas](#prerequisites) distributionen är klar väljer du **SQL-databaser** på den vänstra menyn och väljer sedan **mySampleDatabase** på sidan SQL- **databaser** . Översiktssidan för databasen öppnas, där du kan se det fullständigt kvalificerade servernamnet (som **mynewserver-20170824.database.windows.net**) och alternativ för ytterligare konfiguration.

2. Kopiera det här fullständigt kvalificerade servernamnet för användning vid anslutning till servern och dess databaser i andra snabbstarter.

   ![servernamn](./media/firewall-create-server-level-portal-quickstart/server-name.png)

3. Välj **Konfigurera serverns brandvägg** i verktygsfältet. Sidan **brand Väggs inställningar** för servern öppnas.

   ![IP-brandväggsregel på servernivå](./media/firewall-create-server-level-portal-quickstart/server-firewall-rule.png)

4. Välj **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny IP-brandväggsregel på servernivå. Med en IP-brandväggsregel på servernivå kan du öppna port 1433 för en enskild IP-adress eller för ett IP-adressintervall.

   > [!IMPORTANT]
   > Som standard är åtkomst via Azure SQL Database brand väggen inaktive rad för alla Azure-tjänster. Välj **på** den här sidan om du vill aktivera åtkomst för alla Azure-tjänster.
   >

5. Välj **Spara**. En regel för IP-brandvägg på server nivå skapas för den aktuella IP-adressen som öppnar port 1433 på servern.

6. Stäng sidan **Brandväggsinställningar**.

Med hjälp av SQL Server Management Studio eller något annat verktyg kan du nu ansluta till servern och dess databaser från den här IP-adressen med det Server administratörs konto som skapades tidigare.

## <a name="clean-up-resources"></a>Rensa resurser

Spara de här resurserna om du vill gå till [nästa steg](#next-steps) och lära dig hur du ansluter och skickar frågor till din databas med ett antal olika metoder. Men om du vill ta bort de resurser som du skapade i den här snabbstarten utför du följande steg.

1. På menyn till vänster i Azure-portalen klickar du på **Resursgrupper** och sedan på **myResourceGroup**.
2. På sidan med resursgrupper väljer du **Ta bort**, skriver **myResourceGroup** i textrutan och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Nu när du har en databas kan du [ansluta och köra frågor](connect-query-content-reference-guide.md) med de verktyg och språk du föredrar, till exempel
  - [Ansluta och köra frågor med SQL Server Management Studio](connect-query-ssms.md)
  - [Ansluta och köra frågor med Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- I följande självstudier lär du dig hur du utformar din första databas, skapar tabeller och infogar data:
  - [Skapa din första enkla databas i Azure SQL Database med hjälp av SSMS](design-first-database-tutorial.md)
  - [Utforma en enkel databas i Azure SQL Database och ansluta med C# och ADO.NET](design-first-database-csharp-tutorial.md)
