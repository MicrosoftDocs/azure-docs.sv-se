---
title: Skapa en React-app för att lägga till användare i en ansiktstjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du ställer in din utvecklingsmiljö och distribuerar en ansiktsapp för att få medgivande från kunder.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 39a74c7f3d5fb8f8b60a66947fcce9837ed6ee13
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505113"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>Skapa en React-app för att lägga till användare i en ansiktstjänst

Den här guiden visar hur du kommer igång med exempelprogrammet för ansiktsregistrering. Appen visar metodtips för att få meningsfullt medgivande för att lägga till användare i en tjänst för ansiktsigenkänning och hämta ansiktsdata med hög noggrannhet. Ett integrerat system kan använda en app som den här för att tillhandahålla peklös åtkomstkontroll, identitetsverifiering, spårning av ansikten, personanpassning eller identitetsverifiering baserat på ansiktsdata.

När programmet startas visas en detaljerad medgivandeskärm i programmet. Om användaren ger sitt medgivande frågar appen efter ett användarnamn och lösenord och samlar sedan in en ansiktsbild av hög kvalitet med hjälp av enhetens kamera.

Exempelappen skrivs med JavaScript och React Native-ramverket. Den kan för närvarande distribueras på Android-enheter. fler distributionsalternativ kommer i framtiden.

## <a name="prerequisites"></a>Förutsättningar 

* En Azure-prenumeration [– Skapa en utan kostnad.](https://azure.microsoft.com/free/cognitive-services/)  
* När du har din Azure-prenumeration [skapar du en ansiktsresurs](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) i Azure Portal för att hämta din nyckel och slutpunkt. När den har distribuerats väljer du **Gå till resurs**.  
  * Du behöver nyckeln och slutpunkten från den resurs som du skapade för att ansluta ditt program till Ansikts-API.  
  * För lokal utveckling och testning kan du klistra in API-nyckeln och slutpunkten i konfigurationsfilen. För slutlig distribution lagrar du API-nyckeln på en säker plats och aldrig i koden.  

> [!IMPORTANT]
> De här prenumerationsnycklarna används för att få åtkomst till cognitive service-API:et. Dela inte dina nycklar. Lagra dem på ett säkert sätt, till exempel med Azure Key Vault. Vi rekommenderar också att du återskapar dessa nycklar regelbundet. Endast en nyckel krävs för att göra ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.

## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

1. Klona git-lagringsplatsen för [exempelappen](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Om du vill konfigurera utvecklingsmiljön följer du React <a href="https://reactnative.dev/docs/environment-setup"  title=" Native-dokumentationen "  target="_blank"> för React Native-dokumentationen. </a> Välj **React Native CLI Quickstart (Snabbstart** för React Native CLI) som utvecklingsoperativsystem och välj **Android** som måloperativsystem. Slutför avsnitten **Installera beroenden och** **Android-utvecklingsmiljön**.
1. Öppna filen env.jsi den textredigerare du föredrar, till [exempel Visual Studio kod,](https://code.visualstudio.com/)och lägg till din slutpunkt och nyckel. Du kan hämta din slutpunkt och nyckel i Azure Portal under **fliken** Översikt för din resurs. Det här steget är endast för lokala testningsändamål. &mdash; Checka inte in ansikts-API-nyckeln till fjärrlagringsplatsen.
1. Kör appen med antingen Android Virtual Device-emulatorn från Android Studio eller din egen Android-enhet. Om du vill testa din app på en fysisk enhet följer du relevant <a href="https://reactnative.dev/docs/running-on-device"  title=" React Native-dokumentation "  target="_blank"> react native-dokumentation. </a>  


## <a name="create-a-user-add-experience"></a>Skapa en användarupplevelse för att lägga till  

Nu när du har ställt in exempelappen kan du skräddarsy den efter dina egna behov.

Du kanske till exempel vill lägga till situationsspecifik information på din medgivandesida:

> [!div class="mx-imgBorder"]
> ![sidan för appmedgivande](./media/enrollment-app/1-consent-1.jpg)

Tjänsten tillhandahåller kvalitetskontroller av bilder som hjälper dig att välja om bilden har tillräcklig kvalitet för att lägga till kunden eller försöka ansiktsigenkänning. Den här appen visar hur du kommer åt bildrutor från enhetens kamera, väljer bildrutor av högsta kvalitet och lägger till det identifierade ansiktet i ansikts-API-tjänsten. 

Många problem med ansiktsigenkänning orsakas av referensbilder av låg kvalitet. Några faktorer som kan försämra modellens prestanda är:
* Ansiktsstorlek (ansikten som är avlägsna från kameran)
* Ansiktsorientering (ansikten vända eller lutade bort från kameran)
* Dåliga ljusförhållanden (antingen lågt ljus eller bakgrundsbelysning) där bilden kan vara dåligt exponerad eller ha för mycket brus
* Ocklusion (delvis dolda eller inlösta ansikten), inklusive tillbehör som t.ex. kepsar eller tjockt sämjade glasögon)
* Oskärpa (till exempel genom snabb ansiktsförflyttning när fotot togs). 

> [!div class="mx-imgBorder"]
> ![instruktionssida för appavbildning](./media/enrollment-app/4-instruction.jpg)

Observera att appen även erbjuder funktioner för att ta bort användarens information och alternativet att lägga till igen.

> [!div class="mx-imgBorder"]
> ![profilhanteringssidan](./media/enrollment-app/10-manage-2.jpg)

Om du vill utöka appens funktioner till att omfatta hela upplevelsen kan du läsa [översikten över](enrollment-overview.md) ytterligare funktioner att implementera och metodtips.

## <a name="deploy-the-app"></a>Distribuera appen

### <a name="android"></a>Android

Kontrollera först att appen är redo för produktionsdistribution: ta bort nycklar eller hemligheter från appkoden och kontrollera att du har följt [säkerhetsmetoderna.](../cognitive-services-security.md?tabs=command-line%2ccsharp)

När du är redo att släppa appen för produktion genererar du en lanseringsklar APK-fil, som är paketfilformatet för Android-appar. Den här APK-filen måste signeras med en privat nyckel. Med den här versionen kan du börja distribuera appen till dina enheter direkt. 

Följ dokumentationen Förbered för lansering för att lära dig hur du genererar en privat nyckel, signerar ditt <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> program och </a> genererar en lanserings-APK.  

När du har skapat en signerad APK kan du läsa dokumentationen Publicera din app om du vill veta mer om <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> hur du släpper </a> appen.

## <a name="next-steps"></a>Nästa steg  

I den här guiden har du lärt dig att konfigurera utvecklingsmiljön och komma igång med exempelappen. Om React Native är nytt för dig kan du läsa deras kom [igång-dokument för att](https://reactnative.dev/docs/getting-started) få mer bakgrundsinformation. Det kan också vara bra att bekanta dig med [Ansikts-API.](Overview.md) Läs de andra avsnitten om att lägga till användare innan du börjar utveckla.