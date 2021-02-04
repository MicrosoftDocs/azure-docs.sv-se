---
title: Importera dokument format rikt linjer – QnA Maker
description: Använd de här rikt linjerna för att importera dokument för att få bästa möjliga resultat för ditt innehåll.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549549"
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

### <a name="product-manuals"></a>Produkt handböcker

En manuell är vanligt vis ett hjälp material som medföljer en produkt. Det hjälper användaren att konfigurera, använda, underhålla och felsöka produkten. När QnA Maker bearbetar en manuell extraheras rubriker och under rubriker som frågor och efterföljande innehåll som svar. Se ett exempel [här](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Nedan visas ett exempel på en manuell med en index sida och hierarkiskt innehåll

 ![Produkt hand bok exempel för en kunskaps bas](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extraktion fungerar bäst på handböcker som har en innehålls förteckning och/eller en index sida och en tydlig struktur med hierarkiska rubriker.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broschyrer, rikt linjer, dokument och andra filer

Många andra typer av dokument kan också bearbetas för att generera frågor och svar, förutsatt att de har en tydlig struktur och layout. Dessa omfattar: broschyrer, rikt linjer, rapporter, fakta blad, vetenskapliga handlingar, principer, böcker osv. Se ett exempel [här](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Nedan visas ett exempel på ett delvis strukturerat dokument utan ett index:

 ![Halv strukturerat dokument i Azure Blob Storage](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Strukturerat QnA-dokument

Formatet för strukturerade Question-Answers i DOC-filer är i form av alternerande frågor och svar per rad, en fråga per rad följt av sitt svar på följande rad:

```text
Question1

Answer1

Question2

Answer2
```

Nedan visas ett exempel på ett strukturerat QnA Word-dokument:

 ![Exempel på strukturerade QnA-dokument för en kunskaps bas](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>Strukturerade *txt*-, *TSV* -och *xls* -filer

Kring i form av Structured *. txt*-, *. tsv* -eller *. xls* -filer kan också överföras till QNA Maker för att skapa eller utöka en kunskaps bas.  Dessa kan antingen vara oformaterad text eller ha innehåll i RTF eller HTML.

| Fråga  | Svar  | Metadata (1 nyckel: 1 värde) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Eventuella ytterligare kolumner i käll filen ignoreras.

#### <a name="example-of-structured-excel-file"></a>Exempel på strukturerad Excel-fil

Nedan visas ett exempel på en strukturerad QnA *. xls* -fil med HTML-innehåll:

 ![Strukturerad QnA Excel-exempel för en kunskaps bas](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exempel på alternativa frågor för ett enskilt svar i Excel-fil

Nedan visas ett exempel på en strukturerad QnA *. xls* -fil med flera alternativa frågor för ett enda svar:

 ![Exempel på alternativa frågor för ett enskilt svar i Excel-fil](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

När filen har importer ATS visas fråga-och-svar-paret i kunskaps basen enligt nedan:

 ![Skärm bild av alternativa frågor för enskilda svar som importeras till kunskaps basen](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>Strukturerat data format via import

Om du importerar en kunskaps bas ersätts innehållet i den befintliga kunskaps basen. Importen kräver en strukturerad. TSV-fil som innehåller information om data källan. Den här informationen hjälper QnA Maker att gruppera frågans svars par och attributerar dem till en viss data källa.

| Fråga  | Svar  | Källa| Metadata (1 nyckel: 1 värde) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redaktionellt|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>Flersidig dokumentformatering

* Använd rubriker och under rubriker för att beteckna hierarkier. Till exempel kan du ange den överordnade QnA och H2 för att ange den QnA som ska tas med i frågan. Använd liten rubrik storlek för att beteckna efterföljande hierarkier. Använd inte formatmall, färg eller någon annan mekanism för att göra en struktur i ditt dokument, QnA Maker inte extraherar flera inaktiverade prompter.
* Första bokstaven i rubriken måste vara kapitaliserad.
* Avsluta inte en rubrik med ett frågetecken, `?` .

**Exempel dokument**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-förmåner (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-förmåner (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>URL: er för vanliga frågor

QnA Maker kan stödja vanliga frågor och svar webb sidor i tre olika former:

* Vanliga FAQ-sidor
* Vanliga frågor och svar-sidor med länkar
* Vanliga frågor och svar med en sida med ämnes sidor

### <a name="plain-faq-pages"></a>Vanliga FAQ-sidor

Detta är den vanligaste typen av vanliga frågor och svar, där svaren omedelbart följer frågorna på samma sida.

Nedan visas ett exempel på en sida med vanliga frågor och svar:

![Exempel sidan Vanliga frågor och svar om en kunskaps bas](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Vanliga frågor och svar-sidor med länkar

På den här typen av vanliga frågor och svar sammanställs frågor tillsammans och är länkade till svar som finns i olika delar av samma sida eller på olika sidor.

Nedan visas ett exempel på en sida med vanliga frågor och svar med länkar i avsnitt som finns på samma sida:

 ![Exempel på avsnittet Vanliga frågor och svar om avsnitts länk för en kunskaps bas](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Sidan för överordnad ämnes sida länkar till underordnade svars sidor

Den här typen av vanliga frågor och svar innehåller en avsnitts sida där varje ämne är länkat till en motsvarande uppsättning frågor och svar på en annan sida. QnA Maker crawla alla länkade sidor för att extrahera motsvarande frågor & svar.

Nedan visas ett exempel på en ämnes sida med länkar till FAQ-avsnitt på olika sidor.

 ![Exempel på vanliga frågor om djupgående Länkar för en kunskaps bas](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>Support-URL: er

QnA Maker kan bearbeta delvis strukturerade support webb sidor, till exempel webb artiklar som beskriver hur du utför en specifik uppgift, hur du diagnostiserar och löser ett specifikt problem och vilka metoder som är bästa praxis för en specifik process. Extraktion fungerar bäst på innehåll som har en tydlig struktur med hierarkiska rubriker.

> [!NOTE]
> Extrahering av support artiklar är en ny funktion och är i tidiga steg. Det fungerar bäst för enkla sidor, som är väl strukturerade och inte innehåller komplexa sidhuvuden och sid fötter.

![QnA Maker stöder extrahering från halv strukturerade webb sidor där en tydlig struktur visas med hierarkiska rubriker](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>Importera och exportera kunskaps bas

**TSV-och XLS-filer**, från exporterade kunskaps baser, kan bara användas genom att importera filerna från sidan **Inställningar** på QNA Maker portalen. De kan inte användas som data källor när du skapar kunskaps basen eller från **+ Lägg till fil** eller **+ Lägg till URL** -funktionen på sidan **Inställningar** . 

När du importerar kunskaps basen via dessa **TSV-och XLS-filer** läggs QNA-par till i redigerings källan och inte källorna som kring extraherades från i den exporterade kunskaps basen. 


## <a name="next-steps"></a>Nästa steg

Se en fullständig lista över [innehålls typer och exempel](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)
