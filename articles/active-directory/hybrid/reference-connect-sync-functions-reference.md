---
title: 'Azure AD Connect Sync: Functions reference | Microsoft Docs'
description: Referens för deklarativ etablerings uttryck i Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7454733233f1fd487d774d52a6f46187354ae05c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98246714"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect Sync: Functions reference
I Azure AD Connect används funktioner för att manipulera ett attributvärde under synkroniseringen.  
Syntaxen för funktionerna uttrycks i följande format:  
`<output type> FunctionName(<input type> <position name>, ..)`

Om en funktion är överbelastad och accepterar flera syntaxer visas alla giltiga syntaxer.  
Funktionerna är starkt skrivna och kontrollerar att den typ som skickades matchar den dokumenterade typen.  
Om typen inte matchar genereras ett fel.

Typerna uttrycks med följande syntax:

* **bin** – binär
* **bool** – boolesk
* **DT** – UTC-datum/tid
* **Enum** – uppräkning av kända konstanter
* **exp** – Expression, som förväntas utvärderas till ett booleskt värde
* **mvbin** – multi-Value Binary
* **mvstr** – multi-Value-sträng
* **mvref** – multi-Value reference
* **NUM** – numeriskt
* **Ref** – referens
* **Str** – sträng
* **var** – en variant av (nästan) någon annan typ
* **void** – returnerar inte ett värde

Funktionerna med typerna **mvbin**, **mvstr** och **mvref** kan bara användas med flervärdesattribut. Funktioner med **bin**-, **Str**-och **Ref** -arbete på både enkelvärdesattribut och attribut med flera värden.

## <a name="functions-reference"></a>Referens för funktioner

