---
author: baanders
description: inkludera fil för DefaultAzureCredential i Azure Digitals-exempel – Obs!
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102473729"
---
>[!NOTE]
> I det här exemplet används [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (del av `Azure.Identity` biblioteket) för att autentisera användare med Azure Digitals-instansen när du kör den på din lokala dator. Med den här typen av autentisering söker exemplet efter autentiseringsuppgifter för Azure i din lokala miljö, till exempel en inloggning från en lokal [Azure CLI](/cli/azure/install-azure-cli) eller Visual Studio/Visual Studio Code.
>
> Mer `DefaultAzureCredential` information om hur du använder och andra autentiseringsalternativ finns i [*How-to: Write app authentication code*](../articles/digital-twins/how-to-authenticate-client.md).