---
title: Skapa en röst assistent med Azure percept DK och Azure percept-ljud
description: Lär dig hur du skapar och distribuerar en tal lösning utan kod till din Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 3c5e6fd62e4f4db9ccc1306d32d09b8338cbf963
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098034"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Skapa en röst assistent med Azure percept DK och Azure percept-ljud

I den här självstudien får du skapa en röst assistent från en mall som ska användas med ditt Azure percept DK-och Azure percept-ljud. Röst assistenten körs i [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) och innehåller ett urval av Voice-styrda virtuella objekt. Du styr ett objekt genom att säga ditt nyckelord, som är ett ord eller en kort fras som aktiverar enheten, följt av ett kommando. Varje mall svarar på en uppsättning kommandon.

Den här guiden vägleder dig genom processen med att konfigurera dina enheter, skapa en röst assistent och nödvändiga resurser för [tal tjänster](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview) , testa din röst assistent, konfigurera ditt nyckelord och skapa anpassade nyckelord.

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- Azure percept-ljud
- Högtalare eller hörlurar som kan ansluta till 3,5 mm-ljud uttag (valfritt)
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Installations upplevelse för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): du anslöt din devkit till ett Wi-Fi nätverk, skapat ett IoT Hub och anslöt din devkit till IoT Hub
- [Installation av Azure percept-ljud](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>Skapa en röst assistent med en tillgänglig mall

1. Gå till [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Öppna fliken **demonstrationer & själv studie kurser** .

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Skärm bild av Azure Portal start sida.":::

1. Klicka på **prova röst assistent mallar** under **tal självstudier och demonstrationer**. Då öppnas ett fönster på höger sida av skärmen.

1. Gör följande i fönstret:

    1. I list rutan **IoT Hub** väljer du den IoT-hubb som din devkit är ansluten till.

    1. I list rutan **enhet** väljer du din devkit.

    1. Välj en av de tillgängliga röst assistent mallarna.

    1. Klicka på kryss rutan **Jag godkänner villkoren & villkor för det här projektet** .

    1. Klicka på **Skapa**.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Skärm bild av röst assistentens mall skapas.":::

1. När du klickar på **skapa**, öppnar portalen ett annat fönster för att skapa din tal tema resurs. Gör följande i fönstret:

    1. Välj din Azure-prenumeration i rutan **prenumeration** .

    1. Välj önskad resurs grupp på list menyn **resurs grupp** . Om du vill skapa en ny resurs grupp som ska användas med röst assistenten klickar du på **skapa** under List menyn och följer anvisningarna.

    1. Ange ett namn för **Application-prefix**. Detta är prefixet för ditt projekt och ditt anpassade kommando namn.

    1. Under **region** väljer du den region som resurserna ska distribueras till.

    1. Under **Luis förutsägelse pris nivå** väljer du **standard** (den kostnads fria nivån stöder inte tal begär Anden).

    1. Klicka på knappen **Skapa**. Resurser för röst assistent programmet kommer att distribueras till din prenumeration.

        > [!WARNING]
        > Stäng **inte** fönstret förrän du har distribuerat resursen. Om du stänger fönstret för tidigt kan röst assistenten resultera i ett oväntat beteende. När din resurs har distribuerats visas demon.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Skärm bild av fönstret för val av prenumeration och resurs grupp.":::

## <a name="test-out-your-voice-assistant"></a>Testa din röst assistent

Om du vill interagera med din röst assistent, säger du nyckelordet följt av ett kommando. När öronen känner igen ditt nyckelord, avger enheten en CHIME (som du kan höra om en talare eller hörlurar är anslutna) och lysdioderna blinkar blå. Lysdioderna växlar till racing blått medan kommandot bearbetas. Röst assistentens svar på kommandot skrivs ut i text i demonstrations fönstret och släpps audibly via din talare/hörlurar. Standard nyckelordet (anges bredvid **anpassat nyckelord**) är inställt på "dator" och varje mall har en uppsättning kompatibla kommandon som gör att du kan interagera med virtuella objekt i demonstrations fönstret. Om du till exempel använder service-eller sjukvårds demon säger du "dator, sätt på TV" för att aktivera den virtuella TV: n.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Skärm bild av service demo-fönstret.":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Service-och sjukvårds demonstrations kommandon

Både sjukvårds-och service-demonstrationerna har virtuella TV-apparater, lampor, rullgardiner och termostater som du kan interagera med. Följande kommandon (och ytterligare variationer) stöds:

* "Aktivera/inaktivera ljuset."
* "Aktivera/inaktivera TV: n."
* "Aktivera/inaktivera AC."
* "Öppna/Stäng rullgardiner."
* "Ange temperatur till X grader." (X är den önskade temperaturen, t. ex. 75.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Skärm bild av sjukvårds demonstrations fönstret.":::

### <a name="automotive-demo-commands"></a>Autobils demo kommandon

Den fordonsbaserade demonstrationen har en virtuell plats som är varm, avfrostning och termostat som du kan interagera med. Följande kommandon (och ytterligare variationer) stöds:

* "Aktivera/inaktivera avfrosten."
* "Aktivera/inaktivera platsens varmare."
* "Ange temperatur till X grader." (X är den önskade temperaturen, t. ex. 75.)
* "Öka/minska temperaturen efter Y-grader."


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Skärm bild av fönstret för bil demonstrations fönstret.":::

### <a name="inventory-demo-commands"></a>Kommandon för inventerings demonstration

Inventerings demonstrationen har ett urval av virtuella blå, gula och gröna rutor som du kan använda tillsammans med en virtuell inventerings app. Följande kommandon (och ytterligare variationer) stöds:

* Rutorna Lägg till/ta bort X. (X är antalet rutor, t. ex. 4.)
* "Order/leverera X-rutor".
* "Hur många rutor finns i lager?"
* "Antal Y-rutor". (Y är färgen på rutorna, t. ex. gul.)
* "Leverera allt i lager."


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Skärm bild av inventerings demonstrations fönstret.":::

## <a name="configure-your-keyword"></a>Konfigurera ditt nyckelord

Du kan anpassa nyckelord för ditt röst assistent program.

1. Klicka på **ändra** bredvid **anpassat nyckelord** i demonstrations fönstret.

1. Välj ett av de tillgängliga nyckelorden. Du kan välja bland ett urval av exempel nyckelord och eventuella anpassade nyckelord som du har skapat.

1. Klicka på **Spara**.

### <a name="create-a-custom-keyword"></a>Skapa ett anpassat nyckelord

Du kan skapa egna nyckelord för röst programmet. Träning för ditt anpassade nyckelord kan slutföras på bara några minuter.

1. Klicka på **+ skapa anpassat nyckelord** nästan överst i demonstrations fönstret. 

1. Ange önskat nyckelord, som kan vara ett enstaka ord eller en kort fras.

1. Välj din **tal resurs** (detta visas bredvid **anpassat kommando** i demonstrations fönstret och innehåller ditt programprefix).

1. Klicka på **Spara**. 

## <a name="create-a-custom-command"></a>Skapa ett anpassat kommando

Portalen innehåller också funktioner för att skapa anpassade kommandon med befintliga tal resurser. "Anpassat kommando" syftar på själva programmet röst assistent, inte ett speciellt kommando i det befintliga programmet. Genom att skapa ett anpassat kommando skapar du ett nytt tal projekt som du måste utveckla i [tal Studio](https://speech.microsoft.com/).

Om du vill skapa ett nytt anpassat kommando i demonstrations fönstret klickar du på **+ skapa anpassat kommando** överst på sidan och gör följande:

1. Ange ett namn för ditt anpassade kommando.

1. Ange en beskrivning av ditt projekt (valfritt).

1. Välj önskat språk.

1. Välj din tal resurs.

1. Välj din LUIS-resurs.

1. Välj en LUIS som skapar resurs eller skapa en ny.

1. Klicka på **Skapa**.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Skärm bild av fönstret för att skapa anpassade kommandon.":::

När du har skapat ett anpassat kommando måste du gå till [tal Studio](https://speech.microsoft.com/) för ytterligare utveckling. Om du öppnar tal Studio och inte ser ditt anpassade kommando, följer du dessa steg:

1. Klicka på **tal** under **AI-projekt** på den vänstra panelen i Azure percept Studio.

1. Välj fliken **kommandon** .

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Skärm bild av en lista över anpassade kommandon som kan redige ras.":::

1. Välj det anpassade kommandot som du vill utveckla. Detta öppnar projektet i tal Studio.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Skärm bild av Start skärmen för tal Studio.":::

Mer information om hur du utvecklar anpassade kommandon finns i [dokumentationen om röst tjänsten](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-commands).

## <a name="troubleshooting"></a>Felsökning

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>Röst assistenten skapades men svarar inte på kommandon

Kontrol lera LYSDIODerna på den samspelande tavlan:

* Tre fyllda blå lampor visar att röst assistenten är klar och väntar på nyckelordet.
* Om den mittersta LAMPAn (L02) är vit, slutförs devkit initiering och måste konfigureras med ett nyckelord.
* Om mitt lampa (L02) blinkar vitt är ljudet som inte slutfört initieringen ännu. Initieringen kan ta några minuter att slutföra.

Mer information om LYSDIODs indikatorer finns i [artikeln LED](./audio-button-led-behavior.md).

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>Röst assistenten svarar inte på ett anpassat nyckelord som skapats i tal Studio

Detta kan inträffa om modulen Speech är inaktuell. Följ dessa steg om du vill uppdatera modulen tal till den senaste versionen:

1. Klicka på **enheter** i den vänstra meny panelen på Start sidan för Azure percept Studio.

1. Sök efter och välj din enhet.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Skärm bild av enhets lista i Azure percept Studio.":::

1. I fönstret enhet väljer du fliken **tal** .

1. Kontrol lera versionen av talfunktionen. Om det finns en tillgänglig uppdatering visas en **uppdaterings** knapp bredvid versions numret.

1. Klicka på **Uppdatera** för att distribuera talfunktionen uppdatera. Uppdaterings processen tar vanligt vis 2-3 minuter att slutföra.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med ditt röst assistent program följer du dessa steg för att rensa de tal resurser som du har distribuerat under den här självstudien:

1. Från [Azure Portal](https://portal.azure.com)väljer du **resurs grupper** på den vänstra panelen eller skriver in det i Sök fältet.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Skärm bild av Azure Portal start sidan visar den vänstra meny panelen och resurs grupper.":::

1. Välj din resursgrupp.

1. Välj alla sex resurser som innehåller ditt programprefix och klicka på ikonen **ta bort** på den övre meny panelen.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Skärm bild av tal resurser som valts för borttagning.":::

1. Bekräfta borttagningen genom att skriva **Ja** i bekräftelse rutan, kontrol lera att du har valt rätt resurser och klicka på **ta bort**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Skärm bild av bekräftelse fönstret för borttagning.":::

> [!WARNING]
> Detta tar bort alla anpassade nyckelord som skapats med de tal resurser som du tar bort och demo verktyget för röst assistenten fungerar inte längre.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en tal lösning utan kod kan du prova att skapa en [icke-kodande lösning](./tutorial-nocode-vision.md) för din Azure percept dk.
