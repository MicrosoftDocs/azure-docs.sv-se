---
title: Integrera Application Insights utvecklarportalen
titleSuffix: Azure API Management
description: Lär dig hur du integrerar Application Insights i din hanterade eller egen värdbaserade utvecklarportal.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741898"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Integrera Application Insights utvecklarportalen

En populär funktion i Azure Monitor är Application Insights. Det är en utökningsbar APM-tjänst (Application Performance Management) för utvecklare och DevOps-proffs. Använd den för att övervaka utvecklarportalen och identifiera prestandaavvikelser. Application Insights innehåller kraftfulla analysverktyg som hjälper dig att lära dig vad användarna faktiskt gör när du besöker utvecklarportalen.

## <a name="add-application-insights-to-your-portal"></a>Lägga Application Insights till i portalen

Följ dessa steg för att Application Insights till din hanterade eller egenvärdbaserade utvecklarportal.

> [!IMPORTANT]
> Steg 1 och 2 krävs inte för hanterade portaler. Om du har en hanterad portal går du vidare till steg 4.

1. Konfigurera en lokal [miljö för](developer-portal-self-host.md#step-1-set-up-local-environment) den senaste versionen av utvecklarportalen.

1. Installera **npm-paketet** för att lägga [till Paperbits för Azure:](https://github.com/paperbits/paperbits-azure)

    ```console
    npm install @paperbits/azure --save
    ```

1. I filen `startup.publish.ts` i mappen importerar och registrerar du `src` Application Insights modulen:

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. Hämta portalens konfiguration:

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Utöka platskonfigurationen från föregående steg med Application Insights konfiguration:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Nästa steg

Läs mer om utvecklarportalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)
- [Automatisera portaldistributioner](automate-portal-deployments.md)
- [Utvecklarportalen med egen värd](developer-portal-self-host.md)
