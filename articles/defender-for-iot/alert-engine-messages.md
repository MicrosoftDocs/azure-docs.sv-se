---
title: Aviserings typer och beskrivningar
description: Granska Defender för IoT-aviserings beskrivningar.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/29/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 42a922dab2237925c2294245e112aab70b4245f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727730"
---
# <a name="alert-types-and-descriptions"></a>Aviserings typer och beskrivningar

I den här artikeln beskrivs alla OPF aviserings typer som kan genereras från Defender för IoT-motorer. Aviseringar visas i fönstret aviseringar som låter dig hantera aviserings händelsen. 

## <a name="policy-engine-alerts"></a>Aviseringar för princip motor

Princip motor aviseringar beskriver avvikelser från det inlärt nätverks beteendet.

| Rubrik  | Beskrivning | Allvarlighetsgrad |
|--|--|--|
| Onormal användning av MAC-adresser | En ny käll enhet identifierades i nätverket men har inte auktoriserats. | Mindre |
| Beckhoff program vara har ändrats | Inbyggd program vara har uppdaterats på en käll enhet. Detta kan vara en behörig aktivitet, till exempel ett planerat underhålls förfarande. | Större |
| Databas inloggningen misslyckades | Ett misslyckat inloggnings försök upptäcktes från en käll enhet till en mål server. Detta kan bero på mänskligt fel, men det kan också tyda på ett skadligt försök att kompromettera servern eller data på den. | Större |
| Emerson ROC-versionen av inbyggd program vara har ändrats | Inbyggd program vara har uppdaterats på en käll enhet. Detta kan vara en behörig aktivitet, till exempel ett planerat underhålls förfarande. | Större |
| Extern adress i nätverket som kommunicerats med Internet | En käll enhet som definieras som en del av nätverket kommunicerar med Internet adresser. Källan har inte behörighet att kommunicera med Internet adresser. | Kritiskt |
| Fält enheten identifierades utan förvarning | En ny käll enhet identifierades i nätverket men har inte auktoriserats. | Större |
| Ändring av inbyggd program vara upptäcktes | Inbyggd program vara har uppdaterats på en käll enhet. Detta kan vara en behörig aktivitet, till exempel ett planerat underhålls förfarande. | Större |
| Versionen av inbyggd program vara har ändrats | Inbyggd program vara har uppdaterats på en käll enhet. Detta kan vara en behörig aktivitet, till exempel ett planerat underhålls förfarande. | Större |
| Foxboro I/A ej behörig åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| FTP-inloggningen misslyckades | Ett misslyckat inloggnings försök upptäcktes från en käll enhet till en mål server. Detta kan bero på mänskligt fel, men det kan också tyda på ett skadligt försök att kompromettera servern eller data på den. | Större |
| Funktions kod utlöst otillåtet undantag | En käll enhet (slav) returnerade ett undantag till en mål enhet (Master). | Större |
| Typ inställningar för GOOSE-meddelande | Meddelandet (identifieras av protokoll-ID) inställningarna har ändrats på en käll enhet. | Varning |
| Honeywell-versionen av inbyggd program vara har ändrats | Inbyggd program vara har uppdaterats på en käll enhet. Detta kan vara en behörig aktivitet, till exempel ett planerat underhålls förfarande. | Större |
| Otillåten HTTP-kommunikation | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Internet åtkomst har identifierats | En käll enhet som definieras som en del av nätverket kommunicerar med Internet adresser. Källan har inte behörighet att kommunicera med Internet adresser. | Större |
| Mitsubishi-versionen av inbyggd program vara har ändrats | Inbyggd program vara har uppdaterats på en käll enhet. Detta kan vara en behörig aktivitet, till exempel ett planerat underhålls förfarande. | Större |
| Överträdelse av Modbus-adressintervall | En huvud enhet begärde åtkomst till en ny slav minnes adress. | Större |
| Modbus-versionen av inbyggd program vara har ändrats | Inbyggd program vara har uppdaterats på en käll enhet. Detta kan vara en behörig aktivitet, till exempel ett planerat underhålls förfarande. | Större |
| Ny aktivitet identifierad – CIP-klass | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – CIP Class service | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad-CIP PCCC-kommando | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – CIP symbol | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – EtherNet/IP-I/O-anslutning | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – kommandot EtherNet/IP-protokoll | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – GSM-meddelande kod | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad-LonTalk kommando koder | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny port identifiering | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Varning |
| Ny aktivitet identifierad-LonTalk-nätverks variabel | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – Ovation data förfrågan | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – Läs-och skriv kommando (AMS index Group) | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – Läs/skriv-kommando (AMS index offset) | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet upptäckt – ej behörig DeltaV meddelande typ | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet upptäckt – otillåten DeltaV ROC-åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – otillåten RPC-meddelande typ | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet upptäckt – otillåtet RPC-procedur anrop | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – använder AMS-protokoll kommando | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – använder Siemens SICAM-kommando | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – använder Suitelink-protokoll kommando | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad – använder Suitelink-protokoll-sessioner | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny aktivitet identifierad-använder kommandot Yokogawa VNetIP | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Ny till gång identifierad | En ny käll enhet identifierades i nätverket men har inte auktoriserats. | Större |
| Ny konfiguration av LLDP-enhet | En ny käll enhet identifierades i nätverket men har inte auktoriserats. | Större |
| Ny port identifiering | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Varning |
| Omron fenor obehörigt kommando | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| S7 plus den inbyggda program varan har ändrats | Inbyggd program vara har uppdaterats på en käll enhet. Detta kan vara en behörig aktivitet, till exempel ett planerat underhålls förfarande. | Större |
| Exempel på meddelande typs inställningar för exempel värden | Meddelandet (identifieras av protokoll-ID) inställningarna har ändrats på en käll enhet. | Varning |
| Misstanke om olaglig integritets genomsökning | En sökning upptäcktes på en DNP3-datakälla (Station Station). Den här genomsökningen har inte auktoriserats som inlärd trafik i nätverket. | Större |
| Toshiba dator länk, kommando | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Mindre |
| Otillåten ABB Totalflow-fil åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten ABB Totalflow-register åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig åtkomst till Siemens S7 data block | En käll enhet försökte få åtkomst till en resurs på en annan enhet. Ett åtkomst försök till den här resursen mellan dessa två enheter har inte auktoriserats som inlärd trafik i nätverket. | Varning |
| Obehörig åtkomst till Siemens S7 plus-objekt | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig åtkomst till Wonder-tagg | En käll enhet försökte få åtkomst till en resurs på en annan enhet. Ett åtkomst försök till den här resursen mellan dessa två enheter har inte auktoriserats som inlärd trafik i nätverket. | Större |
| Obehörig åtkomst till BACNet-objekt | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig BACNet-väg | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig databas inloggning | Ett inloggnings försök mellan en käll klient och en mål server upptäcktes. Kommunikation mellan dessa enheter har inte auktoriserats som inlärd trafik i nätverket. | Större |
| Otillåten databas åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten Emerson ROC-åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig GE SRTP fil åtkomst | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåtet GE SRTP-protokoll kommando | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig GE SRTP system minnes åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten HTTP-aktivitet | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten HTTP-Server | Ett obehörigt program upptäcktes på en käll enhet. Programmet har inte auktoriserats som ett lärt program i nätverket. | Större |
| Otillåten HTTP SOAP-åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig HTTP-användaragent | Ett obehörigt program upptäcktes på en käll enhet. Programmet har inte auktoriserats som ett lärt program i nätverket. | Större |
| Obehörig Internet anslutning upptäckt | En käll enhet som definieras som en del av nätverket kommunicerar med Internet adresser. Källan har inte behörighet att kommunicera med Internet adresser. | Kritiskt |
| Otillåtet Mitsubishi MELSEC-kommando | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig åtkomst till MMS-program | En käll enhet försökte få åtkomst till en resurs på en annan enhet. Ett åtkomst försök till den här resursen mellan dessa två enheter har inte auktoriserats som inlärd trafik i nätverket. | Större |
| Obehörig MMS-tjänst | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig multicast/broadcast-anslutning | En multicast/broadcast-anslutning upptäcktes mellan en käll enhet och andra enheter. Multicast/broadcast-kommunikation är inte auktoriserad. | Kritiskt |
| Otillåten namn fråga | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig OPC UA-aktivitet | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig OPC UA-begäran/svar | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten åtgärd upptäcktes av en användardefinierad regel | Trafiken upptäcktes mellan två enheter. Den här aktiviteten är inte tillåten baserat på en anpassad aviserings regel som definierats av en användare. | Större |
| Otillåten PLC-konfiguration läsa | Käll enheten är inte definierad som en programmerings enhet men utförde en Läs-/skriv åtgärd på en mål styrenhet. Programmerings ändringar bör bara utföras av programmerings enheter. Ett programmerings program kan ha installerats på den här enheten. | Varning |
| Otillåten PLC-konfiguration skrivning | Käll enheten skickade ett kommando för att läsa/skriva programmet för en mål kontroll. Den här aktiviteten visades inte tidigare. | Större |
| Obehörigt PLC-program uppladdning | Käll enheten skickade ett kommando för att läsa/skriva programmet för en mål kontroll. Den här aktiviteten visades inte tidigare. | Större |
| Otillåten PLC-programmering | Käll enheten är inte definierad som en programmerings enhet men utförde en Läs-/skriv åtgärd på en mål styrenhet. Programmerings ändringar bör bara utföras av programmerings enheter. Ett programmerings program kan ha installerats på den här enheten. | Kritiskt |
| Otillåten typ av ramtyp | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåtet SAIA S-Bus-kommando | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten Siemens S7-körning av kontroll funktion | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten Siemens S7-körning av användardefinierad funktion | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig Siemens-S7 plus åtkomst till block | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten Siemens S7 plus-åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig SMB-inloggning | Ett inloggnings försök mellan en käll klient och en mål server upptäcktes. Kommunikation mellan dessa enheter har inte auktoriserats som inlärd trafik i nätverket. | Större |
| Otillåten SNMP-åtgärd | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Obehörig SSH-åtkomst | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten Windows-process | Ett obehörigt program upptäcktes på en käll enhet. Programmet har inte auktoriserats som ett lärt program i nätverket. | Större |
| Obehörig Windows-tjänst | Ett obehörigt program upptäcktes på en käll enhet. Programmet har inte auktoriserats som ett lärt program i nätverket. | Större |
| Otillåten åtgärd upptäcktes av en användardefinierad regel | Nya trafik parametrar har identifierats. Denna parameter kombination bryter mot en användardefinierad regel | Större |
| Otillåten Modbus Schneider Electrical extension | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten användning av ASDU-typer | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten användning av DNP3-funktions kod | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |
| Otillåten användning av intern indikation (IIN) | En DNP3 käll enhet (överordnad) rapporterade en intern indikation (IIN) som inte har auktoriserats som inlärd trafik i nätverket. | Större |
| Otillåten användning av Modbus-funktions kod | Nya trafik parametrar har identifierats. Denna parameter kombination har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är inte tillåten. | Större |

