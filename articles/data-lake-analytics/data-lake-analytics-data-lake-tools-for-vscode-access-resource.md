---
title: Få åtkomst till resurser med Data Lake-verktyg
description: Lär dig hur du använder Azure Data Lake verktyg för att komma åt Azure Data Lake Analytics-resurser.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754757"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Få åtkomst till resurser med Azure Data Lake-verktyg

Du kan komma åt Azure Data Lake Analytics-resurser med Azure Data Tools-kommandon eller-åtgärder i VS Code enkelt.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrera med Azure Data Lake Analytics via ett kommando

Du kan komma åt Azure Data Lake Analytics resurser för att lista konton, komma åt metadata och Visa analys jobb.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Visa en lista över Azure Data Lake Analytics konton under din Azure-prenumeration

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **ADL: lista konton**. Kontona visas i fönstret **utdata** .

### <a name="to-access-azure-data-lake-analytics-metadata"></a>För att komma åt Azure Data Lake Analytics metadata

1. Välj Ctrl + Shift + P och ange sedan **ADL: list tabeller**.
2. Välj ett av Data Lake Analyticss kontona.
3. Välj en av de Data Lake Analytics databaserna.
4. Välj ett schema. Du kan se listan över tabeller.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Visa Azure Data Lake Analytics jobb

