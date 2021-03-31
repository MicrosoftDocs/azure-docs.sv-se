---
title: Fråga en SQL Database med hjälp av Frågeredigeraren i Azure Portal (för hands version)
description: Lär dig hur du använder Frågeredigeraren för att köra Transact-SQL-frågor (T-SQL) mot en databas i Azure SQL Database.
titleSuffix: Azure SQL Database
keywords: ansluta till SQL Database, fråga SQL Database, Azure Portal, Portal, Frågeredigeraren
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, contperf-fy21q3-portal
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: a6f13e27a5aa2684a16565c616d781e3d5c3a750
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594197"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Snabb start: Använd Azure Portalens Frågeredigeraren (för hands version) för att fråga en Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Frågeredigeraren är ett verktyg i Azure Portal för att köra SQL-frågor mot databasen i Azure SQL Database eller informations lagret i Azure Synapse Analytics.

I den här snabb starten använder du Frågeredigeraren för att köra Transact-SQL (T-SQL)-frågor mot en databas.

## <a name="prerequisites"></a>Förutsättningar

### <a name="create-a-database-with-sample-data"></a>Skapa en databas med exempeldata

För att slutföra den här snabb starten krävs AdventureWorksLT-exempel databasen. Om du inte har en fungerande kopia av AdventureWorksLT-exempel databasen i SQL Database hjälper följande snabb start dig att snabbt skapa en:

[Snabb start: skapa en databas i Azure SQL Database med hjälp av Azure Portal, PowerShell eller Azure CLI](single-database-create-quickstart.md)

### <a name="set-an-azure-active-directory-admin-for-the-server-optional"></a>Ange en Azure Active Directory administratör för servern (valfritt)

Genom att konfigurera en Azure Active Directory (Azure AD)-administratör kan du använda en enda identitet för att logga in på Azure Portal och databasen. Om du vill använda Azure AD för att ansluta till Frågeredigeraren följer du stegen nedan.

Den här processen är valfri, du kan i stället använda SQL-autentisering för att ansluta till Frågeredigeraren.

> [!NOTE]
> * E-postkonton (till exempel outlook.com, gmail.com, yahoo.com och så vidare) stöds ännu inte som Azure AD-administratörer. Se till att välja en användare som skapats antingen internt i Azure AD eller federerade till Azure AD.
> * Azure AD admin-inloggning fungerar med konton som har 2-Factor Authentication aktiverat, men Frågeredigeraren stöder inte tvåfaktorautentisering.

1. I Azure Portal navigerar du till din SQL Database-Server.

2. Välj **Active Directory administratör** på **SQL Server** -menyn.

3. I verktygsfältet SQL Server **Active Directory administrations** sida väljer du **Ange administratör**.

    ![Välj active directory](./media/connect-query-portal/select-active-directory.png)

4. På sidan **Lägg till administratör** , i sökrutan, anger du en användare eller grupp som du vill söka efter, väljer den som administratör och väljer sedan knappen **Välj** .

5. I verktygsfältet SQL Server **Active Directory administrations** sida väljer du **Spara**.

## <a name="using-sql-query-editor"></a>Använda SQL-Frågeredigeraren

