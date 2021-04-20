---
title: Så här bidrar du med widgetar för utvecklarportalen
titleSuffix: Azure API Management
description: Lär dig mer om rekommenderade riktlinjer att följa när du bidrar med en widget API Management lagringsplatsen för utvecklarportalen.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c4d3ed2aeaac57f721d23d7c7aa1c70ef14e4294
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741877"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>Så här bidrar du widgetar till API Management utvecklarportalen

Om du vill bidra med en widget till GitHub-API Management-portalens [GitHub-lagringsplats](https://github.com/Azure/api-management-developer-portal)följer du den här trestegsprocessen:

1. Förföra lagringsplatsen.

1. Implementera widgeten.

1. Öppna en pull-begäran för att inkludera widgeten i den officiella lagringsplatsen.

Widgeten ärver lagringsplatsens licens. Den kommer att vara [tillgänglig för att installeras](developer-portal-use-community-widgets.md) i den egenvärdsbaserade versionen av portalen. Utvecklarportalteamet kan välja att även inkludera den i den hanterade versionen av portalen.

Se [självstudien om widgetimplementering](developer-portal-implement-widgets.md) för ett exempel på hur du utvecklar en egen widget.

## <a name="contribution-guidelines"></a>Riktlinjer för bidrag

Den här vägledningen är avsedd att säkerställa säkerheten och sekretessen för våra kunder och besökare på deras portaler. Följ dessa riktlinjer för att säkerställa att ditt bidrag godkänns:

1. Placera widgeten i `community/widgets/<your-widget-name>` mappen .

1. Widgetens namn måste vara gemener och alfanumeriska med bindestreck som avgränsar orden. Till exempel `my-new-widget`.

1. Mappen måste innehålla en skärmbild av widgeten i en publicerad portal.

1. Mappen måste innehålla en `readme.md` -fil som följer mallen från `/scaffolds/widget/readme.md` filen.

1. Mappen kan innehålla en `npm_dependencies` fil med npm-kommandon för att installera eller hantera widgetens beroenden.

    Ange uttryckligen versionen för varje beroende. Exempel:  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    Widgeten bör kräva minimala beroenden. Alla beroenden inspekteras noggrant av granskarna. I synnerhet bör logiken i widgeten ha öppen källkod i widgetens mapp. Omslut den inte i ett npm-paket.

1. Ändringar av filer utanför widgetens mapp tillåts inte som en del av ett widget-bidrag. Det inkluderar, men är inte begränsat till, `/package.json` filen.

1. Det är inte tillåtet att mata in spårningsskript eller skicka kundskapade data till anpassade tjänster.

    > [!NOTE]
    > Du kan bara samla in kundskapade data via `Logger` gränssnittet.

## <a name="next-steps"></a>Nästa steg

- Mer information om bidrag finns på GitHub API Management portalen för [utvecklare.](https://github.com/Azure/api-management-developer-portal/)

- Se [Implementera widgetar](developer-portal-implement-widgets.md) för att lära dig hur du utvecklar din egen widget, steg för steg.

- Se [Använda communitywidgetar](developer-portal-use-community-widgets.md) för att lära dig hur du använder widgetar som har bidragit från communityn.