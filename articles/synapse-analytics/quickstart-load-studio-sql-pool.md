---
title: 'Snabb start: Mass inläsning av data med en dedikerad SQL-pool'
description: Använd Synapse Studio för att överföra data till en dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 53b6810b0042df382fd1f553bc4bd0fae61793b3
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672805"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Snabb start: Mass inläsning med Synapse Studio

Det är enkelt att läsa in data med guiden för Mass inläsning i Synapse Studio. Synapse Studio är en funktion i Azure Synapse Analytics. Guiden för Mass inläsning vägleder dig genom att skapa ett T-SQL-skript med [copy-instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) för att överföra data till en dedikerad SQL-pool. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Start punkter i guiden för Mass inläsning

Du kan Mass inläsning av data genom att högerklicka på följande område i Synapse Studio: en fil eller mapp från ett Azure Storage-konto som är kopplat till din arbets yta.

![Skärm bild som visar hur du högerklickar på en fil eller mapp från ett lagrings konto.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Krav

- Guiden genererar en KOPIERINGs instruktion som använder Azure Active Directory (Azure AD) genom strömning för autentisering. Din [Azure AD-användare måste ha åtkomst](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) till arbets ytan med minst Storage BLOB data Contributor Azure-rollen för det Azure Data Lake Storage Gen2 kontot. 

- Du måste ha de [behörigheter som krävs för att använda kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) och skapa tabell behörigheter om du skapar en ny tabell som ska läsas in på.

- Den länkade tjänst som är kopplad till Data Lake Storage Gen2 kontot *måste ha åtkomst till filen eller mappen* för att kunna läsa in den. Om mekanismen för den länkade tjänsten till exempel är en hanterad identitet, måste arbets ytans hanterade identitet ha minst behörighet för lagrings-BLOB-dataläsaren på lagrings kontot.

- Om ett virtuellt nätverk har Aktiver ATS på arbets ytan kontrollerar du att den integrerade körningen som är associerad med Data Lake Storage Gen2 kontots länkade tjänster för källdata och Felfilens plats har interaktiv redigering aktive rad. Interaktiv redigering krävs för autoschema-identifiering, för att Visa käll filens innehåll och bläddra Data Lake Storage Gen2 lagrings konton i guiden.

## <a name="steps"></a>Steg

1. På panelen **käll lagrings plats** väljer du lagrings kontot och filen eller mappen som du läser in från. Guiden försöker automatiskt identifiera Parquet-filer och avgränsade textfiler (CSV), inklusive att mappa käll fälten från filen till rätt mål SQL-datatyper. 

   ![Skärm bild som visar att du väljer en käll plats.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Välj fil formats inställningar, inklusive fel inställningarna för när det finns avvisade rader under Mass inläsnings processen. Du kan också välja **Förhandsgranska data** om du vill se hur kopierings instruktionen kommer att parsa filen så att du kan konfigurera fil formats inställningarna. Välj **Förhandsgranska data** varje gång du ändrar fil formats inställningen för att se hur kopierings instruktionen kommer att parsa filen med den uppdaterade inställningen.

   ![Skärm bild som visar för hands Visa data.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - Guiden för Mass inläsning stöder inte för hands visning av data med avslutande fält-avgränsare. När du anger ett fält med flera tecken kan du förhandsgranska data i en kolumn i guiden. 
   > - När du väljer **Härled kolumn namn** kommer guiden för Mass inläsning att parsa kolumn namnen från den första raden som anges av det **första rad** fältet. Guiden för Mass inläsning ökar automatiskt `FIRSTROW` värdet i kopierings instruktionen med 1 för att ignorera denna rubrik rad. 
   > - Det finns stöd för att ange rader med flera rader i KOPIERINGs instruktionen. Guiden för Mass inläsning stöder dock inte den och kommer att utlösa ett fel.

3. Välj den dedikerade SQL-pool som du använder för att läsa in, inklusive om belastningen ska användas för en befintlig tabell eller en ny tabell.
   ![Skärm bild som visar valet av målplats.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Välj **Konfigurera kolumn mappning** för att kontrol lera att du har rätt kolumn mappning. Observera att kolumn namn identifieras automatiskt om du har aktiverat **härledda kolumn namn**. För nya tabeller är det viktigt att konfigurera kolumn mappningen för att uppdatera data typerna för mål kolumnen.

   ![Skärm bild som visar konfiguration av kolumn mappning.](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Välj **Öppna skript**. Ett T-SQL-skript genereras med COPY-instruktionen som ska läsas in från data Lake.
   ![Skärm bild som visar hur du öppnar SQL-skriptet.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Nästa steg

- Se artikeln [Kopiera instruktion](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) för mer information om kopierings funktionerna.
- Se artikeln [Översikt över data inläsning](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) för information om hur du använder en process för att extrahera, transformera och läsa in (ETL).
