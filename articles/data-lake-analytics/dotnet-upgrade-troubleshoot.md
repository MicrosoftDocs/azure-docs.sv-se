---
title: Felsöka Azure Data Lake Analytics U-SQL-jobb fel på grund av .NET Framework 4.7.2-uppgradering
description: Felsök fel i U-SQL-jobb på grund av uppgraderingen till .NET Framework 4.7.2.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/11/2019
ms.openlocfilehash: ab03ea8a88187289f5dce55f8a396a9d51346a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217685"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics uppgraderar till .NET Framework v-4.7.2

Azure Data Lake Analytics standard körningen uppgraderas från .NET Framework v 4.5.2 till .NET Framework v-4.7.2. Den här ändringen medför en liten risk för att ändra ändringar om din U-SQL-kod använder anpassade sammansättningar och dessa anpassade sammansättningar använder .NET-bibliotek.

Den här uppgraderingen från .NET Framework 4.5.2 till version 4.7.2 innebär att .NET Framework som distribuerats i en U-SQL-körning (standard körning) alltid är 4.7.2. Det finns inte ett alternativ sida vid sida för .NET Framework versioner.

När uppgraderingen till .NET Framework 4.7.2 har slutförts, kommer systemets hanterade kod att köras som version 4.7.2, användare som har angett bibliotek som U-SQL-anpassade sammansättningar kommer att köras i det bakåtkompatibla läge som är lämpligt för den version som sammansättningen har genererats för.

- Om dina paket-dll: er genereras för version 4.5.2, kommer det distribuerade ramverket att behandla dem som 4.5.2-bibliotek, vilket ger (med några undantag) 4.5.2-semantik.
- Du kan nu använda U-SQL-anpassade sammansättningar som använder version 4.7.2-funktioner, om du riktar in .NET Framework 4.7.2.

På grund av den här uppgraderingen till .NET Framework 4.7.2 är det möjligt att införa ändringar i dina U-SQL-jobb som använder anpassade .NET-sammansättningar. Vi rekommenderar att du söker efter bakåtkompatibla problem med proceduren nedan.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Så här söker du efter bakåtkompatibla problem

Sök efter potentiella problem med bakåtkompatibilitet genom att köra .NET-kompatibilitetskontrollen på din .NET-kod i dina U-SQL-anpassade sammansättningar.

> [!Note]
> Verktyget identifierar inte faktiska ändringar. Det identifierar bara anropade .NET-API: er som kan (för vissa indata) orsaka problem. Om du får ett meddelande om ett problem kan din kod ändå vara bra, men du bör kontrol lera mer information.

