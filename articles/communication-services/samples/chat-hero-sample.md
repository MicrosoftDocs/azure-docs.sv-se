---
title: Hjälte-exempel för grupp Chat
titleSuffix: An Azure Communication Services sample overview
description: Översikt över chatt-exemplet med Azure Communication Services för att göra det möjligt för utvecklare att lära sig mer om de inre arbetet i exemplet och lär dig hur du ändrar det.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0eb396935b42b040ab9f152a4965922664deb451
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487843"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>Kom igång med hjälte-exemplet för grupp Chat

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

> [!IMPORTANT]
> [Det här exemplet finns på GitHub.](https://github.com/Azure-Samples/communication-services-web-chat-hero)


Hjälte exemplet Azure Communication Services **Group Chat** visar hur kommunikations tjänsternas webb klient bibliotek kan användas för att bygga en grupp som anropar.

I den här snabb starten ska vi lära dig hur exemplet fungerar innan vi kör exemplet på den lokala datorn. Vi distribuerar sedan exemplet till Azure med dina egna Azure Communication Services-resurser.


## <a name="overview"></a>Översikt

Exemplet har både ett program på klient sidan och ett program på Server sidan. **Program på klient sidan** är ett Redux webb program som använder Microsofts Fluent UI-ramverk. Det här programmet skickar begär anden till ett ASP.NET Core **program på Server sidan** som hjälper programmet på klient sidan att ansluta till Azure.

Exemplet ser ut så här:

:::image type="content" source="./media/chat/landing-page.png" alt-text="Skärm bild som visar exempel programmets landnings sida.":::

När du trycker på knappen "starta en chatt" hämtar webb programmet en åtkomsttoken för användare från program på Server sidan. Denna token används sedan för att ansluta klient appen till Azure Communication Services. När token har hämtats uppmanas du att ange ditt namn och din emoji som ska representera dig i chatten.

:::image type="content" source="./media/chat/pre-chat.png" alt-text="Skärm bild som visar programmets pre-Chat-skärm.":::

När du har konfigurerat ditt visnings namn och din emoji kan du delta i Chat-sessionen. Nu kommer du att se den huvudsakliga chatt-arbetsytan där den centrala chatten är i livet.

:::image type="content" source="./media/chat/main-app.png" alt-text="Skärm bild som visar huvud skärmen i exempel programmet.":::

Komponenter för den huvudsakliga Chat-skärmen:

- **Huvud chatt-områden**: det här är den centrala chatten där användarna kan skicka och ta emot meddelanden. Om du vill skicka meddelanden kan du använda ingångs fältet och trycka på RETUR (eller använda knappen Skicka). Chat-meddelanden som tas emot kategoriseras av avsändaren med rätt namn och emoji. Du ser två typer av meddelanden i chattområdet: 1) om du skriver meddelanden när en användare skriver och 2) skickade och lästa meddelanden för meddelanden.
- **Rubrik**: det är här som användaren ser titeln på chatt-tråden och kontrollerna för att växla mellan list rutorna för deltagare och inställningar, och en lämna-knapp för att avsluta chatt-sessionen.
- **Sido fältet**: här visas deltagare och inställnings information när de växlas med hjälp av kontrollerna i sidhuvudet. Sido fältet deltagare innehåller en lista över deltagare i chatten och en länk för att bjuda in deltagare till chatten. I sid List rutan inställningar kan du konfigurera titeln för chatten.

Nedan hittar du mer information om förutsättningar och steg för att konfigurera exemplet.

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (8.11.2 och senare)](https://nodejs.org/en/download/)
- [Visual Studio (2017 och senare)](https://visualstudio.microsoft.com/vs/)
- [.Net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (se till att installera versionen som motsvarar Visual Studio-instansen, 32 vs 64-bitar)
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../quickstarts/create-communication-resource.md). Du måste registrera din resurs **anslutnings sträng** för den här snabb starten.

## <a name="locally-deploying-the-service--client-app"></a>Distribuera tjänst & klient program lokalt

Ett enda trådat chatt-exempel är i princip två "program" som en klient och ett serverprogram.

Öppna Visual Studio på filen chat. CSPROJ och kör i fel söknings läge så att Chat-frontend-tjänsten startas. När Server-appen besöks från webbläsaren omdirigeras trafiken till den lokalt distribuerade Chat-frontend-tjänsten.

Du kan testa exemplet lokalt genom att öppna flera webbläsare med URL: en för chatten för att simulera en chatt för flera användare.

## <a name="before-running-the-sample-for-the-first-time"></a>Innan du kör exemplet för första gången

1. Öppna en instans av PowerShell, Windows Terminal, kommando tolken eller motsvarande och navigera till den katalog som du vill klona exemplet till.
2. `git clone https://github.com/Azure-Samples/communication-services-web-chat-hero.git`
3. Hämta `Connection String` från Azure Portal. Mer information om anslutnings strängar finns i [skapa en Azure-kommunikations resurser](../quickstarts/create-communication-resource.md)
4. När du `Connection String` har lagt till lägger du till anslutnings strängen i **chatten/appsettings.js** filen som finns under mappen chatt. Mata in anslutnings strängen i variabeln: `ResourceConnectionString` .

### <a name="local-run"></a>Lokal körning

1. Gå till mappen chatt och öppna `Chat.csproj` lösningen i Visual Studio
2. Kör projektet. Webbläsaren öppnas på localhost: 5000.

#### <a name="troubleshooting"></a>Felsökning

- Lösningen kan inte skapas, den genererar fel under NPM installation/build

   Rengör/återskapa C#-lösningen

## <a name="publish-the-sample-to-azure"></a>Publicera exemplet till Azure

1. Högerklicka på `Chat` projektet och välj publicera.
2. Skapa en ny publicerings profil och välj din Azure-prenumeration.
3. Innan du publicerar lägger du till anslutnings strängen med `Edit App Service Settings` och fyller i `ResourceConnectionString` som nyckel och anger din anslutnings sträng (kopieras från appsettings.jspå) som värde.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

>[!div class="nextstepaction"]
>[Hämta exemplet från GitHub](https://github.com/Azure-Samples/communication-services-web-chat-hero)

Mer information finns i följande artiklar:

- Lär dig mer om [chatt-koncept](../concepts/chat/concepts.md)
- Bekanta dig med vårt [chatt klient bibliotek](../concepts/chat/sdk-features.md)
- Granska exemplet [contoso with app](https://github.com/Azure-Samples/communication-services-contoso-med-app)

## <a name="additional-reading"></a>Mer att läsa

- [Exempel](./overview.md) – Hitta fler exempel och exempel på översikts sidan exempel.
- [Redux](https://redux.js.org/) – tillstånds hantering på klient Sidan
- [FluentUI](https://aka.ms/fluent-ui) – Microsoft Powered UI-bibliotek
- [Reagera](https://reactjs.org/) – bibliotek för att skapa användar gränssnitt
- [ASP.net cores](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) ramverk för att skapa webb program
