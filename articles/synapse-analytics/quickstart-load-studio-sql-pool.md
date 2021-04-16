---
title: 'Snabbstart: Massinläsning av data med en dedikerad SQL-pool'
description: Använd Synapse Studio massinläsning av data till en dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 838138fb6ca6f64b4296b54a81bc2764c3f1399c
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567918"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Snabbstart: Massinläsning med Synapse Studio

Det är enkelt att läsa in data med guiden Massinläsning i Synapse Studio. Synapse Studio är en funktion i Azure Synapse Analytics. Guiden Massinläsning vägleder dig genom skapandet [](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) av ett T-SQL-skript med COPY-instruktionen för massinläsning av data till en dedikerad SQL-pool. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Startpunkter i guiden Massinläsning

Du kan massinläsning av data genom att högerklicka på följande område i Synapse Studio: en fil eller mapp från ett Azure Storage-konto som är kopplat till din arbetsyta.

![Skärmbild som visar hur du högerklickar på en fil eller mapp från ett lagringskonto.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Förutsättningar

- Guiden genererar en COPY-instruktion som använder Azure Active Directory (Azure AD) för autentisering. Din [Azure AD-användare måste ha åtkomst](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) till arbetsytan med minst Azure-rollen Storage Blob Data-deltagare för Azure Data Lake Storage Gen2 kontot. 

- Du måste ha de behörigheter [som krävs för att kunna](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) använda COPY-instruktionen och skapa tabell-behörigheter om du skapar en ny tabell att läsa in till.

- Den länkade tjänst som är associerad med *Data Lake Storage Gen2-kontot måste ha åtkomst till filen eller mappen som ska* läsas in. Om autentiseringsmekanismen för den länkade tjänsten till exempel är en hanterad identitet måste arbetsytans hanterade identitet minst ha behörigheten Storage Blob Data Reader för lagringskontot.

- Om ett virtuellt nätverk är aktiverat på arbetsytan kontrollerar du att den integrerade körning som är associerad med Data Lake Storage Gen2-kontots länkade tjänster för källdata och felfilens plats har interaktiv redigering aktiverat. Interaktiv redigering krävs för identifiering av autoschema, förhandsgranskning av källfilens innehåll och surfning Data Lake Storage Gen2 lagringskonton i guiden.

## <a name="steps"></a>Steg

1. På panelen **Källlagringsplats** väljer du lagringskontot och filen eller mappen som du läser in från. Guiden försöker automatiskt identifiera Parquet-filer och CSV-filer (avgränsad text), inklusive mappning av källfälten från filen till lämpliga SQL-måldatatyper. 

   ![Skärmbild som visar val av en källplats.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Välj filformatsinställningar, inklusive felinställningarna för när det finns avvisade rader under massinläsningen. Du kan också välja **Förhandsgranska data för** att se hur COPY-instruktionen parsar filen för att hjälpa dig att konfigurera filformatinställningarna. Välj **Förhandsgranska data** varje gång du ändrar en filformatinställning för att se hur COPY-instruktionen parsar filen med den uppdaterade inställningen.

   ![Skärmbild som visar förhandsgranskning av data.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - Massinläsningsguiden stöder inte förhandsgranskning av data med fältterminatorer med flera tecken. När du anger en fältparentator med flera tecken förhandsgranskar guiden data i en enda kolumn. 
   > - När du väljer **Härner kolumnnamn** parsar guiden Massinläsning kolumnnamnen från den första raden som anges av **fältet Första** raden. Guiden Massinläsning ökar automatiskt värdet i `FIRSTROW` COPY-instruktionen med 1 för att ignorera den här rubrikraden. 
   > - Det finns stöd för att ange radterminatorer med flera tecken i COPY-instruktionen. Men massinläsningsguiden stöder det inte och kastar ett fel.

3. Välj den dedikerade SQL-pool som du använder för att läsa in, inklusive om inläsningen ska ske för en befintlig tabell eller en ny tabell.
   ![Skärmbild som visar val av en målplats.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Välj **Konfigurera kolumnmappning** för att kontrollera att du har rätt kolumnmappning. Namn på anteckningskolumner identifieras automatiskt om du har **aktiverat Härner kolumnnamn**. För nya tabeller är det viktigt att konfigurera kolumnmappningen för att uppdatera målkolumndatatyperna.

   ![Skärmbild som visar konfiguration av kolumnmappning.](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Välj **Öppna skript.** Ett T-SQL-skript genereras med COPY-instruktionen för inläsning från datasjön.
   ![Skärmbild som visar hur du öppnar SQL-skriptet.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Nästa steg

- Mer information [om COPY-funktioner](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) finns i artikeln COPY-instruktion.
- I [översiktsartikeln för](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) dataläsning finns information om hur du använder en process för extrahering, transformering och inläsning (ETL).
