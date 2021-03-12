---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 01e62685fa73a7f547ef5246b28fdfdf659e7afa
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623484"
---
|Språk                                 |1.x         |2x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1)<br/>[För hands version (.NET 5,0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Node 6)|GA (nod 10 & 8)| GA (nod 14, 12, & 10) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|Ej tillämpligt|GA (Java 8)| GA (Java 11 & 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |Ej tillämpligt|GA (PowerShell Core 6)| GA (PowerShell 7 & Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|Ej tillämpligt|GA (python 3,7 & 3,6)| GA (python 3,8, 3,7, & 3,6) <br/> För hands version (python 3,9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |Ej tillämpligt|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> .NET-klass biblioteks program som är mål för körnings version 2. x kan nu köras på .net Core 3,1 i .net Core 2. x kompatibilitetsläge. Mer information finns i avsnittet [funktioner v2. x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> stöds via Transpiling till Java Script.

Mer information om vilka språk versioner som stöds finns i artikeln språkspecifika guide för utvecklare.   
Information om planerade ändringar av språk stöd finns i [Azure-översikt](https://azure.microsoft.com/roadmap/?tag=functions).
