---
title: Validerings principer för Azure API Management | Microsoft Docs
description: Lär dig mer om principer som du kan använda i Azure API Management för att validera begär Anden och svar.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801901"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>API Management principer för att validera begär Anden och svar

Den här artikeln innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](./api-management-policies.md).

Använd validerings principer för att validera API-begäranden och svar mot ett OpenAPI-schema och skydda mot sårbarheter som inmatning av sidhuvuden eller nytto lasten. Även om det inte är en ersättning för en brand vägg för webbaserade program, ger validerings principer flexibilitet att svara på ytterligare en klass av hot som inte omfattas av säkerhets produkter som är beroende av statiska, fördefinierade regler.

## <a name="validation-policies"></a>Validerings principer

- [Validera innehåll](#validate-content) – verifierar storleken eller JSON-schemat för en begäran eller en svars text mot API-schemat. 
- [Validate Parameters](#validate-parameters) – verifierar parametrarna för begärans huvud, fråga eller sökväg mot API-schemat.
- [Verifiera huvuden](#validate-headers) – verifierar svars huvudena mot API-schemat.
- [Validera status kod](#validate-status-code) – validerar HTTP-statuskoden i svar mot API-schemat.

> [!NOTE]
> Den maximala storleken för API-schemat som kan användas av en validerings princip är 4 MB. Om schemat överskrider den här gränsen kommer validerings principerna att returnera fel vid körning. Kontakta [supporten](https://azure.microsoft.com/support/options/)om du vill öka det. 

## <a name="actions"></a>Åtgärder

Varje validerings princip innehåller ett attribut som anger en åtgärd som API Management vidta när en entitet verifieras i en API-begäran eller ett svar mot API-schemat. En åtgärd kan anges för element som representeras i API-schemat och, beroende på principen, för element som inte representeras i API-schemat. En åtgärd som anges i en princips underordnade element åsidosätter en åtgärd som angetts för dess överordnade.

Tillgängliga åtgärder:

| Åtgärd         | Beskrivning          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Ignorera | Hoppa över verifiering. |
| leda | Blockera bearbetningen av begäran eller svar, logga utförlig verifierings fel och returnera ett fel. Bearbetning avbryts när den första uppsättningen fel upptäcks. |
| detect | Logga verifierings fel, utan att avbryta bearbetning av förfrågningar eller svar. |

## <a name="logs"></a>Loggar

Information om validerings felen vid princip körningen loggas till variabeln som `context.Variables` anges i `errors-variable-name` attributet i principens rot element. När ett verifierings fel i en åtgärd har kon figurer ATS `prevent` blockerar det ytterligare begäran eller svars bearbetning och sprids också till `context.LastError` egenskapen. 

Om du vill undersöka fel använder du en [spårnings](api-management-advanced-policies.md#Trace) princip för att logga felen från kontextnoden till [Application Insights](api-management-howto-app-insights.md).

## <a name="performance-implications"></a>Prestandaöverväganden

Att lägga till validerings principer kan påverka API-dataflödet. Följande allmänna principer gäller:
* Ju större API-schemats storlek, desto lägre är data flödet. 
* Ju större nytto lasten i en begäran eller ett svar, desto lägre är data flödet. 
* Storleken på API-schemat har en större inverkan på prestandan än nytto lastens storlek. 
* Verifiering mot ett API-schema som är flera megabyte i storlek kan orsaka timeout eller svars tids gräns under vissa förhållanden. Påverkan är mer uttalad i tjänstens  **användnings** -och **utvecklings** nivåer. 

Vi rekommenderar att du utför belastnings test med dina förväntade produktions arbets belastningar för att bedöma effekten av validerings principerna på API-dataflödet.

## <a name="validate-content"></a>Verifiera innehåll

`validate-content`Principen kontrollerar storleken eller JSON-schemat för en begäran eller ett svar mot API-schemat. Andra format än JSON stöds inte.

### <a name="policy-statement"></a>Princip kommentar

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Exempel

I följande exempel verifieras JSON-nyttolasten i begär Anden och svar i identifierings läge. Meddelanden med nytto laster som är större än 100 KB blockeras. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Element

| Namn         | Beskrivning                                                                                                                                   | Krävs |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validera – innehåll | Rot element.                                                                                                                               | Ja      |
| innehåll | Lägg till ett eller flera av de här elementen för att verifiera innehålls typen i begäran eller svar och utföra den angivna åtgärden.  | Inga |

### <a name="attributes"></a>Attribut

| Name                       | Beskrivning                                                                                                                                                            | Krävs | Standardvärde |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| ospecificerad-innehålls typ-åtgärd | [Åtgärd](#actions) som ska utföras för begär Anden eller svar med en innehålls typ som inte har angetts i API-schemat. |  Ja     | Ej tillämpligt   |
| Max storlek | Den maximala längden på innehållet i begäran eller svaret i byte, kontrol leras mot `Content-Length` rubriken. Om begär ande texten eller svars texten är komprimerad är det här värdet den dekomprimerade längden. Högsta tillåtna värde: 102 400 byte (100 KB).  | Ja       | Ej tillämpligt   |
| storlek-överskriden-åtgärd | [Åtgärd](#actions) som ska utföras för förfrågningar eller svar vars brödtext överskrider den storlek som anges i `max-size` . |  Ja     | Ej tillämpligt   |
| fel-variabel-namn | Namnet på variabeln i `context.Variables` som loggar validerings fel till.  |   Ja    | Ej tillämpligt   |
| typ | Innehålls typ för att köra verifiering av brödtext för, kontrollerad mot `Content-Type` sidhuvudet. Det här värdet är Skift läges okänsligt. Om den är tom används den för alla innehålls typer som anges i API-schemat. |   Inga    |  Ej tillämpligt  |
| verifiera som | Validerings motor som ska användas för verifiering av bröd texten i en begäran eller ett svar med en matchande innehålls typ. För närvarande är det enda värde som stöds "JSON".   |  Ja     |  Ej tillämpligt  |
| åtgärd | [Åtgärd](#actions) som ska utföras för förfrågningar eller svar vars brödtext inte matchar den angivna innehålls typen.  |  Ja      | Ej tillämpligt   |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande, utgående, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="validate-parameters"></a>Verifiera parametrar

`validate-parameters`Principen verifierar parametrarna för sidhuvud, fråga eller sökväg i begär Anden mot API-schemat.

> [!IMPORTANT]
> Om du har importerat ett API med hjälp av en hanterings-API-version tidigare än `2021-01-01-preview` , `validate-parameters` kanske principen inte fungerar. Du kan behöva [Importera ditt API](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) igen med hjälp av hanterings-API-versionen `2021-01-01-preview` eller senare.


### <a name="policy-statement"></a>Princip kommentar

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Exempel

I det här exemplet verifieras alla frågor och Sök vägs parametrar i skydds läget och rubrikerna i identifierings läget. Verifieringen har åsidosatts för flera huvud parametrar:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Element

| Namn         | Beskrivning                                                                                                                                   | Krävs |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validera – parametrar | Rot element. Anger standard validerings åtgärder för alla parametrar i begär Anden.                                                                                                                              | Ja      |
| sidhuvud | Lägg till det här elementet för att åsidosätta standard validerings åtgärder för huvud parametrar i begär Anden.   | Inga |
| DocumentDB | Lägg till det här elementet för att åsidosätta standard validerings åtgärder för frågeparametrar i begär Anden.  | Inga |
| path | Lägg till det här elementet för att åsidosätta standard validerings åtgärder för URL: er för URL-sökvägar  | Inga |
| parameter | Lägg till ett eller flera element för namngivna parametrar för att åsidosätta konfigurationen på högre nivå av verifierings åtgärderna. | Inga |

### <a name="attributes"></a>Attribut

| Name                       | Beskrivning                                                                                                                                                            | Krävs | Standardvärde |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| angiven parameter-åtgärd | [Åtgärd](#actions) att utföra för begär ande parametrar som anges i API-schemat. <br/><br/> När det finns i ett `headers` -, `query` -eller- `path` element, åsidosätter värdet för `specified-parameter-action` i- `validate-parameters` elementet.  |  Ja     | Ej tillämpligt   |
| ospecificerad-parameter-åtgärd | [Åtgärd](#actions) som ska utföras för parametrar för begäran som inte anges i API-schemat. <br/><br/>När värdet i ett `headers` eller `query` -element anges åsidosätts värdet för `unspecified-parameter-action` i- `validate-parameters` elementet. |  Ja     | Ej tillämpligt   |
| fel-variabel-namn | Namnet på variabeln i `context.Variables` som loggar validerings fel till.  |   Ja    | Ej tillämpligt   |
| name | Namnet på den parameter som validerings åtgärden ska åsidosättas för. Det här värdet är Skift läges okänsligt.  | Ja | Ej tillämpligt |
| åtgärd | [Åtgärd](#actions) som ska utföras för parametern med det matchande namnet. Om parametern anges i API-schemat åsidosätter det här värdet konfigurationen på högre nivå `specified-parameter-action` . Om parametern inte anges i API-schemat åsidosätter det här värdet konfigurationen på högre nivå `unspecified-parameter-action` .| Ja | Ej tillämpligt | 

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande

-   **Princip omfattningar:** alla omfattningar

## <a name="validate-headers"></a>Verifiera rubriker

`validate-headers`Principen verifierar svars huvudena mot API-schemat.

> [!IMPORTANT]
> Om du har importerat ett API med hjälp av en hanterings-API-version tidigare än `2021-01-01-preview` , `validate-headers` kanske principen inte fungerar. Du kan behöva importera ditt API igen med hjälp av hanterings-API-versionen `2021-01-01-preview` eller senare.

### <a name="policy-statement"></a>Princip kommentar

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Exempel

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Element

| Namn         | Beskrivning                                                                                                                                   | Krävs |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validera – rubriker | Rot element. Anger standard verifierings åtgärder för alla huvuden i svar.                                                                                                                              | Ja      |
| sidhuvud | Lägg till ett eller flera element för namngivna huvuden för att åsidosätta standard verifierings åtgärderna för huvuden i svar. | Inga |

### <a name="attributes"></a>Attribut

| Name                       | Beskrivning                                                                                                                                                            | Krävs | Standardvärde |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| angiven – huvud åtgärd | [Åtgärd](#actions) som ska utföras för svars rubriker som anges i API-schemat.  |  Ja     | Ej tillämpligt   |
| ej angivet – rubrik-åtgärd | [Åtgärd](#actions) som ska utföras för svars rubriker som inte har angetts i API-schemat.  |  Ja     | Ej tillämpligt   |
| fel-variabel-namn | Namnet på variabeln i `context.Variables` som loggar validerings fel till.  |   Ja    | Ej tillämpligt   |
| name | Namnet på rubriken som ska åsidosätta verifierings åtgärden för. Det här värdet är Skift läges okänsligt. | Ja | Ej tillämpligt |
| åtgärd | [Åtgärd](#actions) som ska utföras för sidhuvudet med det matchande namnet. Om rubriken anges i API-schemat åsidosätter det här värdet värdet för `specified-header-action` i- `validate-headers` elementet. Annars åsidosätts värdet för `unspecified-header-action` i elementet validate-headers. | Ja | Ej tillämpligt | 

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** utgående, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="validate-status-code"></a>Validera status kod

`validate-status-code`Principen validerar HTTP-statuskod i svar mot API-schemat. Den här principen kan användas för att förhindra läckage av Server dels fel som kan innehålla stack spårningar. 

### <a name="policy-statement"></a>Princip kommentar

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Exempel

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Element

| Namn         | Beskrivning                                                                                                                                   | Krävs |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validera-status-kod | Rot element.                                                                                                | Ja      |
| status kod | Lägg till ett eller flera element för HTTP-statuskod om du vill åsidosätta standard verifierings åtgärden för status koder i svar. | Inga |

### <a name="attributes"></a>Attribut

| Name                       | Beskrivning                                                                                                                                                            | Krävs | Standardvärde |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| ej angivet-status-kod-åtgärd | [Åtgärd](#actions) som ska utföras för HTTP-statuskod i svar som inte anges i API-schemat.  |  Ja     | Ej tillämpligt   |
| fel-variabel-namn | Namnet på variabeln i `context.Variables` som loggar validerings fel till.  |   Ja    | Ej tillämpligt   |
| kod | HTTP-statuskod som åsidosätter verifierings åtgärden för. | Ja | Ej tillämpligt |
| åtgärd | [Åtgärd](#actions) som ska utföras för matchande status kod, som inte anges i API-schemat. Om status koden anges i API-schemat träder inte denna åsidosättning i bruk. | Ja | Ej tillämpligt | 

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** utgående, på-fel

-   **Princip omfattningar:** alla omfattningar


## <a name="validation-errors"></a>Verifierings fel
I följande tabell visas alla möjliga fel i validerings principerna. 

* **Information** – kan användas för att undersöka fel. Inte avsedd att delas offentligt.
* **Offentligt svar** – fel som returnerades till klienten. Läcker inte implementerings information.

| **Namn**                             | **Typ**                                                        | **Validerings regel** | **Information**                                                                                                                                       | **Offentligt svar**                                                                                                                       | **Åtgärd**           |
|----|----|---|---|---|----|
| **Validera – innehåll** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | Frågans brödtext är {size} byte lång och den överskrider den konfigurerade gränsen på {maxSize} byte.                                                       | Frågans brödtext är {size} byte lång och överskrider gränsen på {maxSize} byte.                                                          | identifiera/förhindra |
||ResponseBody                                                    | SizeLimit           | Svarets brödtext är {size} byte lång och det överskrider den konfigurerade gränsen på {maxSize} byte.                                                      | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| {messageContentType}                 | RequestBody                                                     | Ospecificerat         | Den ospecificerade innehålls typen {messageContentType} är inte tillåten.                                                                                     | Den ospecificerade innehålls typen {messageContentType} är inte tillåten.                                                                             | identifiera/förhindra |
| {messageContentType}                 | ResponseBody                                                    | Ospecificerat         | Den ospecificerade innehålls typen {messageContentType} är inte tillåten.                                                                                     | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| | ApiSchema                                                       |                     | API: s schema finns inte eller kunde inte matchas.                                                                                          | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
|                                      | ApiSchema                                                       |                     | API: s schema anger inte definitioner.                                                                                                        | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| {messageContentType}                 | RequestBody / ResponseBody                                      | MissingDefinition   | API: s schema innehåller inte definitionen {definitionName}, som är associerad med innehålls typen {messageContentType}.                        | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | Bröd texten i begäran stämmer inte överens med definitionen {definitionName}, som är associerad med innehålls typen {messageContentType}.<br/><br/>{valError. Message} rad: {valError. LineNumber}, position: {valError. LinePosition}                  | Bröd texten i begäran stämmer inte överens med definitionen {definitionName}, som är associerad med innehålls typen {messageContentType}.<br/><br/>{valError. Message} rad: {valError. LineNumber}, position: {valError. LinePosition}            | identifiera/förhindra |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | Svars texten i svaret stämmer inte överens med definitionen {definitionName}, som är associerad med innehålls typen {messageContentType}.<br/><br/>{valError. Message} rad: {valError. LineNumber}, position: {valError. LinePosition}                                       | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
|                                      | RequestBody                                                     | ValidationException | Det går inte att verifiera bröd texten i begäran för innehålls typen {messageContentType}.<br/><br/>{information om undantag}                                                                | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
|                                      | ResponseBody                                                    | ValidationException | Det går inte att verifiera svars texten för innehålls typen {messageContentType}.<br/><br/>{information om undantag}                                                                | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| **Validera-parameter/validate-headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName}/{huvud}           | QueryParameter / PathParameter / RequestHeader                  | Ospecificerat         | Ospecificerad {Path-parameter/frågeparameter/header} {paramName} tillåts inte.                                                               | Ospecificerad {Path-parameter/frågeparameter/header} {paramName} tillåts inte.                                                       | identifiera/förhindra |
| Huvud                         | ResponseHeader                                                  | Ospecificerat         | Det går inte att ange huvudet {huvud}.                                                                                                   | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
|                                      |ApiSchema                                                       |                     | API: s schema finns inte eller kunde inte matchas.                                                                                            | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
|                                       | ApiSchema                                                       |                     | API-schemat anger inte definitioner.                                                                                                          | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| PARAMNAME                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | API: s schema innehåller inte definitionen {definitionName}, som är associerad med {Query-parametern/Path-parametern/-rubriken} {paramName}.  | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| PARAMNAME                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Begäran får inte innehålla flera värden för {Query-parametern/Path-parametern/header} {paramName}.                                           | Begäran får inte innehålla flera värden för {Query-parametern/Path-parametern/header} {paramName}.                                   | identifiera/förhindra |
| Huvud                         | ResponseHeader                                                  | IncorrectMessage    | Svaret får inte innehålla flera värden för rubriken {huvud}.                                                                              | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| PARAMNAME                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Värdet för {Frågeparametern/Path-parametern/header} {paramName} stämmer inte överens med definitionen.<br/><br/>{valError. Message} rad: {valError. LineNumber}, position: {valError. LinePosition}                                          | Värdet för {Frågeparametern/Sök vägs parametern/huvudet} {paramName} överensstämmer inte med definitionen.<br/><br/>{valError. Message} rad: {valError. LineNumber}, position: {valError. LinePosition}                              | identifiera/förhindra |
| Huvud                         | ResponseHeader                                                  | IncorrectMessage    | Värdet för rubriken {huvud} överensstämmer inte med definitionen.<br/><br/>{valError. Message} rad: {valError. LineNumber}, position: {valError. LinePosition}                                                                              | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| PARAMNAME                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Värdet för {Frågeparametern/Path-parametern/Head} {paramName} kan inte parsas enligt definitionen. <br/><br/>priset. Meddelande                                | Värdet för {Frågeparametern/Path-parametern/Head} {paramName} kunde inte parsas enligt definitionen. <br/><br/>priset. Meddelande                      | identifiera/förhindra |
| Huvud                         | ResponseHeader                                                  | IncorrectMessage    | Det gick inte att parsa värdet för rubriken {huvud} enligt definitionen.                                                                  | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| PARAMNAME                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | {Frågeparameter/Path-parameter/header} Det går inte att verifiera {paramName}.<br/><br/>{information om undantag}                                                                      | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| Huvud                         | ResponseHeader                                                  | ValidationError     | Det går inte att verifiera huvudet {huvud}.<br/><br/>{information om undantag}                                                                                                          | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |
| **Validera-status-kod**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-kod}                        | StatusCode                                                      | Ospecificerat         | Svars status koden {status-Code} är inte tillåten.                                                                                                | Begäran kunde inte bearbetas på grund av ett internt fel. Kontakta API-ägaren.                                                       | identifiera/förhindra |


I följande tabell visas alla möjliga orsaks värden för ett verifierings fel tillsammans med möjliga meddelande värden:

|  **Anledning**         |    **Meddelande** |
|---|---|  
| Felaktig begäran       |     {Information} för kontext variabel, {Public Response} för klienten|
| Svaret tillåts inte  | {Information} för kontext variabel, {Public Response} för klienten |






## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

-   [Principer i API Management](api-management-howto-policies.md)
-   [Transformera API: er](transform-api.md)
-   [Princip referens](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar
-   [Principexempel](./policy-reference.md)
-   [Felhantering](./api-management-error-handling-policies.md)
