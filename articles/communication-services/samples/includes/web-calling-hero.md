---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 0504467e387c0875a8e00bd1cf638ec437232092
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554341"
---
Azure Communication Services- **gruppen som anropar ett hjältes exempel** visar hur kommunikations tjänsterna som anropar webb-SDK kan användas för att bygga en grupp som anropar.

I den här snabb starten ska vi lära dig hur exemplet fungerar innan vi kör exemplet på den lokala datorn. Vi distribuerar sedan exemplet till Azure med dina egna Azure Communication Services-resurser.

## <a name="download-code"></a>Ladda ned kod

Hitta projektet för det här exemplet på [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero). En version av exemplet med [Teams interop](../../concepts/teams-interop.md) kan hittas på en separat [gren](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/teams-interop).

## <a name="overview"></a>Översikt

Exemplet har både ett program på klient sidan och ett program på Server sidan. **Program på klient sidan** är ett Redux webb program som använder Microsofts Fluent UI-ramverk. Det här programmet skickar begär anden till ett ASP.NET Core **program på Server sidan** som hjälper programmet på klient sidan att ansluta till Azure.

Exemplet ser ut så här:

:::image type="content" source="../media/calling/landing-page.png" alt-text="Skärm bild som visar landnings sidan för exempel programmet.":::

När du trycker på knappen "starta ett anrop" hämtar webb programmet en åtkomsttoken för användare från program på Server sidan. Denna token används sedan för att ansluta klient appen till Azure Communication Services. När token har hämtats uppmanas du att ange den kamera och mikrofon som du vill använda. Du kommer att kunna inaktivera/aktivera enheter med växlings kontroller:

:::image type="content" source="../media/calling/pre-call.png" alt-text="Skärm bild som visar för anrops skärmen i exempel programmet.":::

När du har konfigurerat ditt visnings namn och dina enheter kan du ansluta till inloggningssessionen. Sedan kan du se huvud anrops arbets ytan där den grundläggande anrops upplevelsen finns.

:::image type="content" source="../media/calling/main-app.png" alt-text="Skärm bild som visar exempel programmets huvud skärm.":::

Komponenter för den huvudsakliga uppringnings skärmen:

- **Medie Galleri**: huvud steget där deltagarna visas. Om en deltagare har sin kamera aktive rad visas deras video flöde här. Varje deltagare har en enskild panel som visar visnings namn och video ström (när det finns en)
- **Rubrik**: det är här som de primära samtals kontrollerna finns för att växla mellan inställningar och deltagar fält, förvandla video och mixa på/av, dela skärmen och lämna samtalet.
- **Sido fältet**: här visas deltagare och inställnings information när de växlas med hjälp av kontrollerna i sidhuvudet. Komponenten kan hämtas med hjälp av X i det övre högra hörnet. Deltagarnas sido fält visar en lista över deltagare och en länk som bjuder in fler användare att chatta. I sid List rutan inställningar kan du konfigurera inställningar för mikrofon och kamera.

> [!NOTE]
> Baserat på begränsningar på webb anrops-SDK: n återges bara en fjärran sluten video ström. Mer information finns i [anropa SDK Stream-stöd](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features#calling-sdk-streaming-support).

Nedan hittar du mer information om förutsättningar och steg för att konfigurera exemplet.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 och senare)](https://nodejs.org/en/download/)
- [Visual Studio (2019 och senare)](https://visualstudio.microsoft.com/vs/)
- [.Net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (se till att installera versionen som motsvarar Visual Studio-instansen, 32 vs 64-bitar)
- En Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../quickstarts/create-communication-resource.md). Du måste registrera din resurs **anslutnings sträng** för den här snabb starten.

## <a name="locally-deploy-the-service--client-applications"></a>Distribuera tjänst & klient program lokalt

Den grupp som anropar exemplet är i princip två program: ClientApp och Service.NET-appen.

När vi vill distribuera lokalt behöver vi starta båda programmen. När Server-appen besöks från webbläsaren använder den lokalt distribuerade ClientApp för användar upplevelsen.

Du kan testa exemplet lokalt genom att öppna flera webbläsare med URL: en för ditt anrop för att simulera ett samtal med flera användare.

### <a name="before-running-the-sample-for-the-first-time"></a>Innan du kör exemplet för första gången

1. Öppna en instans av PowerShell, Windows Terminal, kommando tolken eller motsvarande och navigera till den katalog som du vill klona exemplet till.
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. Hämta `Connection String` från Azure Portal. Mer information om anslutnings strängar finns i [skapa en Azure-kommunikations resurser](../../quickstarts/create-communication-resource.md).
4. När du `Connection String` har lagt till lägger du till anslutnings strängen i filen **anropa/appsetting.jspå** filen som finns i mappen service .net. Mata in anslutnings strängen i variabeln: `ResourceConnectionString` .

### <a name="local-run"></a>Lokal körning

1. Gå till anropa mapp och öppna `Calling.csproj` lösning i Visual Studio.
2. Kör `Calling` projekt. Webbläsaren öppnas med `localhost:5001` .

## <a name="publish-the-sample-to-azure"></a>Publicera exemplet till Azure

1. Högerklicka på `Calling` projektet och välj publicera.
2. Skapa en ny publicerings profil och välj din Azure-prenumeration.
3. Innan du publicerar lägger du till anslutnings strängen med `Edit App Service Settings` och fyller i `ResourceConnectionString` som nyckel och anger din anslutnings sträng (kopieras från appsettings.jspå) som värde.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

>[!div class="nextstepaction"]
>[Hämta exemplet från GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Mer information finns i följande artiklar:

- Bekanta dig med [att använda den anropande SDK: n](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Läs mer om [hur du anropar Works](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Mer att läsa

- [Exempel](./../overview.md) – Hitta fler exempel och exempel på översikts sidan exempel.
- [Redux](https://redux.js.org/) – tillstånds hantering på klient Sidan
- [FluentUI](https://aka.ms/fluent-ui) – Microsoft Powered UI-bibliotek
- [Reagera](https://reactjs.org/) – bibliotek för att skapa användar gränssnitt
- [ASP.net cores](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) ramverk för att skapa webb program
