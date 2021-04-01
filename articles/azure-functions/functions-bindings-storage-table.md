---
title: Azure Table Storage-bindningar för Azure Functions
description: Lär dig hur du använder Azure Table Storage-bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92096733"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table Storage-bindningar för Azure Functions

Azure Functions integreras med [Azure Storage](../storage/index.yml) via [utlösare och bindningar](./functions-triggers-bindings.md). Genom att integrera med Table Storage kan du skapa funktioner som läser och skriver tabell lagrings data.

| Action | Typ |
|---------|---------|
| Läsa tabell lagrings data i en funktion | [Binda in](./functions-bindings-storage-table-input.md) |
| Tillåt en funktion att skriva tabell lagrings data |[Utgående bindning](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

Tabellerna Storage-bindningar finns i [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3. x. Käll koden för paketet finns i [Azure-WebJobs-SDK GitHub-](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Paket-funktioner 1. x

Tabellerna Storage-bindningar finns i [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x. Käll koden för paketet finns i [Azure-WebJobs-SDK GitHub-](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Nästa steg

- [Läsa tabell lagrings data när en funktion körs](./functions-bindings-storage-table-input.md)
- [Skriv tabell lagrings data från en funktion](./functions-bindings-storage-table-output.md)
