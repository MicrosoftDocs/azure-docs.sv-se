---
author: baanders
description: inkludera fil som beskriver en kodlösning för begränsningen mellan klientorganisationen med Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589384"
---
I följande exempel visas hur du anger ett klientorganisations-ID-värde `InteractiveBrowserTenantId` för i `DefaultAzureCredential` alternativen:

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="Skärmbild av kod som visar metoden DefaultAzureCredentialOptions. Värdet för InteractiveBrowserTenantId anges till ett exempelvärde för klientorganisations-ID.":::

Det finns liknande alternativ för att ange en klient för autentisering med Visual Studio och Visual Studio Code. Mer information om tillgängliga alternativ finns i dokumentationen [DefaultAzureCredentialOptions.](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true)