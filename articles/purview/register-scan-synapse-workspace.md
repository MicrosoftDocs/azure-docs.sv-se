---
title: Så här genomsöker du Azure Synapse-arbetsytor
description: Lär dig hur du skannar en Synapse-arbetsyta i Azure avdelningens kontroll Data Catalog.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031448"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Registrera och skanna Azure Synapse-arbetsytor

Den här artikeln beskriver hur du registrerar en Azure Synapse-arbetsyta i avdelningens kontroll och konfigurerar en genomsökning på den.

## <a name="supported-capabilities"></a>Funktioner som stöds

Azure Synapse-arbetsytan genomsöker stöd för att hämta metadata och schema för dedikerade och serverbaserade SQL-databaser i dem. Den klassificerar också data automatiskt baserat på system-och anpassade klassificerings regler.

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
- Du måste vara en Azure avdelningens kontroll data source-administratör
- Konfigurera autentisering enligt beskrivningen i avsnitten nedan

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Konfigurera autentisering för att räkna upp dedikerade SQL Database-resurser under en Synapse-arbetsyta

1. Navigera till den **resurs grupp** eller **prenumeration** som arbets ytan Synapse finns i Azure Portal.  
1. Välj **Access Control (IAM)**   i den vänstra navigerings menyn 
1. Du måste vara ägare eller administratör för användar åtkomst för att lägga till en roll i resurs gruppen eller prenumerationen. Välj *+ Lägg till* . 
1. Ange rollen **läsare** och ange namnet på ditt Azure avdelningens kontroll-konto (som representerar dess MSI) under Välj indatamängd. Klicka på *Spara* för att slutföra roll tilldelningen.
1. Följ steg 2 till 4 ovan för att även lägga till rollen **Storage BLOB data Reader** för Azure avdelningens kontroll MSI i resurs gruppen eller prenumerationen som Synapse-arbetsytan finns i.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>Konfigurera autentisering för uppräkning av Server lös SQL Database-resurser under en Synapse-arbetsyta