## <a name="anomaly-engine-alerts"></a>Avvikelse motor aviseringar

| Rubrik | Beskrivning | Allvarlighetsgrad |
|--|--|--|
| Onormalt undantags mönster i slav | Ett högt antal fel upptäcktes på en käll enhet. Detta kan vara resultatet av ett drifts problem. | Mindre |
| Onormalt HTTP-huvudets längd | Käll enheten skickade ett onormalt meddelande. Detta kan tyda på ett försök att angripa mål enheten. | Kritiskt |
| Onormalt antal parametrar i HTTP-huvud | Käll enheten skickade ett onormalt meddelande. Detta kan tyda på ett försök att angripa mål enheten. | Kritiskt |
| Onormalt periodiskt beteende i kommunikations kanal | En ändring i frekvensen av kommunikationen mellan käll-och mål enheterna upptäcktes. | Mindre |
| Onormal avslutning av program | Ett högt antal stopp kommandon upptäcktes på en käll enhet. Detta kan bero på ett drifts problem eller ett försök att manipulera enheten. | Större |
| Onormal trafik bandbredd | Onormal bandbredd upptäcktes på en kanal. Bandbredden förefaller vara betydligt lägre eller högre än vad som tidigare har upptäckts. För mer information, arbeta med widgeten total bandbredd. | Varning |
| Onormal trafik bandbredd mellan enheter | Onormal bandbredd upptäcktes på en kanal. Bandbredden förefaller vara betydligt lägre eller högre än vad som tidigare har upptäckts. För mer information, arbeta med widgeten total bandbredd. | Varning |
| Adress genomsökning identifierad | En käll enhet identifierade genomsökning av nätverks enheter. Den här enheten har inte auktoriserats som en enhet för nätverks genomsökning. | Kritiskt |
| Sökning efter ARP-adress identifierades | En käll enhet identifierade genomsökning av nätverks enheter med hjälp av ARP (Address Resolution Protocol). Den här enhets adressen har inte auktoriserats som giltig adress för ARP-sökning. | Kritiskt |
| Sökning efter ARP-adress identifierades | En käll enhet identifierade genomsökning av nätverks enheter med hjälp av ARP (Address Resolution Protocol). Den här enhets adressen har inte auktoriserats som giltig adress för ARP-sökning. | Kritiskt |
| ARP-förfalskning | En onormal mängd paket har identifierats i nätverket. Detta kan tyda på ett angrepp, till exempel en ARP-förfalskning eller ICMP-översvämmande angrepp. | Varning |
| Alltför stora inloggnings försök | En käll enhet visade sig att utföra överdriven inloggnings försök till en mål server. Detta kan vara en brute force-attack. Servern kan komprometteras av en skadlig aktör. | Kritiskt |
| Högt antal sessioner | En käll enhet visade sig att utföra överdriven inloggnings försök till en mål server. Detta kan vara en brute force-attack. Servern kan komprometteras av en skadlig aktör. | Kritiskt |
| För hög omstarts takt för en station | Ett högt antal kommandon för omstart har identifierats på en käll enhet. Detta kan bero på ett drifts problem eller ett försök att manipulera enheten. | Större |
| Alltför långa SMB-inloggningsförsök | En käll enhet visade sig att utföra överdriven inloggnings försök till en mål server. Detta kan vara en brute force-attack. Servern kan komprometteras av en skadlig aktör. | Kritiskt |
| ICMP-översvämning | En onormal mängd paket har identifierats i nätverket. Detta kan tyda på ett angrepp, till exempel en ARP-förfalskning eller ICMP-översvämmande angrepp. | Varning |
| Ogiltigt innehåll i HTTP-huvudet | Käll enheten initierade en ogiltig begäran. | Kritiskt |
| Inaktiv kommunikations kanal | En kommunikations kanal mellan två enheter var inaktiv under en period då aktivitet vanligt vis visas. Detta kan tyda på att programmet som genererar den här trafiken har ändrats eller att programmet kanske inte är tillgängligt. Vi rekommenderar att du granskar konfigurationen av installerade program och kontrollerar att den är korrekt konfigurerad. | Varning |
| Sökning efter långa varaktighets adresser upptäcktes | En käll enhet identifierade genomsökning av nätverks enheter. Den här enheten har inte auktoriserats som en enhet för nätverks genomsökning. | Kritiskt |
| Ett försök att gissa lösen ord identifierades | En käll enhet visade sig att utföra överdriven inloggnings försök till en mål server. Detta kan vara en brute force-attack. Servern kan komprometteras av en skadlig aktör. | Kritiskt |
| PLC-genomsökning upptäckt | En käll enhet identifierade genomsökning av nätverks enheter. Den här enheten har inte auktoriserats som en enhet för nätverks genomsökning. | Kritiskt |
| Ports ökning identifierad | En käll enhet identifierade genomsökning av nätverks enheter. Den här enheten har inte auktoriserats som en enhet för nätverks genomsökning. | Kritiskt |
| Oväntad meddelande längd | Käll enheten skickade ett onormalt meddelande. Detta kan tyda på ett försök att angripa mål enheten. | Kritiskt |
| Oväntad trafik för standard port | Trafik identifierades på en enhet med hjälp av en port som är reserverad för ett annat protokoll. | Större |

