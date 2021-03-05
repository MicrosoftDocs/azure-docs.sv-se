---
author: baanders
description: inkludera fil för DefaultAzureCredential i Azure Digitals-exempel – Obs!
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8aa3cbb2a037e49a694192c9852eeae0215c089c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211796"
---
>[!NOTE]
> I det här exemplet används [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (del av `Azure.Identity` biblioteket) för att autentisera användare med Azure Digitals-instansen när du kör den på din lokala dator. Med den här typen av autentisering söker exemplet efter autentiseringsuppgifter för Azure i din lokala miljö, till exempel en inloggning från en lokal [Azure CLI](/cli/azure/install-azure-cli) eller Visual Studio/Visual Studio Code.
>
> Mer `DefaultAzureCredential` information om hur du använder och andra autentiseringsalternativ finns i [*How-to: Write app authentication code*](../articles/digital-twins/how-to-authenticate-client.md).