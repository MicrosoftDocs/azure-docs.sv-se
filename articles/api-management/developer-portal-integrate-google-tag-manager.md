---
title: Integrera Google Taggstyrning utvecklarportalen
titleSuffix: Azure API Management
description: Lär dig hur du Google Taggstyrning i din hanterade eller egenvärdbaserade utvecklarportal i Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c209eb782787146d947b4684d41c5d1e9bb6364e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741901"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Integrera Google Taggstyrning till API Management utvecklarportalen

[Google Taggstyrning är](https://developers.google.com/tag-manager) ett tagghanteringssystem som skapats av Google. Du kan använda den för att hantera JavaScript- och HTML-taggar som används för spårning och analys på webbplatser. Du kan till exempel använda Google Taggstyrning för att integrera Google Analytics, heatmaps eller chattrobotar som LiveChat.

Följ stegen i den här artikeln för att Google Taggstyrning i din hanterade eller egenvärdbaserade utvecklarportal i Azure API Management.

## <a name="add-google-tag-manager-to-your-portal"></a>Lägga Google Taggstyrning i portalen

Följ de här stegen för Google Taggstyrning ansluta till din hanterade eller egenvärdbaserade utvecklarportal.

> [!IMPORTANT]
> Steg 1 och 2 krävs inte för hanterade portaler. Om du har en hanterad portal går du vidare till steg 4.

1. Konfigurera en lokal [miljö för](developer-portal-self-host.md#step-1-set-up-local-environment) den senaste versionen av utvecklarportalen.

1. Installera **npm-paketet** för att lägga [till Paperbits för Google Taggstyrning](https://github.com/paperbits/paperbits-gtm):

    ```sh
    npm install @paperbits/gtm --save
    ```

1. Importera och `startup.publish.ts` registrera `src` GTM-modulen i filen i mappen:

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Utöka platskonfigurationen från föregående steg med Google Taggstyrning konfiguration:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Nästa steg

- [Automatisera portaldistributioner](automate-portal-deployments.md)
- [Utvecklarportalen med egen värd](developer-portal-self-host.md)