## <a name="protocol-violation-engine-alerts"></a>Varning om protokoll överträdelse

| Rubrik | Beskrivning | Allvarlighetsgrad |
|--|--|--|
| Överflödiga felaktiga paket i en enda session | Ett onormalt antal felaktiga paket som skickats från käll enheten till mål enheten. Detta kan tyda på felaktig kommunikation eller ett försök att ändra mål enheten. | Större |
| Uppdatering av inbyggd programvara | En käll enhet skickade ett kommando för att uppdatera den inbyggda program varan på en mål enhet. Kontrol lera att senaste program-, konfigurations-och inbyggd uppgraderingar av inbyggd program vara har gjorts på mål enheten. | Varning |
| Funktions koden stöds inte av utstationen | Mål enheten mottog en ogiltig begäran. | Större |
| Otillåtet BACNet-meddelande | Käll enheten initierade en ogiltig begäran. | Större |
| Ogiltigt anslutnings försök på Port 0 | En käll enhet försökte ansluta till mål enheten på port numret noll (0). För TCP är port 0 reserverad och kan inte användas. För UDP är porten valfritt och värdet 0 innebär ingen port. Det finns vanligt vis ingen tjänst på ett system som lyssnar på Port 0. Den här händelsen kan indikera ett försök att angripa mål enheten, eller ange att ett program har programmerats felaktigt. | Mindre |
| Ogiltig DNP3-åtgärd | Käll enheten initierade en ogiltig begäran. | Större |
| Ogiltig MODBUS-åtgärd (undantag utlöst av Master) | Käll enheten initierade en ogiltig begäran. | Större |
| Ogiltig MODBUS-åtgärd (funktions kod noll) | Käll enheten initierade en ogiltig begäran. | Större |
| Ogiltig protokoll version | Käll enheten initierade en ogiltig begäran. | Större |
| Felaktig parameter skickades till utstationeringen | Mål enheten mottog en ogiltig begäran. | Större |
| Initiering av en inaktuell funktions kod (initiera data) | Käll enheten initierade en ogiltig begäran. | Mindre |
| Initiering av en föråldrad funktions kod (Spara konfiguration) | Käll enheten initierade en ogiltig begäran. | Mindre |
| Master begärde en program lager bekräftelse | Käll enheten initierade en ogiltig begäran. | Varning |
| Modbus-undantag | En käll enhet (slav) returnerade ett undantag till en mål enhet (Master). | Större |
| Slaven het tog emot illegal ASDU-typ | Mål enheten mottog en ogiltig begäran. | Större |
| Slaven het tog emot otillåten kommando orsak till överföring | Mål enheten mottog en ogiltig begäran. | Större |
| Slaven het mottog felaktig gemensam adress | Mål enheten mottog en ogiltig begäran. | Större |
| Felaktig data adress parameter togs emot på slav enhet | Mål enheten mottog en ogiltig begäran. | Större |
| Felaktig data värde parameter togs emot slav enhet | Mål enheten mottog en ogiltig begäran. | Större |
| Slaven het mottog ogiltig funktions kod | Mål enheten mottog en ogiltig begäran. | Större |
| Slaven het tog emot illegal information objekt adress | Mål enheten mottog en ogiltig begäran. | Större |
| Ett okänt objekt har skickats till utstationeringen | Mål enheten mottog en ogiltig begäran. | Större |
| Användning av en reserverad funktions kod | Käll enheten initierade en ogiltig begäran. | Större |
| Användning av felaktig formatering efter Station | Käll enheten initierade en ogiltig begäran. | Varning |
| Användning av reserverade status flaggor (IIN) | En DNP3 käll enhet (Station Station) använde den reserverade interna indikatorn 2,6. Vi rekommenderar att du kontrollerar enhetens konfiguration. | Varning |