1. Logga in på [Azure Portal](https://portal.azure.com/) och välj den databas som du vill fråga.

2. I menyn **SQL-databas** väljer du **Frågeredigeraren (för hands version)**.

    ![hitta frågeredigerare](./media/connect-query-portal/find-query-editor.PNG)

### <a name="establish-a-connection-to-the-database"></a>Upprätta en anslutning till databasen

Även om du är inloggad på portalen måste du ändå ange autentiseringsuppgifter för att komma åt databasen. Du kan ansluta med SQL-autentisering eller Azure Active Directory för att ansluta till databasen.

#### <a name="connect-using-sql-authentication"></a>Anslut med SQL-autentisering

1. På **inloggnings** sidan under **SQL Server-autentisering** anger du ett **inloggnings namn** och **lösen ord** för en användare som har åtkomst till databasen. Om du inte är säker använder du inloggning och lösen ord för Server administratören för databasens Server.

    ![logga in](./media/connect-query-portal/login-menu.png)

2. Välj **OK**.

#### <a name="connect-using-azure-active-directory"></a>Ansluta med Azure Active Directory

I **Frågeredigeraren (förhands granskning)** tittar du på **inloggnings** sidan i avsnittet **Active Directory autentisering** . Autentisering sker automatiskt, så om du är en Azure AD-administratör till databasen visas ett meddelande som säger att du har loggat in. Välj sedan knappen **Fortsätt som** *\<your user or group ID>* . Om sidan indikerar att du inte har loggat in, kan du behöva uppdatera sidan.

### <a name="query-a-database-in-sql-database"></a>Fråga en databas i SQL Database

Följande exempel frågor bör köras mot AdventureWorksLT-exempel databasen.

#### <a name="run-a-select-query"></a>Kör en URVALs fråga

1. Klistra in följande fråga i Frågeredigeraren:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Välj **Kör** och granska sedan utdata i **resultat** fönstret.

   ![resultat från frågeredigeraren](./media/connect-query-portal/query-editor-results.png)

3. Alternativt kan du spara frågan som en. SQL-fil eller exportera returnerade data som en. JSON-,. csv-eller. XML-fil.

#### <a name="run-an-insert-query"></a>Kör en INSERT-fråga

Kör följande [insert](/sql/t-sql/statements/insert-transact-sql/) T-SQL-uttryck för att lägga till en ny produkt i `SalesLT.Product` tabellen.

1. Ersätt den tidigare frågan med denna.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Välj **Kör** för att infoga en ny rad i tabellen `Product`. I fönstret **meddelanden** visas **frågan lyckades: rader som påverkas: 1**.


#### <a name="run-an-update-query"></a>Kör en UPPDATERINGs fråga

Kör följande [Update](/sql/t-sql/queries/update-transact-sql/) T-SQL-uttryck för att ändra din nya produkt.

1. Ersätt den tidigare frågan med denna.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i tabellen `Product`. I fönstret **meddelanden** visas **frågan lyckades: rader som påverkas: 1**.

#### <a name="run-a-delete-query"></a>Kör en BORTTAGNINGs fråga

Kör följande T-SQL-uttryck för att [ta bort din](/sql/t-sql/statements/delete-transact-sql/) nya produkt.

1. Ersätt den föregående frågan med denna:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att ta bort den angivna raden i tabellen `Product`. I fönstret **meddelanden** visas **frågan lyckades: rader som påverkas: 1**.


## <a name="troubleshooting-and-considerations"></a>Felsökning och överväganden

Det finns några saker du behöver veta när du arbetar med frågeredigeraren.

### <a name="configure-local-network-settings"></a>Konfigurera inställningar för lokalt nätverk

Om du får ett av följande fel i Frågeredigeraren:
 - *Dina lokala nätverks inställningar kanske hindrar Frågeredigeraren från att utfärda frågor. Klicka här för instruktioner om hur du konfigurerar dina nätverks inställningar*
 - *Det gick inte att upprätta en anslutning till servern. Detta kan tyda på ett problem med den lokala brand Väggs konfigurationen eller inställningarna för nätverks-proxy*

Detta beror på att Frågeredigeraren använder port 443 och 1443 för att kommunicera. Du måste se till att du har aktiverat utgående HTTPS-trafik på dessa portar. Anvisningarna nedan beskriver hur du gör detta, beroende på vilket operativ system som används. Du kan behöva arbeta med företagets IT-avdelning för att bevilja godkännande för att öppna den här anslutningen i det lokala nätverket.

#### <a name="steps-for-windows"></a>Steg för Windows

1. Öppna **Windows Defender-brandväggen**
2. På den vänstra menyn väljer du **Avancerade inställningar**
3. I **Windows Defender-brandväggen med avancerad säkerhet** väljer du **utgående regler** på menyn på den vänstra sidan.
4. Välj **ny regel...** på den högra menyn

Följ dessa steg i **Guiden ny utgående regel** :

1. Välj **port** som typ av regel som du vill skapa. Välj **Nästa**
2. Välj **TCP**
3. Välj **vissa fjärrportar** och ange "443, 1443". Välj sedan **Nästa**
4. Välj "Tillåt anslutningen om den är säker"
5. Välj **Nästa** och välj sedan **Nästa** igen
5. Behåll "domän", "privat" och "offentlig" alla valda
6. Ge regeln ett namn, till exempel "åtkomst till Azure SQL Query Editor" och eventuellt en beskrivning. Välj sedan **Slutför**

#### <a name="steps-for-mac"></a>Steg för Mac
1. Öppna **Systeminställningar** (Apple-menyn > Systeminställningar).
2. Klicka på **säkerhet & sekretess**.
3. Klicka på **brand vägg**.
4. Om brand väggen är avstängd väljer du **Klicka på låset för att göra ändringarna** längst ned och väljer **Aktivera brand vägg**
4. Klicka på **brand Väggs alternativ**.
5. I fönstret **säkerhets & sekretess** väljer du det här alternativet: "Tillåt att signerad program vara får inkommande anslutningar automatiskt".

#### <a name="steps-for-linux"></a>Steg för Linux
Kör dessa kommandon för att uppdatera program varan iptables
  ```
  sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
  sudo iptables -A OUTPUT -p tcp --dport 1443 -j ACCEPT
  ```

### <a name="connection-considerations"></a>Anslutnings överväganden

* För offentliga anslutningar till Frågeredigeraren måste du [lägga till din utgående IP-adress till serverns tillåtna brand Väggs regler](firewall-create-server-level-portal-quickstart.md) för att få åtkomst till dina databaser och informations lager.

* Om du har en privat länk anslutning på servern och ansluter till Frågeredigeraren från en IP-adress i den privata Virtual Network, fungerar Frågeredigeraren utan att du behöver lägga till klientens IP-adress i brand Väggs reglerna för SQL Database-servern.

* De mest grundläggande RBAC-behörigheterna som krävs för att använda Frågeredigeraren är Läs behörighet till servern och databasen. Alla som har den här åtkomst nivån kan komma åt funktionen Frågeredigeraren. Om du vill begränsa åtkomsten till vissa användare måste du förhindra att de kan logga in i Frågeredigeraren med Azure Active Directory-eller SQL-autentiseringsuppgifter för autentisering. Om de inte kan tilldela sig själva som AAD-administratör för servern eller åtkomst/Lägg till ett SQL-administratörskonto, bör de inte kunna använda Frågeredigeraren.

* Frågeredigeraren stöder inte anslutning till databasen `master`.

* Frågeredigeraren kan inte ansluta till en replik databas med `ApplicationIntent=ReadOnly`

* Om du såg det här fel meddelandet "Det gick inte att verifiera huvudet X-CSRF-Signature", vidta följande åtgärder för att lösa problemet:

    * Kontrol lera att datorns klocka är inställd på rätt tid och tidszon. Du kan också försöka matcha datorns tidszon med Azure genom att söka efter tids zonen för platsen för din instans, t. ex. USA, östra, Stilla havs området och så vidare.
    * Om du använder ett proxy-nätverk måste du kontrol lera att begär ande huvudet "X-CSRF-signatur" inte ändras eller tas bort.

### <a name="other-considerations"></a>Ytterligare överväganden

* Om du trycker på **F5** uppdateras Frågeredigeraren och alla frågor som arbetar på förloras.

* Det finns en 5 minuters timeout för frågekörning.

* Frågeredigeraren stöder endast cylindriska projektioner för geografidatatyper.

* Det finns inget stöd för IntelliSense för databas tabeller och vyer, men Redigeraren stöder komplettera automatiskt för namn som redan har skrivits.

## <a name="next-steps"></a>Nästa steg

Mer information om Transact-SQL (T-SQL) som stöds i Azure SQL Database finns i [lösa skillnader i Transact-SQL vid migrering till SQL Database](transact-sql-tsql-differences-sql-server.md).
