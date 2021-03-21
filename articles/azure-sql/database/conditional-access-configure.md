---
title: Villkorlig åtkomst
description: Lär dig hur du konfigurerar villkorlig åtkomst för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: c18d235977f1256a10e813fa8e02aa3590366fe1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936421"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Villkorlig åtkomst med Azure SQL Database-och Azure Synapse-analys

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md), [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md)och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) stöder villkorlig åtkomst från Microsoft.

Följande steg visar hur du konfigurerar Azure SQL Database, SQL-hanterad instans eller Azure-Synapse för att genomdriva en princip för villkorlig åtkomst.  

## <a name="prerequisites"></a>Förutsättningar

- Du måste konfigurera Azure SQL Database, Azure SQL-hanterad instans eller dedikerad SQL-pool i Azure Synapse för att stödja Azure Active Directory (Azure AD)-autentisering. Detaljerade anvisningar finns i [Konfigurera och hantera Azure Active Directory autentisering med SQL Database eller Azure Synapse](authentication-aad-configure.md).  
- När Multi-Factor Authentication är aktiverat måste du ansluta till ett verktyg som stöds, till exempel den senaste SQL Server Management Studio (SSMS). Mer information finns i [konfigurera Azure SQL Database Multi-Factor Authentication för SQL Server Management Studio](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Konfigurera villkorlig åtkomst

> [!NOTE]
> I exemplet nedan används Azure SQL Database, men du bör välja lämplig produkt som du vill konfigurera villkorlig åtkomst för.

1. Logga in på Azure Portal, Välj **Azure Active Directory** och välj sedan **villkorlig åtkomst**. Mer information finns i [teknisk referens för Azure Active Directory villkorlig åtkomst](../../active-directory/conditional-access/concept-conditional-access-conditions.md).  
   ![Bladet för villkorlig åtkomst](./media/conditional-access-configure/conditional-access-blade.png)

2. I bladet **villkorlig åtkomst – principer** , klickar du på **ny princip**, anger ett namn och klickar sedan på **Konfigurera regler**.  
3. Under **tilldelningar** väljer **du användare och grupper**, markerar **Välj användare och grupper** och väljer sedan användaren eller gruppen för villkorlig åtkomst. Klicka på **Välj** och sedan på **Slutför** för att godkänna ditt val.  
   ![Välj användare och grupper](./media/conditional-access-configure/select-users-and-groups.png)  

4. Välj **molnappar** och klicka på **Välj appar**. Du ser alla appar som är tillgängliga för villkorlig åtkomst. Välj **Azure SQL Database**, klicka på **Välj** längst ned på sidan och klicka sedan på **färdig**.  
   ![Välj SQL Database](./media/conditional-access-configure/select-sql-database.png)  
   Om du inte hittar **Azure SQL Database** som anges i följande tredje skärm bild, utför du följande steg:
   - Anslut till databasen i Azure SQL Database genom att använda SSMS med ett administratörs konto för Azure AD.  
   - Kör `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Logga in på Azure AD och kontrol lera att Azure SQL Database, SQL-hanterad instans eller Azure-Synapse visas i programmen i Azure AD-instansen.  

5. Välj **åtkomst kontroller**, Välj **bevilja** och kontrol lera den princip som du vill använda. I det här exemplet väljer vi **Kräv Multi-Factor Authentication**.  
   ![Välj bevilja åtkomst](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Sammanfattning

Det valda programmet (Azure SQL Database) med Azure AD Premium, tillämpar nu den valda principen för villkorlig åtkomst, vilken **krävs Multi-Factor Authentication.**

Kontakta om du har frågor om Azure SQL Database och Azure Synapse om multifaktorautentisering <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Nästa steg  

En själv studie kurs finns [i skydda din databas i SQL Database](secure-database-tutorial.md).