---
title: Importera dokument format rikt linjer – QnA Maker
description: Använd de här rikt linjerna för att importera dokument för att få bästa möjliga resultat för ditt innehåll.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 76b33d0c09df87196b8fc1f3cbbb0fd06bd30ce1
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430653"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Format rikt linjer för importerade dokument och webb adresser

Granska dessa rikt linjer för formatering för att få bästa möjliga resultat för ditt innehåll.

## <a name="formatting-considerations"></a>Överväganden vid formatering

När du har importerat en fil eller URL, QnA Maker konvertera och lagra innehållet i [markdown-formatet](https://en.wikipedia.org/wiki/Markdown). Konverterings processen lägger till nya rader i texten, t `\n\n` . ex.. En kunskap om markdown-formatet hjälper dig att förstå det konverterade innehållet och hantera ditt kunskaps bas innehåll.

Om du lägger till eller redigerar innehållet direkt i din kunskaps bas använder du **markdown-formatering** för att skapa RTF-innehåll eller ändra markdown format innehåll som redan finns i svaret. QnA Maker stöder mycket av markdown-formatet för att få RTF-funktioner till ditt innehåll. Men klient programmet, till exempel en chatt-robot, kanske inte stöder samma uppsättning markdown-format. Det är viktigt att testa klient programmets visning av svar.

Se en fullständig lista över [innehålls typer och exempel](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base).

## <a name="basic-document-formatting"></a>Grundläggande dokumentformatering

QnA Maker identifierar avsnitt och underavsnitt och relationer i filen baserat på visuella LED trådar som:

* teckenstorlek
* tecken stil
* numreringen
* färger

> [!NOTE]
> Vi stöder inte extrahering av avbildningar från överförda dokument för närvarande.

## <a name="product-manuals"></a>Produkt handböcker

En manuell är vanligt vis ett hjälp material som medföljer en produkt. Det hjälper användaren att konfigurera, använda, underhålla och felsöka produkten. När QnA Maker bearbetar en manuell extraheras rubriker och under rubriker som frågor och efterföljande innehåll som svar. Se ett exempel [här](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Nedan visas ett exempel på en manuell med en index sida och hierarkiskt innehåll

 ![Produkt hand bok exempel för en kunskaps bas](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extraktion fungerar bäst på handböcker som har en innehålls förteckning och/eller en index sida och en tydlig struktur med hierarkiska rubriker.

## <a name="brochures-guidelines-papers-and-other-files"></a>Broschyrer, rikt linjer, dokument och andra filer

Många andra typer av dokument kan också bearbetas för att generera frågor och svar, förutsatt att de har en tydlig struktur och layout. Dessa omfattar: broschyrer, rikt linjer, rapporter, fakta blad, vetenskapliga handlingar, principer, böcker osv. Se ett exempel [här](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Nedan visas ett exempel på ett delvis strukturerat dokument utan ett index:

 ![Halv strukturerat dokument i Azure Blob Storage](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturerat QnA-dokument

Formatet för strukturerade Question-Answers i DOC-filer är i form av alternerande frågor och svar per rad, en fråga per rad följt av sitt svar på följande rad:

```text
Question1

Answer1

Question2

Answer2
```

Nedan visas ett exempel på ett strukturerat QnA Word-dokument:

 ![Exempel på strukturerade QnA-dokument för en kunskaps bas](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturerade *txt*-, *TSV* -och *xls* -filer

Kring i form av Structured *. txt*-, *. tsv* -eller *. xls* -filer kan också överföras till QNA Maker för att skapa eller utöka en kunskaps bas.  Dessa kan antingen vara oformaterad text eller ha innehåll i RTF eller HTML.

| Fråga  | Svar  | Metadata (1 nyckel: 1 värde) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Eventuella ytterligare kolumner i käll filen ignoreras.

### <a name="example-of-structured-excel-file"></a>Exempel på strukturerad Excel-fil

Nedan visas ett exempel på en strukturerad QnA *. xls* -fil med HTML-innehåll:

 ![Strukturerad QnA Excel-exempel för en kunskaps bas](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exempel på alternativa frågor för ett enskilt svar i Excel-fil

Nedan visas ett exempel på en strukturerad QnA *. xls* -fil med flera alternativa frågor för ett enda svar:

 ![Exempel på alternativa frågor för ett enskilt svar i Excel-fil](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

När filen har importer ATS visas fråga-och-svar-paret i kunskaps basen enligt nedan:

 ![Skärm bild av alternativa frågor för enskilda svar som importeras till kunskaps basen](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturerat data format via import

Om du importerar en kunskaps bas ersätts innehållet i den befintliga kunskaps basen. Importen kräver en strukturerad. TSV-fil som innehåller information om data källan. Den här informationen hjälper QnA Maker att gruppera frågans svars par och attributerar dem till en viss data källa.

| Fråga  | Svar  | Källa| Metadata (1 nyckel: 1 värde) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redaktionellt|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Flersidig dokumentformatering

* Använd rubriker och under rubriker för att beteckna hierarkier. Till exempel kan du ange den överordnade QnA och H2 för att ange den QnA som ska tas med i frågan. Använd liten rubrik storlek för att beteckna efterföljande hierarkier. Använd inte formatmall, färg eller någon annan mekanism för att göra en struktur i ditt dokument, QnA Maker inte extraherar flera inaktiverade prompter.
* Första bokstaven i rubriken måste vara kapitaliserad.
* Avsluta inte en rubrik med ett frågetecken, `?` .

**Exempel dokument**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-förmåner (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-förmåner (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Nästa steg

Se en fullständig lista över [innehålls typer och exempel](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)
