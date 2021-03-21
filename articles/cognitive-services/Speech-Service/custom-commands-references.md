---
title: Koncept och definitioner för anpassade kommandon – tal tjänst
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig mer om begrepp och definitioner för program med anpassade kommandon.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 98510132b2341736664dfafa52e9567df95652be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94561105"
---
# <a name="custom-commands-concepts-and-definitions"></a>Koncept och definitioner för anpassade kommandon

Den här artikeln fungerar som en referens för begrepp och definitioner för program med anpassade kommandon.

## <a name="commands-configuration"></a>Konfiguration av kommandon
Kommandon är de grundläggande Bygg stenarna i ett program för anpassade kommandon. Ett kommando är en uppsättning konfigurationer som krävs för att slutföra en viss uppgift som definierats av en användare.

### <a name="example-sentences"></a>Exempel på meningar
Exempel på yttranden är de uppsättnings exempel som användaren kan säga för att utlösa ett visst kommando. Du behöver bara ange ett exempel på yttranden och inte en fullständig förteckning. 

### <a name="parameters"></a>Parametrar
Parametrar är information som krävs av kommandona för att slutföra en uppgift. I komplexa scenarier kan parametrar också användas för att definiera villkor som utlöser anpassade åtgärder.

### <a name="completion-rules"></a>Regler för slut för ande
Regler för slut för ande är en serie regler som ska utföras när kommandot är klart att uppfyllas, till exempel när alla villkor i reglerna är uppfyllda.

### <a name="interaction-rules"></a>Interaktions regler
Interaktions regler är ytterligare regler för att hantera mer detaljerade eller komplexa situationer. Du kan lägga till ytterligare verifieringar eller konfigurera avancerade funktioner, till exempel bekräftelser eller en korrigering i ett steg. Du kan också bygga egna anpassade interaktions regler.

## <a name="parameters-configuration"></a>Parameter konfiguration

Parametrar är information som krävs av kommandon för att slutföra en uppgift. I komplexa scenarier kan parametrar också användas för att definiera villkor som utlöser anpassade åtgärder.

### <a name="name"></a>Name
En parameter identifieras av egenskapen Name. Du bör alltid ge ett beskrivande namn till en parameter. En parameter kan refereras mellan olika avsnitt, till exempel när du skapar villkor, tal svar eller andra åtgärder.
 
### <a name="isglobal"></a>IsGlobal
Den här kryss rutan anger om omfånget för den här parametern delas mellan alla kommandon i programmet. Om en parameter är global kan dess värde tillhandahållas från alla kommando områden. När ett värde har tilldelats kan det refereras från något av kommandona. 

### <a name="required"></a>Obligatorisk
Den här kryss rutan anger om ett värde för den här parametern krävs för kommando uppfyllelse eller slut för ande. Du måste konfigurera svar för att användaren ska kunna ange ett värde om en parameter har marker ATS som obligatorisk.

Observera att om du har konfigurerat en **nödvändig parameter** till att ha ett **Standardvärde** kommer systemet fortfarande att fråga efter parameterns värde.

### <a name="type"></a>Typ
Anpassade kommandon stöder följande parameter typer:

* DateTime
* Geografi
* Antal
* Sträng

Alla dessa parameter typer förutom geografi, stöder standardvärdes konfiguration, som du kan konfigurera från portalen.

### <a name="configuration"></a>Konfiguration
Konfigurationen är en parameter egenskap som endast definierats för typ strängen. Följande värden stöds:

* **Ingen**.
* **Acceptera fullständig indatamängd**: när den är aktive rad accepterar en parameter alla inuttryck. Det här alternativet är användbart när användaren behöver en parameter med fullständig uttryck. Ett exempel är post adresser.
* **Acceptera fördefinierade indatavärden från en extern katalog**: det här värdet används för att konfigurera en parameter som kan anta en mängd olika värden. Ett exempel är en försäljnings katalog. I detta fall finns katalogen på en extern webb slut punkt och kan konfigureras separat.
* **Acceptera fördefinierade indatavärden från intern katalog**: det här värdet används för att konfigurera en parameter som kan anta några värden. I det här fallet måste värdena konfigureras i tal Studio.


