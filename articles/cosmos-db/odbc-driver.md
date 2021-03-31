---
title: Ansluta till Azure Cosmos DB med hjälp av BI Analytics-verktyg
description: Lär dig hur du använder Azure Cosmos DB ODBC-drivrutinen för att skapa tabeller och vyer så att normaliserade data kan visas i BI-och data analys program.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e7d6a67f5322c5bb640430f66ccb0917f6faada1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003505"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Ansluta till Azure Cosmos DB med hjälp av BI Analytics-verktyg med ODBC-drivrutinen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Med Azure Cosmos DB ODBC-drivrutinen kan du ansluta till Azure Cosmos DB med hjälp av BI Analytics-verktyg som SQL Server Integration Services, Power BI Desktop och Tableau så att du kan analysera och skapa visualiseringar av dina Azure Cosmos DB data i dessa lösningar.

Azure Cosmos DB ODBC-drivrutinen är kompatibel med ODBC 3.8 och stöder ANSI SQL-92-syntax. Drivrutinen har omfattande funktioner som hjälper dig renormalisera data i Azure Cosmos DB. Med drivrutinen kan du representera data i Azure Cosmos DB som tabeller och vyer. Du kan utföra SQL-åtgärder mot tabeller och vyer, t.ex. gruppera efter frågor, infogningar, uppdateringar och borttagningar.

> [!NOTE]
> Att ansluta till Azure Cosmos DB med ODBC-drivrutinen stöds för närvarande endast för Azure Cosmos DB SQL API-konton.

## <a name="why-do-i-need-to-normalize-my-data"></a>Varför måste jag normalisera mina data?
Azure Cosmos DB är en schema lös databas som möjliggör snabb program utveckling och möjlighet att iterera data modeller utan att begränsas till ett strikt schema. En enda Azure Cosmos-databas kan innehålla JSON-dokument med olika strukturer. Detta är bra för snabb program utveckling, men när du vill analysera och skapa rapporter över dina data med hjälp av data analys och BI-verktyg måste data ofta förenklas och följa ett särskilt schema.

Det är här som ODBC-drivrutinen kommer i. Med hjälp av ODBC-drivrutinen kan du nu normalisera data i Azure Cosmos DB till tabeller och vyer som passar dina data analys-och rapporterings behov. De omnormaliserade schemana har ingen inverkan på underliggande data och gör inte att utvecklare följer dem. De gör det möjligt för dig att utnyttja ODBC-kompatibla verktyg för att komma åt data. Nu är din Azure Cosmos-databas inte bara en favorit för ditt utvecklings team, men dina data analytiker kommer att älska den också.

Nu ska vi komma igång med ODBC-drivrutinen.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Steg 1: installera Azure Cosmos DB ODBC-drivrutinen

1. Ladda ned driv rutinerna för din miljö:

    | Silverlight | Operativsystem som stöds| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) för 64-bitars Windows| 64-bitars versioner av Windows 8,1 eller senare, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 och Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) för 32-bitars på 64-bitars Windows| 64-bitars versioner av Windows 8,1 eller senare, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 och Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) för 32-bitars Windows|32-bitars versioner av Windows 8,1 eller senare, Windows 8, Windows 7, Windows XP och Windows Vista.|

    Kör MSI-filen lokalt, som startar **installations guiden för Microsoft Azure Cosmos DB ODBC-drivrutin**. 

1. Slutför installations guiden med hjälp av standard ininformation för att installera ODBC-drivrutinen.

1. Öppna **ODBC-datakällans administratörs** app på din dator. Du kan göra detta genom att skriva **ODBC-datakällor** i sökrutan i Windows. 
    Du kan kontrol lera att driv rutinen har installerats genom att klicka på fliken **driv rutiner** och se till att **Microsoft Azure Cosmos DB ODBC-drivrutinen** visas.

    :::image type="content" source="./media/odbc-driver/odbc-driver.png" alt-text="Azure Cosmos DB ODBC-Administrera data Källa":::

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Steg 2: Anslut till din Azure Cosmos-databas

