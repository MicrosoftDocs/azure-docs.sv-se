---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: f102a5dd5b7dccba6643176d06d17a2a65171c90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904084"
---
## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ [Azure Functions Core tools](../articles/azure-functions/functions-run-local.md#v2) version 2.7.1846 eller en senare version.
::: zone-end  
::: zone pivot="programming-language-python"
+ Den Azure Functions Core Tools version som motsvarar din installerade python-version:

   | Python-version | Core tools-version |
   | -------------- | ------------------ |
   | Python 3.8     | [version 3. x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3,6<br/>Python 3.7 | [Version 2.7.1846 eller en senare version](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), aktiva LTS-och UNDERHÅLLs LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,8 (64-bitars)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), som stöds av Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [.NET Core SDK 3,1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8 eller 11. 

+ [Apache maven](https://maven.apache.org), version 3,0 eller senare.

::: zone-end
::: zone pivot="programming-language-other"
+ Utvecklingsverktyg för det språk som du använder. I den här självstudien används [R-programmeringsspråket](https://www.r-project.org/) som exempel.
::: zone-end