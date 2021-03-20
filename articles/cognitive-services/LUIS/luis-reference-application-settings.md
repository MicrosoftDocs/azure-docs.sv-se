---
title: Program inställningar – LUIS
description: Program inställningar för Azure Cognitive Services Language förståelseing-appar lagras i appen och portalen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 0578e3c3c952a475c6beb01ffcf354e19eda6e26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91319171"
---
# <a name="app-and-version-settings"></a>Inställningar för app och version

Dessa inställningar lagras i den [exporterade](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) appen och uppdateras med REST-API: erna eller Luis-portalen.

Om du ändrar inställningarna för program versionen återställs appens utbildnings status till inte tränad.

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


Text referens och exempel är:

* [Skiljetecken](#punctuation-normalization)
* [Dia kritiska tecken](#diacritics-normalization)

## <a name="diacritics-normalization"></a>Normalisering av dia kritiska tecken

Följande yttranden visar hur dia kritiska normalisering påverkar yttranden:

|Med dia kritiska tecken inställt på falskt|Med dia kritiska tecken inställt på Sant|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Språk stöd för dia kritiska tecken

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Portugisiska (Brasilien) `pt-br` dia kritiska tecken

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Nederländska `nl-nl` dia kritiska tecken

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Franska `fr-` dia kritiska tecken

Detta inkluderar både franska och kanadensiska underkulturer.

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Tyska `de-de` dia kritiska tecken

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Italienska `it-it` dia kritiska tecken

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Spanska `es-` dia kritiska tecken

Detta inkluderar både spanska och kanadensiska mexikanska.

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|

## <a name="punctuation-normalization"></a>Normalisering av interpunktion

Följande yttranden visar hur interpunktion påverkar yttranden:

|Med skiljetecken inställt på falskt|Med skiljetecken inställt på Sant|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Interpunktion borttagen

Följande skiljetecken tas bort med `NormalizePunctuation` är inställt på sant.

|Skiljetecken|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|

## <a name="next-steps"></a>Nästa steg

* Lär dig [begrepp](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) för dia kritiska tecken och interpunktion.
