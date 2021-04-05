---
title: Regel grupper och regler för boknings system
titleSuffix: Azure Web Application Firewall
description: Den här sidan innehåller information om regel grupper och regler för att öppna brand Väggs paket för webb program.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e2c88091072921f1ca674868e401c34d354418de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746517"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Regel grupper och regler för brand vägg för webb program brand vägg

Application Gateway brand vägg för webbaserade program (WAF) skyddar webb program mot vanliga sårbarheter och sårbarheter. Detta görs genom regler som definieras baserat på OWASP Core-regeln anger 3,1, 3,0 eller 2.2.9. De här reglerna kan inaktive ras på grund av regel. Den här artikeln innehåller de aktuella reglerna och regel uppsättningarna som erbjuds.

## <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway WAF förkonfigureras som standard med datoriserat system-3,0. Men du kan välja att använda datoriserade boknings system 3,1 eller DATORISERAde 2.2.9 i stället. Datoriserade boknings system 3,1 erbjuder nya regel uppsättningar som skyddar mot Java-infektioner, en inledande uppsättning fil överförings kontroller, fasta falska positiva identifieringar med mera. Datoriserade boknings system 3,0 ger minskad falsk positiv påverkan jämfört med datoriserade boknings 2.2.9. Du kan också [Anpassa regler efter dina behov](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> ![Hanterar regler](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF skyddar mot följande webb sårbarheter:

- SQL-injektering-attacker
- Skript angrepp över hela platsen
- Andra vanliga attacker, till exempel kommando inmatning, HTTP-begäran dold, delning av HTTP-svar och inkludering av fjärrfiler
- HTTP-protokollfel
- Avvikelser i HTTP-protokoll, till exempel saknade värd användar agenter och ta emot rubriker
- Robotar, crawlers och skannrar
- Vanliga program konfigurationer (till exempel Apache och IIS)

### <a name="owasp-crs-31"></a>OWASP BOKNINGS SYSTEM 3,1

Boknings system 3,1 innehåller 13 regel grupper som visas i följande tabell. Varje grupp innehåller flera regler, som kan inaktive ras.

> [!NOTE]
> BOKNINGs-3,1 är bara tillgängligt på WAF_v2 SKU.

