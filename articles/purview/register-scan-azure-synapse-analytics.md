---
title: Så här genomsöker du Azure Synapse Analytics
description: I den här guiden beskrivs hur du skannar Azure Synapse Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 62ca32ab4e348e1488fbb87672e582436b91d05d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98875017"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Registrera och skanna Azure Synapse Analytics

Den här artikeln beskriver hur du registrerar och skannar en instans av Azure Synapse Analytics (tidigare SQL DW) i avdelningens kontroll.

## <a name="supported-capabilities"></a>Funktioner som stöds

Azure Synapse Analytics (tidigare SQL DW) stöder fullständiga och stegvisa genomsökningar för att avbilda metadata och scheman. Genomsökningar klassificerar även data automatiskt baserat på system-och anpassade klassificerings regler.

### <a name="known-limitations"></a>Kända begränsningar

Azure avdelningens kontroll stöder inte genomsökning av [vyer](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true) i Azure Synapse Analytics

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
- Du måste vara en Azure avdelningens kontroll data source-administratör
- Nätverks åtkomst mellan avdelningens kontroll-kontot och Azure Synapse Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Konfigurera autentisering för en sökning

Det finns tre sätt att konfigurera autentisering för Azure Synapse Analytics:

- Hanterad identitet
- SQL-autentisering
- Tjänstens huvudnamn

    > [!Note]
    > Endast huvud inloggningen på server nivå (som skapats av etablerings processen) eller medlemmar i `loginmanager` databas rollen i huvud databasen kan skapa nya inloggningar. Det tar ungefär 15 minuter efter att ha beviljat behörighet, avdelningens kontroll-kontot måste ha rätt behörighet för att kunna söka igenom resurserna.

### <a name="managed-identity-recommended"></a>Hanterad identitet (rekommenderas) 
   
Ditt avdelningens kontroll-konto har sin egen hanterade identitet som i princip är ditt avdelningens kontroll-namn när du skapade det. Du måste skapa en Azure AD-användare i Azure Synapse Analytics (tidigare SQL DW) med det exakta avdelningens kontroll: s hanterade identitets namn genom att följa kraven och självstudien för att [Skapa Azure AD-användare med hjälp av Azure AD-program](../azure-sql/database/authentication-aad-service-principal-tutorial.md).

Exempel på SQL-syntax för att skapa användare och bevilja behörighet:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

Autentiseringen måste ha behörighet att hämta metadata för databasen, scheman och tabeller. Det måste också kunna fråga tabellerna för klassificering. Rekommendationen är att tilldela `db_owner` behörighet till identiteten.

### <a name="service-principal"></a>Tjänstens huvudnamn

Om du vill använda tjänstens huvud namns autentisering för genomsökningar kan du använda en befintlig eller skapa en ny. 

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

Det krävs för att hämta tjänstens huvud namn för program-ID och hemlighet:

1. Navigera till tjänstens huvud namn i [Azure Portal](https://portal.azure.com)
1. Kopiera värdena **program-ID (klient) ID** från **Översikt** och **klient hemlighet** från **certifikat & hemligheter**.
1. Navigera till ditt nyckel valv
1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namnet** på ditt val och **värde** som **klient hemlighet** från tjänstens huvud namn
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Skapa slutligen [en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med tjänstens huvud namn för att konfigurera din sökning 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>Bevilja tjänstens huvud namn åtkomst till din Azure Synapse Analytics (tidigare SQL DW)

Dessutom måste du också skapa en Azure AD-användare i Azure Synapse Analytics genom att följa kraven och självstudien för att [Skapa Azure AD-användare med hjälp av Azure AD-program](../azure-sql/database/authentication-aad-service-principal-tutorial.md). Exempel på SQL-syntax för att skapa användare och bevilja behörighet:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Avdelningens kontroll kommer att behöva **program-ID: t** och **klient hemligheten** för att kunna söka.

### <a name="sql-authentication"></a>SQL-autentisering

Du kan följa anvisningarna i [Skapa inloggning](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) för att skapa en inloggning för Azure Synapse Analytics (tidigare SQL DW) om du inte redan har ett.

När den valda autentiseringsmetoden är **SQL-autentisering** måste du hämta ditt lösen ord och lagra det i nyckel valvet:

1. Hämta lösen ordet för din SQL-inloggning
1. Navigera till ditt nyckel valv
1. Välj **inställningar > hemligheter**
1. Välj **+ generera/importera** och ange **namn** och **värde** som *lösen ord* för SQL-inloggningen
1. Välj **skapa** för att slutföra
1. Om nyckel valvet inte är anslutet till avdelningens kontroll måste du [skapa en ny nyckel valv anslutning](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Slutligen [skapar du en ny autentiseringsuppgift](manage-credentials.md#create-a-new-credential) med nyckeln för att konfigurera din sökning

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Registrera en Azure Synapse Analytics-instans (tidigare SQL DW)

Registrera en ny Azure Synapse Analytics-Server i din Data Catalog genom att göra följande:

1. Navigera till ditt avdelningens kontroll-konto
1. Välj **källor** i det vänstra navigerings fältet
1. Välj **register**
1. På **register källor** väljer du **Azure Synapse Analytics (tidigare SQL DW)**
1. Välj **Fortsätt**

På skärmen **Registrera källor (Azure Synapse Analytics)** gör du följande:

1. Ange ett **namn** som data källan ska visas med i katalogen.
1. Välj hur du vill peka på önskat lagrings konto:
   1. Välj **från Azure-prenumeration**, Välj lämplig prenumeration i list rutan **Azure-prenumeration** och lämplig server från List rutan **Server namn** .
   1. Alternativt kan du välja **ange manuellt** och ange ett **Server namn**.
1. **Slutför** för att registrera data källan.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="alternativ för registrering av källor" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)