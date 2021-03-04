---
title: Konfigurera programmet röst assistent i Azure percept Studio
description: Konfigurera programmet röst assistent i Azure percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 5e41dea3b47c608b9e82ac57fa1cfe5247ea6cc2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099020"
---
# <a name="managing-your-voice-assistant"></a>Hantera din röst assistent

Den här artikeln beskriver hur du konfigurerar nyckelord och kommandon för ditt röst assistent program i [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Anvisningar om hur du konfigurerar ditt nyckelord i IoT Hub i stället för portalen finns i den här [instruktions artikeln](./how-to-configure-voice-assistant.md).

Om du ännu inte har skapat ett röst assistent program kan du läsa [skapa en not-kod röst assistent med Azure percept Studio och Azure percept-ljud](./tutorial-no-code-speech.md).

## <a name="keyword-configuration"></a>Nyckelords konfiguration

Ett nyckelord är ett ord eller en kort fras som används för att aktivera en röst assistent. Till exempel är "Hej Cortana" ett nyckelord för Cortana-assistenten. Röst aktivering gör att användarna kan börja interagera med produkten helt utan kostnad genom att bara prata med nyckelordet. När din produkt kontinuerligt lyssnar efter nyckelordet bearbetas alla ljud lokalt på enheten tills en identifiering görs för att säkerställa att användar data förblir så privat som möjligt.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfiguration i demonstrations fönstret för röst assistenten

1. Klicka på **ändra** bredvid **anpassat nyckelord** på demonstrations sidan.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Skärm bild av service demo-fönstret.":::

    Om demonstrations sidan inte är öppen går du till sidan enhet (se nedan) och klickar på **testa din röst assistent** under **åtgärder** för att få åtkomst till demon.

1. Välj ett av de tillgängliga nyckelorden och klicka på **Spara** för att tillämpa ändringarna.

1. De tre LYSDIODerna på ljud enheten i Azure percept ändras till ljus blått (ingen blinkar) När konfigurationen är klar och din röst assistent är klar att använda.

### <a name="configuration-within-the-device-page"></a>Konfiguration på enhets Sidan

1. På sidan Översikt i [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)klickar du på **enheter** i den vänstra meny rutan.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Skärm bild av översikts sidan för Azure percept Studio med enheter markerade.":::

1. Välj den enhet som ditt röst assistent program har distribuerats till.

1. Öppna fliken **tal** .

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Skärm bild av sidan Edge-enhet med fliken Speech markerat.":::

1. Klicka på **ändra** bredvid **nyckelord**.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Skärm bild av de tillgängliga funktionerna för tal lösning.":::

1. Välj ett av de tillgängliga nyckelorden och klicka på **Spara** för att tillämpa ändringarna.

1. De tre LYSDIODerna på ljud enheten i Azure percept ändras till ljus blått (ingen blinkar) När konfigurationen är klar och din röst assistent är klar att använda.

## <a name="create-a-custom-keyword"></a>Skapa ett anpassat nyckelord

Med [tal Studio](https://speech.microsoft.com/)kan du skapa ett anpassat nyckelord för din röst assistent. Det tar upp till 30 minuter att träna en grundläggande anpassad nyckelords modell.

I [tal Studio-dokumentationen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) får du vägledning om hur du skapar ett anpassat nyckelord. När det har kon figurer ATS kommer det nya nyckelordet att vara tillgängligt i Project Santa Cruz-portalen för användning med ditt röst assistent program.

## <a name="commands-configuration"></a>Konfiguration av kommandon

Anpassade kommandon gör det enkelt att skapa många röst kommando program som är optimerade för röst-och första interaktions upplevelser. Anpassade kommandon lämpar sig bäst för slut för ande av aktiviteter eller kommando-och kontroll scenarier.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfiguration i demonstrations fönstret för röst assistenten

1. Klicka på **ändra** bredvid **anpassat kommando** på demonstrations sidan. Om demonstrations sidan inte är öppen går du till sidan enhet (se nedan) och klickar på **testa din röst assistent** under **åtgärder** för att få åtkomst till demon.

1. Välj ett av de tillgängliga anpassade kommandona och klicka på **Spara** för att tillämpa ändringarna.

### <a name="configuration-within-the-device-page"></a>Konfiguration på enhets Sidan

1. På sidan Översikt i [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)klickar du på **enheter** i den vänstra meny rutan.

1. Välj den enhet som ditt röst assistent program har distribuerats till.

1. Öppna fliken **tal** .

1. Klicka på **ändra** bredvid **kommando**.

1. Välj ett av de tillgängliga anpassade kommandona och klicka på **Spara** för att tillämpa ändringarna.

## <a name="create-custom-commands"></a>Skapa anpassade kommandon

Med [tal Studio](https://speech.microsoft.com/)kan du skapa anpassade kommandon för röst assistenten som ska köras.

I [dokumentationen för tal Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-custom-commands-application) får du vägledning om hur du skapar anpassade kommandon. När de har kon figurer ATS är dina nya kommandon tillgängliga i Azure percept Studio för användning med ditt röst assistent program.

## <a name="next-steps"></a>Nästa steg

När du har skapat ett röst assistent program kan du prova att utveckla en [kostnads klar lösning](./tutorial-nocode-vision.md) med Azure percept dk.