1. Kör bakåtkompatibla kompatibilitetskontroll på dina .NET-DLL-filer antingen av
   1. Använda Visual Studio-tillägget i [.net-ports Analyzer Visual Studio-tillägget](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Hämta och använda det fristående verktyget från [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Instruktioner för att köra ett fristående verktyg finns på [GitHub dotnetapiport-bryta ändringar](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. För 4.7.2. kompatibilitet, `read isRetargeting == True` identifierar möjliga problem.
2. Om-verktyget anger om din kod kan påverkas av eventuella bakåtkompatibla inkompatibiliteter (några vanliga exempel på inkompatibiliteter), kan du ytterligare kontrol lera efter
   1. Analysera koden och identifiera om din kod skickar värden till de påverkade API: erna
   1. Utför en körnings kontroll. Körnings distributionen utfördes inte sida vid sida i ADLA. Du kan utföra en körnings kontroll före uppgraderingen genom att använda VisualStudio lokala kör med en lokal .NET Framework-4.7.2 mot en representativ data uppsättning.
3. Om du faktiskt påverkas av en bakåtkompatibla inkompatibilitet vidtar du nödvändiga åtgärder för att åtgärda det (till exempel att korrigera data eller kod logik).

I de flesta fall bör du inte påverkas av bakåtkompatibla inkompatibilitet.

## <a name="timeline"></a>Tidslinje

Du kan söka efter distributionen av den nya körnings miljön [här,](runtime-troubleshoot.md)och genom att titta på ett tidigare slutfört jobb.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Vad händer om jag inte kan få min kod granskad i tid

Du kan skicka jobbet till den gamla körnings versionen (som är byggd som mål för 4.5.2), men på grund av bristen på .NET Framework sida vid sida-funktioner kommer den fortfarande bara att köras i 4.5.2-kompatibelt läge. Du kanske fortfarande stöter på några bakåtkompatibla problem på grund av det här problemet.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Vilka är de vanligaste bakåtkompatibla problemen som kan uppstå

De vanligaste baklänges-inkompatibiliteterna som granskaren är troligt vis att identifiera är (vi har genererat den här listan genom att köra kontrollen på våra egna interna ADLA-jobb), vilka bibliotek som påverkas (Obs: att du endast kan anropa biblioteken indirekt, så det är viktigt att vidta nödvändiga åtgärder #1 för att kontrol lera om dina jobb påverkas) och möjliga åtgärder att åtgärda. Obs! i nästan alla fall för våra egna jobb är varningarna inaktiverade för att vara falska positiva identifieringar på grund av smala egenskaper för de flesta större ändringar.

- IAsyncResult. CompletedSynchronously-egenskapen måste vara korrekt för att den resulterande uppgiften ska slutföras
  - När du anropar TaskFactory. FromAsync måste implementeringen av egenskapen IAsyncResult. CompletedSynchronously vara korrekt för att den resulterande uppgiften ska slutföras. Det vill säga egenskapen måste returnera True om, och endast om, slutfördes synkront. Tidigare kontrollerades inte egenskapen.
  - Påverkade bibliotek: mscorlib, system. Threading. tasks
  - Föreslagen åtgärd: kontrol lera att TaskFactory. FromAsync returnerar korrekt värde

- DataObject. GetData hämtar nu data som UTF-8
  - För appar som är riktade till .NET Framework 4 eller som körs i .NET Framework 4.5.1 eller tidigare versioner hämtar DataObject. GetData HTML-formaterad data som en ASCII-sträng. Det innebär att icke-ASCII-tecken (tecken vars ASCII-koder är större än 0x7F) representeras av två slumpmässiga tecken. #N # #N#For appar som är riktade till .NET Framework 4,5 eller senare och som körs på .NET Framework 4.5.2 `DataObject.GetData` hämtar HTML-formaterade data som UTF-8, vilket motsvarar tecken som är större än 0x7f.
  - Påverkade bibliotek: Glo
  - Föreslagen åtgärd: se till att data hämtas är det format som du vill använda

- XmlWriter returnerar ogiltiga surrogat par
  - För appar som är riktade till .NET Framework 4.5.2 eller tidigare versioner skriver ett ogiltigt surrogat par med undantags reserv hantering inte alltid ett undantag. För appar som är riktade till .NET Framework 4,6-försök att skriva ett ogiltigt surrogat-par genereras ett `ArgumentException` .
  - Bibliotek som påverkas: System.Xml System.Xml. ReaderWriter
  - Föreslagen åtgärd: se till att du inte skriver ett ogiltigt surrogat par som orsakar argument undantag

- HtmlTextWriter återger inte `<br/>` element korrekt
  - Från och med .NET Framework 4,6, anropa `HtmlTextWriter.RenderBeginTag()` och `HtmlTextWriter.RenderEndTag()` med ett `<BR />` element, infogar bara ett `<BR />` (i stället för två)
  - Påverkade bibliotek: system. Web
  - Föreslagen åtgärd: se till att du infogar den mängd `<BR />` du förväntar dig att se så att inget slumpmässigt beteende visas i produktions jobbet

- Anrop av CreateDefaultAuthorizationContext med ett null-argument har ändrats
  - Implementeringen av den AuthorizationContext som returnerades av ett anrop till `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` med ett null authorizationPolicies-argument har ändrat implementeringen i .NET Framework 4,6.
  - Påverkade bibliotek: system. IdentityModel
  - Föreslagen åtgärd: se till att du hanterar det nya förväntade beteendet när det finns en auktoriseringsprincip
  
- Nu läser RSACng RSA-nycklar med en nyckel storlek som inte är standard
  - I .NET Framework versioner före 4.6.2 kan kunder med icke-standardnyckel storlekar för RSA-certifikat inte komma åt nycklarna via `GetRSAPublicKey()` `GetRSAPrivateKey()` metoderna och. A `CryptographicException` med meddelandet "den begärda nyckel storleken stöds inte" har utlösts. Det här problemet har åtgärd ATS med .NET Framework 4.6.2. På samma sätt `RSA.ImportParameters()` `RSACng.ImportParameters()` fungerar nu med nyckel storlekar som inte är standard utan att det går att kasta `CryptographicException` .
  - Påverkade bibliotek: mscorlib, system. Core
  - Föreslagen åtgärd: se till att RSA-nycklar fungerar som förväntat

- Kontrollen av sökvägs kolon är strängare
  - I .NET Framework 4.6.2 har ett antal ändringar gjorts för att stödja tidigare sökvägar som inte stöds (både i längd och format). Sökning efter korrekt enhets avgränsare (kolon) har gjorts mer korrekt, vilket hade en sido effekt på att blockera vissa URI-sökvägar i några utvalda Sök vägs-API: er där de använde för att tolereras.
  - Påverkade bibliotek: mscorlib, system. Runtime. Extensions
  - Föreslagen åtgärd:

- Anrop till ClaimsIdentity-konstruktorer
  - Från och med .NET Framework 4.6.2, finns det en ändring i hur `T:System.Security.Claims.ClaimsIdentity` konstruktorer med en `T:System.Security.Principal.IIdentity` parameter anger `P:System.Security.Claims.ClaimsIdentify.Actor` egenskapen. Om `T:System.Security.Principal.IIdentity` argumentet är ett `T:System.Security.Claims.ClaimsIdentity` objekt och `P:System.Security.Claims.ClaimsIdentify.Actor` egenskapen för `T:System.Security.Claims.ClaimsIdentity` objektet inte är `null` , `P:System.Security.Claims.ClaimsIdentify.Actor` bifogas egenskapen med hjälp av- `M:System.Security.Claims.ClaimsIdentity.Clone` metoden. I ramverket 4.6.1 och tidigare versioner `P:System.Security.Claims.ClaimsIdentify.Actor` bifogas egenskapen som en befintlig referens. På grund av den här ändringen, från och med .NET Framework 4.6.2, `P:System.Security.Claims.ClaimsIdentify.Actor` är egenskapen för det nya `T:System.Security.Claims.ClaimsIdentity` objektet inte lika med `P:System.Security.Claims.ClaimsIdentify.Actor` egenskapen för konstruktorns `T:System.Security.Principal.IIdentity` argument. I .NET Framework 4.6.1 och tidigare versioner är det lika.
  - Påverkade bibliotek: mscorlib
  - Föreslagen åtgärd: kontrol lera att ClaimsIdentity fungerar som förväntat på ny körning

- Serialisering av kontroll tecken med DataContractJsonSerializer är nu kompatibel med ECMAScript V6 och V8
  - I .NET Framework 4.6.2 och tidigare versioner serialiserade DataContractJsonSerializer inte några särskilda kontroll tecken, till exempel \b, \f och \t, på ett sätt som var kompatibelt med ECMAScript V6-och V8-standarden. Från och med .NET Framework 4,7 är serialiseringen av dessa kontroll tecken kompatibel med ECMAScript V6 och V8.
  - Påverkade bibliotek: System.Runtime.Serialization.Jspå
  - Föreslagen åtgärd: kontrol lera att samma beteende med DataContractJsonSerializer
