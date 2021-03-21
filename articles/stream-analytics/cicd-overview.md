---
title: Kontinuerlig integrering och distribution för Azure Stream Analytics
description: Den här artikeln ger en översikt över en pipeline för kontinuerlig integrering och distribution (CI/CD) för Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 940813f12d542715db47781731144a75e854a98e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019575"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Kontinuerlig integrering och distribution (CI/CD) för Azure Stream Analytics

Du kan distribuera ditt Azure Stream Analytics-jobb kontinuerligt genom att använda käll kontroll integrering. Med käll kontroll integrering kan du använda ett arbets flöde där en kod uppdatering utlöser en resurs distribution till Azure. Den här artikeln beskriver de grundläggande stegen för att skapa en pipeline för kontinuerlig integrering och distribution (CI/CD).

Kom igång med snabb starten för [Azure Stream Analytics](stream-analytics-quick-create-portal.md)om du är nybörjare på Azure Stream Analytics.

## <a name="create-a-cicd-pipeline"></a>Skapa en CI/CD-pipeline

Följ stegen i den här guiden för att skapa en CI/CD-pipeline för Stream Analytics.

1. Utveckla en Azure Stream Analytics fråga.

   Använd Azure Stream Analytics verktyg för [Visual Studio Code](./quick-create-visual-studio-code.md) eller [Visual Studio](stream-analytics-quick-create-vs.md) för att [utveckla och testa frågor lokalt](develop-locally.md). Du kan också [Exportera ett befintligt jobb](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) till ett lokalt projekt.

2. Spara dina Azure Stream Analytics-projekt i käll kontroll systemet, t. ex. en git-lagringsplats.

3. Använd [Azure Stream Analytics CI/CD-verktyg](cicd-tools.md) för att skapa projekten och skapa Azure Resource Management-mallar för distributionen.

4. Kör [automatiserade skript test](cicd-tools.md#automated-test) för kvalitets regression.

5. [Distribuera jobbet](cicd-tools.md#deploy-to-azure) till Azure automatiskt.

## <a name="auto-build-test-and-deploy"></a>Bygg, testa och distribuera automatiskt

Du kan använda kommando raden och [Azure Stream Analytics CI/CD-verktyg](cicd-tools.md) för att bygga, testa och distribuera automatiskt. Du kan också skapa en CI/CD-pipeline i [Azure-pipeliner](set-up-cicd-pipeline.md). Azure-pipelines för att aktivera mer avancerade funktioner, till exempel pipeline hantering, visualisering och utlösare.

## <a name="next-steps"></a>Nästa steg

* [Automatisera byggen, tester och distributioner av ett Azure Stream Analytics jobb med CI/CD-verktyg](cicd-tools.md)
* [Konfigurera en CI/CD-pipeline för Stream Analytics jobb med hjälp av Azure-pipeline](set-up-cicd-pipeline.md)