---
title: Referens för Azure Sentinel UEBA-anrikning | Microsoft Docs
description: I den här artikeln visas de entiteter som genereras av Azure Sentinel-enhetens beteende analys.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901792"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Referens för Azure Sentinel UEBA-berikningar

De här tabellerna listar och beskriver enhets beteckningar som kan användas för att fokusera och skärpa din undersökning av säkerhets incidenter.

De första två tabellerna, **användar insikter** och **enhets insikter**, innehåller entitetsinformation från Active Directory/Azure AD-och Microsoft Threat Intelligence-källor.

<a name="baseline-explained"></a>Resten av tabellerna, under **aktivitets insikter-tabeller**, innehåller information om entiteten baserat på de beteende profiler som skapats av Azure Sentinel-enhetens beteende analys. Aktiviteterna analyseras mot en bas linje som kompileras dynamiskt varje gång den används. Varje aktivitet har en definierad lookback-period som denna dynamiska bas linje härleds från. Den här perioden anges i kolumnen [**bas linje**](#activity-insights-tables) i den här tabellen.

> [!NOTE] 
> Fältet för **anriknings namn** i alla tre tabeller visar två rader med information. Det första, i **fetstil**, är det "användarvänliga namnet" för anrikningen. Den andra *(i kursiv stil och parenteser)* är fält namnet för berikningen som lagras i [**beteende Analytics-tabellen**](identify-threats-with-entity-behavior-analytics.md#data-schema).

## <a name="user-insights-table"></a>Användar insikts tabell

| Namn på anrikning | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Kontots visnings namn**<br>*(AccountDisplayName)* | Kontots visnings namn för användaren. | Admin, Hayden Cook |
| **Konto domän**<br>*(AccountDomain)* | Konto domän namnet för användaren. |  |
| **Konto objekt-ID**<br>*(AccountObjectID)* | Användarens konto objekt-ID. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Mas radie**<br>*(BlastRadius)* | Den förstärkta radien beräknas utifrån flera faktorer: användarens position i organisations trädet och användarens Azure Active Directory roller och behörigheter. | Låg, medel, hög |
| **Är inaktiva konto**<br>*(IsDormantAccount)* | Kontot har inte använts under de senaste 180 dagarna. | Sant, falskt |
| **Är lokal administratör**<br>*(IsLocalAdmin)* | Kontot har lokal administratörs behörighet. | Sant, falskt |
| **Är nytt konto**<br>*(IsNewAccount)* | Kontot har skapats under de senaste 30 dagarna. | Sant, falskt |
| **Lokalt SID**<br>*(OnPremisesSID)* | Lokalt SID för användaren som är relaterad till åtgärden. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Enhets insikts tabell

| Namn på anrikning | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Webbläsare**<br>*Webbläsare* | Webbläsaren som används i åtgärden. | Edge, Chrome |
| **Enhetsfamilj**<br>*(DeviceFamily)* | Enhets serien som används i åtgärden. | Windows |
| **Enhetstyp**<br>*DeviceType* | Den klient enhets typ som används i åtgärden | Skrivbord |
| **HOS**<br>*HOS* | Internet tjänst leverantören som används i åtgärden. |  |
| **Operativsystem**<br>*Opera ting system* | Det operativ system som används i åtgärden. | Windows 10 |
| **Beskrivning av hot-Intel-indikator**<br>*(ThreatIntelIndicatorDescription)* | Beskrivning av den observerade hot indikatorn matchade från den IP-adress som användes i åtgärden. | Värden är medlem i botnät: azorult |
| **Typ av hot-Intel-indikator**<br>*(ThreatIntelIndicatorType)* | Typen av hot indikator matchad från den IP-adress som användes i åtgärden. | Botnät, C2, CryptoMining, Darknet, DDoS, MaliciousUrl, malware, phishing, proxy, oönskade program, visnings lista |
| **Användar agent**<br>*UserAgent* | Användar agenten som används i åtgärden. | Microsoft Azure Graph-klient bibliotek 1,0,<br>Swagger-CODEGEN/1.4.0.0/csharp,<br>EvoSTS |
| **Användar agent serie**<br>*(UserAgentFamily)* | Användar agent serien som används i åtgärden. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Aktivitets insikter-tabeller

#### <a name="action-performed"></a>Utförd åtgärd

| Namn på anrikning | [Bas linje](#baseline-explained) (dagar) | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Första gången användaren utförde åtgärden**<br>*(FirstTimeUserPerformedAction)* | 180 | Åtgärden utfördes för första gången av användaren. | Sant, falskt |
| **Åtgärden utfördes ovanligt av användaren**<br>*(ActionUncommonlyPerformedByUser)* | 10 | Åtgärden utförs vanligt vis inte av användaren. | Sant, falskt |
| **Åtgärden utfördes ovanligt mellan peer-datorer**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | Åtgärden utförs vanligt vis inte bland användarens motparter. | Sant, falskt |
| **Första gången en åtgärd utfördes i klient organisationen**<br>*(FirstTimeActionPerformedInTenant)* | 180 | Åtgärden utfördes för första gången av någon i organisationen. | Sant, falskt |
| **Åtgärden utfördes på ett ovanligt sätt i klienten**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | Åtgärden utförs vanligt vis inte i organisationen. | Sant, falskt |
|

#### <a name="app-used"></a>App som används

| Namn på anrikning | [Bas linje](#baseline-explained) (dagar) | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Första gången användaren använde appen**<br>*(FirstTimeUserUsedApp)* | 180 | Appen användes för första gången av användaren. | Sant, falskt |
| **Appen är ovanligt Använd av användaren**<br>*(AppUncommonlyUsedByUser)* | 10 | Appen används vanligt vis inte av användaren. | Sant, falskt |
| **Appen är ovanligt att användas bland peer-datorer**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | Appen används vanligt vis inte bland användarens motparter. | Sant, falskt |
| **Första gången som appen observerats i klient organisationen**<br>*(FirstTimeAppObservedInTenant)* | 180 | Appen observerades för första gången i organisationen. | Sant, falskt |
| **Appen har inte gemensamt använts i klient organisationen**<br>*(AppUncommonlyUsedInTenant)* | 180 | Appen används vanligt vis inte i organisationen. | Sant, falskt |
| 

#### <a name="browser-used"></a>Webbläsare som används

| Namn på anrikning | [Bas linje](#baseline-explained) (dagar) | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Första gången användaren anslöt via webbläsaren**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | Webbläsaren observerades för första gången av användaren. | Sant, falskt |
| **Webbläsaren är ovanligt Använd av användaren**<br>*(BrowserUncommonlyUsedByUser)* | 10 | Webbläsaren används vanligt vis inte av användaren. | Sant, falskt |
| **Webbläsare som inte används gemensamt mellan peer-datorer**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | Webbläsaren används inte ofta bland användarens peer-datorer. | Sant, falskt |
| **Första gången som observerats i klient organisationen**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | Webbläsaren observerades för första gången i organisationen. | Sant, falskt |
| **Webbläsare som inte används i klient organisationen**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | Webbläsaren används vanligt vis inte i organisationen. | Sant, falskt |
| 

#### <a name="country-connected-from"></a>Land anslutet från

| Namn på anrikning | [Bas linje](#baseline-explained) (dagar) | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Första gången användaren anslöt från land**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | Geo-platsen, som löst från IP-adressen, var ansluten från för första gången av användaren. | Sant, falskt |
| **Land som inte är gemensamt anslutet av användaren**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | Den geografiska platsen, som löst från IP-adressen, är vanligt vis inte ansluten till användaren. | Sant, falskt |
| **Land som inte är gemensamt ansluten mellan peer-datorer**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | Den geografiska platsen, matchad från IP-adressen, är inte vanligt vis ansluten mellan användarens motparter. | Sant, falskt |
| **Första gången anslutningen från det land som observerats i klient organisationen**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | Landet anslöts första gången av någon i organisationen. | Sant, falskt |
| **Landet är ovanligt anslutet från i klient organisationen**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | Den geografiska platsen, som löst från IP-adressen, är inte vanligt vis ansluten från i organisationen. | Sant, falskt |
| 

#### <a name="device-used-to-connect"></a>Enhet som används för att ansluta

| Namn på anrikning | [Bas linje](#baseline-explained) (dagar) | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Första gången användaren anslöt från enheten**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | Käll enheten var ansluten från för första gången av användaren. | Sant, falskt |
| **Enheten används ovanligt av användaren**<br>*(DeviceUncommonlyUsedByUser)* | 10 | Enheten används vanligt vis inte av användaren. | Sant, falskt |
| **Enheten används ovanligt mellan peer-datorer**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | Enheten används vanligt vis inte bland användarens motparter. | Sant, falskt |
| **Första gången enheten observerades i klient organisationen**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | Enheten observerades för första gången i organisationen. | Sant, falskt |
| **Enheten används ovanligt i klient organisationen**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | Enheten används vanligt vis inte i organisationen. | Sant, falskt |
| 

#### <a name="other-device-related"></a>Annan enhets relaterad

| Namn på anrikning | [Bas linje](#baseline-explained) (dagar) | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Första gången användaren var inloggad på enheten**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | Mål enheten var ansluten till för första gången av användaren. | Sant, falskt |
| **Enhets serien används ovanligt i klient organisationen**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | Enhets familjen används vanligt vis inte i organisationen. | Sant, falskt |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Internet tjänst leverantör som används för att ansluta

| Namn på anrikning | [Bas linje](#baseline-explained) (dagar) | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Första gången användaren anslöt via Internet leverantören**<br>*(FirstTimeUserConnectedViaISP)* | 30 | Internet leverantören observerades för första gången av användaren. | Sant, falskt |
| **Internet leverantören är ovanligt Använd av användaren**<br>*(ISPUncommonlyUsedByUser)* | 10 | Internet leverantören används vanligt vis inte av användaren. | Sant, falskt |
| **Internet leverantören är ovanligt användning bland peer-datorer**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | Internet leverantören används vanligt vis inte bland användarens motparter. | Sant, falskt |
| **Första gången anslutningen via ISP i klient organisationen**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | Internet leverantören observerades för första gången i organisationen. | Sant, falskt |
| **ISP som inte används i klient organisationen**<br>*(ISPUncommonlyUsedInTenant)* | 30 | Internet leverantören används vanligt vis inte i organisationen. | Sant, falskt |
| 

#### <a name="resource-accessed"></a>Resurs som används

| Namn på anrikning | [Bas linje](#baseline-explained) (dagar) | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Första gången användaren använder resursen**<br>*(FirstTimeUserAccessedResource)* | 180 | Resursen öppnades för första gången av användaren. | Sant, falskt |
| **Resursen är inte gemensam för användare**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | Resursen används inte ofta av användaren. | Sant, falskt |
| **Resursen är inte allmänt tillgänglig mellan peer-datorer**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | Resursen används inte ofta bland användarens motparter. | Sant, falskt |
| **Första gången resursen användes i klient organisationen**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | Resursen öppnades för första gången av någon i organisationen. | Sant, falskt |
| **Resursen är inte allmänt tillgänglig i klient organisationen**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | Resursen används inte ofta i organisationen. | Sant, falskt |
| 

#### <a name="miscellaneous"></a>Övriga farliga ämnen

| Namn på anrikning | [Bas linje](#baseline-explained) (dagar) | Beskrivning | Exempelvärde |
| --- | --- | --- | --- |
| **Senaste åtgärden när användaren utfördes**<br>*(LastTimeUserPerformedAction)* | 180 | Senaste gången användaren utförde samma åtgärd. | <Timestamp> |
| **Liknande åtgärd utfördes inte tidigare**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | Ingen åtgärd i samma resurs leverantör utfördes av användaren. | Sant, falskt |
| **Käll-IP-plats**<br>*(SourceIPLocation)* | *EJ TILLÄMPLIGT* | Det land som åtgärd ATS från Källans IP-adress. | [Surrey, England] |
| **Ovanligt stor mängd åtgärder**<br>*(UncommonHighVolumeOfOperations)* | 7 | En användare utförde en burst av liknande åtgärder inom samma provider | Sant, falskt |
| **Ovanligt antal problem med villkorlig åtkomst i Azure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Ett ovanligt antal användare kunde inte autentiseras på grund av villkorlig åtkomst | Sant, falskt |
| **Ovanligt antal enheter som lagts till**<br>*(UnusualNumberOfDevicesAdded)* | 5 | En användare har lagt till ett ovanligt antal enheter. | Sant, falskt |
| **Ovanligt antal borttagna enheter**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | En användare tog bort ett ovanligt antal enheter. | Sant, falskt |
| **Ovanligt många användare som lagts till i gruppen**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | En användare har lagt till ett ovanligt antal användare i en grupp. | Sant, falskt |
|