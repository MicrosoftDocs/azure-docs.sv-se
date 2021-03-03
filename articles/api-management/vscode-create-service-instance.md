---
title: Skapa en Azure API Management-instans med Visual Studio Code | Microsoft Docs
description: Visual Studio Code för att skapa en Azure API Management-instans.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: 3105b6f34d7ece81e8145fdd9e89568e66360ddb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649520"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>Snabb start: skapa en ny Azure API Management-tjänstinstans med Visual Studio Code

Azure API Management (APIM) hjälper organisationer att publicera API:er till externa partner och interna utvecklare så att de kan få ut maximalt av sina data och tjänster. API Management lägger grunden till ett effektivt API-program genom engagerade utvecklare, affärsinsikter, analyser, hög säkerhet och skydd. Med APIM kan du skapa och hantera moderna API-gatewayer för befintliga serverdeltjänster som ligger var som helst. Mer information finns i ämnet [Översikt](api-management-key-concepts.md).

I den här snabb starten beskrivs stegen för att skapa en ny API Management-instans med hjälp av *Azure API Management-tillägget* för Visual Studio Code. Du kan också använda tillägget för att utföra vanliga hanterings åtgärder på din API Management-instans.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Kontrol lera också att du har installerat följande:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure API Management-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Starta Visual Studio Code och öppna Azure-tillägget. (Om du inte ser Azure-ikonen i aktivitets fältet kontrollerar du att *azure API Management* -tillägget är aktiverat.)

Välj **Logga in på Azure...** för att starta ett webbläsarfönster och logga in på din Microsoft-konto.

![Logga in på Azure från API Management tillägget för VS Code](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>Skapa en API Management-tjänst

När du har loggat in på Microsoft-konto visas dina Azure-prenumerationer i fönstret *Azure: API Management* Explorer.

Högerklicka på den prenumeration du vill använda och välj **skapa API Management i Azure**.

![Guiden skapa API Management i VS Code](./media/vscode-create-service-instance/vscode-apim-create.png)

I fönstret som öppnas anger du ett namn för den nya API Management-instansen. Det måste vara globalt unikt inom Azure och bestå av 1-50 alfanumeriska tecken och/eller bindestreck, och börja med en bokstav och sluta med en alfanumerisk.

En ny API Management instans (och överordnad resurs grupp) skapas med det angivna namnet. Som standard skapas instansen i regionen *västra USA* med *förbruknings* -SKU.

> [!TIP]
> Om du aktiverar **Avancerad generering** i *inställningarna för Azure API Management-tillägget* kan du också ange en [API Management SKU](https://azure.microsoft.com/pricing/details/api-management/), [Azure-region](https://status.azure.com/en-us/status)och en [resurs grupp](../azure-resource-manager/management/overview.md) för att distribuera din API Management-instans.
>
> Även om *förbruknings* -SKU: n tar mindre än en minut att etablera, tar det vanligt vis 30-40 minuter att skapa andra SKU: er.

Nu är du redo att importera och publicera ditt första API. Du kan göra det och även utföra vanliga API Management åtgärder i tillägget för Visual Studio Code. Mer information finns i [självstudien](visual-studio-code-tutorial.md) .

![Nyligen skapad API Management instans i VS Code API Management förlängnings fönstret](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver tar du bort API Management-instansen genom att högerklicka och välja **Öppna i portalen** för att [ta bort API Management-tjänsten](get-started-create-service-instance.md#clean-up-resources) och dess resurs grupp.

Alternativt kan du välja **ta bort API Management** för att bara ta bort API Management-instansen (den här åtgärden tar inte bort resurs gruppen).

![Ta bort API Management instans från VS Code](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera och hantera API: er med API Management-tillägget](visual-studio-code-tutorial.md)
