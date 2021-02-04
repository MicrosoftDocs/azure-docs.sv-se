---
title: Data källor och innehålls typer – QnA Maker
description: Lär dig hur du importerar fråga-och svars par från data källor och innehålls typer som stöds, som innehåller många standard strukturerade dokument, till exempel PDF, DOCX och TXT-QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 0d4d32aba34a97c6a060c999694f66d79933d011
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556047"
---
# <a name="importing-from-data-sources"></a>Importerar från datakällor

En kunskaps bas består av fråge-och svars par som tas i offentliga URL: er och filer.

## <a name="data-source-locations"></a>Platser för data källor

Innehållet samlas in i en kunskaps bas från en data källa. Platser för data källor är **offentliga URL: er eller filer** som inte kräver autentisering.

[SharePoint-filer](../how-to/add-sharepoint-datasources.md), säkra med autentisering, är undantaget. SharePoint-resurser måste vara filer, inte webb sidor. Om URL:en slutar med ett webbtillägg (till exempel .ASPX) importeras den inte till QnA Maker från SharePoint.

## <a name="chit-chat-content"></a>Innehåll i CHI2TEST

Innehålls uppsättningen CHI2TEST-chatt erbjuds som en fullständig innehålls data källa på flera språk och konversations format. Detta kan vara en utgångs punkt för din robots personlighet, och det sparar tid och kostnad för att skriva dem från grunden. Lär dig [hur du lägger till innehåll i CHI2TEST](../how-to/chit-chat-knowledge-base.md) i din kunskaps bas.

## <a name="structured-data-format-through-import"></a>Strukturerat data format via import

Om du importerar en kunskaps bas ersätts innehållet i den befintliga kunskaps basen. Importen kräver en strukturerad `.tsv` fil som innehåller frågor och svar. Den här informationen hjälper QnA Maker att gruppera frågans svars par och attributerar dem till en viss data källa.

| Fråga  | Svar  | Källa| Metadata (1 nyckel: 1 värde) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redaktionellt|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturerat multi-turn-format genom import

Du kan skapa flera-turn-konversationer i ett `.tsv` fil format. Formatet ger dig möjlighet att skapa fler tråds samtal genom att analysera tidigare chatt-loggar (med andra processer, inte använda QnA Maker) och sedan skapa `.tsv` filen via Automation. Importera filen för att ersätta den befintliga kunskaps basen.

> [!div class="mx-imgBorder"]
> ![Konceptuell modell av 3 nivåer av fråga i flera vända frågor](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Kolumnen för en multi-turn `.tsv` , som är speciell för flera-Turn, **visas**. Ett exempel `.tsv` , som visas i Excel, visar den information som ska inkluderas för att definiera underordnade noder i flera varv:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** är numeriskt och **visnings** texten är text som inte innehåller markdown.

> [!div class="mx-imgBorder"]
> ![Exempel på flera frågor som visas i Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportera som exempel

Om du är osäker på hur du kan representera QnA-paret i `.tsv` filen:
* Använd det här [nedladdnings bara exemplet från GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Eller skapa paret i QnA Maker-portalen, Spara och exportera sedan kunskaps basen för ett exempel på hur du kan representera paret.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Innehålls typer av dokument som du kan lägga till i en kunskaps bas
Innehålls typer innehåller många standard strukturerade dokument som PDF, DOC och TXT.

### <a name="file-and-url-data-types"></a>Fil-och URL-datatyper

Tabellen nedan sammanfattar de typer av innehåll och fil format som stöds av QnA Maker.

|Källtyp|Innehållstyp| Exempel|
|--|--|--|
|URL|Vanliga frågor och svar<br> (Platt, med avsnitt eller med hjälp av avsnitts start sida)<br>Support sidor <br> (Enkla sid artiklar, fel söknings artiklar osv.)|[Vanliga frågor och svar](../troubleshooting.md), <br>[Vanliga frågor och svar med länkar](https://www.microsoft.com/en-us/software-download/faq),<br> [Vanliga frågor och svar om ämnes Sidan](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Support artikel](./best-practices.md)|
|PDF/DOK|Vanliga frågor<br> Produkt handbok,<br> Broschyrer,<br> Grön<br> Reklamblads princip,<br> Support guide,<br> Strukturerad QnA,<br> och så vidare.|**Utan flera turn**<br>[Strukturerad QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Exempel produkt Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exempel semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel på white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Flera turn**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-förmåner (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-förmåner (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Strukturerad QnA-fil<br> (inklusive RTF, HTML-stöd)|**Utan flera turn**:<br>[Exempel på QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Flera turn**:<br>[Strukturerad enkel FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[FAQ.xlsför Surface-dator ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Strukturerad QnA-fil|[Exempel på Chit-Chat. tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Om du behöver autentisering för din data källa kan du använda följande metoder för att hämta innehållet till QnA Maker:

* Ladda ned filen manuellt och importera till QnA Maker
* Lägg till filen från den autentiserade [SharePoint-platsen](../how-to/add-sharepoint-datasources.md)

### <a name="url-content"></a>URL-innehåll

Två typer av dokument kan importeras via **URL** i QNA Maker:

* URL: er för vanliga frågor
* Support-URL: er

Varje typ indikerar ett förväntat format.

### <a name="file-based-content"></a>Filbaserat innehåll

Du kan lägga till filer i en kunskaps bas från en offentlig källa, eller det lokala fil systemet, i [QNA Maker portalen](https://www.qnamaker.ai).

### <a name="content-format-guidelines"></a>Formatriktlinjer för innehåll

Läs mer om [format rikt linjerna](../reference-document-format-guidelines.md) för de olika filerna.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [redigerar kring](../how-to/edit-knowledge-base.md).
