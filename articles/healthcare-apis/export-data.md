---
title: Köra exporten genom att anropa $export kommandot på Azure API för FHIR
description: Den här artikeln beskriver hur du exporterar FHIR-data med $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 48dbd0892c9ec02f203edba55d1104f1ab0118a8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737616"
---
# <a name="how-to-export-fhir-data"></a>Så här exporterar du FHIR-data


Mass export funktionen gör att data kan exporteras från FHIR-servern enligt FHIR- [specifikationen](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Innan du använder $export bör du se till att Azure API för FHIR är konfigurerat för att använda det. Information om hur du konfigurerar export inställningar och skapar Azure Storage-konton finns på [sidan Konfigurera export data](configure-export-data.md).

## <a name="using-export-command"></a>Använda $export kommandot

När du har konfigurerat Azure API för FHIR för export kan du använda kommandot $export för att exportera data från tjänsten. Data lagras i det lagrings konto du angav när du konfigurerade exporten. Information om hur du anropar $export kommandot i FHIR-servern finns i dokumentationen om [HL7 FHIR $export Specification](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Azure API för FHIR har stöd för $export på följande nivåer:
* [System](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Patient](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Grupp av patienter *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) – Azure API för FHIR exporterar alla relaterade resurser men exporterar inte egenskaperna för gruppen: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export` och `Group/[ID]/$export` kan exportera dubbla resurser om resursen finns i ett utrymme med fler än en resurs eller finns i flera grupper.

Dessutom stöds att kontrol lera export statusen via URL: en som returnerades av plats rubriken under köerna, tillsammans med att avbryta det faktiska export jobbet.

## <a name="settings-and-parameters"></a>Inställningar och parametrar

### <a name="headers"></a>Sidhuvuden
Det finns två obligatoriska huvud parametrar som måste anges för $export-jobb. Värdena definieras av den aktuella [$export specifikationen](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Acceptera** -Application/FHIR + JSON
* **Föredra** – svara-asynkron

### <a name="query-parameters"></a>Frågeparametrar
Azure API för FHIR stöder följande frågeparametrar. Alla dessa parametrar är valfria:
|Frågeparameter        | Definieras av FHIR-specifikationen?    |  Beskrivning|
|------------------------|---|------------|
| \_outputFormat | Ja | Stöder för närvarande tre värden för att justeras till FHIR-specifikationen: Application/FHIR + ndjson, Application/ndjson eller bara ndjson. Alla export jobb kommer att returneras `ndjson` och det skickade värdet påverkar inte kod beteendet. |
| \_Starta | Ja | Gör att du endast kan exportera resurser som har ändrats sedan den angivna tiden |
| \_bastyp | Ja | Gör att du kan ange vilka typer av resurser som ska tas med. Skriv till exempel \_ = patient returnerar endast patient resurser|
| \_typefilter | Ja | Om du vill begära detaljerad filtrering kan du använda \_ typeFilter tillsammans med \_ typ parametern. Värdet för parametern _typeFilter är en kommaavgränsad lista över FHIR-frågor som ytterligare begränsar resultaten |
| \_fönster | Nej |  Anger behållaren i det konfigurerade lagrings kontot där data ska exporteras. Om en behållare anges exporteras data till den behållaren i en ny mapp med namnet. Om behållaren inte anges exporteras den till en ny behållare med hjälp av timestamp och jobb-ID. |


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du exporterar FHIR-resurser med hjälp av kommandot $export. Sedan kan du läsa mer om hur du exporterar de data som identifieras:
 
>[!div class="nextstepaction"]
>[Exportera de data som har identifierats](de-identified-export.md)
