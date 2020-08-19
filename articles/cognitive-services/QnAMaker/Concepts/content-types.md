---
title: Innehålls typer-QnA Maker
description: Innehålls typer innehåller många standard strukturerade dokument som PDF, DOCX och TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 04d7c46a7aa347718cf6bc593a92a2f0430eb6fd
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263171"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Innehålls typer av dokument som du kan lägga till i en kunskaps bas
Innehålls typer innehåller många standard strukturerade dokument som PDF, DOC och TXT.

## <a name="file-and-url-data-types"></a>Fil-och URL-datatyper

Tabellen nedan sammanfattar de typer av innehåll och fil format som stöds av QnA Maker.

|Källtyp|Innehållstyp| Exempel|
|--|--|--|
|URL|Vanliga frågor och svar<br> (Platt, med avsnitt eller med hjälp av avsnitts start sida)<br>Support sidor <br> (Enkla sid artiklar, fel söknings artiklar osv.)|[Vanliga frågor och svar](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Vanliga frågor och svar med länkar](https://www.microsoft.com/en-us/software-download/faq),<br> [Vanliga frågor och svar om ämnes Sidan](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Support artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOK|Vanliga frågor<br> Produkt handbok,<br> Broschyrer,<br> Grön<br> Reklamblads princip,<br> Support guide,<br> Strukturerad QnA,<br> och så vidare.|**Utan flera turn**<br>[Strukturerad QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Exempel produkt Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exempel semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel på white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Flera turn**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-förmåner (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-förmåner (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Strukturerad QnA-fil<br> (inklusive RTF, HTML-stöd)|**Utan flera turn**:<br>[Exempel på QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Flera turn**:<br>[Strukturerad enkel FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[FAQ.xlsför Surface-dator ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Strukturerad QnA-fil|[Exempel på Chit-Chat. tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Om du behöver autentisering för din data källa kan du använda följande metoder för att hämta innehållet till QnA Maker:

* Ladda ned filen manuellt och importera till QnA Maker
* Lägg till fil från autentiserad [SharePoint-plats](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>URL-innehåll

Två typer av dokument kan importeras via **URL** i QNA Maker:

* URL: er för vanliga frågor
* Support-URL: er

Varje typ indikerar ett förväntat format.

## <a name="file-based-content"></a>Filbaserat innehåll

Du kan lägga till filer i en kunskaps bas från en offentlig källa, eller det lokala fil systemet, i [QNA Maker portalen](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Formatriktlinjer för innehåll

Läs mer om [format rikt linjerna](../reference-document-format-guidelines.md) för de olika filerna.

## <a name="next-steps"></a>Nästa steg

Förstå vilken information som lagras i ett [QNA-par (frågor och svar)](question-answer-set.md).
