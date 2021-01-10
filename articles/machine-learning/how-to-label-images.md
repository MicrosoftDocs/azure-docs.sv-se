---
title: Tagga bilder i ett etikett projekt
title.suffix: Azure Machine Learning
description: Lär dig hur du använder data märknings verktygen för att snabbt förbereda data för en Machine Learning i ett Azure Machine Learning etiketting-projekt.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: eb8c69a6bf009543e104a472469c243638726f60
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060375"
---
# <a name="tag-images-in-a-labeling-project"></a>Tagga bilder i ett etikett projekt 

När projekt administratören har [skapat ett data etiketts projekt](./how-to-create-labeling-projects.md#create-a-data-labeling-project) i Azure Machine Learning kan du använda etikett verktyget för att snabbt förbereda data för ett Machine Learning-projekt. I den här artikeln beskrivs:

> [!div class="checklist"]
> * Få åtkomst till dina etikett projekt
> * Etikett verktygen
> * Så här använder du verktygen för vissa etikett uppgifter

## <a name="prerequisites"></a>Förutsättningar

* Ett [Microsoft-konto](https://account.microsoft.com/account) eller ett Azure Active Directory konto för organisationen och projektet
* Deltagar nivå åtkomst till arbets ytan som innehåller ett etikett projekt.

## <a name="sign-in-to-the-workspace"></a>Logga in på arbets ytan

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. Välj den prenumeration och arbets yta som innehåller ett etikett projekt.  Hämta den här informationen från projekt administratören.

1. Välj **data etiketter** till vänster för att hitta projektet.  

## <a name="understand-the-labeling-task"></a>Förstå etikettens uppgift

I tabellen med data märknings projekt väljer du **etikett länk** för projektet.

Du ser anvisningar som är speciella för ditt projekt. De förklarar vilken typ av data du använder, hur du ska fatta beslut och annan relevant information. När du har läst den här informationen går du till början av sidan och väljer **aktiviteter**.  Eller på sidans nederkant väljer du **Starta etikettering**.

## <a name="common-features-of-the-labeling-task"></a>Vanliga funktioner i aktivitets etiketten

I alla bild märknings uppgifter väljer du en lämplig tagg eller taggar från en mängd som anges av projekt administratören. Du kan välja de första nio taggarna med hjälp av siffer tangenterna på tangent bordet.  

I bild klassificerings aktiviteter kan du välja att visa flera avbildningar samtidigt. Använd ikonerna ovanför bild ytan för att välja layouten. 

Om du vill markera alla de bilder som visas samtidigt använder du **Markera alla**. Om du vill välja enskilda bilder använder du knappen cirkulär markering i det övre högra hörnet i bilden. Du måste välja minst en bild för att kunna använda en tagg. Om du väljer flera avbildningar, kommer alla Taggar som du väljer att tillämpas på alla valda avbildningar.

Här har vi valt en två-till-två-layout och är på väg att använda taggen "däggdjur" på avbildningarna av Bearer och Orca. Bilden av hajar är redan Taggad som "Cartilaginous fiskar" och Iguana har inte taggats än.

![Flera bildlayouter och val](./media/how-to-label-images/layouts.png)

> [!Important] 
> Växla bara layouter när du har en ny sida med omärkta data. När du växlar layouter rensas sidans pågående märknings arbete.

Azure aktiverar knappen **Skicka** när du har taggat alla bilder på sidan. Välj **Skicka** för att spara ditt arbete.

När du har skickat taggar för data till handen uppdaterar Azure sidan med en ny uppsättning avbildningar från arbets kön.

### <a name="assisted-machine-learning-preview"></a>Assisterad maskin inlärning (för hands version) 

> [!IMPORTANT]
> Assisterad maskin inlärning är för närvarande en offentlig för hands version.
> För hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning-algoritmer kan utlösas. Om dessa algoritmer är aktiverade i projektet kan du se följande:

* När vissa bilder har märkts kan du se uppgifter som är **klustrade** överst på skärmen bredvid projekt namnet.  Det innebär att bilder grupperas tillsammans för att presentera liknande bilder på samma sida.  I så fall kan du växla till en av vyerna för flera bilder för att dra nytta av grupperingen.  

* Vid ett senare tillfälle kan du se uppgifter som är **förmärkta** bredvid projekt namnet.  Bilderna visas sedan med en föreslagen etikett som kommer från en maskin inlärnings klassificerings modell. Ingen Machine Learning-modell har 100% noggrannhet. Vi använder bara bilder för vilka modellen är säker, men dessa bilder kan fortfarande vara felaktigt märkta.  När du ser dessa etiketter korrigerar du eventuella fel etiketter innan du skickar in sidan.  

* För objekt identifierings modeller kan du se avgränsnings rutor och etiketter som redan finns.  Korrigera eventuella fel innan du skickar in sidan.

I synnerhet i ett etikett projekt kan Machine Learning-modellen bara vara tillräckligt exakt för att förmärka en liten del av avbildningarna. När de här bilderna har märkts, kommer etiketts projektet att återgå till manuell etikettering för att samla in mer data för nästa runda av modell träning. Med tiden blir modellen mer säker på en högre andel av avbildningarna, vilket leder till fler förmärknings uppgifter senare i projektet.

## <a name="tag-images-for-multi-class-classification"></a>Tagga bilder för klassificering med flera klasser

Om ditt projekt är av typen "bild klassificering med flera klasser", tilldelar du en enskild tagg till hela bilden. Om du vill granska riktningarna när som helst går du till sidan **instruktioner** och väljer **Visa detaljerade instruktioner**.

Om du upptäcker att du har gjort ett misstag efter att du har tilldelat en tagg till en avbildning kan du åtgärda det. Välj "**X**" på etiketten som visas under bilden för att ta bort taggen. Eller Välj avbildningen och välj en annan klass. Det nyligen markerade värdet ersätter den tidigare tillämpade taggen.

## <a name="tag-images-for-multi-label-classification"></a>Tagga bilder för klassificering med flera etiketter

Om du arbetar med ett projekt av typen "bild klassificering med flera etiketter" ska du använda en *eller flera* Taggar i en bild. Om du vill se de projektspecifika riktningarna väljer du **instruktioner** och går för att **Visa detaljerade instruktioner**.

Välj den bild som du vill etikettera och välj sedan taggen. Taggen tillämpas på alla valda avbildningar och sedan avmarkeras bilderna. Om du vill använda fler taggar måste du markera bilderna igen. Följande animering visar flera etikett markeringar:

1. **Markera allt** används för att tillämpa taggen "oceanen".
1. En enda bild är markerad och märkt "närbild".
1. Tre bilder markeras och märks som "bred vinkel".

![Animering visar flöde för multilabel](./media/how-to-label-images/multilabel.gif)

Du korrigerar ett misstag genom att klicka på "**X**" för att ta bort en enskild tagg eller välja bilderna och sedan välja taggen, vilket raderar taggen från alla valda bilder. Det här scenariot visas här. Om du klickar på "land" tas taggen bort från de två valda bilderna.

![En skärm bild visar flera avmarkeringar](./media/how-to-label-images/multiple-deselection.png)

Azure aktiverar bara knappen **Skicka** när du har tillämpat minst en tagg på varje bild. Välj **Skicka** för att spara ditt arbete.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Tagga bilder och ange avgränsningsrutor för objektidentifiering

Om ditt projekt är av typen objekt identifiering (avgränsnings rutor) anger du en eller flera markerings rutor i bilden och använder en tagg för varje ruta. Bilder kan ha flera avgränsnings rutor, var och en med en enda tagg. Använd **Visa detaljerade instruktioner** för att avgöra om flera avgränsnings rutor används i projektet.

1. Välj en tagg för den avgränsnings ruta som du planerar att skapa.
1. Välj rektangulär **Box-** verktyget ![ ](./media/how-to-label-images/rectangular-box-tool.png) eller välj "R".
3. Klicka och dra diagonalt över ditt mål för att skapa en grov markerings ruta. Dra i kanterna eller hörnen för att justera avgränsnings rutan.

![Skapa avgränsnings ruta](./media/how-to-label-images/bounding-box-sequence.png)

Om du vill ta bort en avgränsnings ruta klickar du på det X-formade mål som visas bredvid markerings rutan när du har skapat den.

Du kan inte ändra taggen för en befintlig avgränsnings ruta. Om du gör en tagg-tilldelning av misstag måste du ta bort avgränsnings rutan och skapa en ny med rätt tagg.

Som standard kan du redigera befintliga avgränsnings rutor. Verktyget **Lock/unlock regioner** ![ Lock/unlock regions ](./media/how-to-label-images/lock-bounding-boxes-tool.png) eller "L" växlar beteendet. Om regionerna är låsta kan du bara ändra form eller plats för en ny avgränsnings ruta.

Använd **områdes manipulations** verktyget ![ det här är en område manipulations verktygs ikon – fyra pilar som pekar utåt från mitten, uppåt, höger, nedåt och till vänster.](./media/how-to-label-images/regions-tool.png) eller "M" för att justera en befintlig avgränsnings ruta. Justera figuren genom att dra i kanterna eller hörnen. Klicka på insidan för att kunna dra hela avgränsnings rutan. Om du inte kan redigera en region har du förmodligen växlat **Lås/Lås upp** regions verktyget.

Använd mallen för **mallbaserade Box** ![ -verktyget ](./media/how-to-label-images/template-box-tool.png) eller "T" för att skapa flera avgränsnings rutor av samma storlek. Om bilden inte har några markerings rutor och du aktiverar mallbaserade rutor, kommer verktyget att producera 50-för-50-pixel-rutor. Om du skapar en avgränsnings ruta och sedan aktiverar mallbaserade rutor, blir alla nya avgränsnings rutor storleken på den sista rutan som du skapade. Du kan ändra storlek på mallbaserade rutor efter placering. Att ändra storlek på en mall-baserad ruta ändrar bara storlek på den aktuella rutan.

Om du vill ta bort *alla* avgränsnings rutor i den aktuella bilden väljer du verktyget **ta bort alla regioner** ![ ta bort regioner ](./media/how-to-label-images/delete-regions-tool.png) .

När du har skapat markerings rutorna för en bild väljer du **Skicka** för att spara ditt arbete, eller så sparas inte ditt arbete.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Tagga bilder och ange polygoner för bild segmentering 

Om ditt projekt är av typen "instans segmentering (Polygon)", anger du en eller flera polygoner i bilden och använder en tagg för varje polygon. Bilder kan ha flera avgränsnings polygoner, var och en med en enda tagg. Använd **Visa detaljerade instruktioner** för att avgöra om flera avgränsnings polygoner används i projektet.

1. Välj en tagg för den polygon som du planerar att skapa.
1. Välj verktyget **Rita polygon region** verktyget ![ Rita polygon region ](./media/how-to-label-images/polygon-tool.png) eller välj "P".
1. Klicka för varje punkt i polygonen.  När du har slutfört formen, dubbelklickar du på den för att slutföra.

    :::image type="content" source="media/how-to-label-images/polygon.gif" alt-text="Skapa polygoner för katt och hund":::

Om du vill ta bort en polygon klickar du på det X-formade mål som visas bredvid polygonen när den har skapats.

Om du vill ändra taggen för en polygon väljer du verktyget **Flytta region** , klickar på polygonen och väljer rätt tagg.

Du kan redigera befintliga polygoner. Verktyget **Lås/Lås upp regioner** ![ redigerar polygoner med verktyget lås/Lås upp regioner ](./media/how-to-label-images/lock-bounding-boxes-tool.png) eller "L" och växlar beteendet. Om regionerna är låsta kan du bara ändra form eller plats för en ny polygon.

Använd verktyget **Lägg till eller ta bort polygoner** ![ Detta är verktygs ikonen Lägg till eller ta bort polygon-punkter.](./media/how-to-label-images/add-remove-points-tool.png) eller "U" för att justera en befintlig polygon. Klicka på polygonen för att lägga till eller ta bort en punkt. Om du inte kan redigera en region har du förmodligen växlat **Lås/Lås upp** regions verktyget.

Om du vill ta bort *alla* polygoner i den aktuella bilden väljer du verktyget **ta** bort alla regioner ![ ta bort alla regioner ](./media/how-to-label-images/delete-regions-tool.png) .

När du har skapat polygonerna för en bild väljer du **Skicka** för att spara ditt arbete, eller så sparas inte ditt arbete.

## <a name="finish-up"></a>Slutför

När du skickar en sida med taggade data tilldelar Azure nya omärkta data till dig från en arbetskö. Om det inte finns några fler omärkta data tillgängliga får du ett meddelande som anger detta tillsammans med en länk till portalens start sida.

När du är klar med etiketter väljer du ditt namn i det övre högra hörnet i etikett portalen och väljer sedan **utloggning**. Om du inte loggar ut så kommer Azure att "tid ut" och tilldela dina data till en annan Labeler.

## <a name="next-steps"></a>Nästa steg

* Lär dig att [träna avbildnings klassificerings modeller i Azure](./tutorial-train-models-with-aml.md)


