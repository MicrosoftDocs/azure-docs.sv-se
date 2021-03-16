---
title: Skapa Cognitive Services resurs med hjälp av klient biblioteket för Azure Management
titleSuffix: Azure Cognitive Services
description: Skapa och hantera Azure Cognitive Services-resurser med hjälp av klient biblioteket för Azure-hantering.
services: cognitive-services
keywords: kognitiva tjänster, kognitiv intelligens, kognitiva lösningar, AI-tjänster
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 03/15/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e042ac263d3a30a9790ba6a3a3d404e5e9cb9aad
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472150"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Snabb start: skapa en Cognitive Services resurs med hjälp av klient biblioteket för Azure-hantering

Använd den här snabb starten för att skapa och hantera Azure Cognitive Services-resurser med hjälp av klient biblioteket för Azure-hantering.

Azure Cognitive Services är en familj av moln-och bas tjänster med REST-API: er och klient bibliotek som är tillgängliga för att hjälpa utvecklare att bygga kognitiv information i sina program. Utvecklare behöver inte Direct artificiell intelligens (AI) eller data vetenskaps kunskaper eller kunskap för att uppnå framgång. Azure Cognitive Services gör det möjligt för utvecklare att enkelt lägga till kognitiva funktioner i sina program med kognitiva lösningar som kan se, höra, tala, förstå och till och med börja på skäl.

Enskilda AI-tjänster representeras av Azure- [resurser](../azure-resource-manager/management/manage-resources-portal.md) som du skapar under din Azure-prenumeration. När du har skapat en resurs kan du använda nycklarna och slut punkten som genereras för att autentisera dina program.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
