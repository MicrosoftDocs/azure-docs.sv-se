---
title: Uppdatera ett kommando från en klient app
titleSuffix: Azure Cognitive Services
description: Lär dig hur du uppdaterar ett kommando från ett klient program.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97560295"
---
# <a name="update-a-command-from-a-client-app"></a>Uppdatera ett kommando från en klient app

I den här artikeln får du lära dig hur du uppdaterar ett pågående kommando från ett klient program.

## <a name="prerequisites"></a>Förutsättningar
> [!div class = "checklist"]
> * En tidigare [skapad app för Anpassade kommandon](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Uppdatera status för ett kommando

Om klient programmet kräver att du uppdaterar statusen för ett pågående kommando utan röst inläsning kan du skicka en händelse för att uppdatera kommandot.

För att illustrera det här scenariot, skicka följande händelse aktivitet för att uppdatera statusen för ett pågående kommando ( `TurnOnOff` ): 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Vi går igenom de viktigaste attributen för den här aktiviteten:

| Attribut | Förklaring |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **bastyp** | Aktiviteten är av typen `"event"` och namnet på händelsen måste vara `"RemoteUpdate"` . |
| **värde** | Attributet `"value"` innehåller de attribut som krävs för att uppdatera det aktuella kommandot. |
| **updatedCommand** | Attributet `"updatedCommand"` innehåller kommandots namn. I det attributet `"updatedParameters"` är en karta med namnen på parametrarna och deras uppdaterade värden. |
| **Avbryt** | Om det pågående kommandot måste avbrytas anger du attributet `"cancel"` till `true` . |
| **updatedGlobalParameters** | Attributet `"updatedGlobalParameters"` är en mappning precis som `"updatedParameters"` , men används för globala parametrar. |
| **processTurn** | Ange attributet till om du vill att det ska gå att bearbeta efter att aktiviteten har `"processTurn"` skickats `true` . |

Du kan testa det här scenariot i portalen med anpassade kommandon:

1. Öppna programmet anpassade kommandon som du skapade tidigare. 
1. Välj **träna** och **testa** sedan.
1. Skicka `turn` .
1. Öppna sido panelen och välj **aktivitets redigeraren**.
1. Skriv och skicka den `RemoteCommand` händelse som anges i föregående avsnitt.
    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar händelsen för ett fjärrkommando.](media/custom-commands/send-remote-command-activity.png)

Observera att värdet för parametern `"OnOff"` har angetts till `"on"` via en aktivitet från klienten i stället för tal eller text.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Uppdatera katalogen för en-parameter för ett kommando

När du konfigurerar en lista över giltiga alternativ för en parameter definieras värdena för parametern globalt för programmet. 

I vårt exempel `SubjectDevice` har parametern en fast lista över vilka värden som stöds, oavsett konversationen.

Om du vill lägga till nya poster i parameterns katalog per konversation kan du skicka följande aktivitet:

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Med den här aktiviteten har du lagt till en post för `"stereo"` till katalogen för parametern `"SubjectDevice"` i kommandot `"TurnOnOff"` .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Skärm bild som visar en katalog uppdatering.":::

Observera några saker:
- Du behöver bara skicka den här aktiviteten en gång (helst, precis som när du har startat en anslutning).
- När du har skickat den här aktiviteten bör du vänta på att händelsen `ParameterCatalogsUpdated` skickas tillbaka till klienten.

## <a name="add-more-context-from-the-client-application"></a>Lägg till mer kontext från klient programmet

Du kan ställa in ytterligare kontext från klient programmet per konversation som senare kan användas i programmet för anpassade kommandon. 

Tänk dig till exempel om det scenario där du vill skicka ID och namn på enheten som är ansluten till programmet för anpassade kommandon.

För att testa det här scenariot ska vi skapa ett nytt kommando i det aktuella programmet:
1. Skapa ett nytt kommando med namnet `GetDeviceInfo` .
1. Lägg till en exempel mening av `get device info` .
1. I slut för ande **regeln lägger du till** en **skicka röst svars** åtgärd som innehåller attributen för `clientContext` .
   ![Skärm bild som visar ett svar för att skicka tal med kontext.](media/custom-commands/send-speech-response-context.png)
1. Spara, träna och testa ditt program.
1. I fönstret test skickar du en aktivitet för att uppdatera klient kontexten.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. Skicka texten `get device info` .
   ![Skärm bild som visar en aktivitet för att skicka klient kontext.](media/custom-commands/send-client-context-activity.png)

Observera några saker:
- Du behöver bara skicka den här aktiviteten en gång (helst, precis som när du har startat en anslutning).
- Du kan använda komplexa objekt för `clientContext` .
- Du kan använda `clientContext` i tal svar för att skicka aktiviteter och för att anropa webb slut punkter.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Uppdatera ett kommando från en webbslutpunkt](./how-to-custom-commands-update-command-from-web-endpoint.md)
