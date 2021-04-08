---
title: Azure AD Connect Cloud Sync-uttryck och funktions referens
description: förhållande
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869d60d4ccb3bae58924fe4221f83b1b0125ae04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614024"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Skriver uttryck för mappningar av attribut i Azure Active Directory
När du konfigurerar en molnbaserad synkronisering är en av de typer av mappningar av attribut som du kan ange en uttrycks mappning. 

Med uttrycks mappningen kan du anpassa attribut med ett skript liknande uttryck.  På så sätt kan du omvandla lokala data till ett nytt eller annat värde.  Du kanske till exempel vill kombinera två attribut till ett enda attribut eftersom detta enda attribut används av ett av dina moln program.

Följande dokument tar upp de skript-liknande uttryck som används för att transformera data.  Detta är endast en del av processen.  Härnäst måste du använda det här uttrycket och placera det i en webbegäran till din klient organisation.  Mer information om detta finns i [transformeringar](how-to-transformation.md)

## <a name="syntax-overview"></a>Syntax-översikt
Syntaxen för-uttryck för attributmappning är reminiscent av Visual Basic for Applications (VBA)-funktioner.

* Hela uttrycket måste definieras i termer av functions, som består av ett namn följt av argument inom parentes: <br>
  *FunctionName ( `<<argument 1>>` , `<<argument N>>` )*