1. Öppna kommando paletten (Ctrl + Shift + P) och välj **ADL: Visa jobb**.
2. Välj ett Data Lake Analytics eller ett lokalt konto.
3. Vänta tills jobb listan visas för kontot.
4. Välj ett jobb från jobb listan. Data Lake verktyg öppnar vyn jobb i den högra rutan och visar viss information i VS-kodens utdata.

    ![Jobb lista](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrera med Azure Data Lake Store via ett kommando

Du kan använda Azure Data Lake Store-relaterade kommandon för att:

- [Bläddra bland Azure Data Lake Store resurser](#list-the-storage-path)
- [Förhandsgranska Azure Data Lake Store-filen](#preview-the-storage-file)
- Överför filen direkt till Azure Data Lake Store i VS-kod
- Hämta filen direkt från Azure Data Lake Store i VS-kod

### <a name="list-the-storage-path"></a>Lista lagrings Sök vägen

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Visa lagrings Sök vägen via kommando-paletten

1. Högerklicka på skript redigeraren och välj **ADL: list Sök väg**.
2. Välj mappen i listan eller Välj **Ange en sökväg** eller **Bläddra till rot Sök vägen**. (Vi använder **Ange en sökväg** som exempel.)
3. Välj ditt Data Lake Analytics-konto.
4. Bläddra till eller ange sökvägen till lagringsmappen (till exempel/output/).  

På paletten kommando listas Sök vägs informationen baserat på dina poster.

![Resultat för lagrings Sök väg](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Ett bekvämare sätt att ange den relativa sökvägen är via snabb menyn.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Visa lagrings Sök vägen via snabb menyn

Högerklicka på Sök vägs strängen och välj **list Sök väg**.

!["List Sök väg" på snabb menyn](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Förhandsgranska lagrings filen

1. Högerklicka på skript redigeraren och välj **ADL: förhands gransknings fil**.
2. Välj ditt Data Lake Analytics-konto.
3. Ange en Azure Storage fil Sök väg (till exempel/output/SearchLog.txt).

Filen öppnas i VS Code.

![Steg och resultat för för hands Grans kar lagrings filen](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Ett annat sätt att förhandsgranska filen är via snabb menyn på filens fullständiga sökväg eller filens relativa sökväg i skript redigeraren.

### <a name="upload-a-file-or-folder"></a>Ladda upp en fil eller mapp

1. Högerklicka på skript redigeraren och välj **Ladda upp fil** eller **Ladda upp mapp**.
2. Välj en fil eller flera filer om du har valt **överför fil**, eller Välj hela mappen om du valde **Ladda upp mapp**. Välj sedan **Ladda upp**.
3. Välj lagringsmappen i listan eller Välj **Ange en sökväg** eller **Bläddra till rot Sök vägen**. (Vi använder **Ange en sökväg** som exempel.)
4. Välj ditt Data Lake Analytics-konto.
5. Bläddra till eller ange sökvägen till lagringsmappen (till exempel/output/).
6. Välj **Välj aktuell mapp** för att ange ditt uppladdnings mål.

![Steg och resultat för att ladda upp en fil eller mapp](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Ett annat sätt att ladda upp filer till lagringen är via snabb menyn på filens fullständiga sökväg eller filens relativa sökväg i skript redigeraren.

Du kan [övervaka överförings statusen](#check-storage-tasks-status).

### <a name="download-a-file"></a>Ladda ned en fil

Du kan hämta en fil med hjälp av kommandot **ADL: Download File** eller **ADL: Download File (avancerat)**.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Hämta en fil via kommandot ADL: Download File (avancerat)

1. Högerklicka på skript redigeraren och välj sedan **Hämta fil (avancerat)**.
2. VS Code visar en JSON-fil. Du kan ange fil Sök vägar och hämta flera filer samtidigt. Instruktioner visas i fönstret **utdata** . Spara (Ctrl + S) JSON-filen om du vill fortsätta att ladda ned filen eller filerna.

    ![JSON-fil med sökvägar för fil hämtning](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

I fönstret **utdata** visas fil hämtnings statusen.

![Fönstret utdata med hämtnings status](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

Du kan [övervaka nedladdnings statusen](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Hämta en fil via kommandot ADL: Download File

1. Högerklicka på skript redigeraren, Välj **Hämta fil** och välj sedan målmappen i dialog rutan **Välj mapp** .

1. Välj mappen i listan eller Välj **Ange en sökväg** eller **Bläddra till rot Sök vägen**. (Vi använder **Ange en sökväg** som exempel.)

1. Välj ditt Data Lake Analytics-konto.

1. Bläddra till eller ange sökvägen till lagringsmappen (till exempel/output/) och välj sedan en fil som ska laddas ned.

![Steg och resultat för att ladda ned en fil](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Ett annat sätt att ladda ned Storage-filer är via snabb menyn på filens fullständiga sökväg eller filens relativa sökväg i skript redigeraren.

Du kan [övervaka nedladdnings statusen](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Kontrol lera status för lagrings aktiviteter

Status för uppladdning och hämtning visas i statusfältet. Välj statusfältet och sedan visas statusen på fliken **utdata** .

![Statusfält och information om utdata](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrera med Azure Data Lake Analytics från Utforskaren

När du har loggat in visas alla prenumerationer för ditt Azure-konto i den vänstra rutan under **Azure-DATALAKE**.

![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics navigering för metadata

Expandera din Azure-prenumeration. Under noden **U-SQL-databaser** kan du bläddra igenom din U-SQL-databas och Visa mappar som **scheman**, **autentiseringsuppgifter**, **sammansättningar**, **tabeller** och **index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics entitets hantering av metadata

Expandera **U-SQL-databaser**. Du kan skapa en databas, schema, tabell, tabell typ, index eller statistik genom att högerklicka på motsvarande nod och sedan välja **skript som ska skapas** på snabb menyn. På sidan öppna skript redigerar du skriptet efter dina behov. Skicka sedan jobbet genom att högerklicka på det och välja **ADL: skicka jobb**.

När du har skapat objektet högerklickar du på noden och väljer sedan **Uppdatera** för att visa objektet. Du kan också ta bort objektet genom att högerklicka på det och sedan välja **ta bort**.

![Kommandot "skript att skapa" på snabb menyn i Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skript sida för det nya objektet](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics sammansättnings registrering

Du kan registrera en sammansättning i motsvarande databas genom att högerklicka på noden **sammansättningar** och sedan välja **Registrera sammansättning**.

![Kommandot "Registrera sammansättning" på snabb menyn för noden sammansättningar](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrera med Azure Data Lake Store från Utforskaren

Bläddra till **data Lake Store**:

- Du kan högerklicka på noden mapp och sedan använda kommandona **Uppdatera**, **ta bort**, **Ladda upp**, **överför,** **Kopiera relativ sökväg** och **Kopiera fullständig sökväg** på snabb menyn.

   ![Snabb Meny kommandon för en mapp-nod i Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Du kan högerklicka på filnoden och sedan använda kommandona för för **hands version**, **Hämta**, **ta bort**, **skapa extraherings skript** (endast tillgängligt för CSV-, TSV-och txt-filer), **Kopiera relativ sökväg** och **Kopiera fullständiga sökvägar** på snabb menyn.

   ![Snabb Meny kommandon för en filnod i Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrera med Azure Blob Storage från Utforskaren

Bläddra till Blob Storage:

- Du kan högerklicka på noden BLOB container och sedan använda kommandona **Uppdatera**, **ta bort BLOB container** och **Ladda upp BLOB** på snabb menyn.

   ![Snabb Meny kommandon för en BLOB container-nod under Blob Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Du kan högerklicka på noden mapp och sedan använda kommandona **Uppdatera** och **Ladda upp BLOB** på snabb menyn.

   ![Snabb Meny kommandon för en mapp-nod under Blob Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Du kan högerklicka på filnoden och sedan använda kommandona **Preview/redigera**, **Hämta**, **ta bort**, **skapa extraherings skript** (endast tillgängligt för CSV-, TSV-och txt-filer), **Kopiera relativ sökväg** och **Kopiera fullständiga sökvägar** på snabb menyn.

    ![Snabb Meny kommandon för en filnod under Blob Storage](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Öppna Data Lake Explorer i portalen

1. Välj Ctrl + Shift + P för att öppna kommando paletten.
2. Ange **Öppna webb Azure Storage Explorer** eller högerklicka på en relativ sökväg eller den fullständiga sökvägen i skript redigeraren och välj sedan **Öppna webb Azure Storage Explorer**.
3. Välj ett Data Lake Analytics konto.

Data Lake-verktyg öppnar Azure Storage sökväg i Azure Portal. Du kan hitta sökvägen och förhandsgranska filen från webben.

## <a name="additional-features"></a>Ytterligare funktioner

Data Lake verktyg för VS Code stöder följande funktioner:

- **IntelliSense-komplettering**: förslag visas i popup-fönster runt objekt som nyckelord, metoder och variabler. Olika ikoner representerar olika typer av objekt:

  - Data typen Scala
  - Komplex data typ
  - Inbyggda UDTs
  - .NET-samling och klasser
  - C#-uttryck
  - Inbyggda C#-UDF: er, Katalogentiteter och UDAAGs
  - U-SQL-funktioner
  - U-SQL-fönster funktioner

    ![Objekt typer i IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **IntelliSense-komplettering på data Lake Analytics metadata**: data Lake-verktyg hämtar information om data Lake Analytics metadata lokalt. IntelliSense-funktionen fyller automatiskt i objekt från Data Lake Analytics metadata. Dessa objekt omfattar databas, schema, tabell, vy, tabell värdes funktion, procedurer och C#-sammansättningar.

  ![IntelliSense-metadata](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense-fel markör**: data Lake verktyg stryker under redigering av fel för U-SQL och C#.
- **Syntax**: data Lake verktyg använder färger för att särskilja objekt som variabler, nyckelord, data typer och funktioner.

    ![Syntax med olika färger](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Vi rekommenderar att du uppgraderar till Azure Data Lake verktyg för Visual Studio version 2.3.3000.4 eller senare. Tidigare versioner är inte längre tillgängliga för hämtning och är nu föråldrade.  

## <a name="next-steps"></a>Nästa steg

- [Utveckla U-SQL med python, R och C Sharp för Azure Data Lake Analytics i VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL lokal körning och lokal fel sökning med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Självstudie: kom igång med Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Självstudie: utveckla U-SQL-skript med hjälp av Data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)