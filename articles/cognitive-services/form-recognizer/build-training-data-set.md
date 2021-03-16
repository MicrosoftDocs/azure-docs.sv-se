---
title: Skapa en tränings data uppsättning för en anpassad modell för formulär igenkänning
titleSuffix: Azure Cognitive Services
description: Lär dig hur du ser till att din tränings data uppsättning är optimerad för att träna en formulär igenkännings modell.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: lajanuar
ms.openlocfilehash: b33ac3cb710a2d2a9d92efadf14dc829cb5da6e8
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467501"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Skapa en tränings data uppsättning för en anpassad modell

När du använder den anpassade modellen för formulär igenkänning kan du ange dina egna utbildnings data till åtgärden [träna anpassad modell](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) så att modellen kan träna till dina branschspecifika formulär. Följ den här guiden för att lära dig hur du samlar in och förbereder data för att träna modellen på ett effektivt sätt.

Du behöver minst fem ifyllda formulär av samma typ.

Om du vill använda manuellt märkta utbildnings data måste du börja med minst fem ifyllda formulär av samma typ. Du kan fortfarande använda omärkta formulär utöver den obligatoriska data uppsättningen.

## <a name="custom-model-input-requirements"></a>Krav för anpassad modell indatamängd

Se först till att din tränings data uppsättning följer kraven för indata för formulär igenkänning.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Tips om tränings data

Följ dessa ytterligare tips för att optimera dina data uppsättningar för utbildning.

* Använd om möjligt textbaserade PDF-dokument i stället för bildbaserade dokument. Skannade PDF-filer hanteras som bilder.
* För ifyllda formulär använder du exempel som har alla sina fält ifyllda.
* Använd formulär med olika värden i varje fält.
* Om dina formulär bilder är av lägre kvalitet kan du använda en större data uppsättning (till exempel 10-15 bilder).

## <a name="upload-your-training-data"></a>Ladda upp dina utbildnings data

När du har lagt samman uppsättningen med formulär dokument som du ska använda för utbildning måste du ladda upp den till en Azure Blob Storage-behållare. Om du inte vet hur du skapar ett Azure Storage-konto med en behållare följer du [Azure Storage snabb start för Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). Använd standard prestanda nivån.

Om du vill använda manuellt märkta data måste du också ladda upp *.labels.js* och *.ocr.jspå* filer som motsvarar dina utbildnings dokument. Du kan använda [exempel etikett verktyget](./quickstarts/label-tool.md) (eller ditt eget användar gränssnitt) för att generera dessa filer.

### <a name="organize-your-data-in-subfolders-optional"></a>Organisera dina data i undermappar (valfritt)

Som standard använder [träna anpassad modell](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) -API bara formulär dokument som finns i roten för din lagrings behållare. Du kan dock träna med data i undermappar om du anger den i API-anropet. Normalt har själva bröd texten i det [anpassade modell](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) anropet följande format, där `<SAS URL>` är URL: en för signaturen för delad åtkomst för din behållare:

```json
{
  "source":"<SAS URL>"
}
```

Om du lägger till följande innehåll i begär ande texten tränar API: n med dokument som finns i undermappar. `"prefix"`Fältet är valfritt och begränsar tränings data uppsättningen till filer vars sökvägar börjar med den angivna strängen. Värdet `"Test"` , till exempel, kommer att orsaka att API: t bara tittar på de filer eller mappar som börjar med ordet "test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar en tränings data uppsättning följer du en snabb start för att träna en anpassad formulär igenkännings modell och börja använda den i formulären.

* [Träna en modell och extrahera formulär data med hjälp av klient biblioteket eller REST API](./quickstarts/client-library.md)
* [Träna med etiketter med hjälp av verktyget för exempel märkning](./quickstarts/label-tool.md)

## <a name="see-also"></a>Se även

* [Vad är formigenkänning?](./overview.md)