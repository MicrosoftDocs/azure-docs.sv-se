---
title: Data lagring – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS lagrar data som krypterats i ett Azure-datalager som motsvarar den region som anges av nyckeln.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a58bcff4e39c4a4a907cd8567b47b074ff299bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97008460"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Data lagring och borttagning i Language Understanding (LUIS) Cognitive Services

LUIS lagrar data som krypterats i ett Azure-datalager som motsvarar [den region](luis-reference-regions.md) som anges av nyckeln. 

* Data som används för att träna modellen, till exempel entiteter, avsikter och yttranden kommer att sparas i LUIS för programmets livs längd. Om en ägare eller deltagare tar bort appen tas dessa data bort med den. Om ett program inte har använts på 90 dagar tas det bort. 

* Program författare kan välja att [Aktivera loggning](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning) på yttranden som skickas till ett publicerat program. Om aktive rad kommer yttranden att sparas i 30 dagar och kan visas av program författaren. Om loggning inte är aktive rad när programmet publiceras, lagras inte dessa data.

## <a name="export-and-delete-app"></a>Exportera och ta bort app
Användare har fullständig kontroll över att [Exportera](luis-how-to-start-new-app.md#export-app) och [ta bort](luis-how-to-start-new-app.md#delete-app) appen. 

## <a name="utterances"></a>Yttranden

Yttranden kan lagras på två olika platser. 

* Under **redigerings processen** skapas och lagras yttranden i avsikten. Yttranden i avsikter krävs för en lyckad LUIS-app. När appen har publicerats och tar emot frågor på slut punkten, bestämmer slut punktens begär ande QueryString, `log=false` om slut punktens uttryck är lagrad. Om slut punkten lagras blir den en del av den aktiva inlärnings-yttranden som finns i avsnittet **build** i portalen i avsnittet **Granska slut punkts yttranden** . 
* När du **granskar slut punkts yttranden** och lägger till en uttryck till ett avsikts sätt, lagras uttryck inte längre som en del av slut punkten yttranden som ska granskas. Den läggs till i appens avsikter. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Ta bort exempel yttranden från ett avsikts syfte

Ta bort exempel yttranden som används för utbildning [Luis](luis-reference-regions.md). Om du tar bort ett exempel på uttryck från din LUIS-app, tas den bort från LUIS-webbtjänsten och är inte tillgänglig för export.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Ta bort yttranden i granskning från aktiv inlärning

Du kan ta bort yttranden från listan över användare yttranden som LUIS föreslår på **[sidan Granska slut punkt yttranden](luis-how-to-review-endpoint-utterances.md)**. Att ta bort yttranden från den här listan förhindrar att de föreslås, men tar inte bort dem från loggar.

Om du inte vill ha aktiva inlärnings yttranden kan du [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Om du inaktiverar aktiv inlärning inaktive ras även loggning.

### <a name="disable-logging-utterances"></a>Inaktivera loggning yttranden
Om du [inaktiverar aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning) inaktive ras loggning.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Ta bort ett konto
Om du inte migreras kan du ta bort ditt konto så tas alla appar bort tillsammans med deras exempel yttranden och loggar. Data bevaras i 90 dagar innan kontot och data tas bort permanent.

Det går inte att ta bort kontot från sidan **Inställningar** . Välj ditt konto namn i det övre högra navigerings fältet för att komma till sidan **Inställningar** .

## <a name="delete-an-authoring-resource"></a>Ta bort en redigerings resurs
Om du har [migrerat till en redigerings resurs](./luis-migration-authoring.md), tar borttagning av resursen från Azure Portal bort alla program som är associerade med den resursen, tillsammans med deras exempel yttranden och loggar. Data bevaras i 90 dagar innan de tas bort permanent.    

Om du vill ta bort din resurs går du till [Azure Portal](https://ms.portal.azure.com/#home) och väljer din Luis Authoring-resurs. Gå till fliken **Översikt** och klicka på knappen **ta bort** högst upp på sidan. Bekräfta sedan att din resurs har tagits bort. 

## <a name="data-inactivity-as-an-expired-subscription"></a>Data inaktivitet som en utgången prenumeration
I syfte att kvarhålla och ta bort data kan en inaktiv LUIS-app hanteras på _Microsofts eget_ sätt som en utgången prenumeration. En app anses vara inaktiv om den uppfyller följande kriterier under de senaste 90 dagarna: 

* **Inga** anrop har gjorts till det.
* Har inte ändrats.
* Har inte tilldelats någon aktuell nyckel.
* Har inte haft en användare att logga in.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om att exportera och ta bort en app](luis-how-to-start-new-app.md)