1. När [du har installerat Azure Cosmos DB ODBC-drivrutinen](#install)klickar du på **Lägg till** i fönstret **administratör för ODBC-datakälla** . Du kan skapa en användar-eller system-DSN. I det här exemplet skapar du en användar-DSN.

1. Välj **Microsoft Azure Cosmos DB ODBC-drivrutin** i fönstret **Skapa ny data källa** och klicka sedan på **Slutför**.

1. I **installations fönstret Azure Cosmos DB ODBC-drivrutin för SDN** , Fyll i följande information: 

    :::image type="content" source="./media/odbc-driver/odbc-driver-dsn-setup.png" alt-text="Installations fönster för Azure Cosmos DB ODBC-drivrutin":::
    - **Data källans namn**: ditt eget namn för ODBC-datakällan. Det här namnet är unikt för ditt Azure Cosmos DB-konto, så ge det ett namn om du har flera konton.
    - **Beskrivning**: en kort beskrivning av data källan.
    - **Värd**: URI för ditt Azure Cosmos DB-konto. Du kan hämta detta från sidan Azure Cosmos DB nycklar i Azure Portal, som du ser i följande skärm bild. 
    - **Åtkomst nyckel**: den primära eller sekundära, skrivskyddade eller skrivskyddade nyckeln på sidan Azure Cosmos DB nycklar i Azure Portal som visas på följande skärm bild. Vi rekommenderar att du använder den skrivskyddade nyckeln om DSN används för skrivskyddad data bearbetning och rapportering.
    :::image type="content" source="./media/odbc-driver/odbc-cosmos-account-keys.png" alt-text="Sidan Azure Cosmos DB nycklar":::
    - **Kryptera åtkomst nyckel för**: Välj det bästa valet baserat på användare av den här datorn. 
    
1. Klicka på knappen **testa** för att kontrol lera att du kan ansluta till ditt Azure Cosmos DB-konto. 

1.  Klicka på **Avancerade alternativ** och ange följande värden:
    *  **REST API version**: Välj [REST API-versionen](/rest/api/cosmos-db/) för dina åtgärder. Standardvärdet 2015-12-16. Om du har behållare med [stora partitionsnyckel](large-partition-keys.md) och behöver REST API version 2018-12-31:
        - Skriv **2018-12-31** för REST API version
        - I **Start** -menyn skriver du "regedit" för att hitta och öppna programmet **Registry Editor** .
        - I Registereditorn navigerar du till sökvägen: **Computer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI**
        - Skapa en ny under nyckel med samma namn som din DSN, t. ex. "contoso-kontots ODBC-DSN".
        - Gå till under nyckeln "Contoso-konto ODBC-DSN".
        - Högerklicka för att lägga till ett nytt **sträng** värde:
            - Värde namn: **IgnoreSessionToken**
            - Värde data: **1** 
             :::image type="content" source="./media/odbc-driver/cosmos-odbc-edit-registry.png" alt-text="register redigerings inställningar":::
    - **Fråga konsekvens**: Välj [konsekvens nivå](consistency-levels.md) för dina åtgärder. Standardvärdet är session.
    - **Antal återförsök**: Ange hur många försök en åtgärd som ska utföras igen om den första begäran inte slutförs på grund av begränsning av tjänst hastighet.
    - **Schema fil**: du har ett antal alternativ här.
        - Som standard när du lämnar den här posten som den är (tom) genomsöker driv rutinen den första sidan med data för alla behållare för att fastställa schemat för varje behållare. Detta kallas för behållar mappning. Utan en schema fil måste driv rutinen utföra genomsökningen för varje driv rutins session och kunna resultera i en högre start tid för ett program med hjälp av DSN. Vi rekommenderar att du alltid associerar en schema fil för en DSN.
        - Om du redan har en schema fil (eventuellt en som du har skapat med hjälp av schema redigeraren) kan du klicka på **Bläddra**, navigera till filen, klicka på **Spara** och sedan på **OK**.
        - Om du vill skapa ett nytt schema klickar du på **OK** och sedan på **schema redigerare** i huvud fönstret. Fortsätt sedan till information om schema redigeraren. När du har skapat den nya schema filen måste du komma ihåg att gå tillbaka till fönstret **Avancerade alternativ** för att inkludera den nya schema filen.

1. När du har slutfört och stängt **installations fönstret Azure Cosmos DB ODBC-drivrutin** , läggs den nya användar-DSN till på fliken Användar-DSN.

    :::image type="content" source="./media/odbc-driver/odbc-driver-user-dsn.png" alt-text="Ny Azure Cosmos DB ODBC-datakälla på fliken Användar-DSN":::

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Steg 3: skapa en schema definition med hjälp av container mappnings metoden

Det finns två typer av samplings metoder som du kan använda: **container mappning** eller **tabell avgränsare**. En samplings session kan använda båda metoderna för att sampla, men varje behållare kan bara använda en speciell samplings metod. Stegen nedan skapar ett schema för data i en eller flera behållare med hjälp av behållar mappnings metoden. Den här samplings metoden hämtar data på sidan i en behållare för att fastställa data strukturen. Den införlivar en behållare i en tabell på ODBC-sidan. Den här samplings metoden är effektiv och snabbt när data i en behållare är homogena. Om en behållare innehåller heterogen typ av data rekommenderar vi att du använder [mappnings metoden för tabell-avgränsare](#table-mapping) eftersom den ger en mer robust samplings metod för att fastställa data strukturerna i behållaren. 

1. När du har slutfört steg 1-4 i [Anslut till din Azure Cosmos-databas](#connect)klickar du på **schema redigeraren** i **installations fönstret Azure Cosmos db odbc-drivrutinens DSN** .

    :::image type="content" source="./media/odbc-driver/odbc-driver-schema-editor.png" alt-text="Knappen schema redigerare i installations fönstret Azure Cosmos DB ODBC-drivrutinens DSN":::
1. I fönstret **schema redigeraren** klickar du på **Skapa nytt**.
    I fönstret **skapa schema** visas alla behållare i Azure Cosmos DB-kontot. 

1. Välj en eller flera behållare som ska samplas och klicka sedan på **exempel**. 

1. På fliken **design visning** visas databasen, schemat och tabellen. I Tabellvy visar sökningen den uppsättning egenskaper som är associerade med kolumn namnen (SQL-namn, källnamn osv.).
    För varje kolumn kan du ändra kolumnens SQL-namn, SQL-typ, SQL-längd (om tillämpligt), skala (om tillämpligt), precision (om tillämpligt) och kan ha värdet null.
    - Du kan ange **kolumnen Dölj** till **Sant** om du vill undanta den kolumnen från frågeresultaten. Kolumnerna som marker ATS Dölj kolumn = True returneras inte för markering och projektion, även om de fortfarande är en del av schemat. Du kan till exempel dölja alla Azure Cosmos DB system egenskaper som krävs från och med "_".
    - Kolumnen **ID** är det enda fält som inte kan döljas eftersom det används som primär nyckel i det normaliserade schemat. 

1. När du är klar med att definiera schemat klickar du på **Arkiv**  |  **Spara**, navigerar till katalogen för att spara schemat och klickar sedan på **Spara**.

1. Om du vill använda det här schemat med en DSN öppnar du **fönstret Azure Cosmos DB ODBC-drivrutin för driv rutins-DSN** (via ODBC-administratören för data källa), klickar på **Avancerade alternativ** och går sedan till det sparade schemat i rutan **schema fil** . Om du sparar en schema fil i en befintlig DSN ändras DSN-anslutningen till omfång till de data och den struktur som definieras av schemat.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Steg 4: skapa en schema definition med mappnings metoden tabell-avgränsare

Det finns två typer av samplings metoder som du kan använda: **container mappning** eller **tabell avgränsare**. En samplings session kan använda båda metoderna för att sampla, men varje behållare kan bara använda en speciell samplings metod. 

Följande steg skapar ett schema för data i en eller flera behållare med hjälp av mappnings metoden **tabell-avgränsare** . Vi rekommenderar att du använder den här samplings metoden när dina behållare innehåller heterogen typ av data. Du kan använda den här metoden för att omfånget av samplingen till en uppsättning attribut och motsvarande värden. Om ett dokument till exempel innehåller egenskapen "typ", kan du begränsa samplingen till värdena för den här egenskapen. Slut resultatet av samplingen skulle vara en uppsättning tabeller för varje värde för typ som du har angett. Skriv t. ex. om du vill att bilen ska producera en Car-tabell medan Type = plan skapar en plan tabell.

1. När du har slutfört steg 1-4 i [Anslut till din Azure Cosmos-databas](#connect)klickar du på **schema redigeraren** i installations fönstret Azure Cosmos db odbc-drivrutinens DSN.

1. I fönstret **schema redigeraren** klickar du på **Skapa nytt**.
    I fönstret **skapa schema** visas alla behållare i Azure Cosmos DB-kontot. 

1. Välj en behållare på fliken **exempel vy** i kolumnen **mappnings definition** för behållaren, klicka på **Redigera**. Välj sedan **tabell avgränsnings** metod i fönstret **mappnings definition** . Gör något av följande:

    a. I rutan **attribut** skriver du namnet på en avgränsnings egenskap. Det här är en egenskap i ditt dokument som du vill använda för att omfånget till, t. ex. City och trycka på RETUR. 

    b. Om du bara vill omfånget av samplingen till vissa värden för attributet du angav ovan, väljer du attributet i rutan markering, anger ett värde i rutan **värde** (t. ex. Seattle) och trycker på RETUR. Du kan fortsätta att lägga till flera värden för attribut. Se bara till att rätt attribut är markerat när du anger värden.

    Om du till exempel inkluderar ett **attribut** värde för stad och du vill begränsa tabellen till att bara innehålla rader med värdet stad för New York och Dubai, anger du ort i rutan attribut och New York och sedan Dubai i rutan **värden** .

1. Klicka på **OK**. 

1. När du har slutfört mappnings definitionerna för de behållare som du vill sampla, klickar du på **exempel** i fönstret **schema redigeraren** .
     För varje kolumn kan du ändra kolumnens SQL-namn, SQL-typ, SQL-längd (om tillämpligt), skala (om tillämpligt), precision (om tillämpligt) och kan ha värdet null.
    - Du kan ange **kolumnen Dölj** till **Sant** om du vill undanta den kolumnen från frågeresultaten. Kolumnerna som marker ATS Dölj kolumn = True returneras inte för markering och projektion, även om de fortfarande är en del av schemat. Du kan till exempel dölja alla Azure Cosmos DB system egenskaper som krävs från och med `_` .
    - Kolumnen **ID** är det enda fält som inte kan döljas eftersom det används som primär nyckel i det normaliserade schemat. 

1. När du är klar med att definiera schemat klickar du på **Arkiv**  |  **Spara**, navigerar till katalogen för att spara schemat och klickar sedan på **Spara**.

1. Klicka på **Avancerade alternativ** i **installations fönstret Azure Cosmos db odbc-drivrutinens DSN** . Gå sedan till den sparade schema filen i rutan **schema fil** och klicka på **OK**. Klicka på **OK** igen för att spara DSN. Detta sparar det schema som du skapade i DSN. 

## <a name="optional-set-up-linked-server-connection"></a>Valfritt Konfigurera anslutning till länkad server

Du kan fråga Azure Cosmos DB från SQL Server Management Studio (SSMS) genom att konfigurera en länkad server anslutning.

1. Skapa en system data källa enligt beskrivningen i [steg 2](#connect), med namnet till exempel `SDS Name` .

1. [Installera SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och Anslut till servern. 

1. I SSMS Frågeredigeraren skapar du ett länkat Server objekt `DEMOCOSMOS` för data källan med följande kommandon. Ersätt `DEMOCOSMOS` med namnet på den länkade servern och `SDS Name` med namnet på system data källan.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Om du vill se det nya länkade Server namnet uppdaterar du listan länkade servrar.

:::image type="content" source="./media/odbc-driver/odbc-driver-linked-server-ssms.png" alt-text="Länkad server i SSMS":::

### <a name="query-linked-database"></a>Fråga länkad databas

Om du vill fråga den länkade databasen anger du en SSMS-fråga. I det här exemplet väljer frågan från tabellen i behållaren med namnet `customers` :

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Kör frågan. Resultatet bör se ut ungefär så här:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Den länkade Cosmos DB servern stöder inte namngivning med fyra delar. Ett fel returneras på liknande sätt som följande meddelande:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>Valfritt Skapa vyer
Du kan definiera och skapa vyer som en del av samplings processen. Dessa vyer motsvarar SQL-vyer. De är skrivskyddade och är begränsade till de val och projektioner av den Azure Cosmos DB SQL-frågan som definierats. 

Om du vill skapa en vy för dina data går du till kolumnen **Visa definitioner** i fönstret **schema redigerare** och klickar på **Lägg till** på raden i behållaren som ska samplas. 

:::image type="content" source="./media/odbc-driver/odbc-driver-create-view.png" alt-text="Skapa en vy av data":::


I fönstret **Visa definitioner** gör du följande:

1. Klicka på **nytt**, ange ett namn för vyn, till exempel EmployeesfromSeattleView, och klicka sedan på **OK**.

1. I fönstret **Redigera vy** anger du en Azure Cosmos DB fråga. Detta måste vara en [Azure Cosmos DB SQL-fråga](./sql-query-getting-started.md), till exempel `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"` och klicka sedan på **OK**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-create-view-2.png" alt-text="Lägg till fråga när du skapar en vy":::


Du kan skapa många vyer som du vill. När du är klar med att definiera vyerna kan du sedan sampla data. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Steg 5: Visa dina data i BI-verktyg som Power BI Desktop

Du kan använda din nya DSN för att ansluta till Azure Cosmos DB med alla ODBC-kompatibla verktyg – det här steget visar bara hur du ansluter till Power BI Desktop och skapar en Power BI visualisering.

1. Öppna Power BI Desktop.

1. Klicka på **Hämta data**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data.png" alt-text="Hämta data i Power BI Desktop":::

1. I fönstret **Hämta data** klickar du på **annan**  |  **ODBC**  |  **Connect**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-2.png" alt-text="Välj ODBC-datakälla i Power BI hämta data":::

1. I fönstret **från ODBC** väljer du namnet på den data källa som du skapade och klickar sedan på **OK**. Du kan lämna de **Avancerade alternativ** posterna tomma.

   :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-3.png" alt-text="Välj data källans namn (DSN) i Power BI hämta data":::

1. I fönstret **åtkomst till en data källa med hjälp av en ODBC-drivrutin** väljer du **standard eller anpassad** och klickar sedan på **Anslut**. Du behöver inte inkludera **egenskaperna för anslutnings strängen för autentiseringsuppgifter**.

1. I fönstret **navigatör** i det vänstra fönstret expanderar du databasen, schemat och väljer sedan tabellen. Resultat fönstret innehåller data med det schema som du skapade.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-4.png" alt-text="Välj tabell i Power BI hämta data":::

1. Om du vill visualisera data i Power BI Skriv bordet markerar du kryss rutan framför tabell namnet och klickar sedan på **Läs in**.

1. I Power BI Desktop längst till vänster väljer du fliken data :::image type="icon" source="./media/odbc-driver/odbc-driver-data-tab.png"::: för att bekräfta att dina data har importer ATS. 

1. Nu kan du skapa visuella objekt med Power BI genom att klicka på fliken rapport :::image type="icon" source="./media/odbc-driver/odbc-driver-report-tab.png"::: , klicka på **nytt visuellt** objekt och sedan anpassa panelen. Mer information om hur du skapar visualiseringar i Power BI Desktop finns i [visualiserings typer i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/). 

## <a name="troubleshooting"></a>Felsökning

Om du får följande fel ser du till att **värd** -och **åtkomst nyckel** värden som du kopierade Azure Portal i [steg 2](#connect) är korrekta och försöker sedan igen. Använd kopierings knapparna till höger om **värden och** **åtkomst nyckel** värden i Azure Portal för att kopiera värdena som är felfria.

```output
[HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}
```


## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Cosmos DB i [Välkommen till Azure Cosmos DB](introduction.md).