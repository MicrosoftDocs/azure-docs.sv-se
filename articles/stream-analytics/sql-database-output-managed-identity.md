---
title: Använda hanterade identiteter för att få åtkomst till Azure SQL Database eller Azure Synapse Analytics – Azure Stream Analytics
description: Den här artikeln beskriver hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics jobb till Azure SQL Database eller Azure Synapse Analytics-utdata.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: e491c421f4af256b2e74fa61eb442d269bdb9e34
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487924"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Använda hanterade identiteter för att få åtkomst till Azure SQL Database eller Azure Synapse Analytics från ett Azure Stream Analytics jobb (förhands granskning)

Azure Stream Analytics stöder Synapse för [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för Azure SQL Database och Azure Analytics-utdata. Hanterade identiteter eliminerar begränsningar för användarbaserade autentiseringsmetoder, t. ex. behovet av att autentisera på grund av lösen ords ändringar eller förfallo datum för användar-token som inträffar var 90: e dag. När du tar bort behovet av att autentisera manuellt, kan dina Stream Analytics-distributioner helt automatiseras.

En hanterad identitet är ett hanterat program registrerat i Azure Active Directory som representerar ett angivet Stream Analytics jobb. Det hanterade programmet används för att autentisera till en mål resurs. Den här artikeln visar hur du aktiverar hanterad identitet för en Azure SQL Database eller en Azure Synapse Analytics-utdata för ett Stream Analytics jobb via Azure Portal.

## <a name="overview"></a>Översikt

Den här artikeln visar de steg som krävs för att ansluta ditt Stream Analytics-jobb till din Azure SQL Database-eller Azure Synapse Analytics SQL-pool med Managed Identity Authentication mode. 

- Först skapar du en systemtilldelad hanterad identitet för ditt Stream Analytics jobb. Detta är jobbets identitet i Azure Active Directory.  

- Lägg till en Active Directory-administratör till din SQL Server-eller Synapse-arbetsyta, vilket möjliggör autentisering med Azure AD (hanterad identitet) för den resursen.

- Sedan skapar du en innesluten användare som representerar Stream Analytics jobbets identitet i databasen. När Stream Analytics-jobbet interagerar med din SQL DB-eller Synapse SQL DB-resurs, är detta den identitet som används för att kontrol lera vilka behörigheter ditt Stream Analytics-jobbet har.

- Bevilja behörighet till ditt Stream Analytics jobb för att få åtkomst till dina SQL Database eller Synapse SQL-pooler.

- Slutligen lägger du till Azure SQL Database/Azure Synapse-analys som utdata i Stream Analyticss jobbet.

## <a name="prerequisites"></a>Förutsättningar

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Följande krävs för att använda den här funktionen:

- Ett Azure Stream Analytics jobb.

- En Azure SQL Database resurs.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Följande krävs för att använda den här funktionen:

- Ett Azure Stream Analytics jobb.

- En SQL-pool för Azure Synapse Analytics.

- Ett Azure Storage-konto som är [konfigurerat för ditt Stream Analytics-jobb](azure-synapse-analytics-output.md).

- Obs! Stream Analytics konto lagrings-MSI integrerad med Synapse SQL MSI är inte tillgänglig för tillfället.

---

## <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Först skapar du en hanterad identitet för ditt Azure Stream Analytics jobb.

1. Öppna Azure Stream Analytics-jobbet i [Azure Portal](https://portal.azure.com).

1. I den vänstra navigerings menyn väljer du **hanterad identitet** som finns under **Konfigurera**. Markera sedan kryss rutan bredvid **Använd tilldelad hanterad identitet** och välj **Spara**.

   ![Välj systemtilldelad hanterad identitet](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)

   Ett tjänst huvud namn för Stream Analytics jobbets identitet skapas i Azure Active Directory. Livs cykeln för den nyligen skapade identiteten hanteras av Azure. När Stream Analytics jobb tas bort, tas den tillhör ande identiteten (dvs. tjänstens huvud namn) automatiskt bort av Azure.

1. När du sparar konfigurationen visas objekt-ID: t (OID) för tjänstens huvud namn som huvud-ID: t enligt nedan: 

   ![Objekt-ID som visas som huvud-ID](./media/sql-db-output-managed-identity/principal-id.png)

   Tjänstens huvud namn har samma namn som Stream Analyticss jobbet. Om namnet på ditt jobb till exempel är *MyASAJob*, är namnet på tjänstens huvud namn också *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Välj en Active Directory administratör

När du har skapat en hanterad identitet väljer du en Active Directory administratör.

1. Gå till din Azure SQL Database-eller Azure Synapse Analytics SQL-pool och välj SQL Server-eller Synapse-arbetsytan resursen finns under. Du kan hitta länken till dessa på resurs översikts sidan bredvid *Server namn* eller *arbets ytans namn*.

1. Välj **Active Directory administratör** eller **SQL Active Directory-administratör** under **Inställningar**, för SQL Server och Synapse-arbetsytan. Välj sedan **Ange administratör**.

   ![Sidan Active Directory administratör](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. På sidan Active Directory administratör söker du efter en användare eller grupp som administratör för SQL Server och klickar på **Välj**. Detta är den användare som kommer att kunna skapa den **inneslutna databas användaren** i nästa avsnitt.

   ![Lägg till Active Directory administratör](./media/sql-db-output-managed-identity/add-admin.png)

   På sidan Active Directory admin visas alla medlemmar och grupper av din Active Directory. Nedtonade användare eller grupper kan inte väljas eftersom de inte stöds som Azure Active Directory administratörer. Se listan över administratörer som stöds i avsnittet **Azure Active Directory funktioner och begränsningar**   i [använda Azure Active Directory autentisering för autentisering med SQL Database eller Azure-Synapse](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

1. Välj **Spara** på sidan **Active Directory administratör** . Processen för att ändra admin tar några minuter.

## <a name="create-a-contained-database-user"></a>Skapa en innesluten databas användare

Därefter skapar du en innesluten databas användare i Azure SQL-eller Azure Synapse-databasen som är mappad till Azure Active Directory identiteten. Den inneslutna databas användaren har ingen inloggning för den primära databasen, men den mappas till en identitet i katalogen som är associerad med databasen. Azure Active Directory identiteten kan vara ett enskilt användar konto eller en grupp. I det här fallet vill du skapa en innesluten databas användare för ditt Stream Analytics jobb. 

Mer information finns i följande artikel för bakgrund i Azure AD-integrering: [Universal Authentication with SQL Database och Azure Synapse Analytics (SSMS-stöd för MFA)](../azure-sql/database/authentication-mfa-ssms-overview.md)

1. Anslut till din Azure SQL-eller Azure Synapse-databas med hjälp av SQL Server Management Studio. **Användar namnet** är en Azure Active Directory användare med behörigheten **ändra alla användare** . Administratören som du ställer in på SQL Server är ett exempel. Använd **Azure Active Directory – universell med MFA** -autentisering. 

   ![Anslut till SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Server namnet `<SQL Server name>.database.windows.net` kan vara ett annat i olika regioner. Till exempel bör regionen Kina använda `<SQL Server name>.database.chinacloudapi.cn` .
 
   Du kan ange en enskild Azure SQL-eller Azure Synapse-databas genom att gå till **alternativ > anslutnings egenskaper > ansluta till databasen**.  

   ![SQL Server anslutnings egenskaper](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. När du ansluter för första gången kan du stöta på följande fönster:

   ![Fönstret ny brand Väggs regel](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. I så fall går du till resursen SQL Server/Synapse-arbetsyta på Azure Portal. Under avsnittet **säkerhet** öppnar du sidan **brand väggar och virtuella nätverk/brand väggar** . 
   1. Lägg till en ny regel med valfritt regel namn.
   1. Använd IP *-adressen från fönstret* **ny brand Väggs regel** för *Start-IP*.
   1. Använd *till* -IP-adress från fönstret **ny regel för brand vägg** för *slut-IP*. 
   1. Välj **Spara** och försök att ansluta från SQL Server Management Studio igen. 

1. När du är ansluten skapar du den inneslutna databas användaren. Följande SQL-kommando skapar en innesluten databas användare som har samma namn som ditt Stream Analytics-jobb. Se till att inkludera hakparenteser runt *ASA_JOB_NAME*. Använd följande T-SQL-syntax och kör frågan. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```
   
    Du kan kontrol lera om du har lagt till den inneslutna databas användaren korrekt genom att köra följande kommando i SSMS under den befintliga databasen och kontrol lera om din *ASA_JOB_NAME* finns under kolumnen "namn".

   ```sql
   SELECT * FROM <SQL_DB_NAME>.sys.database_principals 
   WHERE type_desc = 'EXTERNAL_USER' 
   ```

1. För att Microsofts Azure Active Directory ska kunna verifiera om Stream Analytics-jobbet har åtkomst till SQL Database måste vi ge Azure Active Directory behörighet att kommunicera med databasen. Det gör du genom att gå till sidan "brand väggar och virtuella nätverk"/"brand väggar" i Azure Portal igen och aktivera alternativet "Tillåt Azure-tjänster och-resurser för att få åtkomst till den här servern/arbets ytan".

   ![Brand vägg och virtuellt nätverk](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Bevilja Stream Analytics jobb behörigheter

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

När du har skapat en innesluten databas användare och fått åtkomst till Azure-tjänster i portalen enligt beskrivningen i föregående avsnitt, har ditt Stream Analytics-jobb behörighet från hanterad identitet för att **ansluta** till din Azure SQL Database-resurs via hanterad identitet. Vi rekommenderar att du beviljar behörigheterna **Välj** och **Infoga** för Stream Analyticss jobbet eftersom de behövs senare i arbets flödet Stream Analytics. Med **Select** -behörighet kan jobbet testa anslutningen till tabellen i Azure SQL-databasen. Med **insert** -behörigheten kan du testa slut punkt till slut punkt Stream Analytics frågor när du har konfigurerat en indata och Azure SQL Database-utdata.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

När du har skapat en innesluten databas användare och fått åtkomst till Azure-tjänster i portalen enligt beskrivningen i föregående avsnitt, har ditt Stream Analytics-jobb behörighet från hanterad identitet för att **ansluta** till din Azure Synapse Database-resurs via hanterad identitet. Vi rekommenderar att du ytterligare beviljar behörigheterna **Välj**, **Infoga** och **Administrera databas Mass åtgärder** till det Stream Analytics jobbet eftersom de behövs senare i arbets flödet Stream Analytics. Med **Select** -behörighet kan jobbet testa anslutningen till tabellen i Azure Synapse-databasen. Behörigheterna för att **Infoga** och **Administrera databas Mass åtgärder** gör att du kan testa Stream Analytics från slut punkt till slut punkt när du har konfigurerat en indata och Azure Synapse-databasens utdata.

Om du vill bevilja behörigheten **Administrera databas Mass åtgärder** måste du ge alla behörigheter som är märkta som **kontroll** [underförstådda av databas behörighet](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) till Stream Analytics jobbet. Du behöver den här behörigheten eftersom Stream Analyticss jobbet utför **kopierings** instruktionen, vilket kräver att [databasens Mass åtgärder ADMINISTRERAs och infogas](/sql/t-sql/statements/copy-into-transact-sql).

---

Du kan bevilja dessa behörigheter till Stream Analytics jobb med SQL Server Management Studio. Mer information finns i GRANT (Transact-SQL)-referensen.

Om du bara vill bevilja behörighet till en viss tabell eller ett visst objekt i databasen använder du följande T-SQL-syntax och kör frågan. 

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

```sql
GRANT CONNECT, SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT CONNECT, SELECT, INSERT, CONTROL, ADMINISTER DATABASE BULK OPERATIONS OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

Du kan också högerklicka på din Azure SQL-eller Azure Synapse-databas i SQL Server Management Studio och välja **egenskaper > behörigheter**. Från menyn behörigheter kan du se Stream Analytics jobb som du har lagt till tidigare och du kan manuellt bevilja eller neka behörigheter när du ser anpassa.

Om du vill se alla behörigheter som du har lagt till i *ASA_JOB_NAME* användare kör du följande kommando i SSMS under den aktuella databasen: 

```sql
SELECT dbprin.name, dbprin.type_desc, dbperm.permission_name, dbperm.state_desc, dbperm.class_desc, object_name(dbperm.major_id) 
FROM sys.database_principals dbprin 
LEFT JOIN sys.database_permissions dbperm 
ON dbperm.grantee_principal_id = dbprin.principal_id 
WHERE dbprin.name = '<ASA_JOB_NAME>' 
```

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Skapa en Azure SQL Database-eller Azure Synapse-utdata

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Nu när din hanterade identitet har kon figurer ATS är du redo att lägga till en Azure SQL Database eller Azure Synapse-utdata till ditt Stream Analytics-jobb.

Se till att du har skapat en tabell i SQL Database med lämpligt schema för utdata. Namnet på den här tabellen är en av de obligatoriska egenskaper som måste fyllas i när du lägger till SQL Database-utdata till Stream Analytics-jobbet. Se också till att jobbet har **valt** och **Infoga** behörigheter för att testa anslutningen och köra Stream Analytics frågor. Se avsnittet [bevilja Stream Analytics jobb behörigheter](#grant-stream-analytics-job-permissions) om du inte redan har gjort det. 

1. Gå tillbaka till Stream Analytics jobbet och gå till sidan **utdata** under **jobb sto pol Ogin**. 

1. Välj **Lägg till > SQL Database**. Välj **hanterad identitet** i list rutan autentiseringsläge i fönstret utmatnings egenskaper i SQL Database utgående mottagare.

1. Fyll i resten av egenskaperna. Mer information om hur du skapar en SQL Database utdata finns i [skapa ett SQL Database utdata med Stream Analytics](sql-database-output.md). När du är färdig väljer du **Spara**.

1. När du har klickat på **Spara** ska ett anslutnings test till din resurs automatiskt utlösas. När det är klart har du konfigurerat Stream Analytics-jobbet för att ansluta till dig Azure SQL Database eller Synapse SQL Database med hjälp av Managed Identity Authentication mode. 

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Nu när din hanterade identitet och ditt lagrings konto har kon figurer ATS är du redo att lägga till en Azure SQL Database eller Azure Synapse-utdata till ditt Stream Analytics-jobb.

Se till att du har skapat en tabell i din Azure Synapse-databas med lämpligt schema för utdata. Namnet på den här tabellen är en av de obligatoriska egenskaper som måste fyllas i när du lägger till Azure dataSynapses-utdata i Stream Analytics-jobbet. Se också till att jobbet har **valt** och **Infoga** behörigheter för att testa anslutningen och köra Stream Analytics frågor. Se avsnittet [bevilja Stream Analytics jobb behörigheter](#grant-stream-analytics-job-permissions) om du inte redan har gjort det.

1. Gå tillbaka till Stream Analytics jobbet och gå till sidan **utdata** under **jobb sto pol Ogin**.

1. Välj **Lägg till > Azure Synapse Analytics**. Välj **hanterad identitet** i list rutan autentiseringsläge i fönstret utmatnings egenskaper i SQL Database utgående mottagare.

1. Fyll i resten av egenskaperna. Mer information om hur du skapar en Azure Synapse-utdata finns i [Azure Synapse Analytics-utdata från Azure Stream Analytics](azure-synapse-analytics-output.md). När du är färdig väljer du **Spara**.

1. När du har klickat på **Spara** ska ett anslutnings test till din resurs automatiskt utlösas. När den har slutförts är du nu redo att fortsätta med att använda hanterad identitet för din Azure Synapse Analytics-resurs med Stream Analytics. 

---

## <a name="remove-managed-identity"></a>Ta bort hanterad identitet

Den hanterade identitet som skapats för ett Stream Analytics jobb tas bara bort när jobbet tas bort. Det finns inget sätt att ta bort den hanterade identiteten utan att ta bort jobbet. Om du inte längre vill använda den hanterade identiteten kan du ändra autentiseringsmetoden för utdata. Den hanterade identiteten finns kvar tills jobbet tas bort och kommer att användas om du väljer att använda hanterad identitetsautentisering igen.

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Azure Synapse Analytics-utdata från Azure Stream Analytics](azure-synapse-analytics-output.md)
