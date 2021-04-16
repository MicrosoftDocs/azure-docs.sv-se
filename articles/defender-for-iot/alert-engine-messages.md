---
title: Aviseringstyper och beskrivningar
description: Läs beskrivningarna för Defender for IoT-aviseringar.
ms.date: 4/8/2021
ms.topic: how-to
ms.openlocfilehash: 483563b53a5849b0354986269568bc42b9124cc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478003"
---
# <a name="alert-types-and-descriptions"></a>Aviseringstyper och beskrivningar

I den här artikeln beskrivs alla aviseringstyper som kan genereras från Defender for IoT-motorerna. Aviseringar visas i fönstret Aviseringar, där du kan hantera aviseringshändelsen. 

## <a name="policy-engine-alerts"></a>Principmotoraviseringar

Principmotoraviseringar beskriver identifierade avvikelser från inlärt baslinjebeteende.

| Rubrik  | Beskrivning | Allvarlighetsgrad |
|--|--|--|
| Onormal användning av MAC-adresser | En ny källenhet har identifierats i nätverket men har inte auktoriserats. | Mindre |
| – Programvara har ändrats | Den inbyggda programvaran har uppdaterats på en källenhet. Detta kan vara auktoriserad aktivitet, till exempel en procedur för planerat underhåll. | Större |
| Databasinloggningen misslyckades | Ett misslyckat inloggningsförsök har identifierats från en källenhet till en målserver. Detta kan bero på mänskliga fel, men kan också tyda på ett skadligt försök att kompromettera servern eller data på den. | Större |
| Emerson ROC Firmware-versionen har ändrats | Den inbyggda programvaran har uppdaterats på en källenhet. Detta kan vara auktoriserad aktivitet, till exempel en procedur för planerat underhåll. | Större |
| Extern adress i nätverket som kommunicerar med Internet | En källenhet som definieras som en del av nätverket kommunicerar med Internetadresser. Källan har inte behörighet att kommunicera med Internetadresser. | Kritiskt |
| Fältenhet identifierades oväntat | En ny källenhet har identifierats i nätverket men har inte auktoriserats. | Större |
| Ändring av inbyggd programvara har identifierats | Den inbyggda programvaran har uppdaterats på en källenhet. Detta kan vara auktoriserad aktivitet, till exempel en procedur för planerat underhåll. | Större |
| Versionen för inbyggd programvara har ändrats | Den inbyggda programvaran har uppdaterats på en källenhet. Detta kan vara auktoriserad aktivitet, till exempel en procedur för planerat underhåll. | Större |
| Foxboro I/A-obehörig åtgärd | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| FTP-inloggningen misslyckades | Ett misslyckat inloggningsförsök har identifierats från en källenhet till en målserver. Detta kan bero på mänskliga fel, men kan också tyda på ett skadligt försök att kompromettera servern eller data på den. | Större |
| Funktionskoden utlöste ett obehörigt undantag | En källenhet (undermål) returnerade ett undantag till en målenhet (huvudenhet). | Större |
| INSTÄLLNINGAR FÖR MEDDELANDETYP FÖR MEDDELANDETYP | Meddelandeinställningarna (identifierades av protokoll-ID) har ändrats på en källenhet. | Varning |
| Honeywells version av inbyggd programvara har ändrats | Den inbyggda programvaran har uppdaterats på en källenhet. Detta kan vara auktoriserad aktivitet, till exempel en procedur för planerat underhåll. | Större |
| Ogiltig HTTP-kommunikation | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Internetåtkomst har identifierats | En källenhet som definieras som en del av nätverket kommunicerar med Internetadresser. Källan har inte behörighet att kommunicera med Internetadresser. | Större |
| Firmware Firmware-versionen har ändrats | Den inbyggda programvaran har uppdaterats på en källenhet. Detta kan vara auktoriserad aktivitet, till exempel en procedur för planerat underhåll. | Större |
| Överträdelse av Modbus-adressintervall | En huvudenhet begärde åtkomst till en ny minnesadress till en underström. | Större |
| Versionen för den inbyggda Modbus-programvaran har ändrats | Den inbyggda programvaran har uppdaterats på en källenhet. Detta kan vara auktoriserad aktivitet, till exempel en procedur för planerat underhåll. | Större |
| Ny aktivitet har identifierats – CIP-klass | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – CIP-klasstjänst | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – CIP PCCC-kommando | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – CIP-symbol | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – EtherNet/IP I/O-anslutning | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – EtherNet/IP-protokollkommando | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – GSM-meddelandekod | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – LonTalk-kommandokoder | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny portidentifiering | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Varning |
| Ny aktivitet har identifierats – nätverksvariabeln LonTalk | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – Ovation Data Request | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – läs-/skrivkommando (AMS-indexgrupp) | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – läs-/skrivkommando (AMS-indexförskjutning) | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – Otillåten DeltaV-meddelandetyp | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – Obehörig DeltaV ROC-åtgärd | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – Obehörig RPC-meddelandetyp | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – Anrop av obehörig RPC-procedur | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – med hjälp av AMS-protokollkommandot | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – med kommandot SiCAM i Sicam | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – kommandot Suitelink Protocol används | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – använda Suitelink-protokollsessioner | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny aktivitet har identifierats – Med Yokogawa VNetIP-kommando | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ny tillgång har identifierats | En ny källenhet har identifierats i nätverket men har inte auktoriserats. | Större |
| Ny LLDP-enhetskonfiguration | En ny källenhet har identifierats i nätverket men har inte auktoriserats. | Större |
| Ny portidentifiering | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Varning |
| Kommandot Omron FINS Unauthorized | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| S7 Plus FIRMWARE Inbyggd programvara har ändrats | Den inbyggda programvaran har uppdaterats på en källenhet. Detta kan vara auktoriserad aktivitet, till exempel en procedur för planerat underhåll. | Större |
| Inställningar för meddelandetyp för exempelvärden | Meddelandeinställningarna (identifierades av protokoll-ID) har ändrats på en källenhet. | Varning |
| Misstänkt ogiltig integritetsskanning | En genomsökning upptäcktes på en DNP3-källenhet (utstation). Den här genomsökningen auktoriserades inte som inlärd trafik i nätverket. | Större |
| Toshiba Computer Link Unauthorized Command | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Mindre |
| Obehörig ABB Totalflow-filåtgärd | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Otillåten ABB-åtgärd för totalflödesregistering | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig åtkomst till Datacenter S7 Data Block | En källenhet försökte komma åt en resurs på en annan enhet. Ett åtkomstförsök till den här resursen mellan dessa två enheter har inte godkänts som inlärd trafik i nätverket. | Varning |
| Obehörig åtkomst till Siemens S7 Plus-objekt | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig åtkomst till Wonderware-tagg | En källenhet försökte komma åt en resurs på en annan enhet. Ett åtkomstförsök till den här resursen mellan dessa två enheter har inte godkänts som inlärd trafik i nätverket. | Större |
| Obehörig åtkomst till BACNet-objekt | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig BACNet-väg | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig databasinloggning | Ett inloggningsförsök mellan en källklient och målserver har identifierats. Kommunikationen mellan dessa enheter har inte auktoriserats som inlärd trafik i nätverket. | Större |
| Obehörig databasåtgärd | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig Emerson ROC-åtgärd | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig GE SRTP-filåtkomst | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Unauthorized GE SRTP Protocol Command | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig GE SRTP-systemminnesåtgärd | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig HTTP-aktivitet | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig HTTP-server | Ett obehörigt program har identifierats på en källenhet. Programmet har inte auktoriserats som ett inlärt program i nätverket. | Större |
| Obehörig HTTP SOAP-åtgärd | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig HTTP-användaragent | Ett obehörigt program har identifierats på en källenhet. Programmet har inte auktoriserats som ett inlärt program i nätverket. | Större |
| Obehörig Internetanslutning har identifierats | En källenhet som definieras som en del av nätverket kommunicerar med Internetadresser. Källan har inte behörighet att kommunicera med Internetadresser. | Kritiskt |
| Obehörigt Mel MELSEC-kommando | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig MMS-programåtkomst | En källenhet försökte komma åt en resurs på en annan enhet. Ett åtkomstförsök till den här resursen mellan dessa två enheter har inte auktoriserats som inlärd trafik i nätverket. | Större |
| Obehörig MMS-tjänst | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig multicast-/broadcast-anslutning | En Multicast/Broadcast-anslutning har identifierats mellan en källenhet och andra enheter. Multicast-/broadcast-kommunikation är inte auktoriserad. | Kritiskt |
| Fråga om obehörigt namn | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig OPC UA-aktivitet | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig OPC UA-begäran/-svar | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig åtgärd upptäcktes av en användardefinierad regel | Trafik har identifierats mellan två enheter. Den här aktiviteten är obehörig baserat på en anpassad aviseringsregel som definierats av en användare. | Större |
| Obehöriga AV CONFIGURATION-konfigurationsläsningar | Källenheten definieras inte som en programmeringsenhet utan utför en läs-/skrivåtgärd på en målstyrenhet. Programmeringsändringar bör endast utföras av programmeringsenheter. Ett programmeringsprogram kan ha installerats på den här enheten. | Varning |
| Obehörig SKRIVNING av CONFIGURATION-konfiguration | Källenheten skickade ett kommando för att läsa/skriva programmet för en målstyrenhet. Den här aktiviteten har inte setts tidigare. | Större |
| Obehörig UPLOAD-programuppladdning | Källenheten skickade ett kommando för att läsa/skriva programmet för en målstyrenhet. Den här aktiviteten har inte setts tidigare. | Större |
| Obehörig PROGRAMMERING | Källenheten definieras inte som en programmeringsenhet utan utför en läs-/skrivåtgärd på en målstyrenhet. Programmeringsändringar bör endast utföras av programmeringsenheter. Ett programmeringsprogram kan ha installerats på den här enheten. | Kritiskt |
| Otillåten Profinet-ramtyp | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Ej auktoriserad SAIA S-Bus-kommando | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Unauthorized Siemens S7 Execution of Control Function | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehöriga Siemens S7-körning av användardefinierad funktion | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehöriga Siemens S7 Plus Block Access | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehöriga Siemens S7 Plus-åtgärd | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig SMB-inloggning | Ett inloggningsförsök mellan en källklient och målserver har identifierats. Kommunikationen mellan dessa enheter har inte auktoriserats som inlärd trafik i nätverket. | Större |
| Obehörig SNMP-åtgärd | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig SSH-åtkomst | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obehörig Windows-process | Ett obehörigt program har identifierats på en källenhet. Programmet har inte auktoriserats som ett inlärt program i nätverket. | Större |
| Obehörig Windows-tjänst | Ett obehörigt program har identifierats på en källenhet. Programmet har inte auktoriserats som ett inlärt program i nätverket. | Större |
| Obehörig åtgärd upptäcktes av en användardefinierad regel | Nya trafikparametrar har identifierats. Den här parameterkombinationen bryter mot en användardefinierad regel | Större |
| Oföränderliga Modbus Electric-tillägg | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obevakad användning av ASDU-typer | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Opåläst användning av DNP3-funktionskod | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |
| Obevakad användning av intern indikation (IIN) | En DNP3-källenhet (utstation) rapporterade en intern indikation (IIN) som inte har auktoriserats som inlärd trafik i nätverket. | Större |
| Opåläst användning av Modbus-funktionskod | Nya trafikparametrar har identifierats. Den här parameterkombinationen har inte auktoriserats som inlärd trafik i nätverket. Följande kombination är obehörig. | Större |

