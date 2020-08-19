---
title: Dokument format och namngivnings konventioner – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Detta är en vägledning om dokument format och namngivnings konventioner i en anpassad översättare. Det här konceptet hjälper till att hantera dokument namn bättre och undvika namn konflikter.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 46a19cd4396e598a74f9099727ac80d44c0a44f5
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507254"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Vägledning för dokument format och namngivnings konvention

Alla filer som används för anpassad översättning måste vara minst **fyra** tecken långa.

Den här tabellen innehåller alla fil format som stöds och som du kan använda för att bygga ditt översättnings system:

| Format            | Tillägg   | Beskrivning                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF, . XLIFF | Ett parallellt dokument format, export av översättnings minnes system. De språk som används definieras i filen.                                                                                                                                                              |
| TMX               | . TMX         | Ett parallellt dokument format, export av översättnings minnes system. De språk som används definieras i filen.                                                                                                                                                              |
| Komprimera               | . Komprimera         | ZIP är ett Arkiv fil format.                                                                                                                                                                                                        |
| Locstudio         | . LCL         | Ett Microsoft-format för parallella dokument                                                                                                                                                                                                                                      |
| Microsoft Word    | . DOCX        | Microsoft Word-dokument                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . PDF         | Adobe Acrobat Portable-dokument                                                                                                                                                                                                                                                |
| HTML              | . HTML,. TILLÄGGET  | HTML-dokument                                                                                                                                                                                                                                                                  |
| Textfil         | . FORMAT         | UTF-16-eller UTF-8-kodade textfiler. Fil namnet får inte innehålla japanska tecken.                                                                                                                                                                                        |
| Justerad textfil | . PLACERAS       | Tillägget `.ALIGN` är ett särskilt tillägg som du kan använda om du vet att meningarna i dokument paret är perfekt justerade. Om du anger en `.ALIGN` fil kommer den anpassade översättaren inte att justera meningarna åt dig. |
| Excel-fil        | . XLSX        | Excel-fil (2013 eller senare). Första raden/raden i kalkyl bladet ska vara språkkod.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Ord lista format

För ord listor stöder anpassad översättare alla fil format som stöds för inlärnings uppsättningar. Om du använder en Excel-ordlista ska den första raden/raden i kalkyl bladet vara språk koder.

## <a name="zip-file-formats"></a>Zip-filformat

Dokument kan grupperas i en enda zip-fil och överföras. Den anpassade översättare stöder zip-filformat (ZIP, GZ och TGZ).

Varje dokument i zip-filen med fil namns tillägget TXT, HTML, HTM, PDF, DOCX måste följa denna namngivnings konvention:

{dokument namn} \_ {språkkod} där {Document Name} är namnet på ditt dokument, {language Code} är ISO-LanguageID (två tecken), vilket indikerar att dokumentet innehåller meningar på det språket. Det måste finnas ett under streck (_) före språk koden.

Om du till exempel vill överföra två parallella dokument i ett zip för ett engelskt till spanska-system ska filerna heta "data_en" och "data_es".

Översättning av minnesobjektet (TMX, XLF, XLIFF, LCL, XLSX) krävs inte för att följa den specifika språk namngivnings konventionen.  

## <a name="next-steps"></a>Nästa steg

- Läs om [projektet](workspace-and-project.md#what-is-a-custom-translator-project) för att skapa och hantera dem.