|Regelgrupp|Beskrivning|
|---|---|
|**[Redovisningsjournalmallar](#general-31)**|Allmän grupp|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Låsnings metoder (placering, korrigering)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Skydda mot port-och miljö skannrar|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Skydda mot protokoll-och kodnings problem|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Skydda mot huvud inmatning, begär dold och uppdelning av svar|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Skydda mot fil-och Sök vägs attacker|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Skydda mot RFI-attacker (Remote File inkludering)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Skydda igen fjärrattacker för körning av kod|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Skydda mot PHP-injektering-attacker|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Skydda mot skript angrepp över hela platsen|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Skydda mot SQL-injektions attacker|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Skydda mot session-bindnings attacker|
|**[BEGÄRAN-944-APPLICATION-ATTACK-SESSION-JAVA](#crs944-31)**|Skydda mot JAVA-attacker|

### <a name="owasp-crs-30"></a>OWASP BOKNINGS SYSTEM 3,0

Boknings system 3,0 innehåller 12 regel grupper som visas i följande tabell. Varje grupp innehåller flera regler, som kan inaktive ras.

|Regelgrupp|Beskrivning|
|---|---|
|**[Redovisningsjournalmallar](#general-30)**|Allmän grupp|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Låsnings metoder (placering, korrigering)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Skydda mot port-och miljö skannrar|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Skydda mot protokoll-och kodnings problem|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Skydda mot huvud inmatning, begär dold och uppdelning av svar|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Skydda mot fil-och Sök vägs attacker|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Skydda mot RFI-attacker (Remote File inkludering)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Skydda igen fjärrattacker för körning av kod|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Skydda mot PHP-injektering-attacker|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Skydda mot skript angrepp över hela platsen|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Skydda mot SQL-injektions attacker|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Skydda mot session-bindnings attacker|

### <a name="owasp-crs-229"></a>OWASP BOKNINGS-2.2.9

Datoriserade boknings 2.2.9 innehåller 10 regel grupper, vilket visas i följande tabell. Varje grupp innehåller flera regler, som kan inaktive ras.

|Regelgrupp|Beskrivning|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Skydda mot protokoll överträdelser (t. ex. ogiltiga tecken eller ett GET med en begär ande text)|
|**[crs_21_protocol_anomalies](#crs21)**|Skydda mot felaktig huvud information|
|**[crs_23_request_limits](#crs23)**|Skydda mot argument eller filer som överskrider begränsningarna|
|**[crs_30_http_policy](#crs30)**|Skydda mot begränsade metoder, sidhuvud och filtyper|
|**[crs_35_bad_robots](#crs35)**|Skydda mot webb robotar och skannrar|
|**[crs_40_generic_attacks](#crs40)**|Skydda mot allmänna attacker (t. ex. sessions-uppdelning, filinkludering och php-inmatning)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Skydda mot SQL-injektions attacker|
|**[crs_41_xss_attacks](#crs41xss)**|Skydda mot skript angrepp över hela platsen|
|**[crs_42_tight_security](#crs42)**|Skydda mot Path-traverss-attacker|
|**[crs_45_trojans](#crs45)**|Skydda mot bakdörr-trojaner|

Följande regel grupper och regler är tillgängliga när du använder brand väggen för webbaserade program på Application Gateway.

# <a name="owasp-31"></a>[OWASP 3,1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a> Regel uppsättningar

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Allmänt</p>

|RuleId|Beskrivning|
|---|---|
|200004|Möjlig icke-matchande multipart-gränser.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Beskrivning|
|---|---|
|911100|Metoden tillåts inte av en princip|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Beskrivning|
|---|---|
|913100|Hittade User-Agent associerad med säkerhets skannern|
|913101|Hittade User-Agent kopplade till skript/allmän HTTP-klient|
|913102|Hittade User-Agent associerad med Web Crawler/bot|
|913110|Huvudet för begäran hittades som är associerat med säkerhets skannern|
|913120|Hittade namn/argument för begäran som är associerat med säkerhets skannern|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Beskrivning|
|---|---|
|920100|Ogiltig rad för HTTP-begäran|
|920120|Försök att kringgå multipart/formulär-data|
|920121|Försök att kringgå multipart/formulär-data|
|920130|Det gick inte att parsa begär ande texten.|
|920140|Text för multipart-begäran kunde inte strikt verifieras|
|920160|HTTP-huvudet Content-Length är inte numeriskt.|
|920170|Hämta eller HEAD-begäran med bröd text innehåll.|
|920171|GET-eller HEAD-begäran med överförings kodning.|
|920180|POST-begäran saknar Content-Length-rubrik.|
|920190|Intervall = ogiltigt sista byte-värde.|
|920200|Range = för många fält (6 eller mer)|
|920201|Range = för många fält för PDF-begäran (35 eller mer)|
|920202|Range = för många fält för PDF-begäran (6 eller mer)|
|920210|Flera/motstridiga anslutnings huvud data hittades.|
|920220|URL-kodning missbruk av attack försök|
|920230|Flera URL-kodning har identifierats|
|920240|URL-kodning missbruk av attack försök|
|920250|UTF8-kodning missbruk försök|
|920260|Unicode-angrepp med hel-och halv bredds angrepp|
|920270|Ogiltigt Character i förfrågan (null-Character)|
|920271|Ogiltigt tecken i förfrågan (icke utskrivbara tecken)|
|920272|Ogiltigt tecken i förfrågan (utanför det skrivbara tecken under ASCII 127)|
|920273|Ogiltigt Character i förfrågan (utanför strikt uppsättning)|
|920274|Ogiltigt Character i begärandehuvuden (utanför strikt uppsättning)|
|920280|Begäran saknar ett värd huvud|
|920290|Tomt värd huvud|
|920300|Begäran saknar ett accept-huvud|
|920310|Begäran har ett tomt accept-huvud|
|920311|Begäran har ett tomt accept-huvud|
|920320|Användar agent huvud saknas|
|920330|Tomt användar agent huvud|
|920340|Begäran innehåller innehåll men innehålls typ rubrik saknas|
|920341|Begäran som innehåller innehåll kräver rubrik av innehålls typ|
|920350|Värd huvudet är en numerisk IP-adress|
|920360|Argument namnet är för långt|
|920370|Argument svärdet är för långt|
|920380|För många argument i begäran|
|920390|De totala argument storlekarna har överskridits|
|920400|Den överförda fil storleken är för stor|
|920410|Den totala storleken för överförda filer är för stor|
|920420|Begärans innehålls typ tillåts inte av en princip|
|920430|HTTP-Protokollversionen tillåts inte av en princip|
|920440|URL-filtillägget begränsas av en princip|
|920450|HTTP-huvudet begränsas av principen (% @ {MATCHED_VAR})|
|920460|Onormalt escape-tecken|
|920470|Ogiltigt innehålls typs huvud|
|920480|Begränsa charset-parametern i innehålls typ huvudet|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Beskrivning|
|---|---|
|921110|Dold-attack för HTTP-begäran|
|921120|Uppdelning av HTTP-svar|
|921130|Uppdelning av HTTP-svar|
|921140|Inmatning av HTTP-huvuds angrepp via rubriker|
|921150|Inmatnings attack för HTTP-huvud via nytto Last (CR/LF upptäcktes)|
|921151|Inmatnings attack för HTTP-huvud via nytto Last (CR/LF upptäcktes)|
|921160|Inmatnings attack för HTTP-huvud via nytto Last (CR/LF och rubrik namn upptäckt)|
|921170|Förorening av HTTP-parameter|
|921180|HTTP-parameter nedsmutsning (% {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Beskrivning|
|---|---|
|930100|Sökväg Traversr attacker (/... /)|
|930110|Sökväg Traversr attacker (/... /)|
|930120|Åtkomst försök för operativ system fil|
|930130|Begränsat fil åtkomst försök|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Beskrivning|
|---|---|
|931100|Möjliga RFI-angrepp (Remote File inkludering) = URL-parameter med IP-adress|
|931110|Möjliga RFI-angrepp (Remote File inkludering) = vanliga RFI-känsliga parameter namn som används med URL-nyttolast|
|931120|Möjliga RFI-angrepp (Remote File inkludering) = URL-nyttolast används med avslutande frågetecken (?)|
|931130|Möjliga RFI-angrepp (Remote File inkludering) = Off-Domain referens/länk|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Beskrivning|
|---|---|
|932100|Fjärrkörning av kommando: UNIX-kommando inmatning|
|932105|Fjärrkörning av kommando: UNIX-kommando inmatning|
|932106|Fjärrkörning av kommando: UNIX-kommando inmatning|
|932110|Fjärrkörning av kommando: Windows-kommandorad|
|932115|Fjärrkörning av kommando: Windows-kommandorad|
|932120|Fjärrkommando körning = Windows PowerShell-kommando hittades|
|932130|Fjärrkommando körning = UNIX Shell-uttryck hittades|
|932140|Fjärrkommandon = Windows för/IF-kommandot hittades|
|932150|Fjärrkommandoering: köra kommando körning via UNIX|
|932160|Fjärrkörning av kommando = UNIX-Shell-kod hittades|
|932170|Fjärrkörning av kommando = Shellshock (CVE-2014-6271)|
|932171|Fjärrkörning av kommando = Shellshock (CVE-2014-6271)|
|932180|Begränsat fil överförings försök|
|932190|Fjärrkörning av kommando: kringgå metod för att kringgå jokertecken|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Beskrivning|
|---|---|
|933100|PHP-injektering-attack = inledande/avslutande tagg hittades|
|933110|PHP-injektering-attack = fil uppladdning för PHP-skript hittades|
|933111|PHP-injektering attack: uppladdning av PHP-skriptfilen hittades|
|933120|PHP-injektering-attack = konfigurations direktiv hittades|
|933130|PHP-injektering attack = variabler hittades|
|933131|PHP-injektering attack: variabler hittades|
|933140|PHP-injektering attack: I/O-dataströmmen hittades|
|933150|PHP-injektering-attack = High-Risk PHP-funktions namn hittades|
|933151|PHP-inmatnings attack: Medium-Risk PHP-funktions namn hittades|
|933160|PHP-injektering-attack = High-Risk PHP-funktions anrop hittades|
|933161|PHP-inmatnings attack: Low-Value PHP-funktions anrop hittades|
|933170|PHP-injektering attack: serialiserad objekt inmatning|
|933180|PHP-injektering attack = variabel funktions anrop hittades|
|933190|PHP-injektering: stängnings tag gen för PHP hittades|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Beskrivning|
|---|---|
|941100|XSS-attack identifieras via libinsprutning|
|941101|XSS-attack identifieras via libinsprutning|
|941110|XSS-filter – kategori 1 = skript tagg Vector|
|941130|XSS-filter – kategori 3 = attribut vektor|
|941140|XSS-filter-kategori 4 = JavaScript-URI Vector|
|941150|XSS-filter – kategori 5 = otillåtna HTML-attribut|
|941160|NoScript XSS-InjectionChecker: HTML-inmatning|
|941170|NoScript XSS-InjectionChecker: Attribute insprutning|
|941180|Node-Validator blockeringslistan-nyckelord|
|941190|XSS med formatmallar|
|941200|XSS med VML-ramar|
|941210|XSS med fördunklade Java Script|
|941220|XSS med fördunklade VB-skript|
|941230|XSS med inbäddnings tag gen|
|941240|XSS med attributet import eller implementation|
|941250|IE XSS-filter – attack upptäckt|
|941260|XSS med meta-tagg|
|941270|XSS med "Link" href|
|941280|XSS med taggen Base|
|941290|XSS med taggen "applet"|
|941300|XSS med taggen Object|
|941310|US-ASCII felaktig kodning XSS filter-attack upptäckt.|
|941320|Möjlig XSS-attack upptäckt – HTML-tag-hanterare|
|941330|IE XSS-filter – angrepp identifierat.|
|941340|IE XSS-filter – angrepp identifierat.|
|941350|UTF-7-kodning IE XSS-attack identifierat.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Beskrivning|
|---|---|
|942100|SQL-injektering-attack identifierad via libinsprutning|
|942110|SQL-injektering: testning av vanliga insprutnings test har identifierats|
|942120|SQL-inmatnings attack: SQL-operatorn upptäcktes|
|942130|SQL-inmatnings attack: SQL-Tautology har identifierats.|
|942140|SQL-injektering-attack = gemensamma databas namn har identifierats|
|942150|SQL-injektering|
|942160|Identifierar blinda SQLi-tester med ström spar läge () eller benchmark ().|
|942170|Identifierar SQL-benchmark och vilo läges försök, inklusive villkorliga frågor|
|942180|Identifierar de grundläggande SQL-autentiseringens kringgående-försök 1/3|
|942190|Identifierar körning av MSSQL-kod och informations insamlings försök|
|942200|Identifierar MySQL comment-/Space-Obfuscated-injektioner och avslutnings avslutning|
|942210|Identifierar länkade SQL-injektering-försök 1/2|
|942220|Sökning efter heltals spill attacker tas från Skipfish, förutom 3.0.00738585072|
|942230|Identifierar villkorliga SQL-inmatnings försök|
|942240|Identifierar MySQL charset-växeln och MSSQL-DoS-försök|
|942250|Identifierar matchning mot, SAMMANFOGAr och kör omedelbara inmatningar|
|942251|Identifierar HAVING-injektering|
|942260|Identifierar de grundläggande SQL-autentiseringens kringgående-försök 2/3|
|942270|Söker efter grundläggande SQL-inmatning. Vanlig attack sträng för MySQL Oracle och andra|
|942280|Identifierar postgres pg_sleep insprutning, waitfor Delay-attacker och försök att stänga av databasen|
|942290|Hittar grundläggande MongoDB-försök för SQL-inmatning|
|942300|Identifierar MySQL-kommentarer, villkor och CH (a) r-inmatningar|
|942310|Identifierar länkade SQL-injektering-försök 2/2|
|942320|Identifierar MySQL och PostgreSQL lagrade procedur/funktions inmatning|
|942330|Identifierar klassisk SQL-inmatnings sökning 1/2|
|942340|Identifierar de grundläggande SQL-autentiseringens kringgående-försök 3/3|
|942350|Identifierar MySQL UDF-injektering och andra data-/struktur manipulations försök|
|942360|Identifierar sammanfogad Basic SQL-inmatning och SQLLFI-försök|
|942361|Identifierar grundläggande SQL-inmatning baserat på nyckelordet Alter eller union|
|942370|Identifierar klassisk SQL-inmatnings sökning 2/2|
|942380|SQL-injektering|
|942390|SQL-injektering|
|942400|SQL-injektering|
|942410|SQL-injektering|
|942420|Begränsad identifiering av SQL Character-avvikelse (cookies): # av specialtecken har överskridits (8)|
|942421|Begränsad identifiering av SQL Character-avvikelse (cookies): # av specialtecken har överskridits (3)|
|942430|Begränsad identifiering av SQL Character-avvikelse (argument): # av specialtecken har överskridits (12)|
|942431|Begränsad identifiering av SQL Character-avvikelse (argument): # av specialtecken har överskridits (6)|
|942432|Begränsad identifiering av SQL Character-avvikelse (argument): # av specialtecken har överskridits (2)|
|942440|SQL-kommentaren har identifierats.|
|942450|SQL hex-kodning identifierad|
|942460|Varningar om Meta-Character avvikelse identifiering – upprepade tecken som inte är ord|
|942470|SQL-injektering|
|942480|SQL-injektering|
|942490|Identifierar klassisk SQL-inmatnings sökning 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Beskrivning|
|---|---|
|943100|Möjlig sessions bindnings attack = ange cookie-värden i HTML|
|943110|Möjlig sessions bindnings attack = SessionID-parameter namn med Off-Domain referent|
|943120|Möjlig sessions bindnings attack = SessionID-parameter namn utan referent|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">BEGÄRAN-944-APPLICATION-ATTACK-SESSION-JAVA</p>

|RuleId|Beskrivning|
|---|---|
|944120|Möjlig nytto Last körning och fjärrkörning av kommando|
|944130|Misstänkta Java-klasser|
|944200|Utnyttjande av Java-deserialisering Apache Commons|

# <a name="owasp-30"></a>[OWASP 3,0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a> Regel uppsättningar

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Allmänt</p>

|RuleId|Beskrivning|
|---|---|
|200004|Möjlig icke-matchande multipart-gränser.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Beskrivning|
|---|---|
|911100|Metoden tillåts inte av en princip|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Beskrivning|
|---|---|
|913100|Hittade User-Agent associerad med säkerhets skannern|
|913110|Huvudet för begäran hittades som är associerat med säkerhets skannern|
|913120|Hittade namn/argument för begäran som är associerat med säkerhets skannern|
|913101|Hittade User-Agent kopplade till skript/allmän HTTP-klient|
|913102|Hittade User-Agent associerad med Web Crawler/bot|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Beskrivning|
|---|---|
|920100|Ogiltig rad för HTTP-begäran|
|920130|Det gick inte att parsa begär ande texten.|
|920140|Text för multipart-begäran kunde inte strikt verifieras|
|920160|HTTP-huvudet Content-Length är inte numeriskt.|
|920170|Hämta eller HEAD-begäran med bröd text innehåll.|
|920180|POST-begäran saknar Content-Length-rubrik.|
|920190|Intervall = ogiltigt sista byte-värde.|
|920210|Flera/motstridiga anslutnings huvud data hittades.|
|920220|URL-kodning missbruk av attack försök|
|920240|URL-kodning missbruk av attack försök|
|920250|UTF8-kodning missbruk försök|
|920260|Unicode-angrepp med hel-och halv bredds angrepp|
|920270|Ogiltigt Character i förfrågan (null-Character)|
|920280|Begäran saknar ett värd huvud|
|920290|Tomt värd huvud|
|920310|Begäran har ett tomt accept-huvud|
|920311|Begäran har ett tomt accept-huvud|
|920330|Tomt användar agent huvud|
|920340|Begäran innehåller innehåll men innehålls typ rubrik saknas|
|920350|Värd huvudet är en numerisk IP-adress|
|920380|För många argument i begäran|
|920360|Argument namnet är för långt|
|920370|Argument svärdet är för långt|
|920390|De totala argument storlekarna har överskridits|
|920400|Den överförda fil storleken är för stor|
|920410|Den totala storleken för överförda filer är för stor|
|920420|Begärans innehålls typ tillåts inte av en princip|
|920430|HTTP-Protokollversionen tillåts inte av en princip|
|920440|URL-filtillägget begränsas av en princip|
|920450|HTTP-huvudet begränsas av principen (% @ {MATCHED_VAR})|
|920200|Range = för många fält (6 eller mer)|
|920201|Range = för många fält för PDF-begäran (35 eller mer)|
|920230|Flera URL-kodning har identifierats|
|920300|Begäran saknar ett accept-huvud|
|920271|Ogiltigt tecken i förfrågan (icke utskrivbara tecken)|
|920320|Användar agent huvud saknas|
|920272|Ogiltigt tecken i förfrågan (utanför det skrivbara tecken under ASCII 127)|
|920202|Range = för många fält för PDF-begäran (6 eller mer)|
|920273|Ogiltigt Character i förfrågan (utanför strikt uppsättning)|
|920274|Ogiltigt Character i begärandehuvuden (utanför strikt uppsättning)|
|920460|Onormalt escape-tecken|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Beskrivning|
|---|---|
|921100|Dold-attack för HTTP-begäran.|
|921110|Dold-attack för HTTP-begäran|
|921120|Uppdelning av HTTP-svar|
|921130|Uppdelning av HTTP-svar|
|921140|Inmatning av HTTP-huvuds angrepp via rubriker|
|921150|Inmatnings attack för HTTP-huvud via nytto Last (CR/LF upptäcktes)|
|921160|Inmatnings attack för HTTP-huvud via nytto Last (CR/LF och rubrik namn upptäckt)|
|921151|Inmatnings attack för HTTP-huvud via nytto Last (CR/LF upptäcktes)|
|921170|Förorening av HTTP-parameter|
|921180|HTTP-parameter nedsmutsning (% @ {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Beskrivning|
|---|---|
|930100|Sökväg Traversr attacker (/... /)|
|930110|Sökväg Traversr attacker (/... /)|
|930120|Åtkomst försök för operativ system fil|
|930130|Begränsat fil åtkomst försök|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Beskrivning|
|---|---|
|931100|Möjliga RFI-angrepp (Remote File inkludering) = URL-parameter med IP-adress|
|931110|Möjliga RFI-angrepp (Remote File inkludering) = vanliga RFI-känsliga parameter namn som används med URL-nyttolast|
|931120|Möjliga RFI-angrepp (Remote File inkludering) = URL-nyttolast används med avslutande frågetecken (?)|
|931130|Möjliga RFI-angrepp (Remote File inkludering) = Off-Domain referens/länk|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Beskrivning|
|---|---|
|932120|Fjärrkommando körning = Windows PowerShell-kommando hittades|
|932130|Fjärrkommando körning = UNIX Shell-uttryck hittades|
|932140|Fjärrkommandon = Windows för/IF-kommandot hittades|
|932160|Fjärrkörning av kommando = UNIX-Shell-kod hittades|
|932170|Fjärrkörning av kommando = Shellshock (CVE-2014-6271)|
|932171|Fjärrkörning av kommando = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Beskrivning|
|---|---|
|933100|PHP-injektering-attack = inledande/avslutande tagg hittades|
|933110|PHP-injektering-attack = fil uppladdning för PHP-skript hittades|
|933120|PHP-injektering-attack = konfigurations direktiv hittades|
|933130|PHP-injektering attack = variabler hittades|
|933150|PHP-injektering-attack = High-Risk PHP-funktions namn hittades|
|933160|PHP-injektering-attack = High-Risk PHP-funktions anrop hittades|
|933180|PHP-injektering attack = variabel funktions anrop hittades|
|933151|PHP-injektering-attack = Medium-Risk PHP-funktions namn hittades|
|933131|PHP-injektering attack = variabler hittades|
|933161|PHP-injektering-attack = Low-Value PHP-funktions anrop hittades|
|933111|PHP-injektering-attack = fil uppladdning för PHP-skript hittades|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Beskrivning|
|---|---|
|941100|XSS-attack identifieras via libinsprutning|
|941110|XSS-filter – kategori 1 = skript tagg Vector|
|941130|XSS-filter – kategori 3 = attribut vektor|
|941140|XSS-filter-kategori 4 = JavaScript-URI Vector|
|941150|XSS-filter – kategori 5 = otillåtna HTML-attribut|
|941180|Node-Validator blockeringslistan-nyckelord|
|941190|XSS med formatmallar|
|941200|XSS med VML-ramar|
|941210|XSS med fördunklade Java Script|
|941220|XSS med fördunklade VB-skript|
|941230|XSS med inbäddnings tag gen|
|941240|XSS med attributet import eller implementation|
|941260|XSS med meta-tagg|
|941270|XSS med "Link" href|
|941280|XSS med taggen Base|
|941290|XSS med taggen "applet"|
|941300|XSS med taggen Object|
|941310|US-ASCII felaktig kodning XSS filter-attack upptäckt.|
|941330|IE XSS-filter – angrepp identifierat.|
|941340|IE XSS-filter – angrepp identifierat.|
|941350|UTF-7-kodning IE XSS-attack identifierat.|
|941320|Möjlig XSS-attack upptäckt – HTML-tag-hanterare|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Beskrivning|
|---|---|
|942100|SQL-injektering-attack identifierad via libinsprutning|
|942110|SQL-injektering: testning av vanliga insprutnings test har identifierats|
|942130|SQL-inmatnings attack: SQL-Tautology har identifierats.|
|942140|SQL-injektering-attack = gemensamma databas namn har identifierats|
|942160|Identifierar blinda SQLi-tester med ström spar läge () eller benchmark ().|
|942170|Identifierar SQL-benchmark och vilo läges försök, inklusive villkorliga frågor|
|942190|Identifierar körning av MSSQL-kod och informations insamlings försök|
|942200|Identifierar MySQL comment-/Space-Obfuscated-injektioner och avslutnings avslutning|
|942230|Identifierar villkorliga SQL-inmatnings försök|
|942260|Identifierar de grundläggande SQL-autentiseringens kringgående-försök 2/3|
|942270|Söker efter grundläggande SQL-inmatning. Vanlig attack sträng för MySQL Oracle och andra.|
|942290|Hittar grundläggande MongoDB-försök för SQL-inmatning|
|942300|Identifierar MySQL-kommentarer, villkor och CH (a) r-inmatningar|
|942310|Identifierar länkade SQL-injektering-försök 2/2|
|942320|Identifierar MySQL och PostgreSQL lagrade procedur/funktions inmatning|
|942330|Identifierar klassisk SQL-inmatnings sökning 1/2|
|942340|Identifierar de grundläggande SQL-autentiseringens kringgående-försök 3/3|
|942350|Identifierar MySQL UDF-injektering och andra data-/struktur manipulations försök|
|942360|Identifierar sammanfogad Basic SQL-inmatning och SQLLFI-försök|
|942370|Identifierar klassisk SQL-inmatnings sökning 2/2|
|942150|SQL-injektering|
|942410|SQL-injektering|
|942430|Begränsad identifiering av SQL Character-avvikelse (argument): # av specialtecken har överskridits (12)|
|942440|SQL-kommentaren har identifierats.|
|942450|SQL hex-kodning identifierad|
|942251|Identifierar HAVING-injektering|
|942460|Varningar om Meta-Character avvikelse identifiering – upprepade tecken som inte är ord|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Beskrivning|
|---|---|
|943100|Möjlig sessions bindnings attack = ange cookie-värden i HTML|
|943110|Möjlig sessions bindnings attack = SessionID-parameter namn med Off-Domain referent|
|943120|Möjlig sessions bindnings attack = SessionID-parameter namn utan referent|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a> Regel uppsättningar

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|RuleId|Beskrivning|
|---|---|
|960911|Ogiltig rad för HTTP-begäran|
|981227|Apache-fel = ogiltig URI i begäran.|
|960912|Det gick inte att parsa begär ande texten.|
|960914|Text för multipart-begäran kunde inte strikt verifieras|
|960915|Flerdelade parser har identifierat en möjlig omatchad kant.|
|960016|HTTP-huvudet Content-Length är inte numeriskt.|
|960011|Hämta eller HEAD-begäran med bröd text innehåll.|
|960012|POST-begäran saknar Content-Length-rubrik.|
|960902|Ogiltig användning av identitets kodning.|
|960022|Förväntad rubrik tillåts inte för HTTP 1,0.|
|960020|Pragma-sidhuvudet kräver Cache-Control-sidhuvud för HTTP/1.1-begäranden.|
|958291|Range = Field finns och börjar med 0.|
|958230|Intervall = ogiltigt sista byte-värde.|
|958295|Flera/motstridiga anslutnings huvud data hittades.|
|950107|URL-kodning missbruk av attack försök|
|950109|Flera URL-kodning har identifierats|
|950108|URL-kodning missbruk av attack försök|
|950801|UTF8-kodning missbruk försök|
|950116|Unicode-angrepp med hel-och halv bredds angrepp|
|960901|Ogiltigt Character i förfrågan|
|960018|Ogiltigt Character i förfrågan|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Beskrivning|
|---|---|
|960008|Begäran saknar ett värd huvud|
|960007|Tomt värd huvud|
|960015|Begäran saknar ett accept-huvud|
|960021|Begäran har ett tomt accept-huvud|
|960009|Begäran saknar ett användar agent huvud|
|960006|Tomt användar agent huvud|
|960904|Begäran innehåller innehåll men innehålls typ rubrik saknas|
|960017|Värd huvudet är en numerisk IP-adress|

### <a name="crs_23_request_limits"></a><a name="crs23"></a> crs_23_request_limits

|RuleId|Beskrivning|
|---|---|
|960209|Argument namnet är för långt|
|960208|Argument svärdet är för långt|
|960335|För många argument i begäran|
|960341|De totala argument storlekarna har överskridits|
|960342|Den överförda fil storleken är för stor|
|960343|Den totala storleken för överförda filer är för stor|

### <a name="crs_30_http_policy"></a><a name="crs30"></a> crs_30_http_policy

|RuleId|Beskrivning|
|---|---|
|960032|Metoden tillåts inte av en princip|
|960010|Begärans innehålls typ tillåts inte av en princip|
|960034|HTTP-Protokollversionen tillåts inte av en princip|
|960035|URL-filtillägget begränsas av en princip|
|960038|HTTP-huvudet begränsas av en princip|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a> crs_35_bad_robots

|RuleId|Beskrivning|
|---|---|
|990002|Begäran anger att en säkerhets skanner genomsöker platsen|
|990901|Begäran anger att en säkerhets skanner genomsöker platsen|
|990902|Begäran anger att en säkerhets skanner genomsöker platsen|
|990012|Falsk webbplats sökrobot|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

|RuleId|Beskrivning|
|---|---|
|960024|Varningar om Meta-Character avvikelse identifiering – upprepade tecken som inte är ord|
|950008|Inmatning av inte dokumentade ColdFusion-Taggar|
|950010|Attack för LDAP-inmatning|
|950011|SSI-attack|
|950018|Universal PDF XSS URL har identifierats.|
|950019|Attack för e-postinmatning|
|950012|Dold-attack för HTTP-begäran.|
|950910|Uppdelning av HTTP-svar|
|950911|Uppdelning av HTTP-svar|
|950117|Attack för filinkludering|
|950118|Attack för filinkludering|
|950119|Attack för filinkludering|
|950120|Möjliga RFI-angrepp (Remote File inkludering) = Off-Domain referens/länk|
|981133|Regel 981133|
|981134|Regel 981134|
|950009|Sessionens bindnings attack|
|950003|Sessions-bindning|
|950000|Sessions-bindning|
|950005|Fjärranslutna fil åtkomst försök|
|950002|System kommando åtkomst|
|950006|System kommando inmatning|
|959151|Angrepp för PHP-injektering|
|958976|Angrepp för PHP-injektering|
|958977|Angrepp för PHP-injektering|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Beskrivning|
|---|---|
|981231|SQL-kommentaren har identifierats.|
|981260|SQL hex-kodning identifierad|
|981320|SQL-injektering-attack = gemensamma databas namn har identifierats|
|981300|Regel 981300|
|981301|Regel 981301|
|981302|Regel 981302|
|981303|Regel 981303|
|981304|Regel 981304|
|981305|Regel 981305|
|981306|Regel 981306|
|981307|Regel 981307|
|981308|Regel 981308|
|981309|Regel 981309|
|981310|Regel 981310|
|981311|Regel 981311|
|981312|Regel 981312|
|981313|Regel 981313|
|981314|Regel 981314|
|981315|Regel 981315|
|981316|Regel 981316|
|981317|Avisering om avvikelse identifiering i SQL SELECT-instruktion|
|950007|Skadlig SQL insprutning-attack|
|950001|SQL-injektering|
|950908|SQL-inmatning-attack.|
|959073|SQL-injektering|
|981272|Identifierar blinda SQLi-tester med ström spar läge () eller benchmark ().|
|981250|Identifierar SQL-benchmark och vilo läges försök, inklusive villkorliga frågor|
|981241|Identifierar villkorliga SQL-inmatnings försök|
|981276|Söker efter grundläggande SQL-inmatning. Vanlig attack sträng för MySQL Oracle och andra.|
|981270|Hittar grundläggande MongoDB-försök för SQL-inmatning|
|981253|Identifierar MySQL och PostgreSQL lagrade procedur/funktions inmatning|
|981251|Identifierar MySQL UDF-injektering och andra data-/struktur manipulations försök|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Beskrivning|
|---|---|
|973336|XSS-filter – kategori 1 = skript tagg Vector|
|973338|XSS-filter-kategori 3 = JavaScript-URI Vector|
|981136|Regel 981136|
|981018|Regel 981018|
|958016|XSS-attack (Cross-Site Scripting)|
|958414|XSS-attack (Cross-Site Scripting)|
|958032|XSS-attack (Cross-Site Scripting)|
|958026|XSS-attack (Cross-Site Scripting)|
|958027|XSS-attack (Cross-Site Scripting)|
|958054|XSS-attack (Cross-Site Scripting)|
|958418|XSS-attack (Cross-Site Scripting)|
|958034|XSS-attack (Cross-Site Scripting)|
|958019|XSS-attack (Cross-Site Scripting)|
|958013|XSS-attack (Cross-Site Scripting)|
|958408|XSS-attack (Cross-Site Scripting)|
|958012|XSS-attack (Cross-Site Scripting)|
|958423|XSS-attack (Cross-Site Scripting)|
|958002|XSS-attack (Cross-Site Scripting)|
|958017|XSS-attack (Cross-Site Scripting)|
|958007|XSS-attack (Cross-Site Scripting)|
|958047|XSS-attack (Cross-Site Scripting)|
|958410|XSS-attack (Cross-Site Scripting)|
|958415|XSS-attack (Cross-Site Scripting)|
|958022|XSS-attack (Cross-Site Scripting)|
|958405|XSS-attack (Cross-Site Scripting)|
|958419|XSS-attack (Cross-Site Scripting)|
|958028|XSS-attack (Cross-Site Scripting)|
|958057|XSS-attack (Cross-Site Scripting)|
|958031|XSS-attack (Cross-Site Scripting)|
|958006|XSS-attack (Cross-Site Scripting)|
|958033|XSS-attack (Cross-Site Scripting)|
|958038|XSS-attack (Cross-Site Scripting)|
|958409|XSS-attack (Cross-Site Scripting)|
|958001|XSS-attack (Cross-Site Scripting)|
|958005|XSS-attack (Cross-Site Scripting)|
|958404|XSS-attack (Cross-Site Scripting)|
|958023|XSS-attack (Cross-Site Scripting)|
|958010|XSS-attack (Cross-Site Scripting)|
|958411|XSS-attack (Cross-Site Scripting)|
|958422|XSS-attack (Cross-Site Scripting)|
|958036|XSS-attack (Cross-Site Scripting)|
|958000|XSS-attack (Cross-Site Scripting)|
|958018|XSS-attack (Cross-Site Scripting)|
|958406|XSS-attack (Cross-Site Scripting)|
|958040|XSS-attack (Cross-Site Scripting)|
|958052|XSS-attack (Cross-Site Scripting)|
|958037|XSS-attack (Cross-Site Scripting)|
|958049|XSS-attack (Cross-Site Scripting)|
|958030|XSS-attack (Cross-Site Scripting)|
|958041|XSS-attack (Cross-Site Scripting)|
|958416|XSS-attack (Cross-Site Scripting)|
|958024|XSS-attack (Cross-Site Scripting)|
|958059|XSS-attack (Cross-Site Scripting)|
|958417|XSS-attack (Cross-Site Scripting)|
|958020|XSS-attack (Cross-Site Scripting)|
|958045|XSS-attack (Cross-Site Scripting)|
|958004|XSS-attack (Cross-Site Scripting)|
|958421|XSS-attack (Cross-Site Scripting)|
|958009|XSS-attack (Cross-Site Scripting)|
|958025|XSS-attack (Cross-Site Scripting)|
|958413|XSS-attack (Cross-Site Scripting)|
|958051|XSS-attack (Cross-Site Scripting)|
|958420|XSS-attack (Cross-Site Scripting)|
|958407|XSS-attack (Cross-Site Scripting)|
|958056|XSS-attack (Cross-Site Scripting)|
|958011|XSS-attack (Cross-Site Scripting)|
|958412|XSS-attack (Cross-Site Scripting)|
|958008|XSS-attack (Cross-Site Scripting)|
|958046|XSS-attack (Cross-Site Scripting)|
|958039|XSS-attack (Cross-Site Scripting)|
|958003|XSS-attack (Cross-Site Scripting)|
|973300|Möjlig XSS-attack upptäckt – HTML-tag-hanterare|
|973301|XSS-attack identifierad|
|973302|XSS-attack identifierad|
|973303|XSS-attack identifierad|
|973304|XSS-attack identifierad|
|973305|XSS-attack identifierad|
|973306|XSS-attack identifierad|
|973307|XSS-attack identifierad|
|973308|XSS-attack identifierad|
|973309|XSS-attack identifierad|
|973311|XSS-attack identifierad|
|973313|XSS-attack identifierad|
|973314|XSS-attack identifierad|
|973331|IE XSS-filter – angrepp identifierat.|
|973315|IE XSS-filter – angrepp identifierat.|
|973330|IE XSS-filter – angrepp identifierat.|
|973327|IE XSS-filter – angrepp identifierat.|
|973326|IE XSS-filter – angrepp identifierat.|
|973346|IE XSS-filter – angrepp identifierat.|
|973345|IE XSS-filter – angrepp identifierat.|
|973324|IE XSS-filter – angrepp identifierat.|
|973323|IE XSS-filter – angrepp identifierat.|
|973348|IE XSS-filter – angrepp identifierat.|
|973321|IE XSS-filter – angrepp identifierat.|
|973320|IE XSS-filter – angrepp identifierat.|
|973318|IE XSS-filter – angrepp identifierat.|
|973317|IE XSS-filter – angrepp identifierat.|
|973329|IE XSS-filter – angrepp identifierat.|
|973328|IE XSS-filter – angrepp identifierat.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a> crs_42_tight_security

|RuleId|Beskrivning|
|---|---|
|950103|Attack för Sök vägs Traversal|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|RuleId|Beskrivning|
|---|---|
|950110|Bakdörr-åtkomst|
|950921|Bakdörr-åtkomst|
|950922|Bakdörr-åtkomst|

---

## <a name="next-steps"></a>Nästa steg

- [Anpassa brand Väggs regler för webb program med hjälp av Azure Portal](application-gateway-customize-waf-rules-portal.md)
