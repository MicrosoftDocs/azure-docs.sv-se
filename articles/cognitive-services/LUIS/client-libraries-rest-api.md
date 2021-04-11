---
title: 'Snabb start: Language Understanding (LUIS) SDK-klient bibliotek och REST API'
description: Skapa och fråga en LUIS-app med LUIS SDK-klientcertifikatet och REST API.
ms.topic: quickstart
ms.date: 03/29/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, artificiell intelligens, AI, naturlig språk bearbetning, NLP, LUIS, Azure Luis, Natural Language förståelseing, AI chattrobot, chattrobot Maker, förstå naturligt språk
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: ca45266ce4b8ca784c3d54aafb80a66efaf2a1da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278923"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Snabb start: Language Understanding (LUIS) klient bibliotek och REST API

Skapa och fråga en Azure LUIS artificiell intelligens (AI)-app med klient biblioteken för LUIS SDK med den här snabb starten med C#, python eller Java Script. Du kan också använda spiral för att skicka begär Anden med hjälp av REST API.

Language Understanding (LUIS) gör det möjligt att använda naturlig språk bearbetning (NLP) till en användares konversation, naturligt språk text för att förutsäga den övergripande innebörden och hämta relevant, detaljerad information.

* Med klient biblioteket för **redigering** och REST API kan du skapa, redigera, träna och publicera din Luis-app.
* Klient biblioteket för **förutsägelse körning** och REST API gör att du kan fråga den publicerade appen.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort appen från [Luis-portalen](https://www.luis.ai) och ta bort Azure-resurserna från [Azure Portal](https://portal.azure.com/).

Om du använder REST API tar du bort `ExampleUtterances.JSON` filen från fil systemet när du är klar med snabb starten.

## <a name="troubleshooting"></a>Felsökning

* Autentisering till klient biblioteket – autentiseringsfel indikerar vanligt vis att fel nyckel & slut punkten användes. I den här snabb starten används redigerings nyckeln och slut punkten för förutsägelse körning som en bekvämlighet, men fungerar bara om du inte redan har använt den månatliga kvoten. Om du inte kan använda redigerings nyckeln och slut punkten måste du använda den förutsägelse körnings nyckeln och slut punkten vid åtkomst till klient biblioteket för förutsägelse körnings-SDK.
* Skapa entiteter – om du får ett fel när du skapar den kapslade enheten för maskin inlärning som används i den här självstudien, se till att du kopierade koden och inte ändrade koden för att skapa en annan entitet.
* Skapa exempel yttranden – om du får ett fel när du skapar den märkta exempel uttryck som används i den här självstudien, se till att du kopierade koden och inte ändrade koden för att skapa ett annat etikett exempel.
* Utbildning – om du får ett tränings fel indikerar detta vanligt vis en tom app (inga avsikter med exempel yttranden) eller en app med intenter eller entiteter som är felaktiga.
* Övriga fel – eftersom koden anropar klient biblioteken med text-och JSON-objekt ser du till att du inte har ändrat koden.

Andra fel – om du får ett fel som inte omfattas av föregående lista kan du meddela oss genom att ge feedback längst ned på den här sidan. Inkludera programmeringsspråk och version för de klient bibliotek som du har installerat.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Iterativ app-utveckling för LUIS](./luis-concept-app-iteration.md)