---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88689579"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Följande egenskaper är tillgängliga i `extensionBundle` :

| Egenskap | Beskrivning |
| -------- | ----------- |
| id | Namn området för Microsoft Azure Functions-paket för tillägg. |
| version | Den version av paketet som ska installeras. Functions-körningen plockar alltid den högsta tillåtna versionen som definieras av versions intervallet eller intervallet. Version svärdet ovan tillåter alla paket versioner från 1.0.0 upp till men inte inklusive 2.0.0. Mer information finns i [intervall notation för att ange versions intervall](/nuget/reference/package-versioning#version-ranges). |