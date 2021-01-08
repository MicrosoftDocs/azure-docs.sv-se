---
title: Använda DevOps med GitHub-LUIS
titleSuffix: Azure Cognitive Services
description: Använd DevOps med Language Understanding (LUIS) och GitHub.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 448b3d93ed58e4cfc73da576f0c5871600400ac6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019847"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>Använd DevOps-program för LUIS-utveckling med GitHub-åtgärder

Gå till [Luis DevOps Template lagrings platsen](https://github.com/Azure-Samples/LUIS-DevOps-Template) för en komplett lösning som implementerar metod tips för DevOps och program varu teknik för Luis. Du kan använda den här mallen för att skapa en egen lagrings plats med inbyggt stöd för CI/CD-arbetsflöden och praxis som möjliggör [käll kontroll](luis-concept-devops-sourcecontrol.md), [automatiserade versioner](luis-concept-devops-automation.md), [testning](luis-concept-devops-testing.md)och [versions hantering](luis-concept-devops-automation.md#release-management) med Luis för ditt eget projekt.

## <a name="the-luis-devops-template-repo"></a>LUIS DevOps mal len lagrings platsen

[Luis DevOps Template lagrings platsen](https://github.com/Azure-Samples/LUIS-DevOps-Template) går igenom hur man:

* **Klona mallen lagrings platsen** – kopiera mallen till din egen GitHub-lagringsplats.
* **Konfigurera Luis-resurser** – skapa [Luis-redigering och förutsägelse resurser i Azure](./luis-how-to-azure-subscription.md) som ska användas av arbets flödena för kontinuerlig integrering.
* **Konfigurera CI/CD-arbetsflöden** – Konfigurera parametrar för CI/CD-arbetsflöden och lagra dem i [GitHub hemligheter](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).
* Går **igenom ["dev"-slingan "](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)** – utvecklaren gör uppdateringar till en exempel Luis-app när du arbetar i en utvecklings gren, testar uppdateringarna och genererar sedan en pull-begäran om att föreslå ändringar och för att söka efter gransknings godkännande.
* **Kör CI/CD-arbetsflöden** – kör [arbets flöden för kontinuerlig integrering för att bygga och testa en Luis-app](luis-concept-devops-automation.md) med GitHub-åtgärder.
* **Utföra automatiserad testning** – utföra [automatiserad batch-testning för en Luis-app](luis-concept-devops-testing.md) för att utvärdera appens kvalitet.
* **Distribuera Luis-appen** – köra ett [jobb för kontinuerlig leverans (CD)](luis-concept-devops-automation.md#continuous-delivery-cd) för att publicera Luis-appen.
* **Använd lagrings platsen med ditt eget projekt** – förklarar hur du använder lagrings platsen med ditt eget Luis-program.

## <a name="next-steps"></a>Nästa steg

* Använd [Luis DevOps Template lagrings platsen](https://github.com/Azure-Samples/LUIS-DevOps-Template) för att tillämpa DevOps med ditt eget projekt.
* [Källkontroll och förgreningsstrategier för LUIS](luis-concept-devops-sourcecontrol.md)
* [Testa för LUIS-DevOps](luis-concept-devops-testing.md)
* [Automation-arbetsflöden för LUIS-DevOps](luis-concept-devops-automation.md)
