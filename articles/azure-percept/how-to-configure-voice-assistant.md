---
title: Konfigurera programmet röst assistent med Azure IoT Hub
description: Konfigurera programmet röst assistent med Azure IoT Hub
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: ec3e06b2d161785b5e6978cdf4cc6415fc0eb592
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663884"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>Konfigurera programmet röst assistent med Azure IoT Hub

I den här artikeln beskrivs hur du konfigurerar röst assistent programmet med IoT Hub. En stegvis själv studie kurs som vägleder dig genom processen att skapa en röst assistent med en demo mall finns i [skapa en no-Code-röst assistent med Azure percept Studio och Azure percept-ljud](./tutorial-no-code-speech.md).

## <a name="update-your-voice-assistant-configuration"></a>Uppdatera din röst assistents konfiguration

1. Öppna [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) och skriv **IoT Hub** i Sök fältet. Klicka på ikonen för att öppna sidan IoT Hub.

1. På sidan IoT Hub väljer du IoT Hub som enheten har tillhandahållits till.

1. Välj **IoT Edge** under **Automatisk enhets hantering** i den vänstra navigerings menyn för att visa alla enheter som är anslutna till din IoT Hub.

1. Välj den enhet som ditt röst assistent program har distribuerats till.

1. Klicka på **Ange moduler**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="Avbildning.":::

1. Kontrol lera att följande post finns i avsnittet **container Registry autentiseringsuppgifter** . Lägg till autentiseringsuppgifter om det behövs.

    |Namn|Adress|Användarnamn|Lösenord|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. I avsnittet **IoT Edge modules** väljer du **azureearspeechclientmodule**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="Avbildning.":::

1. Klicka på fliken **Inställningar för modul** . Verifiera följande konfiguration:

    |Bild-URI|Starta om princip|Önskad status|
    |---------|--------------|--------------|
    |azureedgedevices.azurecr.io/azureearspeechclientmodule:preload-devkit |alltid|kör|

    Om inställningarna inte matchar redigerar du dem och klickar på **Uppdatera**.

1. Klicka på fliken **miljövariabler** . Kontrol lera att inga miljövariabler har definierats.

1. Klicka på fliken **container skapa alternativ** . Kontrol lera att dina **HostConfig** -inställningar matchar de som visas nedan. Om de inte matchar, uppdaterar du inställningarna.

    ```
    {
        "HostConfig": {
            "Privileged": true,
            "Binds": [
                "/dev:/dev"
            ]
        }
    }
    ```

1. Klicka på fliken **dubbla inställningar för modul** . Uppdatera **speechConfigs** -avsnittet enligt följande:

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedspeechscenarios.blob.core.windows.net/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > Nyckelordet som används ovan är ett standard nyckelord som är offentligt tillgängligt. Om du vill använda din egen, kan du lägga till ditt eget anpassade nyckelord genom att ladda upp en tabell fil som skapats till Blob Storage. Blob Storage måste konfigureras med antingen anonym åtkomst till behållare eller anonym BLOB-åtkomst.

## <a name="how-to-find-out-appid-key-and-region"></a>Så här hittar du appId, nyckel och region

För att hitta ditt **AppID**, **nyckel** och **region** går du till [tal Studio](https://speech.microsoft.com/):

1. Logga in och välj lämplig tal resurs.
1. På Start sidan för **tal Studio** klickar du på **anpassade kommandon** under **röst assistenter**.
1. Välj mål projekt.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Avbildning.":::

1. Klicka på **Inställningar** i den vänstra meny panelen.
1. **AppID** och **nyckeln** kommer att finnas på fliken **allmänna** inställningar.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="Avbildning.":::

1. Du hittar din **region** genom att öppna fliken **Luis-resurser** i inställningarna. Val av **resurs** val kommer att innehålla regions information.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Avbildning.":::

1. När du har angett din **speechConfigs** -information klickar du på **Uppdatera**.

1. Klicka på fliken **vägar** överst på sidan **Ange moduler** . Se till att du har en väg med följande värde:

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    Lägg till vägen om den inte finns.

1. Klicka på **Granska + Skapa**.

1. Klicka på **Skapa**.

## <a name="next-steps"></a>Nästa steg

När du har uppdaterat din röst assistents konfiguration återgår du till demonstrationen i Azure percept Studio för att interagera med programmet.