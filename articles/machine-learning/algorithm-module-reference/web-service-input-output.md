---
title: 'Webb tjänstens indata/utdata: modulreferens'
description: Lär dig hur du använder webbtjänst-modulerna i Azure Machine Learning designer för att hantera indata och utdata.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 475ad3de8e0a7636a14949d4fcd8a5ec2812ad5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421132"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Moduler för indata för webb tjänst och webb tjänst

Den här artikeln beskriver modulerna för indata för webb tjänsten och webb tjänstens utdata i Azure Machine Learning designer.

Webb tjänstens indatamängd kan bara ansluta till en indataport med typen **DataFrameDirectory**. Webb tjänstens output-modul kan bara anslutas från en utgående port med typen **DataFrameDirectory**. Du hittar de två modulerna i modul trädet under **webb tjänst** kategorin. 

Webb tjänstens inmatnings modul visar var användar data går in i pipelinen. Webb tjänstens output-modul visar var användar data returneras i en pipeline för real tids härledning.

## <a name="how-to-use-web-service-input-and-output"></a>Använda webb tjänstens indata och utdata

När du [skapar en härlednings pipeline i real tid](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) från din utbildnings pipeline, läggs webb tjänstens utdata-och webb tjänst utmatnings moduler automatiskt till för att visa var användar data går in i pipelinen och var data returneras. 

> [!NOTE]
> Automatisk generering av en härlednings pipeline i real tid är en regel baserad och bästa process. Det finns ingen garanti för korrekthet. 

Du kan manuellt lägga till eller ta bort modulerna för indata för webb tjänsten och webb tjänstens utdata för att uppfylla dina krav. Kontrol lera att pipelinen för real tids härledning har minst en modul för indata för webb tjänst och en modul för webb tjänst utmatning. Om du har flera webb tjänst indata eller moduler för webb tjänst utmatning kontrollerar du att de har unika namn. Du kan ange namnet i den högra panelen i modulen.

Du kan också skapa en pipeline för real tids härledning manuellt genom att lägga till webb tjänstens indata och moduler för webb tjänstens utdata i en inskickad pipeline.

> [!NOTE]
> Pipelinen kommer att fastställas första gången du skickar den. Se till att lägga till moduler för indata för webb tjänst och webb tjänst innan du skickar för första gången.

I följande exempel visas hur du manuellt skapar real tids härlednings pipelinen från EXECUTE python script module. 

![Exempel](media/module/web-service-input-output-example.png)
   
När du har skickat pipelinen och körningen är klar kan du distribuera real tids slut punkten.
   
> [!NOTE]
>  I föregående exempel **anger data manuellt** data schema för webb tjänstens indata och är nödvändigt för att distribuera real tids slut punkten. I allmänhet bör du alltid ansluta en modul eller data uppsättning till porten där **webb tjänstens indata** är anslutna för att tillhandahålla data schemat.
   
## <a name="next-steps"></a>Nästa steg
Lär dig mer om [att distribuera real tids slut punkten](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint).

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.