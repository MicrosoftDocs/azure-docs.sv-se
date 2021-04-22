---
title: Tagga bilder i ett etiketteringsprojekt
title.suffix: Azure Machine Learning
description: Lär dig hur du använder datataggningsverktygen för att snabbt förbereda data för en Machine Learning i ett Azure Machine Learning för etikettering.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: 8f1d648c38760865752c87624dfcb112933650c7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872631"
---
# <a name="tag-images-in-a-labeling-project"></a>Tagga bilder i ett etiketteringsprojekt 

När projektadministratören [har skapat](./how-to-create-labeling-projects.md#create-a-data-labeling-project) ett dataetiketteringsprojekt i Azure Machine Learning kan du använda etiketteringsverktyget för att snabbt förbereda data för ett Machine Learning projekt. I den här artikeln beskrivs:

> [!div class="checklist"]
> * Så här kommer du åt dina etiketteringsprojekt
> * Etiketteringsverktygen
> * Så här använder du verktygen för specifika etiketteringsuppgifter

## <a name="prerequisites"></a>Förutsättningar

* Ett [Microsoft-konto](https://account.microsoft.com/account) eller Azure Active Directory ett konto för organisationen och projektet
* Åtkomst på deltagarnivå till arbetsytan som innehåller etiketteringsprojektet.

## <a name="sign-in-to-the-workspace"></a>Logga in på arbetsytan

1. Logga in på [Azure Machine Learning-studio](https://ml.azure.com).

1. Välj den prenumeration och den arbetsyta som innehåller etiketteringsprojektet.  Hämta den här informationen från projektadministratören.

1. Välj **Dataetiketter** till vänster för att hitta projektet.  

## <a name="understand-the-labeling-task"></a>Förstå etikettuppgiften

I tabellen med dataetikettprojekt väljer du **Etikettlänk** för projektet.

Instruktioner som är specifika för ditt projekt visas. De förklarar vilken typ av data du står inför, hur du bör fatta dina beslut och annan relevant information. När du har läst den här informationen väljer du Uppgifter överst på **sidan.**  Eller längst ned på sidan och välj **Start labeling (Starta etikettering).**

## <a name="common-features-of-the-labeling-task"></a>Vanliga funktioner i etikettuppgiften

I alla bildetiketter väljer du en lämplig tagg eller taggar från en uppsättning som har angetts av projektadministratören. Du kan välja de första nio taggarna med hjälp av taltangenterna på tangentbordet.  

I bildklassificeringsuppgifter kan du välja att visa flera bilder samtidigt. Använd ikonerna ovanför bildområdet för att välja layouten. 

Om du vill markera alla bilder som visas samtidigt använder du **Markera alla**. Om du vill välja enskilda bilder använder du knappen cirkulär markering i det övre högra hörnet av bilden. Du måste välja minst en avbildning för att tillämpa en tagg. Om du väljer flera bilder tillämpas alla taggar som du väljer på alla valda bilder.

Här har vi valt en två-i-två-layout och håller på att tillämpa taggen "Mammal" på bilderna på bear och orca. Bilden av en kanin har redan taggats som "Cartilaginous fish" och iguana har inte taggats ännu.

![Flera bildlayouter och markeringar](./media/how-to-label-images/layouts.png)

> [!Important] 
> Växla endast layouter när du har en ny sida med omärkta data. Om du växlar layout rensas sidans pågående taggningsarbete.

Azure aktiverar knappen **Skicka** när du har taggat alla bilder på sidan. Välj **Skicka** för att spara ditt arbete.

När du har skickat taggar för de data som finns till hands uppdaterar Azure sidan med en ny uppsättning bilder från arbetskön.

### <a name="assisted-machine-learning"></a>Assisterad maskininlärning

Maskininlärningsalgoritmer kan utlösas. Om dessa algoritmer är aktiverade i projektet kan du se följande:

* När en del avbildningar har märkts kan du **se** Uppgifter klustrade överst på skärmen bredvid projektnamnet.  Det innebär att bilder grupperas tillsammans för att presentera liknande bilder på samma sida.  I så fall växlar du till en av de flera bildvyerna för att dra nytta av gruppningen.  

* Vid ett senare tillfälle kan du **se Uppgifter förmärkta** bredvid projektnamnet.  Bilder visas sedan med en föreslagen etikett som kommer från en klassificeringsmodell för maskininlärning. Ingen maskininlärningsmodell har 100 % noggrannhet. Även om vi bara använder bilder som modellen är säker på, kan dessa bilder fortfarande vara felaktigt förmärkta.  När du ser dessa etiketter korrigerar du eventuella felaktiga etiketter innan du skickar in sidan.  

* För objektidentifieringsmodeller kan du se begränsningsrutor och etiketter som redan finns.  Korrigera alla som är felaktiga innan du skickar in sidan.

* För segmenteringsmodeller kan polygoner och etiketter redan finnas.  Korrigera alla som är felaktiga innan du skickar in sidan. 

Särskilt tidigt i ett etiketteringsprojekt kanske maskininlärningsmodellen bara är tillräckligt noggrann för att prelabela en liten delmängd av bilder. När bilderna har märkts återgår etiketteringsprojektet till manuell märkning för att samla in mer data för nästa omgång av modellträningen. Med tiden blir modellen säkrare på en högre andel bilder, vilket resulterar i fler prelabel-uppgifter senare i projektet.

## <a name="tag-images-for-multi-class-classification"></a>Tagga bilder för klassificering med flera klasser

Om ditt projekt är av typen "Bildklassificering med flera klasser" tilldelar du en enda tagg till hela bilden. Om du vill granska anvisningarna när som helst går du till **sidan Instruktioner** och väljer Visa **detaljerade instruktioner.**

Om du inser att du har gjort ett misstag när du har tilldelar en tagg till en bild kan du åtgärda det. Välj **"X"** på etiketten som visas under bilden för att rensa taggen. Du kan också välja bilden och välja en annan klass. Det nyligen valda värdet ersätter taggen som tillämpats tidigare.

## <a name="tag-images-for-multi-label-classification"></a>Tagga bilder för klassificering med flera etiketter

Om du arbetar med ett projekt av typen "Bildklassificering med flera etiketter" använder du en *eller flera* taggar på en bild. Om du vill se de projektspecifika anvisningarna väljer **du Instruktioner** och går till Visa **detaljerade instruktioner.**

Välj den bild som du vill märka och välj sedan taggen. Taggen tillämpas på alla valda bilder och sedan avmarkeras bilderna. Om du vill använda fler taggar måste du välja bilderna igen. Följande animering visar taggning med flera etiketter:

1. **Markera alla** används för att tillämpa taggen "Ocean".
1. En enskild bild markeras och taggas "Närbild".
1. Tre bilder är markerade och taggade "Bred vinkel".

![Animering som visar flöde med flera etikett](./media/how-to-label-images/multilabel.gif)

Åtgärda ett misstag genom att klicka på **"X"** för att rensa en enskild tagg eller välja bilderna och sedan välja taggen, vilket rensar taggen från alla valda bilder. Det här scenariot visas här. Om du klickar på "Land" rensas taggen från de två valda bilderna.

![En skärmbild som visar flera avmarkerar](./media/how-to-label-images/multiple-deselection.png)

Azure aktiverar bara knappen **Skicka** när du har tillämpat minst en tagg på varje avbildning. Välj **Skicka** för att spara ditt arbete.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Tagga bilder och ange avgränsningsrutor för objektidentifiering

Om projektet är av typen "Objektidentifiering (avgränsade rutor) anger du en eller flera begränsningsrutor i bilden och tillämpar en tagg på varje ruta. Bilder kan ha flera avgränsade rutor, var och en med en enda tagg. Använd **Visa detaljerade instruktioner** för att avgöra om flera begränsningsrutor används i projektet.

1. Välj en tagg för den begränsningsruta som du planerar att skapa.
1. Välj det **rektangulära** ![ box-verktyget Rektangulära ](./media/how-to-label-images/rectangular-box-tool.png) rutan eller välj "R".
3. Klicka och dra diagonalt över målet för att skapa en ungefärlig begränsningsruta. Om du vill justera begränsningsrutan drar du kanterna eller hörnen.

![Skapa en begränsningsruta](./media/how-to-label-images/bounding-box-sequence.png)

Om du vill ta bort en begränsningsruta klickar du på det X-formade mål som visas bredvid markeringsrutan när du har skapat den.

Du kan inte ändra taggen för en befintlig begränsningsruta. Om du gör ett misstag med taggtilldelningen måste du ta bort begränsningsrutan och skapa en ny med rätt tagg.

Som standard kan du redigera befintliga begränsningsrutor. Verktyget **Lås/lås upp regioner** verktyget Lås/lås upp regioner eller ![ ](./media/how-to-label-images/lock-bounding-boxes-tool.png) "L" växlar det beteendet. Om regioner är låsta kan du bara ändra form eller plats för en ny begränsningsruta.

Använd **regionsmanipuleringsverktyget** Det här är ikonen för regionsmanipuleringsverktyget – fyra pilar som pekar utåt från ![ mitten, upp, höger, nedåt och vänster.](./media/how-to-label-images/regions-tool.png) eller "M" för att justera en befintlig begränsningsruta. Dra i kanterna eller hörnen för att justera formen. Klicka i den inre för att kunna dra hela begränsningsrutan. Om du inte kan redigera en region har du förmodligen lagt till verktyget **Lås/låsa upp** regioner.

Använd **mallbaserade verktygslådan** ![ Mallruta eller ](./media/how-to-label-images/template-box-tool.png) "T" för att skapa flera avgränsade rutor av samma storlek. Om bilden inte har några begränsningsrutor och du aktiverar mallbaserade rutor skapar verktyget rutor på 50 gånger 50 pixlar. Om du skapar en begränsningsruta och sedan aktiverar mallbaserade rutor blir alla nya begränsningsrutor storleken på den sista rutan som du skapade. Mallbaserade rutor kan ändras efter placering. Storleksändring av en mallbaserad ruta ändrar bara storlek på den specifika rutan.

Om du vill *ta* bort alla avgränsade rutor i den aktuella bilden väljer du verktyget **Ta bort alla** regioner verktyget Ta bort ![ ](./media/how-to-label-images/delete-regions-tool.png) regioner.

När du har skapat begränsningsrutorna  för en bild väljer du Skicka för att spara ditt arbete, annars sparas inte pågående arbete.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Tagga bilder och ange polygoner för bildsegmentering 

Om projektet är av typen "Instanssegmentering (Polygon) anger du en eller flera polygoner i bilden och tillämpar en tagg på varje polygon. Bilder kan ha flera avgränsande polygoner, var och en med en enda tagg. Använd **Visa detaljerade instruktioner** för att avgöra om flera avgränsade polygoner används i projektet.

1. Välj en tagg för den polygon som du planerar att skapa.
1. Välj verktyget **Draw polygon region tool** Draw ![ polygon region tool (Rita polygonregion) ](./media/how-to-label-images/polygon-tool.png) eller välj "P".
1. Klicka för varje punkt i polygonen.  När du har slutfört figuren dubbelklickar du för att slutföra.

    :::image type="content" source="media/how-to-label-images/polygon.gif" alt-text="Skapa polygoner för katt och hund":::

Om du vill ta bort en polygon klickar du på det X-formade målet som visas bredvid polygonen efter skapandet.

Om du vill ändra taggen för en polygon väljer du verktyget Flytta **region,** klickar på polygonen och väljer rätt tagg.

Du kan redigera befintliga polygoner. Verktyget **Lås/låsa upp regioner** Redigera polygoner med verktyget ![ lås-/upplåsningsregioner eller ](./media/how-to-label-images/lock-bounding-boxes-tool.png) "L" växlar det beteendet. Om regioner är låsta kan du bara ändra form eller plats för en ny polygon.

Använd verktyget **Lägg till eller ta bort polygonpunkter.** Det här är ikonen för att lägga till eller ta bort ![ polygonpunkter.](./media/how-to-label-images/add-remove-points-tool.png) eller "U" för att justera en befintlig polygon. Klicka på polygonen för att lägga till eller ta bort en punkt. Om du inte kan redigera en region har du förmodligen lagt till verktyget **Lås/låsa upp** regioner.

Om du *vill ta* bort alla polygoner i den aktuella avbildningen väljer du verktyget Ta bort **alla** regioner med verktyget Ta bort ![ alla ](./media/how-to-label-images/delete-regions-tool.png) regioner.

När du har skapat polygonerna  för en bild väljer du Skicka för att spara ditt arbete, annars sparas inte pågående arbete.

## <a name="finish-up"></a>Slutför

När du skickar en sida med taggade data tilldelar Azure nya omärkta data till dig från en arbetskö. Om det inte finns några tillgängliga omärkta data får du ett meddelande som visar detta tillsammans med en länk till portalens startsida.

När du är klar med etiketterna väljer du ditt namn i det övre högra hörnet på etiketteringsportalen och väljer **sedan logga ut**. Om du inte loggar ut kommer Azure så småningom att "time out" och tilldela dina data till en annan etiketterare.

## <a name="next-steps"></a>Nästa steg

* Lär dig att [träna bildklassificeringsmodeller i Azure](./tutorial-train-models-with-aml.md)


