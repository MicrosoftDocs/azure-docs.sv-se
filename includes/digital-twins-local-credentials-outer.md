---
author: baanders
description: inkludera fil för att konfigurera lokal autentisering för DefaultAzureCredential i Azure Digitals-exempel med Intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: dcef8030c47e771e5cd771dac09b5f96e3d38abd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102473790"
---
### <a name="set-up-local-azure-credentials"></a>Konfigurera lokala autentiseringsuppgifter för Azure

I det här exemplet används [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (del av `Azure.Identity` biblioteket) för att autentisera användare med Azure Digitals-instansen när du kör den på din lokala dator. Mer information om olika sätt som en klient-app kan autentisera med Azure Digitals dubbla finns i [*How-to: Write app authentication code*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]