## <a name="anomaly-engine-alerts"></a>Aviseringar om avvikelsemotor

Aviseringar från avvikelsemotorn beskriver identifierade avvikelser i nätverksaktiviteten.

| Rubrik | Beskrivning | Allvarlighetsgrad |
|--|--|--|
| Onormalt undantagsmönster hos under | Ett stort antal fel har identifierats på en källenhet. Detta kan vara resultatet av ett driftproblem. | Mindre |
| Onormal HTTP-huvudlängd | Källenheten skickade ett onormalt meddelande. Detta kan tyda på ett försök att angripa målenheten. | Kritiskt |
| Onormalt antal parametrar i HTTP-huvud | Källenheten skickade ett onormalt meddelande. Detta kan tyda på ett försök att angripa målenheten. | Kritiskt |
| Onormalt regelbundet beteende i kommunikationskanalen | En ändring i kommunikationsfrekvensen mellan käll- och målenheterna har identifierats. | Mindre |
| Onormal avslutning av program | Ett stort antal stoppkommandon har identifierats på en källenhet. Detta kan vara resultatet av ett driftproblem eller ett försök att manipulera enheten. | Större |
| Onormal trafikbandbredd | Onormal bandbredd har identifierats på en kanal. Bandbredden verkar vara betydligt lägre/högre än tidigare upptäckt. Mer information finns i arbeta med widgeten Total bandbredd. | Varning |
| Onormal trafikbandbredd mellan enheter | Onormal bandbredd upptäcktes på en kanal. Bandbredden verkar vara betydligt lägre/högre än vad som har identifierats tidigare. Mer information finns i arbeta med widgeten Total bandbredd. | Varning |
| Adressgenomsökning har identifierats | En källenhet upptäcktes genomsökning av nätverksenheter. Den här enheten har inte auktoriserats som en enhet för nätverksgenomsökning. | Kritiskt |
| ARP-adressgenomsökning har identifierats | En källenhet upptäcktes genomsökning av nätverksenheter med hjälp av Address Resolution Protocol (ARP). Den här enhetsadressen har inte auktoriserats som giltig ARP-genomsökningsadress. | Kritiskt |
| ARP-adressgenomsökning har identifierats | En källenhet upptäcktes genomsökning av nätverksenheter med hjälp av Address Resolution Protocol (ARP). Den här enhetsadressen har inte auktoriserats som giltig ARP-genomsökningsadress. | Kritiskt |
| ARP-förfalskning | En onormal mängd paket upptäcktes i nätverket. Detta kan tyda på en attack, till exempel ett ARP-förfalskning eller en ICMP-attack som översvämmar. | Varning |
| För många inloggningsförsök | En källenhet sågs utföra omfattande inloggningsförsök till en målserver. Det här kan vara en råstyrkattack. Servern kan komprometteras av en illvillig aktör. | Kritiskt |
| För många sessioner | En källenhet sågs utföra omfattande inloggningsförsök till en målserver. Detta kan vara en brute force-attack. Servern kan komprometteras av en illvillig aktör. | Kritiskt |
| För hög omstartsfrekvens för en utstation | Ett stort antal omstartskommandon har identifierats på en källenhet. Detta kan vara resultatet av ett driftproblem eller ett försök att manipulera enheten. | Större |
| För många SMB-inloggningsförsök | En källenhet sågs utföra omfattande inloggningsförsök till en målserver. Detta kan vara en brute force-attack. Servern kan komprometteras av en illvillig aktör. | Kritiskt |
| ICMP-översvämningar | En onormal mängd paket upptäcktes i nätverket. Detta kan tyda på en attack, till exempel ett ARP-förfalskning eller en ICMP-attack som översvämmar. | Varning |
| Ogiltigt HTTP-huvudinnehåll | Källenheten initierade en ogiltig begäran. | Kritiskt |
| Inaktiv kommunikationskanal | En kommunikationskanal mellan två enheter var inaktiv under en period då aktiviteten vanligtvis visas. Detta kan tyda på att programmet som genererar den här trafiken har ändrats eller att programmet kanske inte är tillgängligt. Vi rekommenderar att du granskar konfigurationen av det installerade programmet och kontrollerar att det är korrekt konfigurerat. | Varning |
| Adressgenomsökning med lång varaktighet har identifierats | En källenhet upptäcktes genomsökning av nätverksenheter. Den här enheten har inte auktoriserats som en enhet för nätverksgenomsökning. | Kritiskt |
| Ett försök till lösenords gissande har identifierats | En källenhet sågs utföra omfattande inloggningsförsök till en målserver. Det här kan vara en råstyrkattack. Servern kan komprometteras av en illvillig aktör. | Kritiskt |
| HAR IDENTIFIERATS | En källenhet upptäcktes genomsökning av nätverksenheter. Den här enheten har inte auktoriserats som en enhet för nätverksgenomsökning. | Kritiskt |
| Portgenomsökning har identifierats | En källenhet upptäcktes genomsökning av nätverksenheter. Den här enheten har inte auktoriserats som en enhet för nätverksgenomsökning. | Kritiskt |
| Oväntad meddelandelängd | Källenheten skickade ett onormalt meddelande. Detta kan tyda på ett försök att angripa målenheten. | Kritiskt |
| Oväntad trafik för standardport | Trafik upptäcktes på en enhet med hjälp av en port som är reserverad för ett annat protokoll. | Större |

