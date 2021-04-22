---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7a390c0d19a37ea18f9eac8636683ec35ecbc844
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881433"
---
## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ Versionen [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) version 2.7.1846 eller senare.
::: zone-end  
::: zone pivot="programming-language-python"
+ Den Azure Functions Core Tools version som motsvarar din installerade Python-version:

   | Python-version | Core Tools-version |
   | -------------- | ------------------ |
   | Python 3.8     | [version 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Version 2.7.1846 eller senare](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 eller senare. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/)versionerna Active LTS och Maintenance LTS (8.11.1 och 10.14.1 rekommenderas).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-bitars),](https://www.python.org/downloads/release/python-382/) [Python 3.7 (64-bitars)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bitars)](https://www.python.org/downloads/release/python-368/), som stöds av Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit,](/azure/developer/java/fundamentals/java-jdk-long-term-support)version 8 eller 11. 

+ [Apache Maven](https://maven.apache.org), version 3.0 eller senare.

::: zone-end
::: zone pivot="programming-language-other"
+ Utvecklingsverktyg för det språk du använder. I den här [självstudien används programmeringsspråket R](https://www.r-project.org/) som exempel.
::: zone-end