---
title: Registrera och skanna en lokal SQL Server
description: I den här självstudien beskrivs hur du genomsöker lokal SQL Server med en lokal IR.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 86942f509ab067a53c47b5132c451f0585760050
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549651"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Registrera och skanna en lokal SQL Server

Den här artikeln beskriver hur du registrerar en SQL Server-datakälla i avdelningens kontroll och konfigurerar en genomsökning på den.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den lokala SQL Server-datakällan har stöd för följande funktioner:

- **Fullständiga och stegvisa genomsökningar** för att avbilda metadata och klassificering i ett lokalt nätverk eller en SQL Server som är installerad på en virtuell Azure-dator.

- **Härkomst** mellan data till gångar för ADF-kopiering/data flödes aktiviteter

Lokal data källa för SQL Server stöder:

- alla versioner av SQL från SQL Server 2019 tillbaka till SQL Server 2000

- Autentiseringsmetod: SQL-autentisering

### <a name="known-limitations"></a>Kända begränsningar

Azure avdelningens kontroll stöder inte genomsökning av [vyer](/sql/relational-databases/views/views) i SQL Server.

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).

- Konfigurera en [integration runtime med egen värd](manage-integration-runtimes.md) för att genomsöka data källan.

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Det finns bara ett sätt att konfigurera autentisering för SQL Server lokalt:

- SQL-autentisering

### <a name="sql-authentication"></a>SQL-autentisering

SQL-kontot måste ha åtkomst till **huvud** databasen. Detta beror på att `sys.databases` finns i huvud databasen. Avdelningens kontroll-skannern måste räknas upp för `sys.databases` att det ska gå att hitta alla SQL-databaser på servern.

#### <a name="using-an-existing-server-administrator"></a>Använda en befintlig Server administratör

Om du planerar att använda en befintlig server administratörs användare (sa) för att söka igenom din lokala SQL Server kontrollerar du följande:

1. `sa` är inte ett Windows-konto för autentisering.

2. Den inloggning på server nivå som du planerar att använda måste ha Server roller för offentliga och sysadmin. Du kan kontrol lera detta genom att ansluta till servern, navigera till SQL Server Management Studio (SSMS), navigera till säkerhet, välja den inloggning som du planerar att använda, högerklicka på **Egenskaper** och sedan välja **Server roller**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Inloggning på server nivå.":::

#### <a name="creating-a-new-login-and-user"></a>Skapa en ny inloggning och användare

Följ stegen nedan om du vill skapa en ny inloggning och användare för att kunna söka igenom SQL Server:

> [!Note]
    > Alla stegen nedan kan utföras med hjälp av den kod som anges [här](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql).

1. Navigera till SQL Server Management Studio (SSMS), Anslut till servern, gå till säkerhet, högerklicka på inloggning och skapa ny inloggning. Se till att välja SQL-autentisering.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Skapa ny inloggning och användare.":::

2. Välj Server roller i det vänstra navigerings fältet och kontrol lera att den offentliga rollen är tilldelad.

3. Välj användar mappning i det vänstra navigerings fältet, Välj alla databaser på kartan och välj databas rollen: **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="användar mappning.":::

4. Klicka på OK för att spara.

5. Navigera igen till användaren som du skapade genom att högerklicka och välja **Egenskaper**. Ange ett nytt lösen ord och bekräfta det. Välj "Ange gammalt lösen ord" och ange det gamla lösen ordet. **Du måste ändra ditt lösen ord så snart du skapar en ny inloggning.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="ändra lösen ord.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Lagra lösen ordet för SQL-inloggning i ett nyckel valv och skapa en autentiseringsuppgift i avdelningens kontroll

1. Navigera till ditt nyckel valv i Azure-portal1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namn** och **värde** som *lösen ord* från din SQL Server-inloggning
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Skapa slutligen [en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med **användar namn** och **lösen ord** för att konfigurera din sökning

## <a name="register-a-sql-server-data-source"></a>Registrera en SQL Server-datakälla

1. Navigera till ditt avdelningens kontroll-konto

1. Under källor och skanning i det vänstra navigerings fönstret väljer du **integrerings körningar**. Kontrol lera att en lokal integration Runtime har kon figurer ATS. Om den inte har kon figurer ATS följer du stegen som beskrivs [här](manage-integration-runtimes.md) för att skapa en lokal integration runtime för skanning på en lokal eller virtuell Azure-dator som har åtkomst till ditt lokala nätverk.

1. Välj **källor** i det vänstra navigerings fältet

1. Välj **register**

1. Välj **SQL Server** och **Fortsätt** sedan

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Konfigurera SQL-datakällan.":::

5. Ange ett eget namn och en server slut punkt och välj sedan **Slutför** för att registrera data källan. Om till exempel SQL Server-FQDN är **foobar.Database.Windows.net** anger du *foobar* som server slut punkt.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
