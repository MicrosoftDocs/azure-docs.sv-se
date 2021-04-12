---
title: Registrera och skanna Azure SQL Database
description: I den här självstudien beskrivs hur du skannar Azure SQL Database
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 9873ec2113c0c5704ccca4fa10daf6ac8d572989
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107439"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Registrera och skanna en Azure SQL Database

Den här artikeln beskriver hur du registrerar en Azure SQL Database-datakälla i avdelningens kontroll och konfigurerar en genomsökning på den.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den Azure SQL Database data källan har stöd för följande funktioner:

- **Fullständiga och stegvisa genomsökningar** för att avbilda metadata och klassificering i Azure SQL Database.

- **Härkomst** mellan data till gångar för ADF-kopiering och data flödes aktiviteter.

### <a name="known-limitations"></a>Kända begränsningar

> * Azure avdelningens kontroll stöder inte genomsökning av [vyer](/sql/relational-databases/views/views?view=azuresqldb-current&preserve-view=true) i Azure SQL Database.
> * Azure avdelningens kontroll stöder inte över 300 kolumner på fliken schema och visar "addition-columns-Trunked". 

## <a name="prerequisites"></a>Förutsättningar

1. Skapa ett nytt avdelningens kontroll-konto om du inte redan har ett.

1. Nätverks åtkomst mellan avdelningens kontroll-kontot och Azure SQL Database.


### <a name="set-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Autentisering för att genomsöka Azure SQL Database. Om du behöver skapa en ny autentisering måste du [godkänna databas åtkomst till SQL Database](../azure-sql/database/logins-create-manage.md). Det finns tre autentiseringsmetoder som avdelningens kontroll stöder idag:

- SQL-autentisering
- Tjänstens huvudnamn
- Hanterad identitet

#### <a name="sql-authentication"></a>SQL-autentisering

> [!Note]
> Endast huvud inloggningen på server nivå (som skapats av etablerings processen) eller medlemmar i `loginmanager` databas rollen i huvud databasen kan skapa nya inloggningar. Det tar ungefär **15 minuter** efter att ha beviljat behörighet, avdelningens kontroll-kontot måste ha rätt behörighet för att kunna söka igenom resurserna.

Du kan följa anvisningarna i [Skapa inloggning](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) för att skapa en inloggning för Azure SQL Database om du inte har detta tillgängligt. Du måste ha **användar namn** och **lösen ord** för att kunna utföra nästa steg.

