---
title: Skapa en lösning utan kod i Azure percept Studio
description: Lär dig hur du skapar en lösning utan kod i Azure percept Studio och distribuerar den till din Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 49e5db729dab7abaa440b1adf6a61e9e52a1efbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023137"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Skapa en lösning utan kod i Azure percept Studio

Med Azure percept Studio kan du bygga och distribuera anpassade lösningar för dator visioner, utan kodning krävs. I den här artikeln kommer du att:

- Skapa ett syn projekt i [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)
- Samla in utbildnings avbildningar med din devkit
- Namnge utbildnings avbildningarna i [Custom vision](https://www.customvision.ai/)
- Träna din anpassade objekt identifierings-eller klassificerings modell
- Distribuera din modell till din devkit
- Förbättra din modell genom att ställa in omträning

Den här självstudien är lämplig för utvecklare med liten till ingen AI-upplevelse och de är bara att komma igång med Azure percept.

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- Installations upplevelse för Azure percept DK: du anslöt din devkit till ett Wi-Fi nätverk, skapat ett IoT Hub och anslöt din devkit till IoT Hub

## <a name="create-a-vision-prototype"></a>Skapa en vision-prototyp

1. Starta webbläsaren och gå till [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. På sidan Översikt klickar du på fliken **demonstrationer & självstudier** .  :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Översikts skärm för Azure percept Studio." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. Klicka på **skapa en vision-prototyp** under **synskadade och demonstrationer**.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Demonstrationer och självstudier på Azure percept Studio." lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. På sidan **ny Azure-Percept Custom vision prototyp** gör du följande:

    1. I rutan **projekt namn** anger du ett namn för din vision-prototyp.

    1. Ange en beskrivning av vision-prototypen i rutan **projekt Beskrivning** .

    1. Välj **Azure PERCEPT DK** under den nedrullningsbara menyn **enhets typ** .

    1. Välj en resurs under List menyn **resurs** eller klicka på **skapa en ny resurs**. Om du väljer att skapa en ny resurs gör du följande i fönstret **skapa** :
        1. Ange ett namn för den nya resursen.
        1. Välj din Azure-prenumeration.
        1. Välj en resurs grupp eller skapa en ny.
        1. Välj önskad region.
        1. Välj din pris nivå (vi rekommenderar S0).
        1. Klicka på **skapa** längst ned i fönstret.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Skapa resurs fönster.":::

    1. För **projekt typ** väljer du om ditt vision-projekt ska utföra objekt identifiering eller bild klassificering. Klicka på **Hjälp mig att välja** om du vill ha mer information om projekt typerna.

    1. För **optimering** väljer du om du vill optimera projektet för precision, låg nätverks fördröjning eller en balans mellan båda.

    1. Klicka på knappen **Skapa**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Skapa anpassad prototyp skärm.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Anslut en enhet till projektet och avbilda avbildningar

När du har skapat en vision-lösning måste du lägga till din devkit och dess motsvarande IoT Hub.

1. Slå på din devkit.

1. I list rutan **IoT Hub** väljer du den IoT-hubb som din devkit var ansluten till under OOBE.

1. I list rutan **enheter** väljer du din devkit.

Sedan måste du antingen läsa in bilder eller avbilda avbildningar för att träna din AI-modell. Vi rekommenderar att ladda upp minst 30 bilder per typ av tagg. Om du till exempel vill bygga en hund-och Cat-detektor måste du ladda upp minst 30 avbildningar av hundar och 30 avbildningar av katter. Gör så här för att avbilda avbildningar med visionen som devkit:

1. I fönstret **Skapa avbildning** väljer du **Visa enhets ström** för att visa den vision som video Stream.

1. Kontrol lera video strömmen och se till att din vision är korrekt justerad för att ta träna bilder. Gör justeringar vid behov.

1. I fönstret **Skapa avbildning** klickar du på **ta foto**.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Skärm för avbildnings insamling.":::

1. Du kan också ställa in en automatisk avbildnings avbildning för att samla in en stor mängd avbildningar i taget genom att markera rutan **Automatisk avbildnings insamling** . Välj önskad bild hastighet under **bild frekvensen** och det totala antalet avbildningar som du vill samla in under **mål**. Klicka på **ange automatisk fångst** för att starta den automatiska avbildnings processen.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="List Rute meny för automatisk bild hämtning.":::

När du har tillräckligt många foton klickar du på **Nästa: Tagga bilder och modell utbildning** längst ned på skärmen. Alla avbildningar kommer att sparas i [Custom vision](https://www.customvision.ai/).

> [!NOTE]
> Observera att bild filens storlek inte överstiger 6 MB om du väljer att överföra utbildnings avbildningar direkt till Custom Vision.

## <a name="tag-images-and-train-your-model"></a>Tagga bilder och träna din modell

Lägg till etiketter till dina avbildningar innan du tränar din modell.

1. På sidan **Tagga bilder och modell utbildning** klickar du på **öppna projekt i Custom vision**.

1. Till vänster på sidan **Custom vision** klickar du på **omärkta** under **taggar** för att visa de bilder som du precis har samlat in i föregående steg. Välj en eller flera av dina otaggade bilder.

1. I fönstret **bild information** klickar du på bilden för att börja tagga. Om du har valt objekt identifiering som projekt typ, måste du också rita en [avgränsnings ruta](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images) runt vissa objekt som du vill tagga. Justera avgränsnings rutan efter behov. Skriv taggen Object och klicka **+** för att använda taggen. Om du till exempel skapar en vision-lösning som meddelar dig när en butiks hylla behöver återlagerförs, lägger du till taggen "Tom hylla" till bilder av tomma hyllor och lägger till taggen "fullständig hylla" i bilder av helt lager hyllor. Upprepa för alla otaggade bilder.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Skärmen bild taggning i Custom Vision.":::

1. När du har taggat bilderna klickar du på **X** -ikonen i det övre högra hörnet i fönstret. Klicka på **Taggad** under **taggar** för att visa alla dina nyligen märkta bilder.

1. När bilderna har etiketter ATS är du redo att träna din AI-modell. Det gör du genom att klicka på **träna** nära överst på sidan. Du måste ha minst 15 bilder per typ för att träna din modell (vi rekommenderar att du använder minst 30). Träning tar vanligt vis cirka 30 minuter, men det kan ta längre tid om din avbildnings uppsättning är mycket stor.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Val av träna bild med träna knapp markerat.":::

1. När utbildningen har slutförts visas din modell prestanda på skärmen. Mer information om hur du utvärderar dessa resultat finns i [dokumentationen för modell utvärderingen](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector). Efter utbildningen kanske du också vill [testa din modell](../cognitive-services/custom-vision-service/test-your-model.md) på ytterligare avbildningar och omträna vid behov. Varje gången du tränar din modell kommer den att sparas som en ny iteration. Referens till [Custom vision-dokumentationen](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) innehåller mer information om hur du kan förbättra modell prestanda.

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Modell inlärnings resultat.":::

    > [!NOTE]
    > Om du väljer att testa din modell på ytterligare avbildningar i Custom Vision bör du notera att den inte får överskrida storleken på bild filen.

När du är nöjd med modellens prestanda stänger du Custom Vision genom att stänga fliken webbläsare.

## <a name="deploy-your-ai-model"></a>Distribuera din AI-modell

1. Gå tillbaka till din Azure percept Studio-flik och klicka på **Nästa: utvärdera och distribuera** längst ned på skärmen.

1. I fönstret **utvärdera och distribuera** visas prestandan för den valda modellen iteration. Välj den iteration som du vill distribuera till din devkit under List menyn **modell iteration** och klicka på **distribuera modell** längst ned på skärmen.

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Skärm för modell distribution." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. När du har distribuerat din modell kan du Visa din enhets video ström för att se hur din modell inferencing fungerar.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="Enhets ström som visar hörlurs detektor i åtgärd.":::

När du har stängt det här fönstret kan du gå tillbaka och redigera ditt vision-projekt när som helst genom att klicka på **syn** under **AI-projekt** på Start sidan för Azure percept Studio och välja namnet på ditt vision-projekt.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Sidan syn." lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Förbättra din modell genom att ställa in omträning

När du har tränat din modell och distribuerat den till enheten kan du förbättra modell prestanda genom att ställa in omskolnings parametrar för att samla in fler tränings data. Den här funktionen används för att förbättra en utbildad modells prestanda genom att ge dig möjlighet att avbilda avbildningar baserat på ett sannolikhets intervall. Du kan till exempel ange att enheten bara ska fånga utbildnings bilder när sannolikheten är låg. Här följer några [ytterligare anvisningar](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) om hur du lägger till fler avbildningar och hur du balanserar utbildnings data.

1. Om du vill konfigurera omskolning går du tillbaka till **projektet**, sedan till **projekt Sammanfattning**
1. På fliken **avbildnings insamling** väljer du **Automatisk avbildnings insamling** och **Konfigurera omträning**.
1. Konfigurera den automatiserade avbildnings fångsten för att samla in en stor mängd avbildningar i taget genom att markera rutan **Automatisk bild tagning** .
1. Välj önskad bild hastighet under **bild frekvensen** och det totala antalet avbildningar som du vill samla in under **mål**.
1. I avsnittet **Konfigurera omskolning** väljer du den iteration som du vill samla in fler utbildnings data för och väljer sedan sannolikhets intervallet. Endast bilder som uppfyller sannolikhets frekvensen laddas upp till projektet.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="bild tagning.":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny Azure-resurs för den här självstudien och du inte längre vill utveckla eller använda din vision lösning utför du följande steg för att ta bort resursen:

1. Gå till [Azure-portalen](https://ms.portal.azure.com/).
1. Klicka på **alla resurser**.
1. Klicka på kryss rutan bredvid resursen som skapades under den här självstudien. Resurs typen visas som **Cognitive Services**.
1. Klicka på ikonen **ta bort** längst upp på skärmen.

## <a name="video-walkthrough"></a>Videogenomgång

En visuell genom gång av stegen som beskrivs ovan finns i följande video:

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>Nästa steg

Härnäst kan du läsa mer om hur du hittar information om ytterligare funktioner för visioner i Azure percept Studio.

<!--
Add links to how-to articles and oobe article.
-->