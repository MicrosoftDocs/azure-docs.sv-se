---
title: 'Självstudie: Skapa och importera ordlistor i Azure Purview (förhandsversion)'
description: I den här självstudien beskrivs hur du skapar ordlistor, lägger till ordlistor i en tillgång och importerar ordlistor.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: b240806b934cae54810cf9dfe1a6c1f369cede7e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587518"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Självstudie: Skapa och importera ordlistor i Azure Purview (förhandsversion)

> [!IMPORTANT]
> Azure Purview finns för närvarande i FÖRHANDSVERSION. De [kompletterande användningsvillkoren för Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som inte har släppts allmänt ännu.

En ordlista är ett viktigt verktyg för att underhålla och organisera katalogen. Du skapar din ordlista genom att definiera nya termer eller importera en termlista och sedan tillämpa dessa termer på dina tillgångar.

Den här *självstudien är del 5* i en självstudieserie i fem delar där du lär dig grunderna i hur du hanterar datastyrning i en datae egendom med hjälp av Azure Purview. Den här självstudien bygger på det arbete som du slutförde i del 4: Utforska resursuppsättningar, information, scheman och klassificeringar i [Azure Purview (förhandsversion).](tutorial-schemas-and-classifications.md)

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa ordlistor.
> * Lägg till ordlistetermer till en tillgång.
> * Importera ordlistor.

## <a name="prerequisites"></a>Förutsättningar

* Slutför [självstudie: Utforska resursuppsättningar, information, scheman och klassificeringar i Azure Purview (förhandsversion)](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Skapa ordlistetermer

Du kan skapa affärstermer eller tekniska termer i ordlistan. Du kan också kommentera dina tillgångar genom att använda villkor för dem.

Här är en sammanfattning av några av de vanligaste fälten på **sidan ordlista:**

* **Status:** Tilldela en status till termen (**Utkast,** **Godkänd,** **Har upphört att gälla** eller **Avisering**).

* **Definition**: Ange en definition av termen.

* **Förkortning:** Ange en förkortad version av termen med hjälp av de första bokstäverna i varje ord.

* **Synonymer:** Välj andra termer med samma eller liknande definitioner.

* **Relaterade termer:** Välj andra termer i ordlistan som är relaterade till den här, men som har olika definitioner. Exempel är tekniska termer som är relaterade till en affärsterm, ett kodnamn eller andra termer som ska associeras med termen.

Skapa en ordlista genom att följa dessa steg:

1. Gå till din Azure Purview-resurs i Azure Portal och välj **Öppna Purview Studio.** Du kommer automatiskt till Purview Studio-startsidan.

1. Välj **Ordlista i** den vänstra rutan för att öppna sidan **Ordlista.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Skärmbild som visar sidan med ordlistor.":::

1. Välj **Ny term**  >  **Systemstandard**  >  **Fortsätt.**

1. På fliken **Översikt** på sidan **Ny term** anger du Namn **för den** nya termen: *Finansiella*.

1. Ange **definitionen**: *Den här termen representerar ekonomisk information för Contoso Inc.*

1. I **listrutan Status** väljer du **Godkänd.**

1. När du är klar väljer du **Skapa**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Skärmbild som visar hur du skapar en ny ordlista.":::

## <a name="add-glossary-terms-to-an-asset"></a>Lägga till ordlistor till en tillgång

1. Använd stegen som du lärde dig i [del 1 av den här självstudieserien](tutorial-scan-data.md) för att hitta en tillgång. Till **exempel, Contoso_CashFlow_{N}.csv**.

1. På sidan med tillgångsinformation väljer du **Redigera**.

1. I **listrutan Ordlista** på fliken **Översikt väljer** du **Finans** och sedan **Spara.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Skärmbild som visar hur du lägger till en ordlista till en tillgång.":::

1. Gå till **avsnittet Ordlista på** fliken Översikt **och** observera att termen *Finans* tillämpades på tillgången.

## <a name="import-glossary-terms"></a>Importera ordlistor

Om du vill importera termer eller uppdatera befintliga termer i grupp laddar du upp en i förväg ifylld mall till ordlistan.

I den här proceduren importerar du ordlista via en .csv-fil:

1. Observera att du lagrade filen med *StarterKitTerms.csv*, som är en del av startpaketet som du laddade ned i del 1 av [den här självstudieserien](tutorial-scan-data.md).

   Den här filen innehåller en lista över förifyllda termer som är relevanta för din datae egendom.

 > [!Important]
   > E-postadressen förjingspersonal och experter i . CSV-filen ska vara den primära adressen för användaren från AAD-gruppen. Alternativ e-post, användarens huvudnamn och e-post som inte kommer från AAD stöds inte ännu. Du måste ersätta e-postadresserna med den primära AAD-adressen från din organisation.

1. Börja importera genom att **välja Ordlista** och sedan Importera **termer**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Skärmbild som visar hur du importerar ordlistor.":::

1. På sidan **Importera villkor** väljer du **Systemstandard** och sedan **Fortsätt.**

1. Välj **Bläddra,** gå till den plats där du laddade *StarterKitTerms.csv* och välj sedan **Öppna.**

1. Välj **OK** för att börja importera villkoren.

   När importen är klar visas de nya ordlistan på **sidan Med ordlistor.**

1. Visa var och en av de nyimporterade termerna för att se hur de definieras.

## <a name="create-custom-term-templates"></a>Skapa anpassade termmallar

I det här avsnittet får du lära dig hur du skapar en anpassad termmall med anpassade attribut och importerar data med hjälp av en csv-mallfil.

Det finns flera befintliga mallar för systemtermer som du kan visa genom att **välja Ordlista Hantera**  >  **termmallar**  >  **System**.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="systemtermmallar.":::

Skapa en ny anpassad termmall genom att göra följande:

1. Välj **Ordlista på** menyn till vänster.
1. Välj **Hantera termmallar**  >  **Anpassad**  >  **ny termmall**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="skapa en ny anpassad termmall.":::

Gör **följande på skärmen Ny** termmall:

1. Ange **mallnamn:** `Sensitivity level` .
1. Ange önskad beskrivning, till exempel `Indicates the level of sensitivity for this data.`
1. Välj **+ Nytt attribut för** att öppna en dialogruta för att lägga till attribut.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="skärmen ny termmall startar.":::

1. På skärmen **Nytt attribut** anger du följande parametrar:

   |Inställning|Föreslaget värde|
   |---------|-----------|
   |Attributnamn |är känslig information|
   |Fälttyp | Enskilt val|
   |Markera som obligatoriskt | Markera den här kryssrutan.|
   |+ Lägg till ett alternativ | Lägg till två alternativ. "Ja" och "Nej".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="lägg till ett nytt attribut.":::

1. Upprepa föregående steg för att lägga till ytterligare ett attribut med namnet `can be shared externally` . När båda attributen har lagts till väljer du slutligen **Skapa.**

## <a name="import-terms-from-a-template"></a>Importera termer från en mall

Därefter importerar du en ny term med hjälp **av mallen för känslighetsnivå** som du har skapat. 

1. På skärmen **Ordlistevillkor** väljer du **Importera termer**.

1. Välj **Känslighetsnivå** på skärmen **Importvillkor.** Välj **Fortsätt**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Välj känslighetsnivå.":::

1. Termen mall för **känslighetsnivå innehåller** standardsystemattribut, samt de nya attribut som du har lagt till: `can be shared externally` och `is sensitive information` . Välj **Ladda ned ett exempel. CSV-fil.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Ladda ned csv-exempelfilen.":::

1. En mallfil laddas ned så att du kan redigera och ladda upp en ny ordlista med kundattribut. Öppna CSV-filen som du har laddat ned. Lägg till nya termer och deras lämpliga värden som nya rader i CSV-filen.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Skapa termen i csv-filen.":::

   Alla termer som anges **i kolumnen Relaterade** termer eller **synonymer** som inte redan finns skapas när filen laddas upp. De skapas med hjälp av **standardmallen** System.

1. Om du vill ladda upp filen **återgår du till** skärmen Importera villkor **och** väljer Bläddra bredvid Välj den **fullständiga . CSV-fil som ska laddas upp.** Välj filen i dialogrutan som öppnas och välj sedan **OK.**

1. De nya villkoren visas nu på **skärmen Ordlista.** Du kan visa information om de nya villkoren genom att klicka på termnamnet i listan.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Skapa ordlistor.
> * Lägg till ordlistetermer till en tillgång.
> * Importera ordlistor.

Gå vidare till nästa artikel om du vill veta mer om olika kataloginsikter.

> [!div class="nextstepaction"]
> [Förstå insikter i Azure Purview](concept-insights.md)
