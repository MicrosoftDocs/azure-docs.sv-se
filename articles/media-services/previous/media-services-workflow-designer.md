---
title: Skapa avancerade kodnings arbets flöden med arbetsflödesdesigner | Microsoft Docs
description: Lär dig mer om hur du skapar avancerade kodnings arbets flöden med arbetsflödesdesigner.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 8173da37792948e267aae2078fee9f864bf7bdc9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011161"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Skapa avancerade arbetsflöden för kodning med Workflow Designer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Översikt
**Arbetsflödesdesigner** är ett Windows Desktop-verktyg som används för att utforma och skapa anpassade arbets flöden för kodning med **Media Encoder Premium Workflow**.
Med hjälp av kraften i arbets flödes design verktyget kan du utforma och skapa komplexa arbets flöden som ska köras i **Media Encoder Premium**.  

Arbets flöden kan innehålla kund besluts logik och branchning baserat på filens egenskaper för indatakälla. Du kan skapa arbets flöden med åsidosättningsbar-egenskaper och dynamiska värden för att göra även de mest komplexa kodnings aktiviteterna enkla att upprepa och anpassa i molnet.

Exempel på arbets flöden som du kan skapa är:

* Beslut baserade arbets flöden som inspekterar käll innehållet för lösning och kodar bara de önskade utdata-spåren.  Detta är användbart genom att eliminera de kasserade spåren som skulle genereras genom att minska käll innehållet oavsiktligt.
* Du kan använda flera indatafiler för att stödja under texter, överlägg och häfta samman innehåll. 

Det här verktyget kan också användas för att ändra alla [publicerade arbets flöden](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Kontakta om du vill hämta en kopia av arbetsflödesdesigner-verktyget mepd@microsoft.com .

När en arbets flödes fil har skapats kan den laddas upp som en till gång och sedan användas för kodning av mediefiler. Information om hur du kodar med **Media Encoder Premium Workflow** med hjälp av **.net** finns i [Avancerad kodning med Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Ändra befintliga arbets flöden
De [publicerade standard arbets flödena](media-services-workflow-designer.md#existing_workflows) kan ändras med hjälp av verktyget designer. Du kan hämta standard arbets flödes filerna [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Mappen innehåller också en beskrivning av de här filerna.

Följande videor visar hur du använder designern.

### <a name="day-1--getting-started"></a>Dag 1 – Komma igång
Dag 1 video omslag:

* Översikt över designer
* Basic-arbetsflöden – "Hello World"
* Skapa flera MP4-filer för användning med Azure Media Services strömning

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dag 2
Dag 2 video försättsblad:

* Varierande käll fils scenarier – hantera ljud
* Arbets flöden med avancerad logik
* Diagram steg

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dag 3
Dag 3 video försättsblad:

* Skript i arbets flöden/ritningar
* Begränsningar med aktuell kodare
* Vanliga frågor och svar

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna ett support ärende genom att gå till [nytt support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Azure Premium-kodare arbetsflödesdesigner utbildnings videor](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

