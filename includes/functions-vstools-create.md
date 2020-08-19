---
title: inkludera fil
description: inkludera fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 164620bdcee7ac546468354f999dcb3ad96ecf4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731053"
---
Azure Functions projekt mal len i Visual Studio skapar ett projekt som du kan publicera till en Function-app i Azure. Du kan använda en Function-app för att gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser.

1. Från Visual Studio-menyn väljer du **fil**  >  **nytt**  >  **projekt**.

1. I **skapa ett nytt projekt**, ange *funktioner* i sökrutan, Välj mallen **Azure Functions** och välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**anger du ett **projekt namn** för projektet och väljer sedan **skapa**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

1. Använd värdena i följande tabell för **program inställningarna skapa en ny Azure Functions** :

    | Inställning      | Värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Functions-körning** | **Azure Functions v3 <br /> (.net Core)** | Det här värdet skapar ett funktions projekt som använder version 3. x-körningen av Azure Functions, som stöder .NET Core 3. x. Azure Functions 1.x har stöd för .NET Framework. Mer information finns i [Översikt över Azure Functions körnings versioner](../articles/azure-functions/functions-versions.md).   |
    | **Funktions mal len** | **HTTP-utlösare** | Det här värdet skapar en funktion som utlöses av en HTTP-begäran. |
    | **Lagrings konto (AzureWebJobsStorage)**  | **Storage-emulator** | Eftersom en Azure-funktion kräver ett lagrings konto, tilldelas eller skapas en när du publicerar projektet till Azure. En HTTP-utlösare använder inte en anslutnings sträng för Azure Storage konto. alla andra utlösare kräver en giltig anslutnings sträng för Azure Storage konto.  |
    | **Auktoriseringsnivå** | **Antal** | Funktionen som skapats kan utlösas av alla klienter utan att en nyckel anges. Den här auktoriseringsinställningen gör det enkelt att testa den nya funktionen. Mer information om nycklar och auktorisering finns i [auktoriseringsarkiv](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) och [http-och webhook-bindningar](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Azure Functions projekt inställningar](./media/functions-vs-tools-create/functions-project-settings.png)

    Se till att du anger en **Anonym** **åtkomst nivå** . Om du väljer standard **funktions**nivån måste du presentera [funktions nyckeln](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) i begär Anden för att få åtkomst till funktions slut punkten.

1. Välj **skapa** för att skapa funktionen Project och funktionen http-utlösare.