## <a name="malware-engine-alerts"></a>Aviseringar för skadlig kod

| Rubrik | Beskrivning| Allvarlighetsgrad |
|--|--|--|
| Anslutnings försök till känd skadlig IP | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Större |
| Ogiltigt SMB-meddelande (DoublePulsar bakdörr installation) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Begäran om skadlig domän namn | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Större |
| Test filen för skadlig kod har identifierats-EICAR AV lyckades | En EICAR AV-test-filen upptäcktes i trafik mellan två enheter. Filen är inte skadlig kod. Det används för att bekräfta att antivirus programmet är korrekt installerat. demonstrera vad som händer när ett virus hittas och kontrol lera interna procedurer och reaktioner när ett virus påträffas. Antivirus program bör upptäcka EICAR som om det vore ett verkligt virus. | Större |
| Misstänkt av Conficker skadlig kod | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Större |
| Misstanke om denial of service-attack | En käll enhet försökte initiera ett högt antal nya anslutningar till en mål enhet. Detta kan vara en DOS-attack (Denial of Service) mot mål enheten och kan störa enhets funktioner, påverka prestanda och tjänst tillgänglighet eller orsaka oåterkalleliga fel. | Kritiskt |
| Misstanke om skadlig aktivitet | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Större |
| Misstanke om skadlig aktivitet (BlackEnergy) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (DarkComet) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (Duqu) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (lågan) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (Havex) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (Karagany) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (ljus) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (namn frågor) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Större |
| Misstanke om skadlig aktivitet (Poison Ivy) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (regin) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (Stuxnet) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om skadlig aktivitet (WannaCry) | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Större |
| Misstanke om skadlig kod för NotPetya – ogiltiga SMB-parametrar har identifierats | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om NotPetya skadlig kod-ogiltig SMB-transaktion upptäckt | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |
| Misstanke om fjärrkörning av kod med PsExec | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Större |
| Misstanke om fjärrhantering av Windows-tjänster | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Större |
| Misstänkt körbar fil upptäcktes i slut punkten | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Större |
| Misstänkt trafik har identifierats | Misstänkt nätverks aktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känt skadlig kod. | Kritiskt |