* **Certifikat**
  * [CertExtensionOids](#certextensionoids)
  * [CertFormat](#certformat)
  * [CertFriendlyName](#certfriendlyname)
  * [CertHashString](#certhashstring)
  * [CertIssuer](#certissuer)
  * [CertIssuerDN](#certissuerdn)
  * [CertIssuerOid](#certissueroid)
  * [CertKeyAlgorithm](#certkeyalgorithm)
  * [CertKeyAlgorithmParams](#certkeyalgorithmparams)
  * [CertNameInfo](#certnameinfo)
  * [CertNotAfter](#certnotafter)
  * [CertNotBefore](#certnotbefore)
  * [CertPublicKeyOid](#certpublickeyoid)
  * [CertPublicKeyParametersOid](#certpublickeyparametersoid)
  * [CertSerialNumber](#certserialnumber)
  * [CertSignatureAlgorithmOid](#certsignaturealgorithmoid)
  * [CertSubject](#certsubject)
  * [CertSubjectNameDN](#certsubjectnamedn)
  * [CertSubjectNameOid](#certsubjectnameoid)
  * [CertThumbprint](#certthumbprint)
  * [CertVersion](#certversion)
  * [IsCert](#iscert)
* **Räkning**
  * [CBool](#cbool)
  * [CDate](#cdate)
  * [CGuid](#cguid)
  * [ConvertFromBase64](#convertfrombase64)
  * [ConvertToBase64](#converttobase64)
  * [ConvertFromUTF8Hex](#convertfromutf8hex)
  * [ConvertToUTF8Hex](#converttoutf8hex)
  * [CNum](#cnum)
  * [CRef](#cref)
  * [CStr](#cstr)
  * [StringFromGuid](#stringfromguid)
  * [StringFromSid](#stringfromsid)
* **Datum/tid**
  * [DateAdd](#dateadd)
  * [DateFromNum](#datefromnum)
  * [FormatDateTime](#formatdatetime)
  * [Vidare](#now)
  * [NumFromDate](#numfromdate)
* **Katalog**
  * [DNComponent](#dncomponent)
  * [DNComponentRev](#dncomponentrev)
  * [EscapeDNComponent](#escapedncomponent)
* **Utvärdering**
  * [IsBitSet](#isbitset)
  * [IsDate](#isdate)
  * [IsEmpty](#isempty)
  * [IsGuid](#isguid)
  * [IsNull](#isnull)
  * [IsNullOrEmpty](#isnullorempty)
  * [IsNumeric](#isnumeric)
  * [IsPresent](#ispresent)
  * [IsString](#isstring)
* **Matematik**
  * [BitAnd](#bitand)
  * [BitOr](#bitor)
  * [RandomNum](#randomnum)
* **Multi * Value**
  * [Ingår](#contains)
  * [Reparationer](#count)
  * [Objekt](#item)
  * [ItemOrNull](#itemornull)
  * [Join](#join)
  * [RemoveDuplicates](#removeduplicates)
  * [Del](#split)
* **Program flöde**
  * [Fel](#error)
  * [IIF](#iif)
  * [Välj](#select)
  * [Switch](#switch)
  * [Vilken](#where)
  * [För](#with)
* **Text**
  * [LED](#guid)
  * [InStr](#instr)
  * [InStrRev](#instrrev)
  * [LCase](#lcase)
  * [Från](#left)
  * [Len](#len)
  * [LTrim](#ltrim)
  * [Mid](#mid)
  * [PadLeft](#padleft)
  * [PadRight](#padright)
  * [PCase](#pcase)
  * [Bytt](#replace)
  * [ReplaceChars](#replacechars)
  * [Right](#right)
  * [RTrim](#rtrim)
  * [Reducera](#trim)
  * [UCase](#ucase)
  * [Word](#word)

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
`BitAnd(&HF, &HF7)`  
Returnerar 7 eftersom det hexadecimala "F" och "F7" utvärderas till det här värdet.

---
### <a name="bitor"></a>BitOr
**Beskrivning:**  
Funktionen BitOr anger angivna bitar i ett värde.

**Uttryck**  
`num BitOr(num value1, num value2)`

* värde1, värde2: numeriska värden som ska OR'ed tillsammans

**!**  
Den här funktionen konverterar båda parametrarna till den binära representationen och anger en bit till 1 om en eller båda av motsvarande bitar i masken och flaggan är 1, och till 0 om båda motsvarande bitar är 0. Med andra ord returneras 1 i samtliga fall, förutom om motsvarande bitar i båda parametrarna är 0.

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
### <a name="cdate"></a>CDate
**Beskrivning:**  
Funktionen CDate returnerar en UTC-DateTime från en sträng. DateTime är inte en typ av ursprungligt attribut i Sync men används av vissa funktioner.

**Uttryck**  
`dt CDate(str value)`

* Värde: en sträng med datum, tid och eventuellt tidszon

**!**  
Den returnerade strängen är alltid i UTC.

**Exempel:**  
`CDate([employeeStartTime])`  
Returnerar ett datum/tid baserat på medarbetarens start tid

`CDate("2013-01-10 4:00 PM -8")`  
Returnerar en DateTime som representerar "2013-01-11 12:00 AM"


---
### <a name="certextensionoids"></a>CertExtensionOids
**Beskrivning:**  
Returnerar OID-värden för alla kritiska tillägg för ett certifikat objekt.

**Uttryck**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certformat"></a>CertFormat
**Beskrivning:**  
Returnerar namnet på formatet för X. 509v3-certifikatet.

**Uttryck**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Beskrivning:**  
Returnerar det associerade aliaset för ett certifikat.

**Uttryck**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certhashstring"></a>CertHashString
**Beskrivning:**  
Returnerar SHA1-hash-värdet för X. 509v3-certifikatet som en hexadecimal sträng.

**Uttryck**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certissuer"></a>CertIssuer
**Beskrivning:**  
Returnerar namnet på den certifikat utfärdare som utfärdade X. 509v3-certifikatet.

**Uttryck**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Beskrivning:**  
Returnerar det unika namnet för certifikat utfärdaren.

**Uttryck**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certissueroid"></a>CertIssuerOid
**Beskrivning:**  
Returnerar OID för certifikat utfärdaren.

**Uttryck**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Beskrivning:**  
Returnerar information om nyckelalgoritm för det här X. 509v3-certifikatet som en sträng.

**Uttryck**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Beskrivning:**  
Returnerar Key algorithm-parametrarna för X. 509v3-certifikatet som en hexadecimal sträng.

**Uttryck**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certnameinfo"></a>CertNameInfo
**Beskrivning:**  
Returnerar ämnes-och utfärdarens namn från ett certifikat.

**Uttryck**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.
*   X509NameType: X509NameType-värdet för ämnet.
*   includesIssuerName: sant om du vill inkludera utfärdarens namn. annars FALSE.

---
### <a name="certnotafter"></a>CertNotAfter
**Beskrivning:**  
Returnerar det datum i lokal tid som ett certifikat inte längre är giltigt.

**Uttryck**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certnotbefore"></a>CertNotBefore
**Beskrivning:**  
Returnerar det datum i lokal tid som ett certifikat börjar gälla.

**Uttryck**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Beskrivning:**  
Returnerar OID för den offentliga nyckeln för X. 509v3-certifikatet.

**Uttryck**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Beskrivning:**  
Returnerar OID för parametrarna för den offentliga nyckeln för X. 509v3-certifikatet.

**Uttryck**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Beskrivning:**  
Returnerar serie numret för X. 509v3-certifikatet.

**Uttryck**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Beskrivning:**  
Returnerar OID för algoritmen som används för att skapa signaturen för ett certifikat.

**Uttryck**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certsubject"></a>CertSubject
**Beskrivning:**  
Hämtar det unika ämnes namnet från ett certifikat.

**Uttryck**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Beskrivning:**  
Returnerar det unika ämnes namnet från ett certifikat.

**Uttryck**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Beskrivning:**  
Returnerar OID för ämnes namnet från ett certifikat.

**Uttryck**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certthumbprint"></a>CertThumbprint
**Beskrivning:**  
Returnerar tumavtrycket för ett certifikat.

**Uttryck**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="certversion"></a>CertVersion
**Beskrivning:**  
Returnerar formatet X. 509-versionen av ett certifikat.

**Uttryck**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.

---
### <a name="cguid"></a>CGuid
**Beskrivning:**  
Funktionen CGuid konverterar sträng representationen av ett GUID till dess binära representation.

**Uttryck**  
`bin CGuid(str GUID)`

* En sträng formaterad i det här mönstret: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX eller {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX}

---
### <a name="contains"></a>Innehåller
**Beskrivning:**  
Funktionen contains hittar en sträng inuti ett multi-värde-attribut

**Uttryck**  
`num Contains (mvstring attribute, str search)` – Skift läges känsligt  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` – Skift läges känsligt

* attribut: det multi-värde-attribut som ska genomsökas.
* Sök: sträng att söka efter i attributet.
* Casetype: CaseInsensitive eller CaseSensitive.

Returnerar index i multi-Value-attributet där strängen hittades. 0 returneras om strängen inte hittas.

**!**  
För flervärdesattribut med flera värden hittar sökningen del strängar i värdena.  
För referens-attribut måste den genomsökta strängen exakt matcha värdet som ska anses vara en matchning.

**Exempel:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Om attributet proxyAddresses har en primär e-postadress (anges med versaler "SMTP:"), returnerar proxyAddress-attributet, annars returneras ett fel.

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
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Beskrivning:**  
Funktionen ConvertFromUTF8Hex konverterar det angivna UTF8 hex-kodade värdet till en sträng.

**Uttryck**  
`str ConvertFromUTF8Hex(str source)`

* Källa: UTF8 2 – byte-kodad STING

**!**  
Skillnaden mellan den här funktionen och ConvertFromBase64 ([], UTF8) i som resultatet är läsvänlig för attributet DN.  
Det här formatet används av Azure Active Directory som DN.

**Exempel:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Returnerar "*Hello World!*"

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
### <a name="cnum"></a>CNum
**Beskrivning:**  
Funktionen CNum tar en sträng och returnerar en numerisk datatyp.

**Uttryck**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Beskrivning:**  
Konverterar en sträng till ett referens-attribut

**Uttryck**  
`ref CRef(str value)`

**Exempel:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

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
### <a name="dateadd"></a>DateAdd
**Beskrivning:**  
Returnerar ett datum som innehåller ett datum som har lagts till ett angivet tidsintervall.

**Uttryck**  
`dt DateAdd(str interval, num value, dt date)`

* Interval: sträng uttryck som är det tidsintervall som du vill lägga till. Strängen måste ha ett av följande värden:
  * yyyy-år
  * q kvartal
  * m månad
  * y-dagen på året
  * d dag
  * w veckodag
  * WW-veckan
  * h timme
  * n minut
  * s sekund
* värde: antalet enheter som du vill lägga till. Det kan vara positivt (för att få datum i framtiden) eller negativa (för att hämta datum tidigare).
* Date: DateTime som representerar datumet som intervallet läggs till i.

**Exempel:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Lägger till 3 månader och returnerar en DateTime som representerar "2001-04-01".

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
### <a name="dncomponentrev"></a>DNComponentRev
**Beskrivning:**  
Funktionen DNComponentRev returnerar värdet för en angiven DN-komponent som går från höger (End).

**Uttryck**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: det referens-attribut som ska tolkas
* ComponentNumber – komponenten i DN att returnera
* Alternativ: DC – ignorera alla komponenter med "DC ="

**Exempel:**  
Om DN är "CN = Johan, OU = Atlanta, OU = GA, OU = US, DC = contoso, DC = com" och  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Båda returnerar oss.

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
### <a name="escapedncomponent"></a>EscapeDNComponent
**Beskrivning:**  
Funktionen EscapeDNComponent tar en komponent i ett DN och avmarkerar den så att den kan representeras i LDAP.

**Uttryck**  
`str EscapeDNComponent(str value)`

**Exempel:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Kontrollerar att objektet kan skapas i en LDAP-katalog även om attributet displayName innehåller tecken som måste undantas i LDAP.

---
### <a name="formatdatetime"></a>FormatDateTime
**Beskrivning:**  
Funktionen FormatDateTime används för att formatera en DateTime till en sträng med ett angivet format

**Uttryck**  
`str FormatDateTime(dt value, str format)`

* värde: ett värde i DateTime-format
* format: en sträng som representerar det format som ska konverteras till.

**!**  
Möjliga värden för formatet hittar du här: [anpassade datum-och tids format för funktionen format](/dax/custom-date-and-time-formats-for-the-format-function).

**Exempel:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-MM-dd")`  
Resulterar i "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Kan resultera i "20140905081453.0 Z"

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
### <a name="instrrev"></a>InStrRev
**Beskrivning:**  
Funktionen InStrRev söker efter den sista förekomsten av en del sträng i en sträng

**Uttryck**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: strängen som ska genomsökas
* stringmatch: strängen som ska hittas
* Start: Start position för att hitta del strängen
* Jämför: vbTextCompare eller vbBinaryCompare

**!**  
Returnerar positionen där under strängen hittades eller 0 om den inte hittades.

**Exempel:**  
`InStrRev("abbcdbbbef","bb")`  
Returnerar 7

---
### <a name="isbitset"></a>IsBitSet
**Beskrivning:**  
Funktionen IsBitSet testar om en bit har angetts eller inte

**Uttryck**  
`bool IsBitSet(num value, num flag)`

* värde: ett numeriskt värde som utvärderas. flagga: ett numeriskt värde som har den bit som ska utvärderas

**Exempel:**  
`IsBitSet(&HF,4)`  
Returnerar true eftersom bit "4" är inställt på det hexadecimala värdet "F"

---
### <a name="isdate"></a>IsDate
**Beskrivning:**  
Om uttrycket kan utvärderas som en DateTime-typ, utvärderar funktionen IsDate till true.

**Uttryck**  
`bool IsDate(var Expression)`

**!**  
Används för att avgöra om CDate () kan lyckas.

---
### <a name="iscert"></a>IsCert
**Beskrivning:**  
Returnerar sant om rå data kan serialiseras i .NET X509Certificate2-certifikat objekt.

**Uttryck**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: byte mat ris representation av ett X. 509-certifikat. Byte mat ris kan vara binära (DER) kodade eller Base64-kodade X. 509-data.
---
### <a name="isempty"></a>IsEmpty
**Beskrivning:**  
Om attributet finns i CS eller MV, men utvärderas till en tom sträng, evalueras funktionen IsEmpty till true.

**Uttryck**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**Beskrivning:**  
Om strängen kan konverteras till ett GUID, utvärderas funktionen IsGuid till true.

**Uttryck**  
`bool IsGuid(str GUID)`

**!**  
Ett GUID definieras som en sträng enligt något av följande mönster: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX eller {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX}

Används för att avgöra om CGuid () kan lyckas.

**Exempel:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Om StrAttribute har ett GUID-format returnerar du en binär representation, annars returnerar ett null-värde.

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
### <a name="isnumeric"></a>IsNumeric
**Beskrivning:**  
Funktionen IsNumeric returnerar ett booleskt värde som anger om ett uttryck kan utvärderas som en siffer typ.

**Uttryck**  
`bool IsNumeric(var Expression)`

**!**  
Används för att avgöra om CNum () kan lyckas parsa uttrycket.

---
### <a name="isstring"></a>IsString
**Beskrivning:**  
Om uttrycket kan utvärderas till en sträng typ, utvärderar funktionen IsString till true.

**Uttryck**  
`bool IsString(var expression)`

**!**  
Används för att avgöra om CStr () kan lyckas parsa uttrycket.

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
### <a name="itemornull"></a>ItemOrNull
**Beskrivning:**  
Funktionen ItemOrNull returnerar ett objekt från en multi-Value-sträng/-attribut.

**Uttryck**  
`var ItemOrNull(mvstr attribute, num index)`

* attribut: multi-Value-attribut
* index: indexet till ett objekt i multi-Value-strängen.

**!**  
ItemOrNull-funktionen är användbar tillsammans med funktionen contains eftersom den senare funktionen returnerar indexet till ett objekt i multi-Value-attributet.

Om index ligger utanför intervallet returnerar värdet null.

---
### <a name="join"></a>Anslut
**Beskrivning:**  
Funktionen Join tar en multi-Value-sträng och returnerar en ensiffrig sträng med angiven avgränsare som infogas mellan varje objekt.

**Uttryck**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribut: multi-Value-attribut som innehåller strängar som ska anslutas.
* avgränsare: valfri sträng som används för att avgränsa del strängarna i den returnerade strängen. Om det utelämnas används blank tecken (""). Om avgränsaren är en tom sträng ("") eller inget, kombineras alla objekt i listan utan avgränsare.

**Kommentarer**  
Det finns en paritet mellan funktionerna Join och Split. Funktionen Join tar en matris med strängar och kopplar dem med hjälp av en avgränsnings sträng, för att returnera en enskild sträng. Funktionen Split tar en sträng och avgränsar den i avgränsaren för att returnera en sträng mat ris. En viktig skillnad är dock att kopplingen kan sammanfoga strängar med valfri avgränsnings sträng, delning kan bara separera strängar med en enda tecken avgränsare.

**Exempel:**  
`Join([proxyAddresses],",")`  
Kan returnera: " SMTP:john.doe@contoso.com , smtp:jd@contoso.com "

---
### <a name="lcase"></a>LCase
**Beskrivning:**  
Funktionen LCase konverterar alla tecken i en sträng till gemener.

**Uttryck**  
`str LCase(str value)`

**Exempel:**  
`LCase("TeSt")`  
Returnerar "test".

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
Returnerar "Joh".

---
### <a name="len"></a>Len
**Beskrivning:**  
Funktionen len returnerar antalet tecken i en sträng.

**Uttryck**  
`num Len(str value)`

**Exempel:**  
`Len("John Doe")`  
Returnerar 8

---
### <a name="ltrim"></a>LTrim
**Beskrivning:**  
Funktionen LTrim tar bort inledande blank steg från en sträng.

**Uttryck**  
`str LTrim(str value)`

**Exempel:**  
`LTrim(" Test ")`  
Returnerar "test"

---
### <a name="mid"></a>Mellan
**Beskrivning:**  
Funktionen MID returnerar ett angivet antal tecken från en angiven position i en sträng.

**Uttryck**  
`str Mid(str string, num start, num NumChars)`

* sträng: strängen att returnera tecken från
* Start: ett tal som identifierar start positionen i strängen för att returnera tecken från
* NumChars: ett tal som identifierar antalet tecken som ska returneras från positionen i strängen

**!**  
Returnera numChars tecken som börjar från positionen börjar i strängen.  
En sträng som innehåller numChars tecken från positionen börjar i strängen:

* Om numChars = 0 returneras en tom sträng.
* Om numChars < 0, returnerar du Indatasträngen.
* Om start > längden på strängen returnerar du Indatasträngen.
* Om start <= 0 returnerar du Indatasträngen.
* Om strängen är null returneras en tom sträng.

Om det inte finns några numChar tecken kvar i strängen från positions Start, returneras så många tecken som möjligt.

**Exempel:**  
`Mid("John Doe", 3, 5)`  
Returnerar "HN gör".

`Mid("John Doe", 6, 999)`  
Returnerar "berg"

---
### <a name="now"></a>Now
**Beskrivning:**  
Funktionen Now returnerar en DateTime som anger aktuellt datum och tid, enligt datorns system datum och tid.

**Uttryck**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Beskrivning:**  
Funktionen NumFromDate returnerar ett datum i datum formatet för AD.

**Uttryck**  
`num NumFromDate(dt value)`

**Exempel:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Returnerar 129699324000000000

---
### <a name="padleft"></a>PadLeft
**Beskrivning:**  
PadLeft-funktionen vänsterjusteras – fyller en sträng med en angiven längd med hjälp av ett angivet utfyllnads tecken.

**Uttryck**  
`str PadLeft(str string, num length, str padCharacter)`

* sträng: strängen som ska padas.
* Length: ett heltal som representerar önskad sträng längd.
* padCharacter: en sträng som består av ett enda tecken som ska användas som pad-tecken

**!**

* Om längden på strängen är mindre än längd läggs padCharacter flera gånger till i början (vänster) av strängen tills den har en längd som är lika med längd.
* PadCharacter kan vara ett blank stegs tecken, men det får inte vara ett null-värde.
* Om längden på strängen är lika med eller större än längden returneras strängen oförändrad.
* Om strängen har en längd som är större än eller lika med längd returneras en sträng som är identisk med en sträng.
* Om längden på strängen är mindre än längd returneras en ny sträng med den önskade längden som innehåller strängen utfyllnad med en padCharacter.
* Om strängen är null returnerar funktionen en tom sträng.

**Exempel:**  
`PadLeft("User", 10, "0")`  
Returnerar "000000User".

---
### <a name="padright"></a>PadRight
**Beskrivning:**  
PadRight-funktionen Högerjusterar en sträng till en angiven längd med hjälp av ett angivet utfyllnads tecken.

**Uttryck**  
`str PadRight(str string, num length, str padCharacter)`

* sträng: strängen som ska padas.
* Length: ett heltal som representerar önskad sträng längd.
* padCharacter: en sträng som består av ett enda tecken som ska användas som pad-tecken

**!**

* Om längden på strängen är mindre än längden läggs padCharacter upprepade gånger till slutet av strängen tills den har en längd som är lika med längd.
* padCharacter kan vara ett blank stegs tecken, men det får inte vara ett null-värde.
* Om längden på strängen är lika med eller större än längden returneras strängen oförändrad.
* Om strängen har en längd som är större än eller lika med längd returneras en sträng som är identisk med en sträng.
* Om längden på strängen är mindre än längd returneras en ny sträng med den önskade längden som innehåller strängen utfyllnad med en padCharacter.
* Om strängen är null returnerar funktionen en tom sträng.

**Exempel:**  
`PadRight("User", 10, "0")`  
Returnerar "User000000".

---
### <a name="pcase"></a>PCase
**Beskrivning:**  
Funktionen PCase konverterar det första tecknet i varje blankstegsavgränsad ord i en sträng till versaler, och alla andra tecken konverteras till gemener.

**Uttryck**  
`String PCase(string)`

**!**

* Den här funktionen ger för närvarande inte rätt Skift läge för att konvertera ett ord som är helt versalt, till exempel en akronym.

**Exempel:**  
`PCase("TEsT")`  
Returnerar "test".

`PCase(LCase("TEST"))`  
Returnerar "test"

---
### <a name="randomnum"></a>RandomNum
**Beskrivning:**  
Funktionen RandomNum returnerar ett slumpmässigt tal mellan ett angivet intervall.

**Uttryck**  
`num RandomNum(num start, num end)`

* Start: ett tal som identifierar den nedre gränsen för det slumpmässiga värdet som ska genereras
* End: ett tal som identifierar den övre gränsen för det slumpmässiga värdet som ska genereras

**Exempel:**  
`Random(100,999)`  
Kan returnera 734.

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
**Beskrivning:**  
Funktionen REPLACE ersätter alla förekomster av en sträng med en annan sträng.

**Uttryck**  
`str Replace(str string, str OldValue, str NewValue)`

* sträng: en sträng som ska ersätta värden i.
* OldValue: strängen att söka efter och ersätta.
* NewValue: strängen som ska ersättas.

**!**  
Funktionen identifierar följande särskilda monikers:

* \n – ny rad
* \r – vagn retur
* \t – Tab

**Exempel:**  
`Replace([address],"\r\n",", ")`  
Ersätter CRLF med kommatecken och blank steg och kan leda till "One Microsoft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>ReplaceChars
**Beskrivning:**  
Funktionen ReplaceChars ersätter alla förekomster av tecken som finns i ReplacePattern-strängen.

**Uttryck**  
`str ReplaceChars(str string, str ReplacePattern)`

* sträng: en sträng som ska ersätta tecken i.
* ReplacePattern: en sträng som innehåller en ord lista med tecken som ska ersättas.

Formatet är {source1}: {target1}, {SOURCE2}: {TARGET2}, {source '}, {targetn} där källa är det tecken som ska hittas och som är mål strängen som ska ersättas med.

**!**

* Funktionen tar varje förekomst av definierade källor och ersätter dem med målen.
* Källan måste vara exakt ett (Unicode) tecken.
* Källan får inte vara tom eller längre än ett Character (parsningsfel).
* Målet kan ha flera tecken, till exempel ö: OE, β: SS.
* Målet kan vara tomt som anger att specialtecknet ska tas bort.
* Källan är Skift läges känslig och måste vara en exakt matchning.
* , (Komma) och: (kolon) är reserverade tecken och kan inte ersättas med den här funktionen.
* Blank steg och andra vita tecken i ReplacePattern-strängen ignoreras.

**Exempel:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Returnerar Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Returnerar "ONeil" och det enskilda skalet definieras som ska tas bort.

---
### <a name="right"></a>Höger
**Beskrivning:**  
Funktionen Right returnerar ett angivet antal tecken från höger (End) i en sträng.

**Uttryck**  
`str Right(str string, num NumChars)`

* sträng: strängen att returnera tecken från
* NumChars: ett tal som identifierar antalet tecken som ska returneras från slutet (till höger) av sträng

**!**  
NumChars-tecken returneras från den sista positionen i strängen.

En sträng som innehåller de sista numChars tecknen i strängen:

* Om numChars = 0 returneras en tom sträng.
* Om numChars < 0, returnerar du Indatasträngen.
* Om strängen är null returneras en tom sträng.

Om strängen innehåller färre tecken än det tal som anges i NumChars returneras en sträng som är identisk med en sträng.

**Exempel:**  
`Right("John Doe", 3)`  
Returnerar "berg".

---
### <a name="rtrim"></a>RTrim
**Beskrivning:**  
Funktionen RTrim tar bort avslutande blank steg från en sträng.

**Uttryck**  
`str RTrim(str value)`

**Exempel:**  
`RTrim(" Test ")`  
Returnerar "test".

---
### <a name="select"></a>Välj
**Beskrivning:**  
Bearbeta alla värden i ett multi-Value-attribut (eller utdata från ett uttryck) baserat på angiven funktion.

**Uttryck**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* objekt: representerar ett element i multi-Value-attributet
* attribut: multi-Value-attribut
* uttryck: ett uttryck som returnerar en mängd värden
* villkor: alla funktioner som kan bearbeta ett objekt i attributet

**Exempel:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Returnera alla värden i otherPhone efter bindestreck (-) som har tagits bort.

---
### <a name="split"></a>Dela
**Beskrivning:**  
Funktionen Split tar en sträng separerad med en avgränsare och gör den till en sträng med flera värden.

**Uttryck**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* värde: strängen med ett avgränsnings tecken att separera.
* avgränsare: ett enskilt tecken som ska användas som avgränsare.
* gräns: högsta antal värden som kan returneras.

**Exempel:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Returnerar en multi-Value-sträng med 2 element som är användbara för attributet proxyAddress.

---
### <a name="stringfromguid"></a>StringFromGuid
**Beskrivning:**  
Funktionen StringFromGuid tar ett binärt GUID och konverterar den till en sträng

**Uttryck**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Beskrivning:**  
Funktionen StringFromSid konverterar en byte mat ris som innehåller en säkerhets identifierare till en sträng.

**Uttryck**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Switch
**Beskrivning:**  
Funktionen switch används för att returnera ett enskilt värde baserat på utvärderade villkor.

**Uttryck**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* uttryck: det variant-uttryck som du vill utvärdera.
* värde: värdet som ska returneras om motsvarande uttryck är sant.

**!**  
Argument listan för Switch-funktionen består av par med uttryck och värden. Uttrycken utvärderas från vänster till höger och värdet som associeras med det första uttrycket som ska utvärderas till True returneras. Om delarna inte är korrekt kopplade uppstår ett körnings fel.

Om till exempel Uttr1 är sant returnerar switch value1. Om expr-1 är falskt, men uttr-2 är true, returnerar switch värdet-2 och så vidare.

Växeln returnerar ett Nothing om:

* Inget av uttrycken är sant.
* Det första sanna uttrycket har ett motsvarande värde som är null.

Växeln utvärderar alla uttryck, även om den bara returnerar en av dem. Av den anledningen bör du se efter om det finns oönskade sido effekter. Om exempelvis utvärderingen av ett uttryck resulterar i en division med noll uppstår ett fel.

Värdet kan också vara fel funktionen, som returnerar en anpassad sträng.

**Exempel:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Returnerar det språk som talas i några större städer, annars returnerar ett fel.

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
### <a name="ucase"></a>UCase
**Beskrivning:**  
Funktionen UCase konverterar alla tecken i en sträng till versaler.

**Uttryck**  
`str UCase(str string)`

**Exempel:**  
`UCase("TeSt")`  
Returnerar "TEST".

---
### <a name="where"></a>Var

**Beskrivning:**  
Returnerar en delmängd av värden från ett multi-Value-attribut (eller utdata av ett uttryck) baserat på ett angivet villkor.

**Uttryck**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* objekt: representerar ett element i multi-Value-attributet
* attribut: multi-Value-attribut
* villkor: alla uttryck som kan utvärderas till true eller false
* uttryck: ett uttryck som returnerar en mängd värden

**Exempel:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Returnera certifikat värden i multi-Value-attributet userCertificate som inte har upphört att gälla.

---
### <a name="with"></a>With
**Beskrivning:**  
With-funktionen är ett sätt att förenkla ett komplext uttryck genom att använda en variabel som representerar en under uttryck som visas en eller flera gånger i det komplexa uttrycket.

**Uttryck**
`With(var variable, exp subExpression, exp complexExpression)`  
* variabel: representerar under uttryck.
* under uttryck: under uttryck representeras av variabeln.
* complexExpression: ett komplext uttryck.

**Exempel:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Fungerar som likvärdigt med:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Som endast returnerar inaktuella certifikat värden i attributet userCertificate.


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

## <a name="additional-resources"></a>Ytterligare resurser
* [Förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect synkronisering: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