## <a name="protocol-violation-engine-alerts"></a>Aviseringar för protokollöverträdelsemotor

Aviseringar från protokollmotorn beskriver identifierade avvikelser i paketstrukturen eller fältvärden jämfört med protokollspecifikationer.

| Rubrik | Beskrivning | Allvarlighetsgrad |
|--|--|--|
| För många felaktiga paket i en enda session | Ett onormalt antal felaktiga paket som skickats från källenheten till målenheten. Detta kan tyda på felaktig kommunikation eller ett försök att manipulera målenheten. | Större |
| Uppdatering av inbyggd programvara | En källenhet skickade ett kommando för att uppdatera den inbyggda programvaran på en målenhet. Kontrollera att de senaste uppgraderingarna av programmering, konfiguration och inbyggd programvara som gjorts till målenheten är giltiga. | Varning |
| Funktionskod stöds inte av outstation | Målenheten tog emot en ogiltig begäran. | Större |
| Ogiltigt BACNet-meddelande | Källenheten initierade en ogiltig begäran. | Större |
| Ogiltigt anslutningsförsök på port 0 | En källenhet försökte ansluta till målenheten på portnummer noll (0). För TCP är port 0 reserverad och kan inte användas. För UDP är porten valfri och värdet 0 innebär att det inte finns någon port. Det finns vanligtvis ingen tjänst i ett system som lyssnar på port 0. Den här händelsen kan tyda på ett försök att angripa målenheten eller ange att ett program har programmerats felaktigt. | Mindre |
| Ogiltig DNP3-åtgärd | Källenheten initierade en ogiltig begäran. | Större |
| Ogiltig MODBUS-åtgärd (undantag som utlöses av huvudhanterare) | Källenheten initierade en ogiltig begäran. | Större |
| Ogiltig MODBUS-åtgärd (funktionskod noll) | Källenheten initierade en ogiltig begäran. | Större |
| Ogiltig protokollversion | Källenheten initierade en ogiltig begäran. | Större |
| Felaktig parameter skickades till utstation | Målenheten tog emot en ogiltig begäran. | Större |
| Initiering av en föråldrad funktionskod (initiera data) | Källenheten initierade en ogiltig begäran. | Mindre |
| Initiering av en föråldrad funktionskod (Spara konfiguration) | Källenheten initierade en ogiltig begäran. | Mindre |
| Master begärde en bekräftelse på programlagret | Källenheten initierade en ogiltig begäran. | Varning |
| Modbus-undantag | En källenhet (undermål) returnerade ett undantag till en målenhet (master). | Större |
| Svart enhet mottagen ogiltig ASDU-typ | Målenheten tog emot en ogiltig begäran. | Större |
| Den underrede enheten tog emot ett ogiltigt kommando som orsakar överföringen | Målenheten tog emot en ogiltig begäran. | Större |
| Den underrede enheten har tagit emot en ogiltig gemensam adress | Målenheten tog emot en ogiltig begäran. | Större |
| Parametern Otillåten dataadress togs emot av den underdrigt mottagna enheten | Målenheten tog emot en ogiltig begäran. | Större |
| Parametern För ogiltigt datavärde togs emot av den underdringsenhet som togs emot | Målenheten tog emot en ogiltig begäran. | Större |
| Otillåten funktionskod togs emot av den underifikna enheten | Målenheten tog emot en ogiltig begäran. | Större |
| Otillåten informationsobjektsadress mottagen från en underlig enhet | Målenheten tog emot en ogiltig begäran. | Större |
| Okänt objekt som skickats till utstation | Målenheten tog emot en ogiltig begäran. | Större |
| Användning av en reserverad funktionskod | Källenheten initierade en ogiltig begäran. | Större |
| Användning av felaktig formatering efter utstation | Källenheten initierade en ogiltig begäran. | Varning |
| Användning av flaggor för reserverad status (IIN) | En DNP3-källenhet (utstation) använde den reserverade interna indikatorn 2.6. Vi rekommenderar att du kontrollerar enhetens konfiguration. | Varning |

