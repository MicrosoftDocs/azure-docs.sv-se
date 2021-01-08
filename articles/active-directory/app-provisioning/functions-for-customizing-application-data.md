---
title: Referens för att skriva uttryck för mappningar av attribut i Azure Active Directory
description: Lär dig hur du använder uttrycks mappningar för att transformera attributvärden till ett acceptabelt format vid automatisk etablering av SaaS app-objekt i Azure Active Directory. Innehåller en referens lista med funktioner.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 02/05/2020
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: 462f60bbae9fd7b61993a2ccccd40fa5ca4ccc28
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012975"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>Referens för att skriva uttryck för mappningar av attribut i Azure AD

När du konfigurerar etablering till ett SaaS-program är en av de typer av mappningar av attribut som du kan ange en uttrycks mappning. För dessa måste du skriva ett skript liknande uttryck som gör att du kan omvandla dina användares data till format som är mer acceptabla för SaaS-programmet.

## <a name="syntax-overview"></a>Syntax-översikt

Syntaxen för-uttryck för attributmappning är reminiscent av Visual Basic for Applications (VBA)-funktioner.

* Hela uttrycket måste definieras i termer av functions, som består av ett namn följt av argument inom parentes: *functionname ( `<<argument 1>>` , `<<argument N>>` )*
* Du kan kapsla funktioner i varandra. Till exempel:  *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* Du kan skicka tre olika typer av argument till funktioner:
  
  1. Attribut, som måste omges av hakparenteser. Exempel: [attributeName]
  2. Strängkonstant, som måste omges av dubbla citat tecken. Till exempel: "USA"
  3. Andra funktioner. Till exempel: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Om du behöver ett omvänt snedstreck (\) eller citat tecken (") i strängen för sträng konstanter måste det föregås av ett omvänt snedstreck (\). Exempel: "företags namn: \\ " contoso \\ "
* Syntaxen är Skift läges känslig, som måste beaktas när du skriver dem som strängar i en funktion vs Copy klistrar in dem direkt härifrån. 

## <a name="list-of-functions"></a>Lista över funktioner

[Lägg](#append) &nbsp; &nbsp; till &nbsp; &nbsp; [](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp; [](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp; [](#coalesce) &nbsp; &nbsp; Sammanslagning &nbsp; &nbsp; [](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; [](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; [](#count) &nbsp; &nbsp; Antal &nbsp; &nbsp; [](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; [](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp; [](#guid) &nbsp; &nbsp; GUID &nbsp; &nbsp; [](#iif) &nbsp; &nbsp; IIF &nbsp; &nbsp; [](#instr) &nbsp; &nbsp; Instr &nbsp; &nbsp; [](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp; [](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp; [](#ispresent) &nbsp; &nbsp; IsPresent &nbsp; &nbsp; [](#isstring) &nbsp; &nbsp; IsString &nbsp; &nbsp; [](#item) &nbsp; &nbsp; Objekt &nbsp; &nbsp; [](#join) &nbsp; &nbsp; Anslut &nbsp; &nbsp; [](#left) &nbsp; &nbsp; Vänster &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [inte](#not) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>Lägg till

**Funktion:** Lägg till (källa, suffix)

**Beskrivning:** Tar ett käll sträng värde och lägger till suffixet i slutet av det.

**Komponentparametrar**

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet från källobjektet. |
| **huvudnamnssuffix** |Obligatorisk |Sträng |Strängen som du vill lägga till i slutet av source-värdet. |

---
### <a name="bitand"></a>BitAnd
**Funktion:** BitAnd (värde1, värde2)

**Beskrivning:** Den här funktionen konverterar båda parametrarna till den binära representationen och anger en bit till:

- 0 – om en eller båda av motsvarande bitar i värde1 och värde2 är 0
- 1 – om båda motsvarande bitar är 1.

Med andra ord returneras 0 i samtliga fall, förutom när motsvarande bitar i båda parametrarna är 1.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **value1** |Obligatorisk |num |Numeriskt värde som ska AND'ed med värde2|
| **värde2** |Obligatorisk |num |Numeriskt värde som ska AND'ed med värde1|

**Exempel**
`BitAnd(&HF, &HF7)`

11110111 och 00000111 = 00000111 `BitAnd` returnerar 7, det binära värdet för 00000111.

---
### <a name="cbool"></a>CBool
**Funktioner** 
`CBool(Expression)`

**Beskrivning:**  
 `CBool` Returnerar ett booleskt värde baserat på det utvärderade uttrycket. Om uttrycket utvärderas till ett värde som inte är noll `CBool` returnerar *True*, annars returneras *falskt*.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **expression** |Obligatorisk | uttryck | Ett giltigt uttryck |

**Exempel:** 
`CBool([attribute1] = [attribute2])`                                                                    
Returnerar true om båda attributen har samma värde.

---
### <a name="coalesce"></a>Coalesce
**Funktion:** Sammanslagning (source1, SOURCE2,..., defaultValue)

**Beskrivning:** Returnerar det första käll värde som inte är NULL. Om alla argument är NULL och defaultValue finns returneras defaultValue. Om alla argument är NULL och defaultValue inte finns, returnerar sammanslagningen NULL.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **source1 ... Källa** | Obligatorisk | Sträng |Obligatoriskt, variabel antal gånger. Vanligt vis namnet på attributet från källobjektet. |
| **Standar** | Valfritt | Sträng | Standardvärdet som ska användas när alla käll värden är NULL. Kan vara en tom sträng ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Funktion:** ConvertToBase64 (källa)

**Beskrivning:** Funktionen ConvertToBase64 konverterar en sträng till en Unicode base64-sträng.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |Sträng som ska konverteras till bas 64|

**Exempel**
`ConvertToBase64("Hello world!")`

Returnerar "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funktion:** ConvertToUTF8Hex (källa)

**Beskrivning:** Funktionen ConvertToUTF8Hex konverterar en sträng till ett hexadecimalt värde för UTF8-kodning.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |Sträng som ska konverteras till UTF8 hex|

**Exempel**
`ConvertToUTF8Hex("Hello world!")`

Returnerar 48656C6C6F20776F726C6421

---
### <a name="count"></a>Antal
**Funktion:** Count (attribut)

**Beskrivning:** Funktionen COUNT returnerar antalet element i ett multi-värde-attribut

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **basattributet** |Obligatorisk |attribut |Ett flervärdesattribut som innehåller element som räknas|

---
### <a name="cstr"></a>CStr
**Funktion:** CStr (värde)

**Beskrivning:** Funktionen CStr konverterar ett värde till en sträng data typ.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **värde** |Obligatorisk | numerisk, referens eller boolesk | Kan vara ett numeriskt värde, ett referens-eller Boolean-attribut. |

**Exempel**
`CStr([dn])`

Returnerar "CN = Johan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Funktion:** DateFromNum (värde)

**Beskrivning:** Funktionen DateFromNum konverterar ett värde i ADs datum format till en DateTime-typ.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **värde** |Obligatorisk | Datum | AD-datum som ska konverteras till DateTime-typ |

**Exempel**
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

Returnerar en DateTime som representerar 1 januari 2012 vid 11:12:00.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funktion:** FormatDateTime (källa, inputFormat, outputFormat)

**Beskrivning:** Tar en datum sträng från ett format och konverterar det till ett annat format.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet från källobjektet. |
| **inputFormat** |Obligatorisk |Sträng |Förväntat format för Source-värdet. För format som stöds, se [/dotNet/Standard/Base-types/Custom-date-and-Time-format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
| **outputFormat** |Obligatorisk |Sträng |Formatet för datum för utdata. |

---
### <a name="guid"></a>GUID
**Funktion:** GUID ()

**Beskrivning:** Funktions-GUID genererar ett nytt slumpmässigt GUID

---
### <a name="iif"></a>IIF
**Funktion:** IIF (Condition, valueIfTrue, valueIfFalse)

**Beskrivning:** Funktionen IIF returnerar en uppsättning möjliga värden baserat på ett angivet villkor.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **moduletype** |Obligatorisk |Variabel eller uttryck |Ett värde eller uttryck som kan utvärderas till true eller false. |
| **valueIfTrue** |Obligatorisk |Variabel eller sträng | Om villkoret utvärderas till sant returneras det returnerade värdet. |
| **valueIfFalse** |Obligatorisk |Variabel eller sträng |Om villkoret utvärderas till false returneras det returnerade värdet.|

**Exempel**
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**Funktion:** InStr (värde1, värde2, start, compareType)

**Beskrivning:** Funktionen InStr söker efter den första förekomsten av en del sträng i en sträng

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **value1** |Obligatorisk |Sträng |Sträng som ska genomsökas |
| **värde2** |Obligatorisk |Sträng |Sträng som ska hittas |
| **har** |Valfritt |Integer |Start position för att hitta del strängen|
| **compareType** |Valfritt |Enum |Kan vara vbTextCompare eller vbBinaryCompare |

**Exempel**
`InStr("The quick brown fox","quick")`

Utvärderas till 5

`InStr("repEated","e",3,vbBinaryCompare)`

Utvärderas till 7

---
### <a name="isnull"></a>IsNull
**Funktion:** IsNull (uttryck)

**Beskrivning:** Om uttrycket utvärderas till null returnerar funktionen IsNull True. För ett-attribut uttrycks ett null-värde av frånvaron av attributet.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **expression** |Obligatorisk |uttryck |Uttryck som ska utvärderas |

**Exempel**
`IsNull([displayName])`

Returnerar true om attributet inte finns.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funktion:** IsNullOrEmpty (uttryck)

**Beskrivning:** Om uttrycket är null eller en tom sträng returnerar funktionen IsNullOrEmpty värdet true. För ett-attribut utvärderar detta till sant om attributet saknas eller finns, men är en tom sträng.
Inversen till den här funktionen heter IsPresent.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **expression** |Obligatorisk |uttryck |Uttryck som ska utvärderas |

**Exempel**
`IsNullOrEmpty([displayName])`

Returnerar true om attributet inte finns eller är en tom sträng.

---
### <a name="ispresent"></a>IsPresent
**Funktion:** IsPresent (uttryck)

**Beskrivning:** Om uttrycket utvärderas till en sträng som inte är null och inte är tomt, returnerar funktionen IsPresent True. Inversen till den här funktionen heter IsNullOrEmpty.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **expression** |Obligatorisk |uttryck |Uttryck som ska utvärderas |

**Exempel**
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**Funktion:** IsString (uttryck)

**Beskrivning:** Om uttrycket kan utvärderas till en sträng typ, utvärderar funktionen IsString till true.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **expression** |Obligatorisk |uttryck |Uttryck som ska utvärderas |

---
### <a name="item"></a>Objekt
**Funktion:** Objekt (attribut, index)

**Beskrivning:** Funktionen item returnerar ett objekt från en multi-valueion String/Attribute.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **basattributet** |Obligatorisk |Attribut |Multi-Value-attribut som ska genomsökas |
| **Tabbindex** |Obligatorisk |Integer | Index till ett objekt i en multi-Value-sträng|

**Exempel:** 
 `Item([proxyAddresses], 1)` Returnerar det andra objektet i multi-Value-attributet.

---
### <a name="join"></a>Slå ihop
**Funktion:** Gå till (avgränsare, source1, SOURCE2,...)

**Beskrivning:** JOIN () liknar append (), förutom att det kan kombinera flera **käll** sträng värden till en enda sträng, och varje värde skiljs åt av en **avgränsnings** sträng.

Om ett av käll värdena är ett flervärdesattribut, kopplas alla värden i det attributet samman, avgränsade med avgränsning svärdet.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **brytning** |Obligatorisk |Sträng |Sträng som används för att avgränsa käll värden när de sammanfogas till en sträng. Kan vara "" om ingen avgränsare krävs. |
| **source1 ... Källa** |Obligatoriskt, variabel antal gånger |Sträng |Sträng värden som ska sammanfogas tillsammans. |

---
### <a name="left"></a>Vänster
**Funktion:** Left (sträng, NumChars)

**Beskrivning:** Funktionen Left returnerar ett angivet antal tecken från vänster i en sträng. Om numChars = 0 returneras en tom sträng.
Om numChars < 0, returnerar du Indatasträngen.
Om strängen är null returneras en tom sträng.
Om strängen innehåller färre tecken än det tal som anges i numChars returneras en sträng som är identisk med sträng (dvs. med alla tecken i parameter 1).

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **Sträng** |Obligatorisk |Attribut | Strängen att returnera tecken från |
| **NumChars** |Obligatorisk |Integer | Ett tal som identifierar antalet tecken som ska returneras från början (vänster) av sträng|

**Exempel**
`Left("John Doe", 3)`

Returnerar "Joh".

---
### <a name="mid"></a>Mellan
**Funktion:** Mid (källa, start, längd)

**Beskrivning:** Returnerar en del sträng av käll värde. En under sträng är en sträng som bara innehåller några av tecknen från käll strängen.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet. |
| **har** |Obligatorisk |heltal |Index i **käll** strängen där under strängen ska starta. Det första alfabetet i strängen kommer att ha indexet 1, andra tecken kommer att ha index 2 och så vidare. |
| **length** |Obligatorisk |heltal |Del strängens längd. Om längden slutar utanför **käll** strängen returnerar funktionen del sträng från **Start** index till slutet av **käll** strängen. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funktion:** NormalizeDiacritics (källa)

**Beskrivning:** Kräver ett sträng argument. Returnerar strängen, men med dia kritiska tecken ersatta med motsvarande icke-dia kritiska tecken. Används vanligt vis för att konvertera förnamn och efter namn som innehåller dia kritiska tecken (accenttecken) till juridiska värden som kan användas i olika användar identifierare som användarens huvud namn, SAM-kontonamn och e-postadresser.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng | Vanligt vis attributet förnamn eller efter namn. |

---
### <a name="not"></a>Inte
**Funktion:** Inte (källa)

**Beskrivning:** Vänder det booleska värdet för **källan**. Om **källobjektet** är true returnerar false. Annars returnerar true.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Boolesk sträng |Förväntade **käll** värden är "true" eller "false". |

---
### <a name="numfromdate"></a>NumFromDate
**Funktion:** NumFromDate (värde)

**Beskrivning:** Funktionen NumFromDate konverterar ett DateTime-värde till Active Directory format som krävs för att ange attribut som [accountExpires](/windows/win32/adschema/a-accountexpires). Använd den här funktionen för att konvertera DateTime-värden som tagits emot från moln HR-appar som Workday och SuccessFactors till motsvarande AD-representation. 

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **värde** |Obligatorisk | Sträng | Datum/tid-sträng i formatet stöds. För format som stöds, se https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx . |

**Exempel:**
* Exempel på arbets dagar som förutsätter att du vill mappa attributet *ContractEndDate* från Workday som är i fältet format *2020-12-31-08:00* till *accountExpires* i AD, så här kan du använda den här funktionen och ändra tids förskjutnings förskjutningen så att den matchar dina nationella inställningar. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors-exempel förutsätter att du vill mappa attributet *EndDate* från SuccessFactors som finns i formatet *M/d/ÅÅÅÅ HH: mm: ss tt* till *accountExpires* i AD, så här kan du använda den här funktionen och ändra tids zons förskjutningen så att den matchar dina nationella inställningar.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Funktion:** RemoveDuplicates (attribut)

**Beskrivning:** Funktionen RemoveDuplicates använder en sträng med flera värden och ser till att varje värde är unikt.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **basattributet** |Obligatorisk |Multi-Value-attribut |Multi-Value-attribut som ska ha dubbletter borttagna|

**Exempel:** 
 `RemoveDuplicates([proxyAddresses])` Returnerar ett sanerat proxyAddress-attribut där alla dubblettvärden har tagits bort.

---
### <a name="replace"></a>Ersätt
**Funktion:** Ersätt (källa, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, mall)

**Beskrivning:** Ersätter värden i en sträng i ett skift läges känsligt sätt. Funktionen beter sig på olika sätt beroende på vilka parametrar som anges:

* När **OldValue** och **replacementValue** anges:
  
  * Ersätter alla förekomster av **OldValue** i **källan**  med **replacementValue**
* När **OldValue** och **mall** tillhandahålls:
  
  * Ersätter alla förekomster av **OldValue** i **mallen** med **käll** värde
* När **regexPattern** och **replacementValue** anges:

  * Funktionen tillämpar **regexPattern** på **käll** strängen och du kan använda regex-gruppens namn för att skapa strängen för **replacementValue**
* När **regexPattern**, **regexGroupName**, **replacementValue** anges:
  
  * Funktionen tillämpar **regexPattern** på **käll** strängen och ersätter alla värden som matchar **regexGroupName** med **replacementValue**
* När **regexPattern**, **regexGroupName**, **replacementAttributeName** anges:
  
  * Om **källan** inte har något värde returneras **källan**
  * Om **källan** har ett värde tillämpar funktionen **regexPattern** på **käll** strängen och ersätter alla värden som matchar **RegexGroupName** med värdet som är kopplat till **replacementAttributeName**

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet från **källobjektet** . |
| **Gammalt** |Valfritt |Sträng |Värdet som ska ersättas i **källa** eller **mall**. |
| **regexPattern** |Valfritt |Sträng |Regex-mönster för värdet som ska ersättas i **källan**. Eller, när **replacementPropertyName** används, mönster för att extrahera värdet från **replacementPropertyName**. |
| **regexGroupName** |Valfritt |Sträng |Namnet på gruppen inuti **regexPattern**. Endast när  **replacementPropertyName** används kommer vi att extrahera värdet för den här gruppen som **replacementValue** från **replacementPropertyName**. |
| **replacementValue** |Valfritt |Sträng |Nytt värde som ersätter det gamla ett med. |
| **replacementAttributeName** |Valfritt |Sträng |Namnet på attributet som ska användas för ersättnings värde |
| **webbplatsmall** |Valfritt |Sträng |När ett **mallnamn** anges söker vi efter **OldValue** i mallen och ersätter det med **käll** värde. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funktion:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Beskrivning:** Kräver minst två argument, vilket är unika regler för generering av unika värden som definieras med hjälp av uttryck. Funktionen utvärderar varje regel och kontrollerar sedan värdet som genereras för unikhet i mål appen/katalogen. Det första unika värdet som du hittar är det som returnerades. Om alla värden redan finns i målet kommer posten att få deponerats och orsaken loggas i gransknings loggarna. Det finns ingen övre bindning till det antal argument som kan anges.


 - Detta är en funktion på den översta nivån, den kan inte kapslas.
 - Den här funktionen kan inte tillämpas på attribut som har en matchande prioritet.   
 - Den här funktionen är endast avsedd att användas för att skapa poster. När du använder det med ett-attribut ställer du in egenskapen **tillämpa mappning** på **endast när objekt skapas**.
 - Den här funktionen stöds för närvarande endast för "arbets dag för Active Directory användar etablering". Det kan inte användas med andra etablerings program. 


**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Minst 2 krävs, ingen övre bindning |Sträng | Lista med regler för generering av unika värden som ska utvärderas. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktion:** SingleAppRoleAssignment ([appRoleAssignments])

**Beskrivning:** Returnerar en enskild appRoleAssignment från listan över alla appRoleAssignments som har tilldelats till en användare för ett visst program. Den här funktionen krävs för att konvertera appRoleAssignments-objektet till en enda roll namn sträng. Observera att det bästa sättet är att se till att endast en appRoleAssignment är tilldelad en användare åt gången, och om flera roller tilldelas kan den returnerade roll strängen inte vara förutsägbar. 

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Obligatorisk |Sträng |**[appRoleAssignments]** -objekt. |

---
### <a name="split"></a>Dela
**Funktion:** Dela (källa, avgränsare)

**Beskrivning:** Delar upp en sträng i en Multivärdes mat ris med hjälp av det angivna avgränsnings tecken.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |**käll** värde att uppdatera. |
| **avgränsare** |Obligatorisk |Sträng |Anger det tecken som ska användas för att dela strängen (exempel: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Funktion:** StripSpaces (källa)

**Beskrivning:** Tar bort alla blank steg ("") från käll strängen.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |**käll** värde att uppdatera. |

---
### <a name="switch"></a>Switch
**Funktion:** Switch (källa, defaultValue, KEY1, värde1, key2, värde2,...)

**Beskrivning:** Returnerar **värdet** för den **nyckeln** när **källobjektet** matchar en **nyckel**. Om **käll** värde inte matchar några nycklar returnerar **DefaultValue**.  **Nyckel** -och **värde** parametrar måste alltid komma in i par. Funktionen förväntar sig alltid ett jämnt antal parametrar. Funktionen ska inte användas för referensbaserade attribut som chef. 

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |**Käll** värde att uppdatera. |
| **Standar** |Valfritt |Sträng |Standardvärdet som ska användas när källan inte matchar några nycklar. Kan vara en tom sträng (""). |
| **knapp** |Obligatorisk |Sträng |**Nyckel** att jämföra **käll** värde med. |
| **värde** |Obligatorisk |Sträng |Ersättnings värde för den **källa** som matchar nyckeln. |

---
### <a name="tolower"></a>ToLower
**Funktion:** ToLower (källa, kultur)

**Beskrivning:** Tar ett *käll* sträng värde och konverterar det till gemener med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet från källobjektet |
| **substrat** |Valfritt |Sträng |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Funktion:** ToUpper (källa, kultur)

**Beskrivning:** Tar ett *käll* sträng värde och konverterar det till versaler med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet från källobjektet. |
| **substrat** |Valfritt |Sträng |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

---
### <a name="word"></a>Word
**Funktion:** Ord (sträng, WordNumber, avgränsare)

**Beskrivning:** Funktionen ord returnerar ett ord som finns i en sträng, baserat på parametrar som beskriver avgränsarna som ska användas och ord numret som ska returneras. Varje sträng med tecken i strängen avgränsade med ett av tecknen i avgränsare identifieras som ord:

Om talet < 1 returneras en tom sträng.
Om strängen är null returnerar en tom sträng.
Om strängen innehåller färre än tal ord, eller om strängen inte innehåller ord som identifieras av avgränsare returneras en tom sträng.

**Komponentparametrar** 

| Namn | Krävs/upprepas | Typ | Kommentarer |
| --- | --- | --- | --- |
| **Sträng** |Obligatorisk |Multi-Value-attribut |Sträng för att returnera ett ord från.|
| **WordNumber** |Obligatorisk | Integer | Nummer som identifierar vilket ord nummer som ska returneras|
| **avgränsare** |Obligatorisk |Sträng| En sträng som representerar de avgränsare som ska användas för att identifiera ord|

**Exempel**
`Word("The quick brown fox",3," ")`

Returnerar "brun".

`Word("This,string!has&many separators",3,",!&#")`

Returnerar "har".

---

## <a name="examples"></a>Exempel
### <a name="strip-known-domain-name"></a>Strip-känt domän namn
Du måste randig ett känt domän namn från en användares e-postadress för att få ett användar namn. Om domänen till exempel är "contoso.com" kan du använda följande uttryck:

**Uttryck** 
`Replace([mail], "@contoso.com", , ,"", ,)`

**Exempel på indata/utdata:** 

* **Inmatad** (e-post): " john.doe@contoso.com "
* **Utdata**: "John. berg"

### <a name="append-constant-suffix-to-user-name"></a>Lägg till konstant suffix i användar namn
Om du använder en Salesforce-Sandbox kan du behöva lägga till ytterligare ett suffix till alla användar namn innan du synkroniserar dem.

**Uttryck** 
`Append([userPrincipalName], ".test")`

**Exempel på indata/utdata:** 

* **Inmatade**: (userPrincipalName): " John.Doe@contoso.com "
* **Utdata**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generera användaralias genom att sammanfoga delar av för-och efter namn
Du måste generera ett användaralias genom att göra de första 3 bokstäverna för användarens förnamn och de första fem bokstäverna i användarens efter namn.

**Uttryck** 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Exempel på indata/utdata:** 

* **Inmatade** (givenName): "John"
* **Inmatad** (efter namn): "berg"
* **Utdata**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Ta bort dia kritiska tecken från en sträng
Du måste ersätta tecken som innehåller accenttecken med motsvarande tecken som inte innehåller accenttecken.

**Uttryck:** NormalizeDiacritics ([givenName])

**Exempel på indata/utdata:** 

* **Inmatade** (givenName): "Zoë"
* **Utdata**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dela upp en sträng i en Multivärdes mat ris
Du måste ta en kommaavgränsad lista med strängar och dela upp dem i en matris som kan kopplas till ett flervärdesattribut som Salesforce: s PermissionSets-attribut. I det här exemplet har en lista över behörighets uppsättningar fyllts i extensionAttribute5 i Azure AD.

**Uttryck:** Dela ([extensionAttribute5], ",")

**Exempel på indata/utdata:** 

* **Inmatade** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Utmatnings datum som en sträng i ett visst format
Du vill skicka datum till ett SaaS-program i ett visst format. Till exempel vill du formatera datum för ServiceNow.

**Uttryck** 

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Exempel på indata/utdata:**

* **Inmatade** (extensionAttribute1): "20150123105347.1 z"
* **Utdata**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersätt ett värde baserat på fördefinierade alternativ uppsättningar

Du måste definiera tids zonen för användaren baserat på den delstats kod som lagras i Azure AD. Om tillstånds koden inte matchar något av de fördefinierade alternativen använder du standardvärdet "Australien/Sydney".

**Uttryck** 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Exempel på indata/utdata:**

* **Inmatade** (State): "QLD"
* **Utdata**: "Australien/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Ersätt tecken med ett reguljärt uttryck
Du måste hitta tecken som matchar ett reguljärt uttrycks värde och ta bort dem.

**Uttryck** 

Ersätt ([smek namn],, "[a-zA-Z_] *",, "",,)

**Exempel på indata/utdata:**

* **Inmatade** (smek namn: "john_doe72"
* **Utdata**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konvertera genererat userPrincipalName-värde (UPN) till gemener
I exemplet nedan genereras UPN-värdet genom att sammanfoga PreferredFirstName-och PreferredLastName-käll fälten och ToLower-funktionen fungerar på den genererade strängen för att konvertera alla tecken till gemener. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Exempel på indata/utdata:**

* **Inmatade** (PreferredFirstName): "John"
* **Inmatade** (PreferredLastName): "Svensson"
* **Utdata**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generera ett unikt värde för userPrincipalName-attributet (UPN)
Baserat på användarens förnamn, mellan namn och efter namn, måste du generera ett värde för attributet UPN och kontrol lera att det är unikt i mål-AD-katalogen innan du tilldelar värdet till UPN-attributet.

**Uttryck** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Exempel på indata/utdata:**

* **Inmatade** (PreferredFirstName): "John"
* **Inmatade** (PreferredLastName): "Svensson"
* **Utdata**: " John.Smith@contoso.com " om UPN-värdet John.Smith@contoso.com inte redan finns i katalogen
* **Utdata**: " J.Smith@contoso.com " om UPN-värdet John.Smith@contoso.com redan finns i katalogen
* **Utdata**: " Jo.Smith@contoso.com " om ovanstående två UPN-värden redan finns i katalogen

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Flödes post värde om det inte är NULL, annars Flow userPrincipalName
Du vill flöda e-postattributet om det finns. Om så inte är fallet, vill du flöda värdet för userPrincipalName i stället.

**Uttryck** 
`Coalesce([mail],[userPrincipalName])`

**Exempel på indata/utdata:** 

* **Inmatad** (e-post): null
* **Inmatade** (userPrincipalName): " John.Doe@contoso.com "
* **Utdata**: " John.Doe@contoso.com "

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera användar etablering/avetablering för SaaS-appar](../app-provisioning/user-provisioning.md)
* [Anpassa mappningar av attribut för användar etablering](../app-provisioning/customize-application-attributes.md)
* [Omfångs filter för användar etablering](define-conditional-rules-for-provisioning-user-accounts.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Kontoetableringsmeddelanden](../app-provisioning/user-provisioning.md)
* [Lista över guider om hur man integrerar SaaS-appar](../saas-apps/tutorial-list.md)
