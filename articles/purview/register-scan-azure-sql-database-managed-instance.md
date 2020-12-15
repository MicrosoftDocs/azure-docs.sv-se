---
title: Registrera och skanna Azure SQL Database hanterade instansen
description: I den här självstudien beskrivs hur du skannar Azure SQL Database hanterade instanser
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 6eb17537fd64b192f64c36b38bab57e11d751328
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400785"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Registrera och skanna en Azure SQL Database Hanterad instans

Den här artikeln beskriver hur du registrerar en Azure SQL Database Hanterad instans data källa i avdelningens kontroll och konfigurerar en genomsökning på den.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den Azure SQL Database hanterade instans data källan har stöd för följande funktioner:

- **Fullständiga och stegvisa genomsökningar** för att avbilda metadata och klassificering i Azure SQL Database Hanterad instans.

- **Härkomst** mellan data till gångar för ADF-kopiering och data flödes aktiviteter.

### <a name="known-limitations"></a>Kända begränsningar

Azure avdelningens kontroll stöder inte genomsökning av [vyer](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15) i Azure SQL-hanterad instans.

## <a name="prerequisites"></a>Krav

- Skapa ett nytt avdelningens kontroll-konto om du inte redan har ett.

- [Konfigurera offentlig slut punkt i Azure SQL-hanterad instans](https://docs.microsoft.com/azure/azure-sql/managed-instance/public-endpoint-configure)
    > [!Note]
    > Din organisation måste kunna tillåta offentlig slut punkt eftersom den **privata slut punkten inte stöds ännu** av avdelningens kontroll. Om du använder privat slut punkt kommer sökningen inte att lyckas.

### <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Autentisering för att genomsöka Azure SQL Database hanterade instansen. Om du behöver skapa en ny autentisering måste du [godkänna databas åtkomst till SQL Database Hanterad instans](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage). Det finns tre autentiseringsmetoder som avdelningens kontroll stöder idag:

- SQL-autentisering
- Tjänstens huvudnamn
- Hanterad identitet

#### <a name="sql-authentication"></a>SQL-autentisering

> [!Note]
> Endast huvud inloggningen på server nivå (som skapats av etablerings processen) eller medlemmar i `loginmanager` databas rollen i huvud databasen kan skapa nya inloggningar. Det tar ungefär **15 minuter** efter att ha beviljat behörighet, avdelningens kontroll-kontot måste ha rätt behörighet för att kunna söka igenom resurserna.

Du kan följa anvisningarna i [Skapa inloggning](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) för att skapa en inloggning för Azure SQL Database Hanterad instans om du inte har det här alternativet. Du måste ha **användar namn** och **lösen ord** för att kunna utföra nästa steg.

1. Navigera till ditt nyckel valv i Azure Portal
1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namn** och **värde** som *lösen ord* från din Azure SQL Database hanterade instans
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Skapa slutligen [en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med **användar namn** och **lösen ord** för att konfigurera din sökning

#### <a name="service-principal-and-managed-identity"></a>Tjänstens huvud namn och hanterad identitet

Det finns flera steg för att tillåta avdelningens kontroll att använda tjänstens huvud namn för att genomsöka din Azure SQL Database hanterade instans

##### <a name="create-or-use-an-existing-service-principal"></a>Skapa eller Använd ett befintligt huvud namn för tjänsten

> [!Note]
> Hoppa över det här steget om du använder **hanterad identitet**

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
- [Konfigurera och hantera Azure AD-autentisering med Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure)
- Skapa en Azure AD-användare i Azure SQL Database Hanterad instans genom att följa kraven och självstudien om att [skapa inneslutna användare som är mappade till Azure AD-identiteter](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)
- Tilldela `db_owner` (**rekommenderas**) behörighet till identiteten

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Lägg till tjänstens huvud namn i Key Vault och avdelningens kontroll autentiseringsuppgift

> [!Note]
> Om du planerar att använda **hanterad identitet** kan du hoppa över det här steget eftersom standard identiteten för avdelningens kontroll redan finns i **avdelningens kontroll-MSI**

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

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="alternativ för registrering av källor" border="true":::
    
> [!Note]
> Azure-avdelningens kontroll stöder för närvarande inte VNET-konfiguration. Därför kan du inte göra IP-baserade brand Väggs inställningar.

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Registrera en data källa för en Azure SQL Database Hanterad instans

1. Navigera till ditt avdelningens kontroll-konto

1. Välj **källor** i det vänstra navigerings fältet

1. Välj **register**

1. Välj **Azure SQL Database Hanterad instans** och **Fortsätt** sedan

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Konfigurera SQL Data Source":::

1. Välj **ange manuellt**

1. Ange **fullständigt kvalificerat domän namn** och **port nummer** för den offentliga slut punkten. Välj sedan **Slutför** för att registrera data källan.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Lägg till Azure SQL Database Hanterad instans":::

    T.ex. `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> Om du tar bort din sökning raderas inte dina till gångar från tidigare Azure SQL Database kontrollerade instans genomsökningar.

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)
