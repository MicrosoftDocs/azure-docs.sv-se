---
title: Ladda upp ett dokument – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Funktionen för att överföra dokument överför parallella dokument (två dokument där det är ursprunget och det andra är översättningen) till tjänsten.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 8597b3e4ffab44672e88374010fa829211fbc18b
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507337"
---
# <a name="upload-a-document"></a>Ladda upp ett dokument

I en [anpassad översättare](https://portal.customtranslator.azure.ai)kan du överföra parallella dokument för att träna dina översättnings modeller. [Parallella dokument](what-are-parallel-documents.md) är par av dokument där en är en översättning av det andra. Ett dokument i paret innehåller meningar i käll språket och det andra dokumentet innehåller de meningarna översatta till mål språket.

Innan du överför dokumenten granskar du [rikt linjerna för dokument format och namngivnings konvention](document-formats-naming-convention.md) för att kontrol lera att fil formatet stöds i en anpassad översättare.

## <a name="how-to-upload-document"></a>Hur överför du dokument?

Från den [anpassade översättnings](https://portal.customtranslator.azure.ai) portalen klickar du på fliken dokument för att gå till dokument sidan.

![Länk till dokument uppladdning](media/how-to/how-to-upload-1.png)


1.  Klicka på knappen Överför filer på sidan dokument.

    ![Ladda upp dokument sida](media/how-to/how-to-upload-2.png)

2.  Fyll i följande information i dialog rutan:

    a.  Dokument typ:

    -  Utbildning: de här dokumenten kommer att användas för inlärnings uppsättningen.
    -  Justering: de här dokumenten kommer att användas för justerings uppsättningen.
    -  Testning: de här dokumenten kommer att användas för testnings uppsättningen.
    -  Fras ord lista: de här dokumenten kommer att användas för fras ord lista.
    -  Ord lista: de här dokumenten kommer att användas för ord lista för mening

    b.  Språk par

    c.  Åsidosätt dokumentet om det finns: Markera den här kryss rutan om du vill skriva över befintliga dokument med samma namn.

    d.  Fyll i det relevanta avsnittet för antingen parallell data eller kombinations data.

    -  Parallella data:
        -  Käll fil: Välj käll språk filen från den lokala datorn.
        -  Målfil: Välj mål språk fil från den lokala datorn.
        -  Dokument namn: används endast om du överför parallella filer.

    - Kombinations data:
        -  Kombinations fil: Välj kombinations filen från den lokala datorn. Din kombinations fil har båda meningarna käll-och mål språk. [Namngivnings konvention](document-formats-naming-convention.md) är viktigt för kombinations filer.

    e.  Klicka på överför

    ![Dialog rutan Ladda upp dokument](media/how-to/how-to-upload-dialog.png)

3.  Nu bearbetar vi dina dokument och försöker extrahera meningar. Du kan klicka på "Visa uppladdnings förlopp" för att kontrol lera statusen för dina dokument när de bearbetas.

    ![Dialog rutan Ladda upp dokument bearbetning](media/how-to/how-to-upload-processing-dialog.png)

4.  Den här sidan visar status och eventuella fel för varje fil i överföringen. Du kan visa tidigare överförings status när som helst genom att klicka på fliken "Ladda upp historik".

    ![Dialog rutan Ladda upp dokument historik](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Visa överförings historik

På sidan överförings historik kan du Visa historik för alla dokument överförings detaljer som dokument typ, språk par, uppladdnings status osv.

1. Från den [anpassade översättnings](https://portal.customtranslator.azure.ai) portalen klickar du på fliken Ladda upp historik för att visa historik.

    ![Fliken Ladda upp historik](media/how-to/how-to-upload-history-1.png)

2. Den här sidan visar status för alla tidigare överföringar. Den visar överföringar från senaste till minst senaste. För varje uppladdning visas dokument namn, överförings status, uppladdnings datum, antal överförda filer, typ av överförd fil och språkets fil namn.

    ![Sidan Ladda upp historik](media/how-to/how-to-document-history-2.png)

3. Klicka på en post för överförings historik. På sidan information om överförings historik kan du Visa filerna som laddats upp som en del av överföringen, Uppladdad status för filen, språk för filen och fel meddelandet (om det finns några fel i överföringen).

## <a name="next-steps"></a>Nästa steg

- Använd [sidan dokument information](how-to-view-document-details.md) för att granska listan över extraherade meningar.
- [Hur man tränar en modell](how-to-train-model.md).
