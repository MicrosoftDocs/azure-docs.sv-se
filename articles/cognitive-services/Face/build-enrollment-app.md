---
title: Bygg en registrerings app för Android med reagera
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar din utvecklings miljö och distribuerar en app för ansikts registrering för att få tillåtelse från kunder.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 218579176b807bbdae85646f27eaa7f301d4b9a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428277"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>Bygg en registrerings app för Android med reagera

I den här guiden får du lära dig hur du kommer igång med exemplets registrerings program. Appen visar bästa praxis för att få ett meningsfullt medgivande för att registrera användare i en ansikts igenkännings tjänst och få hög exakthet för ansikts data. Ett integrerat system kan använda en app för registrering som detta för att tillhandahålla touch-kontroll, identitets verifiering, närvaro spårning, anpassnings-eller identitets verifiering, baserat på deras ansikts data.

När programmet startas visas användarna en detaljerad medgivande skärm. Om användaren ger sitt medgivande uppmanas du att ange ett användar namn och lösen ord i appen och sedan fånga en hög kvalitets bild med enhetens kamera.

Appen för exempel registrering skrivs med Java Script och den reagera inbyggda ramverket. Den kan för närvarande distribueras på Android-enheter. fler distributions alternativ kommer i framtiden.

## <a name="prerequisites"></a>Förutsättningar 

* En Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/cognitive-services/).  
* När du har en Azure-prenumeration kan du [skapa en ansikts resurs](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats väljer **du gå till resurs**.  
  * Du behöver nyckeln och slut punkten från resursen som du skapade för att ansluta ditt program till Ansikts-API.  
  * För lokal utveckling och testning kan du klistra in API-nyckeln och slut punkten i konfigurations filen. För slutlig distribution lagrar du API-nyckeln på en säker plats och aldrig i koden.  

> [!IMPORTANT]
> Dessa prenumerations nycklar används för att få åtkomst till ditt kognitiva tjänst-API. Dela inte dina nycklar. Lagra dem på ett säkert sätt, till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar nycklarna regelbundet. Endast en nyckel krävs för att göra ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.

## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

1. Klona git-lagringsplatsen för [appen för exempel registrering](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Om du vill konfigurera din utvecklings miljö följer du anvisningarna för att svara på den inbyggda dokumentationen <a href="https://reactnative.dev/docs/environment-setup"  title=" "  target="_blank"> </a> . Välj **reagerar inbyggd CLI-snabb start** som ditt utvecklings operativ system och välj **Android** som mål operativ system. Slutför avsnitten som **installerar beroenden** och **Android utvecklings miljö**.
1. Öppna filen env.jsi din önskade text redigerare, t. ex. [Visual Studio Code](https://code.visualstudio.com/), och Lägg till din slut punkt och nyckel. Du kan hämta din slut punkt och nyckel i Azure Portal under fliken **Översikt** för din resurs. Det här steget gäller bara för lokala testnings ändamål &mdash; kontrol lera inte din ansikts-API nyckel till din fjärrlagringsplats.
1. Kör appen med antingen den virtuella Android-hälsoenhetens emulator från Android Studio eller din egen Android-enhet. Om du vill testa din app på en fysisk enhet följer du relevant dokumentation om att reagera på den <a href="https://reactnative.dev/docs/running-on-device"  title=" "  target="_blank"> interna dokumentationen </a> .  


## <a name="create-an-enrollment-experience"></a>Skapa en registrerings upplevelse  

Nu när du har konfigurerat appen för exempel registrering kan du skräddarsy den efter dina egna behov av registrerings miljö.

Du kanske till exempel vill lägga till information om situationen på din godkännande sida:

> [!div class="mx-imgBorder"]
> ![Sidan appens godkännande](./media/enrollment-app/1-consent-1.jpg)

Tjänsten tillhandahåller bild kvalitets kontroller för att hjälpa dig att välja om avbildningen har tillräcklig kvalitet för att registrera kunden eller försöka identifiera ansikts igenkänning. Den här appen visar hur du kommer åt ramar från enhetens kamera, väljer de bild rutor som är högst kvalitet och registrerar identifierade ansikte i Ansikts-APIs tjänsten. 

Många ansikts igenkännings problem orsakas av referens avbildningar med låg kvalitet. Vissa faktorer som kan försämra modell prestanda är:
* Ansikts storlek (ansikten som är avlägsen från kameran)
* Ansikts orientering (ansikten är aktiverade eller lutade bort från kameran)
* Dåliga ljus förhållanden (antingen låg ljus eller belysning) där bilden kan vara dåligt exponerad eller har för mycket brus
* Ocklusion (delvis dolda eller blockerade ansikten), inklusive tillbehör som hatt eller tjocka-med kanter glasögon)
* Oskärpa (till exempel vid snabb ansikts förflyttning när fotografiet togs). 

> [!div class="mx-imgBorder"]
> ![registrerings instruktions sida för app-avbildning](./media/enrollment-app/4-instruction.jpg)

Observera att appen även erbjuder funktioner för att ta bort användarens registrering och alternativet att registrera igen.

> [!div class="mx-imgBorder"]
> ![Sidan profil hantering](./media/enrollment-app/10-manage-2.jpg)

Om du vill utöka appens funktioner så att de täcker den fullständiga registreringen kan du läsa [översikten över](enrollment-overview.md) ytterligare funktioner för att implementera och bästa praxis.

## <a name="deploy-the-enrollment-app"></a>Distribuera appen för registrering

### <a name="android"></a>Android

Kontrol lera först att appen är redo för produktions distribution: ta bort alla nycklar eller hemligheter från App-koden och se till att du har följt de [rekommenderade säkerhets metoderna](../cognitive-services-security.md?tabs=command-line%2ccsharp).

När du är redo att publicera din app för produktion genererar du en APK-fil för klar ande paket, vilket är paket fil formatet för Android-appar. Den här APK-filen måste vara signerad med en privat nyckel. Med den här versionen av versionen kan du börja distribuera appen till dina enheter direkt. 

Följ anvisningarna för att förbereda inför lanseringen <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> för </a> att lära dig hur du skapar en privat nyckel, signerar ditt program och genererar en versions APK.  

När du har skapat en signerad APK går du <a href="https://developer.android.com/studio/publish"  title=" till publicera appen "  target="_blank"> publicera din app- </a> dokumentation för att lära dig mer om hur du släpper appen.

## <a name="next-steps"></a>Nästa steg  

I den här guiden har du lärt dig hur du konfigurerar din utvecklings miljö och kommer igång med appen för exempel registrering. Om du inte har arbetat med att reagera internt kan du läsa mer bakgrunds information i [komma igång-dokument](https://reactnative.dev/docs/getting-started) . Det kan också vara bra att bekanta dig med [ansikts-API](Overview.md). Läs de andra avsnitten om registrerings program dokumentationen innan du börjar utveckla.