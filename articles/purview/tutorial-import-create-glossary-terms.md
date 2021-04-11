---
title: 'Självstudie: skapa och importera ord listans villkor i Azure avdelningens kontroll (för hands version)'
description: I den här självstudien beskrivs hur du skapar ord listans villkor, lägger till ord lista i en till gång och importerar ord listans villkor.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 9443c6fbaca16cf075745972a1655a2b4b2ea43c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077541"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Självstudie: skapa och importera ord listans villkor i Azure avdelningens kontroll (för hands version)

> [!IMPORTANT]
> Azure avdelningens kontroll är för närvarande en för hands version. Kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.

En ord lista är ett viktigt verktyg för att underhålla och organisera din katalog. Du skapar ord listan genom att definiera nya villkor eller importera en term lista och sedan tillämpa dessa villkor på till gångarna.

Den här självstudien är *del 5 i en själv studie serie i fem delar* där du får lära dig grunderna i hur du hanterar data styrning över en datafastighet med Azure avdelningens kontroll. Den här självstudien bygger på det arbete som du avslutade i [del 4: utforska resurs uppsättningar, information, scheman och klassificeringar i Azure avdelningens kontroll (för hands version)](tutorial-schemas-and-classifications.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa ord listans villkor.
> * Lägg till ord listans villkor till en till gång.
> * Importera ord listans villkor.

## <a name="prerequisites"></a>Förutsättningar

* Fullständig [självstudie: utforska resurs uppsättningar, information, scheman och klassificeringar i Azure Dataavdelningens kontrolls (för hands version)](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Skapa ordlistetermer

Du kan skapa affärs-eller tekniska villkor i ord listan. Du kan också kommentera dina till gångar genom att använda villkor.

Här är en sammanfattning av några av de vanligaste fälten på sidan **ordboks villkor** :

* **Status**: tilldela en status till termen (**utkast**, **godkänd**, **förfallen** eller **Varning**).

* **Definition**: Ange en definition av termen.

* **Akronym**: Ange en förkortad version av termen genom att använda de första bokstäverna i varje ord.

* **Synonymer**: Välj andra termer med samma eller liknande definitioner.

* **Relaterade villkor**: Välj andra villkor i ord listan som är relaterade till detta, men som har olika definitioner. Exempel är tekniska villkor som är relaterade till ett affärs villkor, ett kod namn eller andra villkor som ska associeras med termen.

Skapa en ord lista med följande steg:

1. Gå till Azure avdelningens kontroll-resursen i Azure Portal och välj **Öppna avdelningens kontroll Studio**. Du kommer automatiskt till avdelningens kontroll Studios start sida.

1. Välj **ord lista** i det vänstra fönstret för att öppna sidan **ord lista** .

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Skärm bild som visar sidan ord listans villkor.":::

1. Välj **nytt term**  >  **system standard**  >  **Fortsätt**.

1. På fliken **Översikt** på sidan **nytt villkor** anger du **namnet** på den nya termen: *ekonomi*.

1. Ange **definitionen**: *den här termen representerar finansiell information för Contoso Inc.*

1. I list rutan **status** väljer du **godkänd**.

1. När du är klar väljer du **skapa**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Skärm bild som visar hur du skapar en ny ord lista.":::

## <a name="add-glossary-terms-to-an-asset"></a>Lägg till villkor för ord lista i en till gång

1. Använd de steg som du fick i [del 1 av den här själv studie serien](tutorial-scan-data.md) för att hitta en till gång. Till exempel **Contoso_CashFlow_ {N}. csv**.

1. På sidan till gångs information väljer du **Redigera**.

1. I list rutan **ord** lista på fliken **Översikt** väljer du **finans** och väljer sedan **Spara**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Skärm bild som visar hur du lägger till en ord lista till en till gång.":::

1. Gå till avsnittet **ord lista villkor** på fliken **Översikt** och Observera att den *ekonomiska* termen har tillämpats på till gången.

## <a name="import-glossary-terms"></a>Importera ord listans villkor

Om du vill importera villkor eller uppdatera befintliga villkor i bulk laddar du upp en förifylld mall till ord listan.

I den här proceduren importerar du ord listans villkor via en CSV-fil:

1. Observera att du har lagrat filen med namnet *StarterKitTerms.csv*, som är en del av det start paket som du laddade ned i [del 1 av den här själv studie serien](tutorial-scan-data.md).

   Den här filen innehåller en lista med förifyllda villkor som är relevanta för din datafastighet.

1. Börja importera genom att välja **ord lista** och sedan **Importera villkor**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Skärm bild som visar hur du importerar ord listans villkor.":::

1. På sidan **import villkor** väljer du **system standard** och väljer sedan **Fortsätt**.

1. Välj **Bläddra**, gå till den plats där du laddade ned *StarterKitTerms.csv* och välj sedan **Öppna**.

1. Välj **OK** för att börja importera villkoren.

   När importen är färdig visas de nya ord listans villkor på sidan **ord lista** .

1. Visa var och en av de nyligen importerade villkoren för att se hur de är definierade.

## <a name="create-custom-term-templates"></a>Skapa anpassade term mallar

I det här avsnittet får du lära dig hur du skapar en anpassad term med anpassade attribut och importerar data med hjälp av en mall CSV-fil.

Det finns flera befintliga mallar för system term, som du kan visa genom att välja **ord lista**  >  **Hantera term mallar**  >  **system**.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="mallar för system villkor.":::

Gör så här för att skapa en ny anpassad term mall:

1. Välj **ord lista** på vänster sida-menyn.
1. Välj **Hantera term mallar**  >  **anpassad**  >  **ny term mall**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="screate en ny mall för anpassad term.":::

Utför följande steg på skärmen **ny term mall** :

1. Ange **mallnamn**: `Sensitivity level` .
1. Ange önskad beskrivning, till exempel `Indicates the level of sensitivity for this data.`
1. Välj **+ nytt attribut** för att öppna en dialog ruta för att lägga till attribut.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="skärm start för ny term mall.":::

1. På skärmen **nytt attribut** anger du följande parametrar:

   |Inställning|Föreslaget värde|
   |---------|-----------|
   |Attributnamn |är känslig information|
   |Fälttyp | Enkelt alternativ|
   |Markera som obligatoriskt | Markera den här kryssrutan.|
   |+ Lägg till ett alternativ | Lägg till två alternativ. "Ja" och "nej".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="Lägg till ett nytt attribut.":::

1. Upprepa föregående steg för att lägga till ett annat attribut med namnet `can be shared externally` . När båda attributen har lagts till väljer du **skapa**.

## <a name="import-terms-from-a-template"></a>Importera villkor från en mall

Därefter importerar du en ny term med hjälp av mallen **känslighets nivå** som du har skapat. 

1. Välj **import villkor** på skärmen **ord listans villkor** .

1. Välj **känslighets nivå** på skärmen **Importera villkor** . Välj **Fortsätt**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Välj känslighets nivå.":::

1. Term mal len för **känslighets nivån** innehåller standardattribut för systemet, samt de nya attribut som du har lagt till: `can be shared externally` och `is sensitive information` . Välj **Hämta ett exempel. CSV** -fil.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Hämta exempel filen CSV.":::

1. En mallfil hämtas så att du kan redigera och ladda upp ny ord lista med Kundattribut. Öppna den CSV-fil som du har laddat ned. Lägg till nya termer och deras lämpliga värden som nya rader i CSV-filen.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Skapa term i CSV-fil.":::

   De termer som anges i kolumnen **relaterade villkor** eller **synonymer** som inte redan finns skapas när filen laddas upp. De kommer att skapas med **systemets standard** mal len.

1. Om du vill ladda upp filen återgår du till skärmen **Importera villkor** och väljer **Bläddra** bredvid listan **Slutför. CSV-fil att ladda upp** . Välj din fil i dialog rutan som öppnas och välj sedan **OK**.

1. De nya villkoren visas nu på skärmen **ord listans villkor** . Du kan visa information om de nya villkoren genom att klicka på term namnet i listan.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Skapa ord listans villkor.
> * Lägg till ord listans villkor till en till gång.
> * Importera ord listans villkor.

Gå vidare till nästa artikel om du vill lära dig mer om olika katalog insikter.

> [!div class="nextstepaction"]
> [Förstå insikter i Azure Purview](concept-insights.md)