## <a name="malware-engine-alerts"></a>Aviseringar från motorn för skadlig kod

Aviseringar från motorn för skadlig kod beskriver upptäckt skadlig nätverksaktivitet.

| Rubrik | Beskrivning| Allvarlighetsgrad |
|--|--|--|
| Anslutningsförsök till känd skadlig IP-adress | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Större |
| Ogiltigt SMB-meddelande (DoublePulsar-bakdörr) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Begäran om skadligt domännamn | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Större |
| Testfil för skadlig kod har identifierats – EICAR AV Success | En EICAR AV-testfil upptäcktes i trafik mellan två enheter. Filen är inte skadlig kod. Den används för att bekräfta att antivirusprogrammet är korrekt installerat. visa vad som händer när ett virus hittas och kontrollera interna procedurer och reaktioner när ett virus hittas. Antivirusprogram bör identifiera EICAR som om det vore ett verkligt virus. | Större |
| Misstänkt skadlig Conficker-kod | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Större |
| Misstänkt DoS-attack | En källenhet försökte initiera ett stort antal nya anslutningar till en målenhet. Detta kan vara en DoS-attack (Denial Of Service) mot målenheten och kan avbryta enhetsfunktioner, påverka prestanda och tjänstens tillgänglighet eller orsaka oåterkalleliga fel. | Kritiskt |
| Misstänkt skadlig aktivitet | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Större |
| Misstänkt skadlig aktivitet (BlackEnergy) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (DarkComet) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (Duqu) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (FSE) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (Havex) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (Cargany) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (LightsOut) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (namnfrågor) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Större |
| Misstänkt skadlig aktivitet (skadlig ivy) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (Regin) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (Stuxnet) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt skadlig aktivitet (WannaCry) | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan associeras med en attack som utnyttjar en metod som används av känd skadlig kod. | Större |
| Misstänkt skadlig kod i NotPetya – Ogiltiga SMB-parametrar har identifierats | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstänkt notPetya-skadlig kod – ogiltig SMB-transaktion har identifierats | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |
| Misstanke om fjärrkörning av kod med PsExec | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Större |
| Misstanke om fjärrhantering av Windows-tjänster | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Större |
| Misstänkt körbar fil har identifierats på slutpunkten | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Större |
| Misstänkt trafik har identifierats | Misstänkt nätverksaktivitet har identifierats. Den här aktiviteten kan vara associerad med en attack som utnyttjar en metod som används av känd skadlig kod. | Kritiskt |