### <a name="validation"></a>Validering
Valideringar är tillämpliga för vissa parameter typer som låter dig konfigurera begränsningar för en Parameters värde. För närvarande stöder anpassade kommandon valideringar av följande parameter typer:

* DateTime
* Antal

## <a name="rules-configuration"></a>Regel konfiguration
En regel i anpassade kommandon definieras av en uppsättning *villkor* som, när de är uppfyllda, kör en uppsättning *åtgärder*. Med regler kan du också konfigurera *efter körnings tillstånd* och *förväntningar* för nästa turn.

### <a name="types"></a>Typer
Anpassade kommandon stöder följande regel kategorier:

* **Regler för slut för ande**: de här reglerna måste utföras när kommandot har slutförts. Alla regler som kon figurer ATS i det här avsnittet för vilka villkoren är sanna, kommer att köras. 
* **Interaktions regler**: de här reglerna kan användas för att konfigurera ytterligare anpassade verifieringar, bekräftelser och en korrigering i ett steg, eller för att utföra någon annan anpassad dialog logik. Interaktions regler utvärderas vid varje aktivering i bearbetningen och kan användas för att utlösa slut för ande regler.

De olika åtgärderna som kon figurer ATS som en del av en regel utförs i den ordning som de visas i redigerings portalen.

### <a name="conditions"></a>Villkor
Villkor är de krav som måste uppfyllas för att en regel ska kunna köras. Regel villkor kan vara av följande typer:

* **Parameterns värde är lika med**: den konfigurerade parameterns värde är lika med ett angivet värde.
* **Inget parameter värde**: de konfigurerade parametrarna får inte ha något värde.
* **Obligatoriska parametrar**: den konfigurerade parametern har ett värde.
* **Alla obligatoriska parametrar**: alla parametrar som marker ATS som obligatoriska har ett värde.
* **Uppdaterade parametrar**: ett eller flera parameter värden har uppdaterats till följd av bearbetning av aktuella indata (uttryck eller aktivitet).
* **Bekräftelsen lyckades**: uttryck eller aktiviteten för indatamängden var en lyckad bekräftelse (Ja).
* **Bekräftelse nekades**: inuttryck eller Activity var inte en lyckad bekräftelse (Nej).
* **Föregående kommando måste uppdateras**: det här villkoret används i instanser när du vill fånga en negationad bekräftelse tillsammans med en uppdatering. I bakgrunden konfigureras det här villkoret för när dialog motorn identifierar en negativ bekräftelse där avsikten är samma som föregående Turn, och användaren har svarat med en uppdatering.

### <a name="actions"></a>Åtgärder
* **Skicka tal svar**: Skicka ett tal svar tillbaka till klienten.
* **Uppdatera parameter värde**: uppdatera värdet för en kommando parameter till ett angivet värde.
* **Rensa parameter värde**: Rensa kommandots parameter värde.
* **Anropa webb slut punkt**: gör ett anrop till en webb slut punkt.
* **Skicka aktivitet till klient**: skicka en anpassad aktivitet till klienten.

### <a name="expectations"></a>Prestationer
Förväntningar används för att konfigurera tips för bearbetning av nästa användarindata. Följande typer stöds:

* **Förväntar bekräftelse från användare**: denna förväntande åtgärd anger att programmet förväntar sig en bekräftelse (Ja/Nej) för nästa användarindata.
* **Förväntade parameter indata från användare**: denna förväntad anger en eller flera kommando parametrar som programmet förväntar sig från användarindata.

### <a name="post-execution-state"></a>Efter körnings tillstånd
Status efter körning är dialog läget när den aktuella indatan har bearbetats (uttryck eller aktivitet). Det är av följande typer:

* **Behåll nuvarande tillstånd**: Behåll endast det aktuella läget.
* **Slutför kommandot**: Slutför kommandot och inga ytterligare regler för kommandot kommer att bearbetas.
* **Regler för att köra slut för ande**: kör alla giltiga regler för slut för ande.
* **Vänta på användarens indata**: vänta på nästa användarindata.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Se exempel på GitHub](https://aka.ms/speech/cc-samples)