1. Navigera till ditt nyckel valv i Azure Portal
1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namn** och **värde** som *lösen ord* från din Azure SQL Database
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Skapa slutligen [en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med **användar namn** och **lösen ord** för att konfigurera din sökning

#### <a name="service-principal-and-managed-identity"></a>Tjänstens huvud namn och hanterad identitet

Det finns flera steg för att tillåta avdelningens kontroll att använda tjänstens huvud namn eller avdelningens kontrolls **hanterade identitet** för att skanna Azure SQL Database

   > [!Note]
   > Avdelningens kontroll kommer att behöva **program-ID: t** och **klient hemligheten** för att kunna söka.

##### <a name="create-or-use-an-existing-service-principal"></a>Skapa eller Använd ett befintligt huvud namn för tjänsten

> [!Note]
> Hoppa över det här steget om du använder avdelningens kontroll- **hanterad identitet**

Om du vill använda ett huvud namn för tjänsten kan du använda ett befintligt konto eller skapa ett nytt. 

> [!Note]
> Följ dessa steg om du behöver skapa ett nytt huvud namn för tjänsten:
> 1. Navigera till [Azure Portal](https://portal.azure.com).
> 1. Välj **Azure Active Directory** på menyn till vänster.
> 1. Välj **Appregistreringar**.
> 1. Välj **+ ny program registrering**.
> 1. Ange ett namn för **programmet** (tjänstens huvud namn).
> 1. Välj **konton endast i den här organisations katalogen**.
> 1. För omdirigerings **-** URI väljer du webb och anger vilken URL du vill ha. Det behöver inte vara verkligt eller arbete.
> 1. Välj **Registrera**.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Konfigurera Azure AD-autentisering i databas kontot

Tjänstens huvud namn eller hanterade identitet måste ha behörighet att hämta metadata för databasen, scheman och tabeller. Det måste också kunna fråga tabellerna för klassificering.

- [Konfigurera och hantera Azure AD-autentisering med Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Om du använder hanterad identitet har ditt avdelningens kontroll-konto sin egen hanterade identitet som i princip är ditt avdelningens kontroll-namn när du skapade det. Du måste skapa en Azure AD-användare i Azure SQL Database med den exakta avdelningens kontroll-hanterade identiteten eller ditt eget tjänst huvud namn genom att följa de här självstudierna för [att skapa tjänstens huvud namn användare i Azure SQL Database](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database). Du måste tilldela rätt behörighet (t. ex. `db_owner` eller `db_datareader` ) till identiteten. Exempel på SQL-syntax för att skapa användare och bevilja behörighet:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > `Username`Är ditt eget tjänst huvud namn eller din avdelningens kontroll-hanterade identitet. Du kan läsa mer om [fasta databas roller och deras funktioner](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles).
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Lägg till tjänstens huvud namn i Key Vault och avdelningens kontroll autentiseringsuppgift

> [!Note]
> Om du planerar att använda avdelningens kontroll: s **hanterade identitet** kan du hoppa över det här steget, eftersom standard identiteten för avdelningens kontroll redan finns i **avdelningens kontroll-MSI-** autentiseringsuppgifter.

Det krävs för att hämta tjänstens huvud namn för program-ID och hemlighet:

1. Navigera till tjänstens huvud namn i [Azure Portal](https://portal.azure.com)
1. Kopiera värdena **program-ID (klient) ID** från **Översikt** och **klient hemlighet** från **certifikat & hemligheter**.
1. Navigera till ditt nyckel valv
1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namnet** på ditt val och **värde** som **klient hemlighet** från tjänstens huvud namn
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Skapa slutligen [en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med tjänstens huvud namn för att konfigurera din sökning

### <a name="firewall-settings"></a>Brandväggsinställningar

Din databas server måste tillåta att Azure-anslutningar aktive ras. Detta gör att Azure-avdelningens kontroll kan komma åt och ansluta servern. Du kan följa instruktionen anvisningar för anslutningar inifrån [Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure).

1. Navigera till ditt databas konto
1. Välj Server namnet på sidan **Översikt**
1. Välj **säkerhets > brand väggar och virtuella nätverk**
1. Välj **Ja** om **du vill tillåta Azure-tjänster och-resurser åtkomst till den här servern**

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Tillåt Azure-tjänster och-resurser åtkomst till den här servern." border="true":::
    
> [!Note]
> Azure-avdelningens kontroll stöder för närvarande inte VNET-konfiguration. Därför kan du inte göra IP-baserade brand Väggs inställningar.

## <a name="register-an-azure-sql-database-data-source"></a>Registrera en data källa för Azure SQL Database

Gör så här för att registrera en ny Azure SQL Database i din data katalog:

1. Navigera till ditt avdelningens kontroll-konto

1. Välj **källor** i det vänstra navigerings fältet

1. Välj **register**

1. Välj **Azure SQL Database** på **register källor**. Välj **Fortsätt**.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="Registrera ny data Källa" border="true":::

Gör följande på skärmen **Registrera källor (Azure SQL Database)** :

1. Ange ett **namn** som data källan ska visas med i katalogen.
1. Välj **från Azure-prenumeration**, Välj lämplig prenumeration i list rutan **Azure-prenumeration** och lämplig server från List rutan **Server namn** .
1. **Slutför** för att registrera data källan.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="alternativ för registrering av källor" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> * Om du tar bort din sökning raderas inte dina till gångar från tidigare Azure SQL Database genomsökningar.
> * Till gången kommer inte längre att uppdateras med schema ändringar om din käll tabell ändras och skannar om käll tabellen när du har redigerat beskrivningen på fliken schema i avdelningens kontroll.

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
