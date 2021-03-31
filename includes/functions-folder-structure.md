---
title: ta med fil
description: ta med fil
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018459"
---
Koden för alla funktioner i en speciell Function-app finns i en rotmapp som innehåller en värd konfigurations fil och en eller flera undermappar. Varje undermapp innehåller koden för en separat funktion. Mappstrukturen visas i följande representation:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

I version 2. x och högre av Functions-körningsmiljön måste alla funktioner i Function-appen dela samma språks tack.  

[host.jsi](../articles/azure-functions/functions-host-json.md) filen innehåller körnings bara konfigurationer och finns i rotmappen i Function-appen. En *bin* -mapp innehåller paket och andra biblioteks filer som funktions programmet kräver. Se språkspecifika krav för ett Function app-projekt:

* [C#-klass bibliotek (. CSPROJ)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F #-skript](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