> [!NOTE]
> Du måste vara **Synapse-administratör** på arbets ytan för att köra dessa kommandon. Läs mer om Synapse-behörigheter [här](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Navigera till din Synapse-arbetsyta
1. Navigera till **data** -avsnittet och en av dina server utan SQL-databaser
1. Klicka på Ellipse-ikonen och starta ett nytt SQL-skript
1. Lägg till Azure avdelningens kontroll-kontots MSI (som representeras av konto namnet) som **sysadmin** på SERVERns SQL-databaser genom att köra kommandot nedan i SQL-skriptet:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>Konfigurera autentisering för genomsökning av resurser under en Synapse-arbetsyta

Det finns tre sätt att konfigurera autentisering för en Azure Synapse-Källa:

- Hanterad identitet
- Tjänstens huvudnamn
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>Använda hanterad identitet för dedikerade SQL-databaser

1. Navigera till din **Synapse-arbetsyta**
1. Navigera till **data** -avsnittet och en av dina server utan SQL-databaser
1. Klicka på Ellipse-ikonen och starta ett nytt SQL-skript
1. Lägg till Azure avdelningens kontroll-kontots MSI (som representeras av konto namnet) som **db_owner** på den DEDIKERADe SQL-databasen genom att köra kommandot nedan i SQL-skriptet:

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>Använda hanterad identitet för Server lös SQL-databaser

1. Navigera till din **Synapse-arbetsyta**
1. Navigera till **data** -avsnittet och en av dina server utan SQL-databaser
1. Klicka på Ellipse-ikonen och starta ett nytt SQL-skript
1. Lägg till Azure avdelningens kontroll-kontots MSI (som representeras av konto namnet) som **sysadmin** på SERVERns SQL-databaser genom att köra kommandot nedan i SQL-skriptet:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>Använda tjänstens huvud namn för dedikerade SQL-databaser

> [!NOTE]
> Du måste först konfigurera en ny **autentiseringsuppgift** av typen tjänst huvud namn genom att följa anvisningarna [här](manage-credentials.md).

1. Navigera till din **Synapse-arbetsyta**
1. Navigera till **data** -avsnittet och en av dina server utan SQL-databaser
1. Klicka på Ellipse-ikonen och starta ett nytt SQL-skript
1. Lägg till **tjänstens huvud namn-ID** som **db_owner** på den dedikerade SQL-databasen genom att köra kommandot nedan i SQL-skriptet:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>Använda tjänstens huvud namn för Server lös SQL-databaser

1. Navigera till din **Synapse-arbetsyta**
1. Navigera till **data** -avsnittet och en av dina server utan SQL-databaser
1. Klicka på Ellipse-ikonen och starta ett nytt SQL-skript
1. Lägg till Azure avdelningens kontroll-kontots MSI (som representeras av konto namnet) som **sysadmin** på SERVERns SQL-databaser genom att köra kommandot nedan i SQL-skriptet:
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> Du måste konfigurera autentisering för varje dedikerad SQL-databas på din Synapse-arbetsyta, som du tänker registrera och skanna. De behörigheter som anges ovan för Server lös SQL-databas gäller för alla i din arbets yta, vilket innebär att du bara måste köra den en gång.
    
## <a name="register-an-azure-synapse-source"></a>Registrera en Azure Synapse-källa

Gör så här för att registrera en ny Azure Synapse-källa i data katalogen:

1. Navigera till ditt avdelningens kontroll-konto
1. Välj **källor** i det vänstra navigerings fältet
1. Välj **register**
1. Välj **Azure Synapse Analytics (flera)** på **register källor**
1. Välj **Fortsätt**

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Konfigurera Azure Synapse source":::

På skärmen **Registrera källor (Azure Synapse Analytics)** gör du följande:

1. Ange ett **namn** som data källan ska visas med i katalogen.
1. Alternativt kan du välja en **prenumeration** att filtrera ned till.
1. **Välj ett namn på Synapse-arbetsytan** i list rutan. SQL-slutpunkterna fylls i automatiskt baserat på ditt val av arbets yta. 
1. Välj en **samling** eller skapa en ny (valfritt)
1. **Slutför** registreringen av data källan

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Fyllnings information för Azure Synapse source":::

## <a name="creating-and-running-a-scan"></a>Skapa och köra en sökning

Om du vill skapa och köra en ny genomsökning gör du följande:

1. Navigera till avsnittet **källor** .

1. Välj den data källa som du har registrerat.

1. Klicka på Visa information och välj **+ Ny skanning** eller Använd ikonen Skanna snabb åtgärd på käll panelen

1. Fyll i *namnet* och välj alla typer av resurser som du vill genomsöka i den här källan. **SQL Database** är den enda typ som stöds för närvarande inom en Synapse-arbetsyta.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure Synapse source scanning":::

1. Välj **autentiseringsuppgifter** för att ansluta till resurserna i din data källa. 
  
1. Inom varje typ kan du välja att antingen söka igenom alla resurser eller en delmängd av dem efter namn.
1.  Fortsätt genom att klicka på **Fortsätt** . 

1.  Välj en **skannings regel uppsättning** av typen Azure Synapse SQL. Du kan också skapa skannings regel uppsättningar infogade.

1. Välj din genomsöknings utlösare. Du kan schemalägga att den ska köras **varje vecka/månad** eller **en gång**

1. Granska din genomsökning och välj Spara för att slutföra installationen   

## <a name="viewing-your-scans-and-scan-runs"></a>Visa genomsökningar och skannings körningar

1. Visa information om källan genom att klicka på **Visa detaljer** på panelen under avsnittet källor. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Information om Azure Synapse-källor"::: 

1. Visa information om genomsöknings körningar genom att gå till sidan med **genomsöknings information** .
    1. *Statusfältet* är en kort sammanfattning av de underordnade resursernas körnings status. Den kommer att visas på genomsökningen på arbets ytans nivå.
    1. Grönt betyder att det lyckades, medan rött innebär fel. Grått innebär att genomsökningen fortfarande pågår
    1. Du kan klicka i varje skanning för att visa mer detaljerade Detaljer

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Information om Azure Synapse-genomsökning" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. Visa en sammanfattning av de senaste misslyckade genomsöknings körningarna längst ned på sidan med käll information. Du kan också klicka i om du vill visa mer detaljerad information som rör dessa körningar.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Hantera dina genomsökningar – redigera, ta bort eller Avbryt
Gör följande för att hantera eller ta bort en genomsökning:

- Gå till hanterings centret. Välj data källor under avsnittet källor och sökning och välj sedan på önskad data källa.

- Välj den genomsökning som du vill hantera. Du kan redigera sökningen genom att välja Redigera.

- Du kan ta bort din genomsökning genom att välja ta bort.
- Om en genomsökning körs kan du även avbryta den.

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)   