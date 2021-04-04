---
title: Serverförtroendegrupper
titleSuffix: Azure SQL Managed Instance
description: Lär dig mer om gruppen Server förtroende och hur du hanterar förtroende mellan Azure SQL Managed instances.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: f9d5528746a85668677ab122d98e954bd39cd163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790737"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>Använd Server förtroende grupper för att konfigurera och hantera förtroende mellan SQL-hanterade instanser
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Gruppen Server förtroende är ett begrepp som används för att hantera förtroende mellan Azure SQL-hanterade instanser. Genom att skapa en grupp upprättas ett certifikat baserat förtroende mellan medlemmarna. Detta förtroende kan användas för olika scenarier i flera instanser. Om du tar bort servrar från gruppen eller tar bort gruppen tas förtroendet mellan servrarna bort. Om du vill skapa eller ta bort en server förtroende grupp måste användaren ha Skriv behörighet för en hanterad instans.
[Server förtroende grupp](/azure/templates/microsoft.sql/allversions) är ett Azure Resource Manager-objekt som har märkts som **SQL-förtroende grupp** i Azure Portal.

> [!NOTE]
> Gruppen Server förtroende introduceras i en offentlig för hands version av distribuerade transaktioner mellan Azure SQL Managed instances och har för närvarande vissa begränsningar som beskrivs längre fram i den här artikeln.

## <a name="server-trust-group-setup"></a>Installation av Server förtroende grupp

I följande avsnitt beskrivs hur du installerar Server förtroende grupp.

1. Gå till [Azure-portalen](https://portal.azure.com/).

2. Gå till den Azure SQL-hanterade instans som du planerar att lägga till i en nyligen skapad Server förtroende grupp.

3. Välj fliken **SQL-förtroende grupper** på **säkerhets** inställningarna.

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Serverförtroendegrupper":::

4. På sidan konfiguration av Server förtroende grupp väljer du ikonen **ny grupp** .

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Ny grupp":::

5. Ange **grupp namnet** i bladet **SQL Trust Group** Create. Det måste vara unikt i alla regioner där grupp medlemmarna finns. **Förtroende omfång** definierar typen av kors instans scenario som aktive ras med Server förtroende gruppen. I för hands versionen är endast tillämpligt förtroende omfång **distribuerade transaktioner**, så det är förvalt och kan inte ändras. Alla **grupp medlemmar** måste tillhöra samma **prenumeration** , men de kan vara under olika resurs grupper. Välj **resurs grupp** och **SQL Server/instans** för att välja den Azure SQL-hanterade instans som ska vara medlem i gruppen.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Bladet skapa server förtroende grupp":::

6. När alla obligatoriska fält har fyllts i klickar du på **Spara**.

## <a name="server-trust-group-maintenance-and-deletion"></a>Underhåll och borttagning av Server förtroende grupp

Det går inte att redigera Server förtroende gruppen. Om du vill ta bort en hanterad instans från en grupp måste du ta bort gruppen och skapa en ny.

I följande avsnitt beskrivs borttagnings processen för en server betrodd grupp. 
1. Gå till Azure-portalen.
2. Navigera till en hanterad instans som tillhör förtroende gruppen.
3. Välj fliken **SQL-förtroende grupper** på **säkerhets** inställningarna.
4. Välj den förtroende grupp som du vill ta bort.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Välj server förtroende grupp":::
5. Klicka på **ta bort grupp**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Ta bort Server förtroende grupp":::
6. Skriv in namnet på Server förtroende gruppen för att bekräfta borttagningen och klicka på **ta bort**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Bekräfta borttagning av Server förtroende grupp":::

> [!NOTE]
> Att ta bort Server förtroende gruppen kanske inte omedelbart tar bort förtroendet mellan de två hanterade instanserna. Borttagning av förtroenden kan verkställas genom att anropa en [redundansväxling](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) av hanterade instanser. Kontrol lera de [kända problemen](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues) för de senaste uppdateringarna på detta.

## <a name="limitations"></a>Begränsningar

Under den offentliga för hands versionen gäller följande begränsningar för Server förtroende grupper.
 * Namnet på Server förtroende gruppen måste vara unikt i alla regioner där dess medlemmar finns.
 * Gruppen får bara innehålla Azure SQL-hanterade instanser och de måste vara under samma Azure-prenumeration.
 * I för hands versionen kan gruppen ha exakt två hanterade instanser. Om du vill köra distribuerade transaktioner över fler än två hanterade instanser måste du skapa en server förtroende grupp för varje par av de hanterade instanserna.
 * Distribuerade transaktioner är det enda giltiga omfånget för Server förtroende grupper.
 * Server förtroende gruppen kan bara hanteras från Azure Portal. PowerShell-och CLI-stödet kommer senare.
 * Det går inte att redigera Server förtroende gruppen på Azure Portal. Den kan bara skapas eller släppas.
 * Ytterligare begränsningar för distribuerade transaktioner kan vara relaterade till ditt scenario. Det viktigaste är att det måste finnas en anslutning mellan hanterade instanser via privata slut punkter, via VNET eller VNET-peering. Se till att du är medveten om de aktuella [begränsningarna för distribuerade transaktioner för en hanterad instans](../database/elastic-transactions-overview.md#limitations).

## <a name="next-steps"></a>Nästa steg

* Mer information om distribuerade transaktioner i Azure SQL-hanterad instans finns i [distribuerade transaktioner](../database/elastic-transactions-overview.md).
* Information om versions uppdateringar och kända problem finns i [viktig information om Managed instance](../database/doc-changes-updates-release-notes.md).
* Om du har funktions förfrågningar kan du lägga till dem i [forumen för hanterade instanser](https://feedback.azure.com/forums/915676-sql-managed-instance).