## <a name="operational-engine-alerts"></a>Aviseringar för driftmotor

Aviseringar från driftmotorn beskriver identifierade driftincidenter eller entiteter som inte fungerar.

| Rubrik | Beskrivning | Allvarlighetsgrad |
|--|--|--|
| Ett S7-stoppKOMMANDO skickades | Källenheten skickade ett stoppkommando till en målstyrenhet. Kontrollanten slutar fungera tills ett startkommando skickas. | Varning |
| BACNet-åtgärden misslyckades | En server returnerade en felkod. Detta indikerar ett serverfel eller en ogiltig begäran från en klient. | Större |
| Felaktigt MMS-enhetstillstånd | En MMS Virtual Manufacturing Device (VMD) skickade ett statusmeddelande. Meddelandet anger att servern kanske inte är korrekt konfigurerad, delvis i drift eller inte fungerar alls. | Större |
| Ändring av enhetskonfiguration | En konfigurationsändring har identifierats på en källenhet. | Mindre |
| Kontinuerligt händelsebuffertspill vid utstation | En buffertspillshändelse upptäcktes på en källenhet. Händelsen kan orsaka skadade data, programkrasch eller körning av skadlig kod. | Större |
| Kontrollantåterställning | En källenhet skickade ett återställningskommando till en målstyrenhet. Kontrollanten slutade fungera tillfälligt och startades igen automatiskt. | Varning |
| Kontrollantstopp | Källenheten skickade ett stoppkommando till en målstyrenhet. Kontrollanten slutar fungera tills ett startkommando skickas. | Varning |
| Enheten kunde inte ta emot en dynamisk IP-adress | Källenheten är konfigurerad för att ta emot en dynamisk IP-adress från en DHCP-server men fick ingen adress. Detta anger ett konfigurationsfel på enheten eller ett driftfel på DHCP-servern. Vi rekommenderar att du meddelar nätverksadministratören om incidenten | Större |
| Enheten misstänks vara frånkopplad (svarar inte) | En källenhet svarade inte på ett kommando som skickades till den. Det kan ha kopplats från när kommandot skickades. | Större |
| EtherNet/IP CIP-tjänstbegäran misslyckades | En server returnerade en felkod. Detta indikerar ett serverfel eller en ogiltig begäran från en klient. | Större |
| Kommandot EtherNet/IP Encapsulation Protocol misslyckades | En server returnerade en felkod. Detta indikerar ett serverfel eller en ogiltig begäran från en klient. | Större |
| Händelsebuffert spill i utstation | En buffertspillshändelse har identifierats på en källenhet. Händelsen kan orsaka skadade data, programkrasch eller körning av skadlig kod. | Större |
| Den förväntade säkerhetskopieringen har inte inträffat | Förväntad säkerhetskopierings-/filöverföringsaktivitet inträffar inte mellan två enheter. Detta kan tyda på fel i processen för säkerhetskopiering/filöverföring. | Större |
| Fel med GE SRTP-kommando | En server returnerade en felkod. Detta indikerar ett serverfel eller en ogiltig begäran från en klient. | Större |
| KOMMANDOT GE SRTP StopKOMMANDO skickades | Källenheten skickade ett stoppkommando till en målstyrenhet. Kontrollanten slutar fungera tills ett startkommando skickas. | Varning |
| CONTROL-kontrollblocket kräver ytterligare konfiguration | En källenhet skickade ett MEDDELANDE AVSV som anger att enheten behöver i uppdragsningen. Det innebär att KONTROLLBLOCK-kontrollblocket kräver ytterligare konfiguration och ATT MEDDELANDENa delvis eller helt inte fungerar. | Större |
| DATASET-datauppsättningskonfigurationen har ändrats | Ett meddelande (identifierat av protokoll-ID) har ändrats på en källenhet. Det innebär att enheten rapporterar en annan datamängd för det här meddelandet. | Varning |
| Oväntad status för Honeywell-kontrollanten | En Honeywell-kontrollant skickade ett oväntat diagnostikmeddelande som anger en statusändring. | Varning |
| HTTP-klientfel | Källenheten initierade en ogiltig begäran. | Varning |
| Ogiltig IP-adress | Systemet har identifierat trafik mellan en källenhet och en IP-adress som är en ogiltig adress. Detta kan tyda på fel konfiguration eller ett försök att generera ogiltig trafik. | Mindre |
| Master-Slave för autentisering | Autentiseringsprocessen mellan en DNP3-källenhet (master) och en målenhet (utstation) misslyckades. | Mindre |
| MMS-tjänstbegäran misslyckades | En server returnerade en felkod. Detta indikerar ett serverfel eller en ogiltig begäran från en klient. | Större |
| Ingen trafik har identifierats i sensorgränssnittet | En sensor slutade identifiera nätverkstrafik i ett nätverksgränssnitt. | Kritiskt |
| OPC UA-servern har utlyst en händelse som kräver användarens uppmärksamhet | En OPC UA-server skickade ett händelsemeddelande till en klient. Den här typen av händelse kräver användar uppmärksamhet | Större |
| OPC UA-tjänstbegäran misslyckades | En server returnerade en felkod. Detta indikerar ett serverfel eller en ogiltig begäran från en klient. | Större |
| Utstation har startats om | En kall omstart upptäcktes på en källenhet. Det innebär att enheten var fysiskt avstängd och återigen aktiverad. | Varning |
| Utstation startas om ofta | Ett stort antal kallstarter har identifierats på en källenhet. Det innebär att enheten stängdes av fysiskt och återigen återigen ett stort antal gånger. | Mindre |
| Utstationeringens konfiguration har ändrats | En konfigurationsändring har identifierats på en källenhet. | Större |
| Utstationeringens skadade konfiguration har identifierats | Denna DNP3-källenhet (utstation) rapporterade en skadad konfiguration. | Större |
| Profinet DCP-kommandot misslyckades | En server returnerade en felkod. Detta indikerar ett serverfel eller en ogiltig begäran från en klient. | Större |
| Profinet Device Factory Reset | En källenhet skickade ett fabriksåterställningskommando till en Profinet-målenhet. Återställningskommandot rensar Profinet-enhetskonfigurationer och stoppar åtgärden. | Varning |
| RPC-åtgärden misslyckades | En server returnerade en felkod. Detta indikerar ett serverfel eller en ogiltig begäran från en klient. | Större |
| Konfiguration av exempelvärden– meddelandedatauppsättning har ändrats | Ett meddelande (identifierat av protokoll-ID) har ändrats på en källenhet. Det innebär att enheten rapporterar en annan datamängd för det här meddelandet. | Varning |
| Oåterkalleligt fel för den obevakade enheten | Ett oåterkalleligt tillståndsfel upptäcktes på en källenhet. Den här typen av fel indikerar vanligtvis ett maskinvarufel eller att det inte går att utföra ett visst kommando. | Större |
| Misstänkt maskinvaruproblem vid outstation | Ett oåterkalleligt tillståndsfel upptäcktes på en källenhet. Den här typen av fel indikerar vanligtvis ett maskinvarufel eller att det inte går att utföra ett visst kommando. | Större |
| Misstänkt MODBUS-enhet som inte svarar | En källenhet svarade inte på ett kommando som skickades till den. Det kan ha kopplats från när kommandot skickades. | Mindre |
| Trafik som identifierats i sensorgränssnittet | En sensor återupptog identifieringen av nätverkstrafik i ett nätverksgränssnitt. | Varning |

## <a name="next-steps"></a>Nästa steg

Du kan [hantera aviseringshändelser.](how-to-manage-the-alert-event.md)
Lär dig hur du [vidarebefordrar aviseringsinformation](how-to-forward-alert-information-to-partners.md).
