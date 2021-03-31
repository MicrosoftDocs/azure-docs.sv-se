---
title: Felsöka Azure API Management-principer i Visual Studio Code | Microsoft Docs
description: Lär dig hur du felsöker Azure API Management-principer med Azure API Management Visual Studio Code-tillägget
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 2e45d1274cf7332dbca70eaa8fc51f0ac98e5359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648024"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Felsöka Azure API Management-principer i Visual Studio Code

[Principer](api-management-policies.md) i Azure API Management ger kraftfulla funktioner som hjälper API-utgivare att hantera kors stycknings problem, till exempel autentisering, auktorisering, begränsning, cachelagring och omvandling. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. 

I den här artikeln beskrivs hur du felsöker API Management principer med [Azure API Management-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

## <a name="prerequisites"></a>Förutsättningar

* Skapa en instans av API Management Developer-Tier genom att följa den här [snabb](get-started-create-service-instance.md) starten först.

* Installera [Visual Studio Code](https://code.visualstudio.com/) och den senaste versionen av [Azure API Management-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

* Importera ett API till API Management-instansen. Till exempel steg, se [Självstudier: använd API Management tillägget för Visual Studio Code för att importera och hantera API: er](visual-studio-code-tutorial.md).

## <a name="restrictions-and-limitations"></a>Villkor och begränsningar

Den här funktionen är endast tillgänglig på nivån för utvecklare av API Management. Varje API Management instans har endast stöd för en parallell felsökningssession.

## <a name="initiate-a-debugging-session"></a>Initiera en felsökningssession

1. Starta Visual Studio-kod
2. Navigera till API Management-tillägget under Azure-tillägg
3. Hitta API Management-instansen för fel sökning
4. Hitta API och åtgärd för att felsöka
5. Högerklicka på åtgärden och välj **Starta princip fel sökning**

I det här läget försöker tillägget att initiera och upprätta en felsökningssession med API Management Gateway.

![Starta fel sökning](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Skicka en testbegäran
När sessionen har upprättats öppnar tillägget en ny redigerare som gör att vi kan skapa och skicka en test-HTTP-begäran till den här åtgärden som utnyttjar [rest-klientprogrammet](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

Du kommer att märka att **OCP-APIM-debug-** huvudet redan har lagts till i begäran. Den här rubriken krävs och värdet måste anges till service nivå, prenumerations nyckel för all åtkomst för att utlösa fel söknings funktionen i API Management Gateway.

Ändra HTTP-begäran i redigeraren enligt ditt test scenario. Klicka sedan på **skicka begäran** för att skicka test förfrågan till API Management Gateway.

![Skicka en testbegäran](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Felsökningsprinciper
När test-HTTP-begäran har skickats öppnar tillägget fel söknings fönstret som visar de effektiva principerna för åtgärden och stoppar vid den första effektiva principen. 

![Felsöka principer](media/api-management-debug-policies/main-window.png)

Om du vill följa princip pipelinen kan du stega genom enskilda principer eller ange en Bryt punkt för en princip och steget direkt till principen. 

I **variabel** panelen kan du kontrol lera värdena för systemskapade och användardefinierade variabler. På panelen **Bryt punkter** kan du se en lista över alla Bryt punkter som har ställts in. I panelen **anrops stack** kan du se den aktuella effektiva princip omfattningen. 

Om det uppstår ett fel när principen körs visas information om felet på den princip där det hände. 

![undantag](media/api-management-debug-policies/exception.png)

> [!TIP]
> Kom ihåg att avsluta felsökningssessionen genom att klicka på **stopp** knappen när du är färdig.


## <a name="next-steps"></a>Nästa steg

+ Läs mer om [API Management-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 
+ Rapportera problem i [GitHub-lagringsplatsen](https://github.com/Microsoft/vscode-apimanagement)

