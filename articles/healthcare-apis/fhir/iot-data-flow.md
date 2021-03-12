---
title: 'Begrepp: data flöde i Azure IoT Connector för FHIR (för hands version) i Azure API för FHIR'
description: Förstå Azure IoT Connector för FHIR (förhands granskning) data flödet. Azure IoT Connector för FHIR (för hands version) matar in, normaliserar, grupper, transformerar och behåller IoMT data till Azure API för FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: dc20d4cc54824fc65eba6669eab9a1c32d7ef9eb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019675"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>Azure IoT-anslutningsprogram för FHIR (förhandsversion) – dataflöde

Den här artikeln innehåller en översikt över data flödet i Azure IoT Connector för snabba hälso-och sjukvårds resurser (FHIR&#174;) *. Du lär dig mer om olika data bearbetnings faser i Azure IoT Connector för FHIR som omvandlar enhets data till FHIR-baserade [observations](https://www.hl7.org/fhir/observation.html) resurser.

![Azure IoT-anslutning för FHIR data flöde](media/concepts-iot-data-flow/iot-connector-data-flow.png)

Diagrammet ovan visar vanliga data flöden med Azure IoT Connector för FHIR. 

Nedan visas olika steg som data går igenom när de tas emot av Azure IoT Connector för FHIR.

## <a name="ingest"></a>Mata in
Inmatning är det första steget där enhets data tas emot i Azure IoT Connector för FHIR. Inmatnings slut punkten för enhets data finns i en [Azure Event Hub](../../event-hubs/index.yml). Azure Event Hub-plattformen stöder hög skalning och data flöde med möjlighet att ta emot och bearbeta miljon tals meddelanden per sekund. Det gör det också möjligt för Azure IoT Connector för FHIR att använda meddelanden asynkront, vilket tar bort behovet av enheter för att vänta medan enhets data bearbetas.

> [!NOTE]
> JSON är det enda format som stöds för tillfället för enhets data.

## <a name="normalize"></a>Normalisera
Normalisering är nästa steg där enhets data hämtas från ovanstående Azure Event Hub och bearbetas med hjälp av enhets mappnings mallar. Den här mappnings processen resulterar i omvandling av enhets data till ett normaliserat schema. 

Normaliserings processen fören klar inte bara data bearbetning i senare skeden, men ger även möjlighet att projicera ett indatameddelande i flera normaliserade meddelanden. Till exempel kan en enhet skicka flera viktiga tecken för text temperatur, puls frekvens, blod tryck och respiration frekvens i ett enda meddelande. Detta indatameddelande skulle skapa fyra separata FHIR-resurser. Varje resurs representerar olika viktiga tecken, med indatamängden projiceras i fyra olika normaliserade meddelanden.

## <a name="group"></a>Group
Grupp är nästa steg där de normaliserade meddelanden som är tillgängliga från föregående steg grupperas med hjälp av tre olika parametrar: enhets identitet, mätnings typ och tids period.

Gruppering av enhets identitet och mått typ möjliggör användning av [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) -mått typ. Den här typen är ett koncist sätt att representera en tidsbaserad serie mätningar från en enhet i FHIR. Tids perioden styr svars tiden då observations resurser som genereras av Azure IoT Connector för FHIR skrivs till Azure API för FHIR.

> [!NOTE]
> Värdet för tids perioden är standard 15 minuter och kan inte konfigureras för för hands versionen.

## <a name="transform"></a>Transformering
I Transformations skedet bearbetas grupperade, normaliserade meddelanden genom FHIR mappar. Meddelanden som matchar en malltyp omvandlas till FHIR observations resurser som anges genom mappningen.

I det här läget hämtas även [enhets](https://www.hl7.org/fhir/device.html) resursen, tillsammans med tillhör ande [patient](https://www.hl7.org/fhir/patient.html) resurs, från FHIR-servern med hjälp av enhets-ID: n som finns i meddelandet. Dessa resurser läggs till som en referens till observations resursen som skapas.

> [!NOTE]
> Alla identiteter som ser ut cachelagras när de har lösts för att minska belastningen på FHIR-servern. Om du planerar att återanvända enheter med flera patienter rekommenderar vi att du skapar en virtuell enhets resurs som är unik för patienten och skickar den virtuella enhets identifieraren i meddelande nytto lasten. Den virtuella enheten kan länkas till den faktiska enhets resursen som överordnad.

Om det inte finns någon enhets resurs för en angiven enhets identifierare i FHIR-servern beror resultatet av värdet i `Resolution Type` uppsättningen vid tidpunkten för skapandet. När det är inställt på `Lookup` , ignoreras det angivna meddelandet och pipelinen fortsätter att bearbeta andra inkommande meddelanden. Om det är inställt på `Create` , skapar Azure IoT-anslutaren för FHIR en enhet med inga ben och patient resurser på FHIR-servern.  

## <a name="persist"></a>Försöker
När observations FHIR-resursen har genererats i Transformations fasen sparas resursen i Azure API för FHIR. Om FHIR-resursen är ny kommer den att skapas på servern. Om FHIR-resursen redan finns kommer den att uppdateras.

## <a name="next-steps"></a>Nästa steg

Klicka nedan för att lära dig hur du skapar mallar för enhets-och FHIR mappar.

>[!div class="nextstepaction"]
>[FHIR för Azure IoT Connector för mappning av](iot-mapping-templates.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version). FHIR är ett registrerat varumärke som tillhör HL7 och används med behörigheten för HL7. 