## <a name="operational-engine-alerts"></a>Aviseringar för drifts motor

| Rubrik | Beskrivning | Allvarlighetsgrad |
|--|--|--|
| Ett S7 Stop PLC-kommando har skickats | Käll enheten skickade ett stopp-kommando till en mål styrenhet. Kontrollanten slutar att fungera tills ett start kommando har skickats. | Varning |
| BACNet-åtgärden misslyckades | En server returnerade en felkod. Detta indikerar ett Server fel eller en ogiltig begäran från en klient. | Större |
| Felaktigt status för MMS-enhet | En virtuell MMS-Manufacturing-enhet (VMD) skickade ett status meddelande. Meddelandet anger att servern kanske inte är korrekt konfigurerad, delvis drift eller inte fungerar alls. | Större |
| Ändring av enhets konfiguration | En konfigurations ändring upptäcktes på en käll enhet. | Mindre |
| Oavbruten händelse buffertspill vid utstation | En buffertöverskridning-händelse upptäcktes på en käll enhet. Händelsen kan leda till att data skadas, att programmet kraschar eller att skadlig kod körs. | Större |
| Återställning av styrenhet | En käll enhet skickade ett återställnings kommando till en mål styrenhet. Styrenheten stoppades tillfälligt och startade igen automatiskt. | Varning |
| Kontroll av styrenheten stoppas | Käll enheten skickade ett stopp-kommando till en mål styrenhet. Kontrollanten slutar att fungera tills ett start kommando har skickats. | Varning |
| Enheten kunde inte ta emot en dynamisk IP-adress | Käll enheten är konfigurerad att ta emot en dynamisk IP-adress från en DHCP-server men fick ingen adress. Detta tyder på ett konfigurations fel på enheten, eller ett drifts fel i DHCP-servern. Vi rekommenderar att du meddelar nätverks administratören om incidenten | Större |
| Enheten kan misstänkas vara frånkopplad (svarar inte) | En käll enhet svarade inte på ett kommando som skickats till den. Den kan ha kopplats från när kommandot skickades. | Större |
| Begäran om EtherNet-CIP-tjänst misslyckades | En server returnerade en felkod. Detta indikerar ett Server fel eller en ogiltig begäran från en klient. | Större |
| Kommandot för EtherNet/IP-inkapslings protokoll misslyckades | En server returnerade en felkod. Detta indikerar ett Server fel eller en ogiltig begäran från en klient. | Större |
| Spill i händelsens buffert i utstationen | En buffertöverskridning-händelse upptäcktes på en käll enhet. Händelsen kan leda till att data skadas, att programmet kraschar eller att skadlig kod körs. | Större |
| Den förväntade säkerhets kopieringen utfördes inte | Förväntad säkerhets kopierings-/fil överförings aktivitet sker inte mellan två enheter. Detta kan tyda på fel i processen för säkerhets kopiering/fil överföring. | Större |
| GE SRTP kommando haveri | En server returnerade en felkod. Detta indikerar ett Server fel eller en ogiltig begäran från en klient. | Större |
| GE SRTP Stop PLC-kommando skickat | Käll enheten skickade ett stopp-kommando till en mål styrenhet. Kontrollanten slutar att fungera tills ett start kommando har skickats. | Varning |
| GOOSE Control Block kräver ytterligare konfiguration | En käll enhet skickade ett GOOSE-meddelande som anger att enheten behöver provision. Det innebär att GOOSE-kontroll blocket kräver ytterligare konfigurations-och GOOSE-meddelanden är delvis eller helt icke-operativa. | Större |
| Konfigurationen av GOOSE-datauppsättningen ändrades | Ett meddelande (identifierat av protokoll-ID) dataset har ändrats på en käll enhet. Det innebär att enheten rapporterar en annan data uppsättning för det här meddelandet. | Varning |
| Oväntad status för Honeywell Controller | En Honeywell kontrollant skickade ett oväntat diagnostikpaket som indikerar en status ändring. | Varning |
| HTTP-klient fel | Käll enheten initierade en ogiltig begäran. | Varning |
| Ogiltig IP-adress | Systemet upptäckte trafik mellan en käll enhet och en IP-adress som är en ogiltig adress. Detta kan tyda på felaktig konfiguration eller ett försök att generera ogiltig trafik. | Mindre |
| Master-Slave autentiseringsfel | Det gick inte att autentisera processen mellan en DNP3 käll enhet (Master) och en målenhet (Station Station). | Mindre |
| Begäran om MMS-tjänst misslyckades | En server returnerade en felkod. Detta indikerar ett Server fel eller en ogiltig begäran från en klient. | Större |
| Ingen trafik har identifierats i sensor gränssnittet | En sensor slutade identifiera nätverks trafik i ett nätverks gränssnitt. | Kritiskt |
| OPC UA-Server utlöste en händelse som kräver användarens uppmärksamhet | En OPC UA-Server skickade ett händelse meddelande till en klient. Den här typen av händelse kräver användar åtgärd | Större |
| OPC UA service-begäran misslyckades | En server returnerade en felkod. Detta indikerar ett Server fel eller en ogiltig begäran från en klient. | Större |
| Datorn har startats om | En kall omstart upptäcktes på en käll enhet. Det innebär att enheten är fysiskt avstängd och tillbaka igen. | Varning |
| Stationerna startar om ofta | Ett högt antal kall omstarter upptäcktes på en käll enhet. Det innebär att enheten har stängts av fysiskt och sedan en gång till ett alltför stort antal gånger. | Mindre |
| Profilens konfiguration har ändrats | En konfigurations ändring upptäcktes på en käll enhet. | Större |
| Den skadade konfigurationen av Stations stationen har identifierats | Den här käll enheten för DNP3 (stationen) rapporterade en skadad konfiguration. | Större |
| Ett noDCP-kommando misslyckades | En server returnerade en felkod. Detta indikerar ett Server fel eller en ogiltig begäran från en klient. | Större |
| Återställ enhet fabriks återställning | En käll enhet skickade ett fabriks återställnings kommando till en förfinad mål enhet. Kommandot reset rensar konfigurationerna med hjälp av enhets konfiguration och stoppar åtgärden. | Varning |
| RPC-åtgärden misslyckades | En server returnerade en felkod. Detta indikerar ett Server fel eller en ogiltig begäran från en klient. | Större |
| Konfiguration av meddelande data uppsättning för exempel värden ändrades | Ett meddelande (identifierat av protokoll-ID) dataset har ändrats på en käll enhet. Det innebär att enheten rapporterar en annan data uppsättning för det här meddelandet. | Varning |
| Slav enhet som inte går att återställa | Ett oåterkalleligt villkors fel upptäcktes på en käll enhet. Den här typen av fel indikerar vanligt vis ett maskin varu fel eller om det inte går att utföra ett speciellt kommando. | Större |
| Misstanke om maskin varu problem i utstationeringen | Ett oåterkalleligt villkors fel upptäcktes på en käll enhet. Den här typen av fel indikerar vanligt vis ett maskin varu fel eller om det inte går att utföra ett speciellt kommando. | Större |
| Misstanke om att MODBUS-enheten inte svarar | En käll enhet svarade inte på ett kommando som skickats till den. Den kan ha kopplats från när kommandot skickades. | Mindre |
| Trafik upptäcktes i sensor gränssnittet | En sensor återupptog identifiering av nätverks trafik i ett nätverks gränssnitt. | Varning |

## <a name="next-steps"></a>Nästa steg

Du kan [Hantera aviserings händelser](how-to-manage-the-alert-event.md).
Lär dig hur du [vidarebefordrar aviserings information](how-to-forward-alert-information-to-partners.md).
