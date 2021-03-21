---
title: Hantera Azure Cosmos DB resurser med Azure Storage Explorer
description: Lär dig hur du ansluter till Azure Cosmos DB och hanterar dess resurser med Azure Storage Explorer.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 5b09ce48226b3c31efce4966ec776c10931cc391
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348661"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Hantera Azure Cosmos DB resurser med Azure Storage Explorer
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Du kan använda Azure Storage Explorer för att ansluta till Azure Cosmos DB. Du kan ansluta till Azure Cosmos DB konton som finns i Azure och suveräna moln från Windows, macOS eller Linux.

Använd samma verktyg för att hantera olika Azure-entiteter på ett och samma ställe. Du kan hantera Azure Cosmos DB entiteter, manipulera data, uppdatera lagrade procedurer och utlösare tillsammans med andra Azure-entiteter som lagrings blobbar och köer. Azure Storage Explorer stöder Cosmos-konton som kon figurer ATS för SQL-, MongoDB-, Graph-och table API: er.

> [!NOTE]
> Azure Cosmos DB-integreringen med Storage Explorer har blivit inaktuell. Alla befintliga funktioner tas inte bort och det gäller under minst ett år, räknat från den här versionen. Du bör använda [Azure Portal](https://portal.azure.com/), [Azure Portal Desktop-appen](https://portal.azure.com/App/Download) eller den fristående [Azure Cosmos DB Explorer](data-explorer.md) i stället. Alternativen innehåller många nya funktioner som för närvarande inte stöds i Storage Explorer.

## <a name="prerequisites"></a>Förutsättningar

Ett Cosmos-konto med ett SQL-API eller ett Azure Cosmos DB-API för MongoDB. Om du inte har något konto kan du skapa ett i Azure Portal. Se [Azure Cosmos DB: bygga en SQL API-webbapp med .net och Azure Portal](create-sql-api-dotnet.md) för mer information.

## <a name="installation"></a>Installation

Om du vill installera de nyaste Azure Storage Explorer-bitarna, se [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Vi har stöd för Windows-, Linux-och macOS-versioner.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

1. När du har installerat **Azure Storage Explorer** väljer du **plugin-** ikonen i det vänstra fönstret.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Skärm bild som visar plugin-ikonen i det vänstra fönstret.":::

1. Välj **Lägg till ett Azure-konto** och välj sedan **Logga in**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Skärm bild av fönstret Anslut till Azure Storage som visar alternativ knappen Lägg till ett Azure-konto markerat och list menyn i Azure-miljön.":::

1. I dialog rutan **Azure-inloggning** väljer du logga in och anger sedan dina autentiseringsuppgifter **för** Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Skärm bild av inloggnings fönstret som visar var du anger dina autentiseringsuppgifter för din Azure-prenumeration.":::

1. Välj din prenumeration i listan och välj sedan **Använd**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Skärm bild av fönstret konto hantering som visar en lista över prenumerationer och knappen tillämpa.":::

    Explorer-fönstret uppdateras och visar kontona i den valda prenumerationen.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Skärm bild av fönstret Utforskaren, uppdaterat för att Visa kontona i den valda prenumerationen.":::

    Ditt **Cosmos DB-konto** är anslutet till din Azure-prenumeration.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Använd en anslutnings sträng för att ansluta till Azure Cosmos DB

Du kan använda en anslutnings sträng för att ansluta till en Azure Cosmos DB. Den här metoden stöder endast SQL-och table-API: er. Följ dessa steg för att ansluta till en anslutnings sträng:

1. Hitta **lokala och anslutna** i det vänstra trädet, högerklicka på **Cosmos DB konton** och välj sedan **Anslut till Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Skärm bild som visar den nedrullningsbara menyn när du högerklickar, med Anslut till Azure Cosmos D B markerat.":::

2. I fönstret **Anslut till Cosmos DB** :
   1. Välj API på den nedrullningsbara menyn.
   1. Klistra in anslutnings strängen i rutan **anslutnings sträng** . Information om hur du hämtar den primära anslutnings strängen finns i [Hämta anslutnings strängen](manage-with-powershell.md#list-keys).
   1. Ange en **konto etikett** och välj sedan **Nästa** för att kontrol lera sammanfattningen.
   1. Välj **Anslut** för att ansluta Azure Cosmos DB-kontot.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Skärm bild av fönstret Anslut till Cosmos D B, som visar den nedrullningsbara menyn för API, rutan anslutnings sträng och rutan konto etikett.":::

> [!NOTE]
> Om Azure Storage Explorer visar att den Azure Cosmos DB anslutnings strängen har ett ogiltigt format kontrollerar du att anslutnings strängen har ett semikolon ( `;` ) i slutet. Ett exempel på en giltig Azure Cosmos DB anslutnings sträng skulle vara: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Använd en lokal emulator för att ansluta till Azure Cosmos DB

Använd följande steg för att ansluta till en Azure Cosmos DB med en emulator. Den här metoden stöder endast SQL-konton.

1. Installera Cosmos DB emulator och öppna den. Information om hur du installerar emulatorn finns i [Cosmos DB emulator](./local-emulator.md).

1. Hitta **lokala och anslutna** i det vänstra trädet, högerklicka på **Cosmos DB konton** och välj sedan **Anslut till Cosmos DB emulator**.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Skärm bild som visar menyn som visas när du högerklickar, med Anslut till Azure Cosmos D B-emulator markerat.":::

1. I fönstret **Anslut till Cosmos DB** :
   1. Klistra in anslutnings strängen i rutan **anslutnings sträng** . Information om hur du hämtar den primära anslutnings strängen finns i [Hämta anslutnings strängen](manage-with-powershell.md#list-keys).
   1. Ange en **konto etikett** och välj sedan **Nästa** för att kontrol lera sammanfattningen.
   1. Välj **Anslut** för att ansluta Azure Cosmos DB-kontot.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Skärm bild av fönstret Anslut till Cosmos D B, som visar rutan anslutnings sträng och rutan konto etikett.":::

## <a name="azure-cosmos-db-resource-management"></a>Resurshantering för Azure Cosmos DB

Använd följande åtgärder för att hantera ett Azure Cosmos DB-konto:

* Öppna kontot i Azure Portal.
* Lägg till resursen i listan snabb åtkomst.
* Sök och uppdatera resurser.
* Skapa och ta bort databaser.
* Skapa och ta bort samlingar.
* Skapa, redigera, ta bort och filtrera dokument.
* Hantera lagrade procedurer, utlösare och användardefinierade funktioner.

### <a name="quick-access-tasks"></a>Uppgifter för snabbåtkomst

Du kan högerklicka på en prenumeration i fönstret Explorer för att utföra många snabb åtgärds uppgifter, till exempel:

* Högerklicka på ett Azure Cosmos DB konto eller en databas och välj sedan **Öppna i portalen** för att hantera resursen i webbläsaren på Azure Portal.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Skärm bild som visar menyn som visas när du högerklickar, med öppna i Portal markerat.":::

* Högerklicka på ett Azure Cosmos DB konto, en databas eller en samling och välj sedan **Lägg till i snabb åtkomst** för att lägga till den i snabb åtkomst menyn.

* Välj **Sök härifrån** för att aktivera nyckelords sökning under den valda sökvägen.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Skärm bild som visar sökrutan markerad.":::

### <a name="database-and-collection-management"></a>Databas- och samlingshantering

#### <a name="create-a-database"></a>Skapa en databas

1. Högerklicka på Azure Cosmos DB konto och välj sedan **skapa databas**.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Skärm bild som visar menyn som visas när du högerklickar, med skapa databas markerat.":::

1. Ange databas namnet och tryck sedan på **RETUR** för att slutföra.

#### <a name="delete-a-database"></a>Ta bort en databas

1. Högerklicka på databasen och välj sedan **ta bort databas**. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Skärm bild som visar menyn som visas när du högerklickar, där ta bort databas har marker ATS.":::

1. Välj **Ja** i popup-fönstret. Databasnoden tas bort och Azure Cosmos DB-konto uppdateras automatiskt.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Skärm bild av bekräftelse fönstret med knappen Ja markerat.":::

#### <a name="create-a-collection"></a>Skapa en samling

1. Högerklicka på databasen och välj sedan **skapa samling**.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Skärm bild som visar menyn som visas när du högerklickar med skapa samling markerat.":::

1. Ange den begärda informationen i fönstret Skapa samling, t. ex. **samlings-ID** och **lagrings kapacitet** och så vidare. Slutför genom att välja **OK**.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Skärm bild av fönstret Skapa samling som visar knapparna för samling I D och lagrings kapacitet.":::

1. Välj **obegränsad** så att du kan ange en partitionsnyckel och välj sedan **OK** för att slutföra.

   > [!NOTE]
   > Om en partitionsnyckel används när du skapar en samling kan du inte ändra värdet för partitionsnyckel i samlingen när du har skapat den.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Skärm bild av fönstret Skapa samling, som visar obegränsad har marker ATS för lagrings kapacitet och rutan partitionsnyckel har marker ATS.":::

#### <a name="delete-a-collection"></a>Ta bort en samling

- Högerklicka på samlingen, Välj **ta bort samling** och välj sedan **Ja** i popup-fönstret.

    Samlingsnoden tas bort och databasen uppdateras automatiskt.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Skärm bild som visar menyn som visas när du högerklickar, med ta bort samling markerat.":::

### <a name="document-management"></a>Dokumenthantering

#### <a name="create-and-modify-documents"></a>Skapa och ändra dokument

- Öppna **dokument** i den vänstra rutan, Välj **nytt dokument**, redigera innehållet i den högra rutan och välj sedan **Spara**.
- Du kan också uppdatera ett befintligt dokument och sedan välja **Spara**. Om du vill ignorera ändringarna väljer du **Ignorera**.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Skärm bild som visar dokument som marker ATS i det vänstra fönstret. I den högra rutan är nytt dokument, Spara och ta bort markerade.":::

#### <a name="delete-a-document"></a>Ta bort ett dokument

* Klicka på **ta bort** om du vill ta bort det valda dokumentet.

#### <a name="query-for-documents"></a>Fråga för dokument

* Redigera dokument filtret genom att ange en [SQL-fråga](./sql-query-getting-started.md)och välj sedan **Använd**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Skärm bild av den högra rutan med filter och tillämpa knappar, ID-nummer och fråga-rutan markerad.":::

### <a name="graph-management"></a>Graph-hantering

#### <a name="create-and-modify-a-vertex"></a>Skapa och ändra ett hörn

* Om du vill skapa ett nytt hörn öppnar du **Graph** från den vänstra rutan, väljer **nytt hörn**, redigerar innehållet och väljer sedan **OK**.
* Om du vill ändra en befintlig topp markerar du Penn ikonen i den högra rutan.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Skärm bild som visar graf valt i det vänstra fönstret och som visar nya hörn och Penn ikonen markerad i den högra rutan.":::

#### <a name="delete-a-graph"></a>Ta bort en graf

* Om du vill ta bort ett formhörn väljer du pappers korgs ikonen bredvid namnet på hörn platsen.

#### <a name="filter-for-graph"></a>Filter för graf

* Om du vill redigera graf-filtret anger du en [Gremlin-fråga](gremlin-support.md)och väljer sedan **Använd filter**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Skärm bild som visar graf som marker ATS i det vänstra fönstret och visar filtret Använd och där rutan fråga är markerad i den högra rutan.":::

### <a name="table-management"></a>Tabellhantering

#### <a name="create-and-modify-a-table"></a>Skapa och ändra en tabell

* Så här skapar du en ny tabell:
   1. Öppna **entiteter** i den vänstra rutan och välj sedan **Lägg till**.
   1. Redigera innehållet i dialog rutan **Lägg till entitet** .
   1. Välj knappen **Lägg till egenskap** för att lägga till en egenskap.
   1. Välj **Infoga**.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Skärm bild som visar entiteter markerade i det vänstra fönstret och visar Lägg till, redigera, Lägg till egenskap och Infoga markerade i den högra rutan.":::

* Om du vill ändra en tabell väljer du **Redigera**, ändrar innehållet och väljer sedan **Uppdatera**.

   

#### <a name="import-and-export-table"></a>Importera och exportera tabell

* Importera genom att välja knappen **Importera** och välj sedan en befintlig tabell.
* Om du vill exportera väljer du knappen **Exportera** och väljer sedan ett mål.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Skärm bild som visar knapparna importera och Exportera markerade i den högra rutan.":::

#### <a name="delete-entities"></a>Ta bort entiteter

* Välj entiteterna och välj sedan knappen **ta bort** .

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Skärm bild som visar knappen Ta bort markerad i den högra rutan och ett bekräftelse fönster med Ja markerat.":::

#### <a name="query-a-table"></a>Fråga en tabell

- Välj knappen **fråga** , ange ett villkor för frågan och välj sedan knappen **Kör fråga** . Om du vill stänga frågefönstret väljer du knappen **Stäng fråga** .

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Skärm bild av den högra rutan, som visar knappen Kör fråga och knappen Stäng fråga markerad.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Hantera lagrade procedurer, utlösare och UDF:er

* Så här skapar du en lagrad procedur:
  1. I det vänstra trädet högerklickar du på **lagrade procedurer** och väljer sedan **skapa lagrad procedur**.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Skärm bild av den vänstra rutan, som visar menyn som visas när du högerklickar, med skapa lagrad procedur markerad.":::
  
  1. Ange ett namn till vänster, ange skripten för lagrade procedurer i den högra rutan och välj sedan **skapa**.
  
* Om du vill redigera en befintlig lagrad procedur dubbelklickar du på proceduren, gör uppdateringen och väljer sedan **Uppdatera** för att spara. Du kan också välja **Ignorera** för att avbryta ändringen.

* Åtgärderna för **utlösare** och **UDF** liknar **lagrade procedurer**.

## <a name="troubleshooting"></a>Felsökning

Följande är lösningar på vanliga problem som uppstår när du använder Azure Cosmos DB i Storage Explorer.

### <a name="sign-in-issues"></a>Inloggningsproblem

Starta först om ditt program för att se om det löser problemet. Fortsätt fel sökningen om problemet kvarstår.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Självsignerat certifikat i certifikatkedjan

Det finns några orsaker till att du kan se det här felet, de två vanligaste är:

* Du är bakom en *transparent proxy*. Någon, till exempel din IT-avdelning, fångar upp HTTPS-trafik, dekrypterar den och krypterar den sedan med hjälp av ett självsignerat certifikat.

* Du kör program vara, till exempel antivirus program. Programmet matar in ett självsignerat TLS/SSL-certifikat i de HTTPS-meddelanden som du får.

När Storage Explorer hittar ett självsignerat certifikat vet det inte om HTTPS-meddelandet det tar emot har manipulerats. Om du har en kopia av det självsignerade certifikatet kan du tala om för Storage Explorer att lita på det. Om du är osäker på vem som har matat in certifikatet kan du följa dessa steg och försöka ta reda på följande:

1. Installera OpenSSL:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): Alla ljusa versioner är OK.
     - macOS och Linux: bör ingå i ditt operativ system.

1. Kör OpenSSL:
    * Windows: gå till installations katalogen, klicka på **/bin/** och dubbelklicka sedan på **openssl.exe**.
    * Mac och Linux: köra **openssl** från en Terminal.
1. Kör `s_client -showcerts -connect microsoft.com:443` .
1. Leta efter självsignerade certifikat. Om du är osäker, som är självsignerad, kan du leta efter var som ämnet ("s:") och utfärdaren ("i:") är samma.
1. Om du hittar några självsignerade certifikat kan du kopiera och klistra in allt från och inklusive **-----BEGIN certificate-----** för att **-----slut certifikat-----** till en ny. CER-fil för var och en.
1. Öppna Storage Explorer och gå sedan till **Redigera**  >  **SSL-certifikat**  >  **Importera certifikat**. Använd fil väljaren för att hitta, välja och öppna. CER-filer som du har skapat.

Om du inte hittar några självsignerade certifikat kan du skicka feedback för mer hjälp.

#### <a name="unable-to-retrieve-subscriptions"></a>Det gick inte att hämta prenumerationer

Om du inte kan hämta dina prenumerationer när du har loggat in kan du prova följande förslag:

* Kontrol lera att ditt konto har åtkomst till prenumerationerna. Det gör du genom att logga in på [Azure Portal](https://portal.azure.com/).
* Kontrol lera att du har loggat in i rätt miljö:
  * [Azure](https://portal.azure.com/)
  * [Azure Kina](https://portal.azure.cn/)
  * [Azure Tyskland](https://portal.microsoftazure.de/)
  * [Azure US Government](https://portal.azure.us/)
  * Anpassad miljö/Azure Stack
* Om du är bakom en proxyserver ser du till att Storage Explorer proxy är korrekt konfigurerad.
* Ta bort kontot och Lägg sedan till det igen.
* Ta bort följande filer från din Hem Katalog (t. ex.: C:\Users\ContosoUser) och Lägg sedan till kontot igen:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Öppna Developer-konsolen genom att trycka på F12-tangenten. Titta på konsolen för eventuella fel meddelanden när du loggar in.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Skärm bild av konsolen för utvecklarverktyg som visar konsolen markerad.":::

#### <a name="unable-to-see-the-authentication-page"></a>Det gick inte att visa autentiseringssidan

Om du inte kan se autentiseringssidan:

* Beroende på anslutnings hastigheten kan det ta en stund innan inloggnings sidan har lästs in. Vänta minst en minut innan du stänger dialog rutan autentisering.
* Om du är bakom en proxyserver ser du till att Storage Explorer proxy är korrekt konfigurerad.
* I konsolen för utvecklarverktyg (utvecklarverktyg) tittar du på Svaren för att se om du kan hitta några LED trådar för varför autentiseringen inte fungerar.

#### <a name="cant-remove-an-account"></a>Det går inte att ta bort ett konto

Om du inte kan ta bort ett konto, eller om länken autentisera inte gör något:

* Ta bort följande filer från din arbets katalog och Lägg sedan till kontot igen:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Om du vill ta bort SAS-kopplade lagringsresurser, ta bort:
  * %AppData%/StorageExplorer-mapp för Windows
  * /Users/<your_name>/Library/Application SUpport/StorageExplorer för macOS
  * ~/.config/StorageExplorer för Linux
  
  > [!NOTE]
  > Om du tar bort de här filerna **måste du ange alla autentiseringsuppgifter på samma** sätt.

### <a name="httphttps-proxy-issue"></a>Problem med HTTP/HTTPS-proxy

Du kan inte Visa Azure Cosmos DB noder i det vänstra trädet när du konfigurerar en HTTP/HTTPS-proxy i ASE. Du kan använda Azure Cosmos DB data Utforskaren i Azure Portal som ett arbete runt.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problem med ”Utvecklingsnod” under ”Lokala och kopplade” noder

Det finns inget svar när du har valt noden **utveckling** under den **lokala och kopplade** noden i det vänstra trädet. Det här beteendet är förväntat.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Skärm bild som visar den valda utvecklings noden.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Bifoga ett Azure Cosmos DB-konto i fel i **lokal och ansluten** nod

Om du ser följande fel när du har kopplat ett Azure Cosmos DB-konto i den **lokala och anslutna** noden, kontrollerar du att du använder rätt anslutnings sträng.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Skärm bild av popup-fönstret det går inte att hämta underordnade resurser fel som indikerar getaddrinfo-ENOTFOUND.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Nodfel vid expandera Microsoft Azure Cosmos DB

Följande fel kan visas när du försöker expandera noder i det vänstra trädet.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Skärm bild av popup-fönstret det går inte att hämta underordnade resurser fel, vilket indikerar att det inte går att ansluta till det här Cosmos D B-kontot.":::

Prova följande förslag:

* Kontrol lera om Azure Cosmos DBs kontot är i etablerings förlopp. Försök igen när kontot har skapats.
* Om kontot finns under **snabb åtkomst** eller **lokala och anslutna** noder kontrollerar du om kontot har tagits bort. I så fall måste du ta bort noden manuellt.

## <a name="next-steps"></a>Nästa steg

* Titta på den här videon för att se hur du använder Azure Cosmos DB i Azure Storage Explorer: [använd Azure Cosmos db i Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Läs mer om Storage Explorer och om att ansluta fler tjänster i [Kom igång med Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).
