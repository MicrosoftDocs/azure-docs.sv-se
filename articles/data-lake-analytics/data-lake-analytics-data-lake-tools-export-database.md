---
title: Exportera U-SQL-databas – Azure Data Lake verktyg för Visual Studio
description: Lär dig hur du använder Azure Data Lake verktyg för Visual Studio för att exportera en U-SQL-databas och importera den automatiskt till ett lokalt konto.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 11/27/2017
ms.openlocfilehash: 0a768990a33f0d60a3222bd3ad323edcc53c9ec0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219096"
---
# <a name="export-a-u-sql-database"></a>Exportera en U-SQL-databas

I den här artikeln lär du dig att använda [Azure Data Lake verktyg för Visual Studio](https://aka.ms/adltoolsvs) för att exportera en u-SQL-databas som ett enda u-SQL-skript och hämtade resurser. Du kan importera den exporterade databasen till ett lokalt konto i samma process.

Kunderna upprätthåller vanligt vis flera miljöer för utveckling, testning och produktion. De här miljöerna finns på både ett lokalt konto på en utvecklares lokala dator och i ett Azure Data Lake Analytics konto i Azure. 

När du utvecklar och justerar U-SQL-frågor i utvecklings-och test miljöer, behöver utvecklare ofta återskapa sitt arbete i en produktions databas. Guiden för databas export hjälper dig att påskynda processen. Genom att använda guiden kan utvecklare klona den befintliga databas miljön och exempel data till andra Data Lake Analytics-konton.

## <a name="export-steps"></a>Exportera steg

### <a name="step-1-export-the-database-in-server-explorer"></a>Steg 1: exportera databasen i Server Explorer

Alla Data Lake Analytics-konton som du har behörighet för visas i Server Explorer. Så här exporterar du databasen:

1. I Server Explorer expanderar du det konto som innehåller den databas som du vill exportera.
2. Högerklicka på databasen och välj sedan **Exportera**. 
   
    ![Server Explorer – exportera en databas](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Om meny alternativet **Exportera** inte är tillgängligt måste du [Uppdatera verktyget till den senaste versionen](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Steg 2: Konfigurera de objekt som du vill exportera

Om du bara behöver en liten del av en stor databas kan du konfigurera en delmängd av objekt som du vill exportera i guiden Exportera. 

Export åtgärden slutförs genom att köra ett U-SQL-jobb. Att exportera från ett Azure-konto medför därför viss kostnad.

![Guiden för databas export – Välj Exportera objekt](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Steg 3: kontrol lera objekt listan och andra konfigurationer

I det här steget kan du verifiera de valda objekten i **list rutan Exportera objekt** . Om det finns några fel väljer du **föregående** för att gå tillbaka och konfigurera de objekt som du vill exportera korrekt.

Du kan också konfigurera andra inställningar för export målet. Konfigurations beskrivningar visas i följande tabell:

|Konfiguration|Beskrivning|
|-------------|-----------|
|Målnamn|Det här namnet anger var du vill spara de exporterade databas resurserna. Exempel är sammansättningar, ytterligare filer och exempel data. En mapp med det här namnet skapas under den lokala data rot katalogen.|
|Projekt katalog|Den här sökvägen definierar var du vill spara det exporterade U-SQL-skriptet. Alla definitioner för databas objekt sparas på den här platsen.|
|Endast schema|Om du väljer det här alternativet exporteras endast databas definitioner och resurser (t. ex. sammansättningar och ytterligare filer).|
|Schema och data|Om du väljer det här alternativet exporteras databas definitioner, resurser och data. De översta N raderna i tabeller exporteras.|
|Importera till lokal databas automatiskt|Om du väljer det här alternativet importeras den exporterade databasen automatiskt till den lokala databasen när exporten är färdig.|

![Guiden för databas export – listan Exportera objekt och andra konfigurationer](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Steg 4: kontrol lera export resultatet

När du har exporterat kan du Visa de exporterade resultaten i logg fönstret i guiden. I följande exempel visas hur du hittar exporterade U-SQL-skript och databas resurser, inklusive sammansättningar, ytterligare filer och exempel data:

![Guiden för databas Export – Exportera resultat](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importera den exporterade databasen till ett lokalt konto

Det enklaste sättet att importera den exporterade databasen är att markera kryss rutan **Importera till lokal databas automatiskt** under export processen i steg 3. Om du inte markerar den här kryss rutan söker du först efter det exporterade U-SQL-skriptet i export loggen. Kör sedan U-SQL-skriptet lokalt för att importera databasen till ditt lokala konto.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importera den exporterade databasen till ett Data Lake Analytics konto

Så här importerar du databasen till ett annat Data Lake Analytics konto:

1. Ladda upp de exporterade resurserna, inklusive sammansättningar, ytterligare filer och exempel data, till standard Azure Data Lake Store kontot för det Data Lake Analytics-konto som du vill importera till. Mappen exporterad resurs finns under den lokala rotmappen. Ladda upp hela mappen till roten för standard Data Lake Stores kontot.
2. När överföringen är färdig skickar du det exporterade U-SQL-skriptet till det Data Lake Analytics konto som du vill importera databasen till.

## <a name="known-limitations"></a>Kända begränsningar

För närvarande, om du väljer alternativet **schema och data** i steg 3, kör verktyget ett U-SQL-jobb för att exportera data som lagras i tabeller. På grund av detta kan data export processen vara långsam och du kan ådra dig kostnader. 

## <a name="next-steps"></a>Nästa steg

* [Läs om U-SQL-databaser](/u-sql/data-definition-language-ddl-statements) 
* [Testa och felsöka U-SQL-jobb med lokal körning och Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)