* Du kan kapsla funktioner i varandra. Exempel: <br> *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* Du kan skicka tre olika typer av argument till funktioner:
  
  1. Attribut, som måste omges av hakparenteser. Exempel: [attributeName]
  2. Strängkonstant, som måste omges av dubbla citat tecken. Till exempel: "USA"
  3. Andra funktioner. Till exempel: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Om du behöver ett omvänt snedstreck (\) eller citat tecken (") i strängen för sträng konstanter måste det föregås av ett omvänt snedstreck (\). Exempel: "företags namn: \\ " contoso \\ "

## <a name="list-of-functions"></a>Lista över funktioner
| Lista över funktioner | Beskrivning |
|-----|----|
|[Append](#append) (Lägg till)|Tar ett käll sträng värde och lägger till suffixet i slutet av det.|
|[BitAnd](#bitand)|Funktionen BitAnd anger angivna bitar i ett värde.|
|[CBool](#cbool)|Funktionen CBool returnerar ett booleskt värde baserat på det utvärderade uttrycket|
|[ConvertFromBase64](#convertfrombase64)|Funktionen ConvertFromBase64 konverterar det angivna base64-kodade värdet till en vanlig sträng.|
|[ConvertToBase64](#converttobase64)|Funktionen ConvertToBase64 konverterar en sträng till en Unicode base64-sträng. |
|[ConvertToUTF8Hex](#converttoutf8hex)|Funktionen ConvertToUTF8Hex konverterar en sträng till ett hexadecimalt värde för UTF8-kodning.|
|[Reparationer](#count)|Funktionen COUNT returnerar antalet element i ett multi-värde-attribut|
|[Cstr](#cstr)|Funktionen CStr konverterar till en sträng data typ.|
|[DateFromNum](#datefromnum)|Funktionen DateFromNum konverterar ett värde i ADs datum format till en DateTime-typ.|
|[DNComponent](#dncomponent)|Funktionen DNComponent returnerar värdet för en angiven DN-komponent som går från vänster.|
|[Fel](#error)|Fel funktionen används för att returnera ett anpassat fel.|
|[FormatDateTime](#formatdatetime) |Tar en datum sträng från ett format och konverterar det till ett annat format.| 
|[LED](#guid)|Funktions-GUID genererar ett nytt slumpmässigt GUID.|           
|[IIF](#iif)|Funktionen IIF returnerar en uppsättning möjliga värden baserat på ett angivet villkor.|
|[InStr](#instr)|Funktionen InStr söker efter den första förekomsten av en del sträng i en sträng.|
|[IsNull](#isnull)|Om uttrycket utvärderas till null returnerar funktionen IsNull True.|
|[IsNullOrEmpty](#isnullorempty)|Om uttrycket är null eller en tom sträng returnerar funktionen IsNullOrEmpty värdet true.|         
|[IsPresent](#ispresent)|Om uttrycket utvärderas till en sträng som inte är null och inte är tomt, returnerar funktionen IsPresent True.|    
|[IsString](#isstring)|Om uttrycket kan utvärderas till en sträng typ, utvärderar funktionen IsString till true.|
|[Objekt](#item)|Funktionen item returnerar ett objekt från en multi-valueion String/Attribute.|
|[Join](#join) |JOIN () liknar append (), förutom att det kan kombinera flera **käll** sträng värden till en enda sträng, och varje värde skiljs åt av en **avgränsnings** sträng.| 
|[Från](#left)|Funktionen Left returnerar ett angivet antal tecken från vänster i en sträng.|
|[Mid](#mid) |Returnerar en del sträng av käll värde. En under sträng är en sträng som bara innehåller några av tecknen från käll strängen.|
|[NormalizeDiacritics](#normalizediacritics)|Kräver ett sträng argument. Returnerar strängen, men med dia kritiska tecken ersatta med motsvarande icke-dia kritiska tecken.|
|[Ogiltigt](#not) |Vänder det booleska värdet för **källan**. Om **källobjektet** är "*True*" returnerar "*false*". Annars returnerar "*True*".| 
|[RemoveDuplicates](#removeduplicates)|Funktionen RemoveDuplicates använder en sträng med flera värden och ser till att varje värde är unikt.| 
|[Bytt](#replace) |Ersätter värden i en sträng. | 
|[SelectUniqueValue](#selectuniquevalue)|Kräver minst två argument, vilket är unika regler för generering av unika värden som definieras med hjälp av uttryck. Funktionen utvärderar varje regel och kontrollerar sedan värdet som genereras för unikhet i mål appen/katalogen.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Returnerar en enskild appRoleAssignment från listan över alla appRoleAssignments som har tilldelats till en användare för ett visst program.| 
|[Del](#split)|Delar upp en sträng i en Multivärdes mat ris med hjälp av det angivna avgränsnings tecken.|
|[StringFromSID](#stringfromsid)|Funktionen StringFromSid konverterar en byte mat ris som innehåller en säkerhets identifierare till en sträng.| 
|[StripSpaces](#stripspaces) |Tar bort alla blank steg ("") från käll strängen.| 
|[Switch](#switch)|Returnerar **värdet** för den **nyckeln** när **källobjektet** matchar en **nyckel**. | 
|[ToLower](#tolower)|Tar ett *käll* sträng värde och konverterar det till gemener med de angivna kultur reglerna.| 
|[ToUpper](#toupper)|Tar ett *käll* sträng värde och konverterar det till versaler med de angivna kultur reglerna.|
|[Reducera](#trim)|Funktionen trim tar bort inledande och avslutande blank steg från en sträng.|
|[Word](#word)|Funktionen ord returnerar ett ord som finns i en sträng, baserat på parametrar som beskriver avgränsarna som ska användas och ord numret som ska returneras.|

---
### <a name="append"></a>Lägg till
**Funktioner**<br> Lägg till (källa, suffix)

**Beskrivning:**<br> Tar ett käll sträng värde och lägger till suffixet i slutet av det.

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet från källobjektet. |
   | **huvudnamnssuffix** |Obligatorisk |Sträng |Strängen som du vill lägga till i slutet av source-värdet. |

---
### <a name="bitand"></a>BitAnd
**Beskrivning:**  
Funktionen BitAnd anger angivna bitar i ett värde.

**Uttryck**  
`num BitAnd(num value1, num value2)`

* värde1, värde2: numeriska värden som ska AND'ed tillsammans

**!**  
Den här funktionen konverterar båda parametrarna till den binära representationen och anger en bit till:

* 0 – om en eller båda av motsvarande bitar i *Värde1* och *värde2* är 0
* 1 – om båda motsvarande bitar är 1.

Med andra ord returneras 0 i samtliga fall, förutom när motsvarande bitar i båda parametrarna är 1.

**Exempel:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Returnerar 7 eftersom det hexadecimala "F" och "F7" utvärderas till det här värdet.

---

### <a name="cbool"></a>CBool
**Beskrivning:**  
Funktionen CBool returnerar ett booleskt värde baserat på det utvärderade uttrycket

**Uttryck**  
`bool CBool(exp Expression)`

**!**  
Om uttrycket utvärderas till ett värde som inte är noll returnerar CBool True, annars returneras FALSKT.

**Exempel:**  
`CBool([attrib1] = [attrib2])`  

Returnerar true om båda attributen har samma värde.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Beskrivning:**  
Funktionen ConvertFromBase64 konverterar det angivna base64-kodade värdet till en vanlig sträng.

**Uttryck**  
`str ConvertFromBase64(str source)` – antar Unicode för kodning  
`str ConvertFromBase64(str source, enum Encoding)`

* Källa: Base64-kodad sträng  
* Kodning: Unicode, ASCII, UTF8

**Exempel**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Båda exemplen returnerar "*Hello World!*"

---
### <a name="converttobase64"></a>ConvertToBase64
**Beskrivning:**  
Funktionen ConvertToBase64 konverterar en sträng till en Unicode base64-sträng.  
Konverterar värdet för en matris med heltal till motsvarande sträng representation som är kodad med bas-64 siffror.

**Uttryck**  
`str ConvertToBase64(str source)`

**Exempel:**  
`ConvertToBase64("Hello world!")`  
Returnerar "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Beskrivning:**  
Funktionen ConvertToUTF8Hex konverterar en sträng till ett hexadecimalt värde för UTF8-kodning.

**Uttryck**  
`str ConvertToUTF8Hex(str source)`

**!**  
Utdataformatet för den här funktionen används av Azure Active Directory som attribut för DN-attribut.

**Exempel:**  
`ConvertToUTF8Hex("Hello world!")`  
Returnerar 48656C6C6F20776F726C6421

---
### <a name="count"></a>Antal
**Beskrivning:**  
Funktionen COUNT returnerar antalet element i ett multi-värde-attribut

**Uttryck**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Beskrivning:**  
Funktionen CStr konverterar till en sträng data typ.

**Uttryck**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* värde: kan vara ett numeriskt värde, ett referens-eller Boolean-attribut.

**Exempel:**  
`CStr([dn])`  
Kan returnera "CN = Johan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Beskrivning:**  
Funktionen DateFromNum konverterar ett värde i ADs datum format till en DateTime-typ.

**Uttryck**  
`dt DateFromNum(num value)`

**Exempel:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Returnerar en DateTime som representerar 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponent
**Beskrivning:**  
Funktionen DNComponent returnerar värdet för en angiven DN-komponent som går från vänster.

**Uttryck**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: det referens-attribut som ska tolkas
* ComponentNumber: komponenten i DN att returnera

**Exempel:**  
`DNComponent(CRef([dn]),1)`  
Om DN är "CN = Johan, OU =..." returneras Joe

---
### <a name="error"></a>Fel
**Beskrivning:**  
Fel funktionen används för att returnera ett anpassat fel.

**Uttryck**  
`void Error(str ErrorMessage)`

**Exempel:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Om attributet accountName inte finns genererar du ett fel på objektet.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funktioner**<br> FormatDateTime (källa, inputFormat, outputFormat)

**Beskrivning:**<br> Tar en datum sträng från ett format och konverterar det till ett annat format.

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet från källobjektet. |
   | **inputFormat** |Obligatorisk |Sträng |Förväntat format för Source-värdet. För format som stöds, se [/dotNet/Standard/Base-types/Custom-date-and-Time-format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
   | **outputFormat** |Obligatorisk |Sträng |Formatet för datum för utdata. |

---
### <a name="guid"></a>GUID
**Beskrivning:**  
Funktions-GUID genererar ett nytt slumpmässigt GUID

**Uttryck**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Beskrivning:**  
Funktionen IIF returnerar en uppsättning möjliga värden baserat på ett angivet villkor.

**Uttryck**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* villkor: ett värde eller uttryck som kan utvärderas till true eller false.
* valueIfTrue: om villkoret utvärderas till sant returneras det returnerade värdet.
* valueIfFalse: om villkoret utvärderas till false returneras det returnerade värdet.

**Exempel:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Om användaren är en intern, returnerar alias för en användare med "t-" tillagd i början av den, annars returneras användarens alias som det är.

---
### <a name="instr"></a>InStr
**Beskrivning:**  
Funktionen InStr söker efter den första förekomsten av en del sträng i en sträng

**Uttryck**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: strängen som ska genomsökas
* stringmatch: strängen som ska hittas
* Start: Start position för att hitta del strängen
* Jämför: vbTextCompare eller vbBinaryCompare

**!**  
Returnerar positionen där under strängen hittades eller 0 om den inte hittades.

**Exempel:**  
`InStr("The quick brown fox","quick")`  
Evalues till 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Utvärderas till 7

---
### <a name="isnull"></a>IsNull
**Beskrivning:**  
Om uttrycket utvärderas till null returnerar funktionen IsNull True.

**Uttryck**  
`bool IsNull(var Expression)`

**!**  
För ett-attribut uttrycks ett null-värde av frånvaron av attributet.

**Exempel:**  
`IsNull([displayName])`  
Returnerar true om attributet inte finns i CS eller MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Beskrivning:**  
Om uttrycket är null eller en tom sträng returnerar funktionen IsNullOrEmpty värdet true.

**Uttryck**  
`bool IsNullOrEmpty(var Expression)`

**!**  
För ett-attribut utvärderar detta till sant om attributet saknas eller finns, men är en tom sträng.  
Inversen till den här funktionen heter IsPresent.

**Exempel:**  
`IsNullOrEmpty([displayName])`  
Returnerar true om attributet inte finns eller är en tom sträng i CS eller MV.

---
### <a name="ispresent"></a>IsPresent
**Beskrivning:**  
Om uttrycket utvärderas till en sträng som inte är null och inte är tomt, returnerar funktionen IsPresent True.

**Uttryck**  
`bool IsPresent(var expression)`

**!**  
Inversen till den här funktionen heter IsNullOrEmpty.

**Exempel:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Objekt
**Beskrivning:**  
Funktionen item returnerar ett objekt från en multi-valueion String/Attribute.

**Uttryck**  
`var Item(mvstr attribute, num index)`

* attribut: multi-Value-attribut
* index: indexet till ett objekt i multi-Value-strängen.

**!**  
Funktionen item är användbar tillsammans med funktionen contains eftersom den senare funktionen returnerar indexet till ett objekt i multi-Value-attributet.

Genererar ett fel om indexet ligger utanför intervallet.

**Exempel:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Returnerar den primära e-postadressen.

---
### <a name="isstring"></a>IsString
**Beskrivning:**  
Om uttrycket kan utvärderas till en sträng typ, utvärderar funktionen IsString till true.

**Uttryck**  
`bool IsString(var expression)`

**!**  
Används för att avgöra om CStr () kan lyckas parsa uttrycket.

---
### <a name="join"></a>Anslut
**Funktioner**<br> Gå till (avgränsare, source1, SOURCE2,...)

**Beskrivning:**<br> JOIN () liknar append (), förutom att det kan kombinera flera **käll** sträng värden till en enda sträng, och varje värde skiljs åt av en **avgränsnings** sträng.

Om ett av käll värdena är ett flervärdesattribut, kopplas alla värden i det attributet samman, avgränsade med avgränsning svärdet.

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **brytning** |Obligatorisk |Sträng |Sträng som används för att avgränsa käll värden när de sammanfogas till en sträng. Kan vara "" om ingen avgränsare krävs. |
   | **source1 ... Källa** |Obligatoriskt, variabel antal gånger |Sträng |Sträng värden som ska sammanfogas tillsammans. |

---
### <a name="left"></a>Vänster
**Beskrivning:**  
Funktionen Left returnerar ett angivet antal tecken från vänster i en sträng.

**Uttryck**  
`str Left(str string, num NumChars)`

* sträng: strängen att returnera tecken från
* NumChars: ett tal som identifierar antalet tecken som ska returneras från början (vänster) av sträng

**!**  
En sträng som innehåller de första numChars tecknen i strängen:

* Om numChars = 0 returneras en tom sträng.
* Om numChars < 0, returnerar du Indatasträngen.
* Om strängen är null returneras en tom sträng.

Om strängen innehåller färre tecken än det tal som anges i numChars returneras en sträng som är identisk med sträng (dvs. med alla tecken i parameter 1).

**Exempel:**  
`Left("John Doe", 3)`  
Returnerar `Joh` .

---
### <a name="mid"></a>Mellan
**Funktioner**<br> Mid (källa, start, längd)

**Beskrivning:**<br> Returnerar en del sträng av käll värde. En under sträng är en sträng som bara innehåller några av tecknen från käll strängen.

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet. |
   | **har** |Obligatorisk |heltal |Index i **käll** strängen där under strängen ska starta. Det första alfabetet i strängen kommer att ha indexet 1, andra tecken kommer att ha index 2 och så vidare. |
   | **length** |Obligatorisk |heltal |Del strängens längd. Om längden slutar utanför **käll** strängen returnerar funktionen del sträng från **Start** index till slutet av **käll** strängen. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funktioner**<br> NormalizeDiacritics (källa)

**Beskrivning:**<br> Kräver ett sträng argument. Returnerar strängen, men med dia kritiska tecken ersatta med motsvarande icke-dia kritiska tecken. Används vanligt vis för att konvertera förnamn och efter namn som innehåller dia kritiska tecken (accenttecken) till juridiska värden som kan användas i olika användar identifierare som användarens huvud namn, SAM-kontonamn och e-postadresser.

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **källicensservern** |Obligatorisk |Sträng | Vanligt vis attributet förnamn eller efter namn. |

---
### <a name="not"></a>Inte
**Funktioner**<br> Inte (källa)

**Beskrivning:**<br> Vänder det booleska värdet för **källan**. Om **källobjektet** är "*True*" returnerar "*false*". Annars returnerar "*True*".

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **källicensservern** |Obligatorisk |Boolesk sträng |Förväntade **käll** värden är "true" eller "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Beskrivning:**  
Funktionen RemoveDuplicates använder en sträng med flera värden och ser till att varje värde är unikt.

**Uttryck**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exempel:**  
`RemoveDuplicates([proxyAddresses])`  
Returnerar ett sanerat proxyAddress-attribut där alla dubblettvärden har tagits bort.

---
### <a name="replace"></a>Ersätt
**Funktioner**<br> Ersätt (källa, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, mall)

**Beskrivning:**<br>
Ersätter värden i en sträng. Den fungerar på olika sätt beroende på vilka parametrar som anges:

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

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
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
**Funktioner**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Beskrivning:**<br> Kräver minst två argument, vilket är unika regler för generering av unika värden som definieras med hjälp av uttryck. Funktionen utvärderar varje regel och kontrollerar sedan värdet som genereras för unikhet i mål appen/katalogen. Det första unika värdet som du hittar är det som returnerades. Om alla värden redan finns i målet kommer posten att få deponerats och orsaken loggas i gransknings loggarna. Det finns ingen övre bindning till det antal argument som kan anges.

> [!NOTE]
> - Detta är en funktion på den översta nivån, den kan inte kapslas.
> - Den här funktionen kan inte tillämpas på attribut som har en matchande prioritet.  
> - Den här funktionen är endast avsedd att användas för att skapa poster. När du använder det med ett-attribut ställer du in egenskapen **tillämpa mappning** på **endast när objekt skapas**.
> - Den här funktionen stöds för närvarande endast för "arbets dag för Active Directory användar etablering". Det kan inte användas med andra etablerings program. 


**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Minst 2 krävs, ingen övre bindning |Sträng | Lista med regler för generering av unika värden som ska utvärderas. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funktioner**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Beskrivning:**<br> Returnerar en enskild appRoleAssignment från listan över alla appRoleAssignments som har tilldelats till en användare för ett visst program. Den här funktionen krävs för att konvertera appRoleAssignments-objektet till en enda roll namn sträng. Observera att det bästa sättet är att se till att endast en appRoleAssignment är tilldelad en användare åt gången, och om flera roller tilldelas kan den returnerade roll strängen inte vara förutsägbar. 

**Komponentparametrar**<br> 

  | Name | Krävs/upprepas | Typ | Kommentarer |
  |--- | --- | --- | --- |
  | **AppRoleAssignments** |Obligatorisk |Sträng |**[appRoleAssignments]** -objekt. |

---
### <a name="split"></a>Dela
**Funktioner**<br> Dela (källa, avgränsare)

**Beskrivning:**<br> Delar upp en sträng i en Multivärdes mat ris med hjälp av det angivna avgränsnings tecken.

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **källicensservern** |Obligatorisk |Sträng |**käll** värde att uppdatera. |
   | **avgränsare** |Obligatorisk |Sträng |Anger det tecken som ska användas för att dela strängen (exempel: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**Beskrivning:**  
Funktionen StringFromSid konverterar en byte mat ris som innehåller en säkerhets identifierare till en sträng.

**Uttryck**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Funktioner**<br> StripSpaces (källa)

**Beskrivning:**<br> Tar bort alla blank steg ("") från käll strängen.

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **källicensservern** |Obligatorisk |Sträng |**käll** värde att uppdatera. |

---
### <a name="switch"></a>Switch
**Funktioner**<br> Switch (källa, defaultValue, KEY1, värde1, key2, värde2,...)

**Beskrivning:**<br> Returnerar **värdet** för den **nyckeln** när **källobjektet** matchar en **nyckel**. Om **käll** värde inte matchar några nycklar returnerar **DefaultValue**.  **Nyckel** -och **värde** parametrar måste alltid komma in i par. Funktionen förväntar sig alltid ett jämnt antal parametrar.

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **källicensservern** |Obligatorisk |Sträng |**Käll** värde att kontrol lera. |
   | **Standar** |Valfritt |Sträng |Standardvärdet som ska användas när källan inte matchar några nycklar. Kan vara en tom sträng (""). |
   | **knapp** |Obligatorisk |Sträng |**Nyckel** att jämföra **käll** värde med. |
   | **värde** |Obligatorisk |Sträng |Ersättnings värde för den **källa** som matchar nyckeln. |

---
### <a name="tolower"></a>ToLower
**Funktioner**<br> ToLower (källa, kultur)

**Beskrivning:**<br> Tar ett *käll* sträng värde och konverterar det till gemener med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Komponentparametrar**<br> 

   | Name | Krävs/upprepas | Typ | Kommentarer |
   | --- | --- | --- | --- |
   | **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet från källobjektet |
   | **substrat** |Valfritt |Sträng |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

---

### <a name="toupper"></a>ToUpper
**Funktioner**<br> ToUpper (källa, kultur)

**Beskrivning:**<br> Tar ett *käll* sträng värde och konverterar det till versaler med de angivna kultur reglerna. Om det inte finns någon angiven *kultur* information, används en invariant kultur.

**Komponentparametrar**<br> 

  | Name | Krävs/upprepas | Typ | Kommentarer |
  | --- | --- | --- | --- |
  | **källicensservern** |Obligatorisk |Sträng |Vanligt vis namnet på attributet från källobjektet. |
  | **substrat** |Valfritt |Sträng |Formatet för kultur namnet baserat på RFC 4646 är *languagecode2-land/regioncode2*, där *languagecode2* är språk koden för två bokstäver och *land/regioncode2* är under kultur koden med två bokstäver. Exempel är ja-JP för japanska (Japan) och en-US för engelska (USA). I de fall där en språkkod med två bokstäver inte är tillgänglig används en kod med tre bokstäver härledd från ISO 639-2.|

---

### <a name="trim"></a>Trim
**Beskrivning:**  
Funktionen trim tar bort inledande och avslutande blank steg från en sträng.

**Uttryck**  
`str Trim(str value)`  

**Exempel:**  
`Trim(" Test ")`  
Returnerar "test".

`Trim([proxyAddresses])`  
Tar bort inledande och avslutande blank steg för varje värde i attributet proxyAddress.

---
### <a name="word"></a>Word
**Beskrivning:**  
Funktionen ord returnerar ett ord som finns i en sträng, baserat på parametrar som beskriver avgränsarna som ska användas och ord numret som ska returneras.

**Uttryck**  
`str Word(str string, num WordNumber, str delimiters)`

* sträng: strängen som ett ord ska returneras från.
* WordNumber: ett tal som identifierar vilket ord nummer som ska returneras.
* avgränsare: en sträng som representerar de avgränsare som ska användas för att identifiera ord

**!**  
Varje sträng med tecken i strängen avgränsade med ett av tecknen i avgränsare identifieras som ord:

* Om talet < 1 returneras en tom sträng.
* Om strängen är null returnerar en tom sträng.

Om strängen innehåller färre än tal ord, eller om strängen inte innehåller ord som identifieras av avgränsare returneras en tom sträng.

**Exempel:**  
`Word("The quick brown fox",3," ")`  
Returnerar "brun"

`Word("This,string!has&many separators",3,",!&#")`  
Returnerar "har"

## <a name="examples"></a>Exempel
### <a name="strip-known-domain-name"></a>Strip-känt domän namn
Du måste randig ett känt domän namn från en användares e-postadress för att få ett användar namn. <br>
Om domänen till exempel är "contoso.com" kan du använda följande uttryck:

**Uttryck** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Exempel på indata/utdata:** <br>

* **Inmatad** (e-post): " john.doe@contoso.com "
* **Utdata**: "John. berg"

### <a name="append-constant-suffix-to-user-name"></a>Lägg till konstant suffix i användar namn
Om du använder en Salesforce-Sandbox kan du behöva lägga till ytterligare ett suffix till alla användar namn innan du synkroniserar dem.

**Uttryck** <br>
`Append([userPrincipalName], ".test")`

**Exempel på indata/utdata:** <br>

* **Inmatade**: (userPrincipalName): " John.Doe@contoso.com "
* **Utdata**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generera användaralias genom att sammanfoga delar av för-och efter namn
Du måste generera ett användaralias genom att göra de första 3 bokstäverna för användarens förnamn och de första fem bokstäverna i användarens efter namn.

**Uttryck** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Exempel på indata/utdata:** <br>

* **Inmatade** (givenName): "John"
* **Inmatad** (efter namn): "berg"
* **Utdata**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Ta bort dia kritiska tecken från en sträng
Du måste ersätta tecken som innehåller accenttecken med motsvarande tecken som inte innehåller accenttecken.

**Uttryck** <br>
NormalizeDiacritics ([givenName])

**Exempel på indata/utdata:** <br>

* **Inmatade** (givenName): "Zoë"
* **Utdata**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dela upp en sträng i en Multivärdes mat ris
Du måste ta en kommaavgränsad lista med strängar och dela upp dem i en matris som kan kopplas till ett flervärdesattribut som Salesforce: s PermissionSets-attribut. I det här exemplet har en lista över behörighets uppsättningar fyllts i extensionAttribute5 i Azure AD.

**Uttryck** <br>
Dela ([extensionAttribute5], ",")

**Exempel på indata/utdata:** <br>

* **Inmatade** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Utmatnings datum som en sträng i ett visst format
Du vill skicka datum till ett SaaS-program i ett visst format. <br>
Till exempel vill du formatera datum för ServiceNow.

**Uttryck** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Exempel på indata/utdata:**

* **Inmatade** (extensionAttribute1): "20150123105347.1 z"
* **Utdata**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Ersätt ett värde baserat på fördefinierade alternativ uppsättningar

Du måste definiera tids zonen för användaren baserat på den delstats kod som lagras i Azure AD. <br>
Om tillstånds koden inte matchar något av de fördefinierade alternativen använder du standardvärdet "Australien/Sydney".

**Uttryck** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Exempel på indata/utdata:**

* **Inmatade** (State): "QLD"
* **Utdata**: "Australien/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Ersätt tecken med ett reguljärt uttryck
Du måste hitta tecken som matchar ett reguljärt uttrycks värde och ta bort dem.

**Uttryck** <br>

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

**Uttryck** <br>

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


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)