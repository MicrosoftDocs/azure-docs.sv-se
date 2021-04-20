---
title: Använda communitywidgetar i utvecklarportalen
titleSuffix: Azure API Management
description: Lär dig mer om communitywidgetar för API Management utvecklarportalen och hur du matar in och använder dem i din kod.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: de6160aa2e556297287ae9e9ecd58a93e54d863f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741826"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Använda communitywidgetar i utvecklarportalen

Alla utvecklare placerar sina community-bidragna widgetar i mappen på API Management `/community/widgets/` [GitHub-lagringsplatsen för utvecklarportalen.](https://github.com/Azure/api-management-developer-portal) Alla har godkänts av utvecklarportalteamet. Du kan använda widgetarna genom att mata in dem [i din egen version](developer-portal-self-host.md) av portalen. Den hanterade versionen av utvecklarportalen stöder för närvarande inte community-widgetar.

> [!NOTE]
> Utvecklarportalteamet inspekterar noggrant bidragna widgetar och deras beroenden. Teamet kan dock inte garantera att det är säkert att läsa in widgetarna. Använd din egen bedömning när du bestämmer dig för att använda en widget från communityn. Läs våra [riktlinjer för widgetbidrag för](developer-portal-widget-contribution-guidelines.md#contribution-guidelines) att lära dig mer om våra förebyggande åtgärder.

## <a name="inject-and-use-external-widgets"></a>Mata in och använda externa widgetar

1. Konfigurera en lokal [miljö för](developer-portal-self-host.md#step-1-set-up-local-environment) den senaste versionen av utvecklarportalen.

1. Gå till widgetens mapp i `/community/widgets` katalogen . Läs widgetens beskrivning i `readme.md` filen.

1. Registrera widgeten i portalens moduler:

    1. `src/apim.design.module.ts` – en modul som registrerar designtidsberoenden.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` – en modul som registrerar publish-time-beroenden.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` – en modul som registrerar körningsberoenden.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Kontrollera om widgeten har en `npm_dependencies` -fil.

1. I så fall kopierar du kommandona från filen och kör dem i lagringsplatsens översta katalog.

    Om du gör det installeras widgetens beroenden.

1. Kör `npm start`.

Du kan se widgeten i **kategorin Community** i widgetväljaren.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Skärmbild av widgetväljaren":::


## <a name="next-steps"></a>Nästa steg


Läs mer om utvecklarportalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)

- [Bidra med widgetar](developer-portal-widget-contribution-guidelines.md)
