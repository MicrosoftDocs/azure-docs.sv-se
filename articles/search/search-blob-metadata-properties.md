---
title: Egenskaper för innehålls metadata
titleSuffix: Azure Cognitive Search
description: Metadata-egenskaper för dokument kan ge innehåll till fält i ett sökindex eller information som informerar indexerings beteende vid körning. Den här artikeln innehåller metadata-egenskaper som stöds i Azure Kognitiv sökning.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: cbb35f596a1d32816d1a73b462bf590d9dde0d52
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668426"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Egenskaper för innehålls metadata som används i Azure Kognitiv sökning

SharePoint Online och Azure Blob Storage kan innehålla olika innehåll och många av dessa innehålls typer har metadata-egenskaper som kan vara användbara för index. Precis som du kan skapa sökfält för standard-BLOB-egenskaper som kan **`metadata_storage_name`** du skapa fält för metadataegenskaper som är relaterade till ett dokument format.

## <a name="supported-document-formats"></a>Dokumentformat som stöds

Kognitiv sökning stöder BLOB-indexering och SharePoint Online-dokument indexering för följande dokument format:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Egenskaper efter dokument format

I följande tabell sammanfattas bearbetningen för varje dokument format och en beskrivning av egenskaperna för metadata som extraheras av en BLOB-indexerare och SharePoint Online-indexeraren.

| Dokument format/innehålls typ | Extraherade metadata | Bearbetnings information |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Remsa HTML-kod och extrahera text |
| PDF (program/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument (exklusive bilder) |
| DOCX (Application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| DOC (program/MSWord) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| WORD XML (Application/VND. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Stripe XML-kod och extrahera text |
| WORD 2003 XML (Application/VND. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Stripe XML-kod och extrahera text |
| XLSX (Application/VND. openxmlformats-officedocument. SpreadsheetML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| XLS (Application/VND. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| XLSM (Application/VND. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| PPTX (Application/VND. openxmlformats-officedocument. presentationml. presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument |
| PPT (Application/VND. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument |
| PPTM (Application/VND. MS-PowerPoint. presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahera text, inklusive inbäddade dokument |
| MSG (Application/VND. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahera text, inklusive text som extraherats från bilagor. `metadata_message_to_email`, `metadata_message_cc_email` och `metadata_message_bcc_email` är sträng samlingar, är resten av fälten strängar.|
| ODT (Application/VND. Oasis. OpenDocument. text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahera text, inklusive inbäddade dokument |
| ODS (Application/VND. Oasis. OpenDocument. kalkyl blad) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahera text, inklusive inbäddade dokument |
| ODP (Application/VND. Oasis. OpenDocument. presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extrahera text, inklusive inbäddade dokument |
| ZIP (program/zip) |`metadata_content_type` |Extrahera text från alla dokument i arkivet |
| GZ (program/gzip) |`metadata_content_type` |Extrahera text från alla dokument i arkivet |
| EPUB (Application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrahera text från alla dokument i arkivet |
| XML (program/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Stripe XML-kod och extrahera text |
| JSON (Application/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrahera text<br/>Obs! Om du behöver extrahera flera dokument fält från en JSON-BLOB kan du läsa mer i [INDEXERA JSON-blobbar](search-howto-index-json-blobs.md) |
| EML (meddelande/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrahera text, inklusive bifogade filer |
| RTF (program/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrahera text|
| Oformaterad text (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrahera text|

## <a name="see-also"></a>Se även

* [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
* [Förstå blobbar med AI](search-blob-ai-integration.md)
* [Översikt över BLOB-indexering](search-blob-storage-integration.md)
* [SharePoint online-indexering](search-howto-index-sharepoint-online.md)