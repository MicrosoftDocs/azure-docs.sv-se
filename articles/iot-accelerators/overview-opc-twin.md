---
title: Vad är OPC med dubbla – Azure | Microsoft Docs
description: 'Den här artikeln innehåller en översikt över OPC, dubbla. OPC dubbla ger identifiering, registrering och fjärrkontroll av industriella enheter via REST-API: er.'
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9825e5e85c8da27b0f3fc2b9d78fcc9ca3513389
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646953"
---
# <a name="what-is-opc-twin"></a>Vad är OPC?

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

OPC garn består av mikrotjänster som använder Azure IoT Edge och IoT Hub för att ansluta molnet och fabriks nätverket. OPC dubbla ger identifiering, registrering och fjärrkontroll av industriella enheter via REST-API: er. OPC fläta kräver inte en OPC-SDK (Unified Architecture), är programmerings språk oberoende och kan ingå i ett Server lös arbets flöde. I den här artikeln beskrivs flera OPCs dubbla användnings fall.

## <a name="discovery-and-control"></a>Identifiering och kontroll
Du kan använda OPC-dubbla för enkel identifiering och registrering.

### <a name="simple-discovery-and-registration"></a>Enkel identifiering och registrering
OPC med dubbla ger fabriks operatörer möjlighet att söka igenom fabriks nätverket så att OPC UA-servrar kan identifieras och registreras. Som ett alternativ kan fabriks operatörer även registrera OPC UA-enheter manuellt med hjälp av en känd identifierings-URL. Om du till exempel vill ansluta till alla OPC UA-enheter efter att IoT Edge Gateway med en OPC-multimodul har installerats på fabriks golvet, kan fabriks operatören fjärrutlösa en sökning i nätverket och visuellt se alla OPC UA-servrar. 

### <a name="simple-control"></a>Enkel kontroll
OPC med dubbla ger fabriks operatörerna möjlighet att reagera på händelser och omkonfigurera sina fabriks maskiner från molnet antingen automatiskt eller manuellt i farten. OPC dubbla tillhandahåller REST-API: er för att anropa tjänster på OPC UA-servern, bläddra i adress utrymmet och läsa/skriva variabler och köra metoder. En panna använder till exempel temperatur-KPI för att kontrol lera produktions linjen. Temperatur sensorn publicerar ändringen i data med hjälp av OPC Publisher. Fabriks operatören tar emot en avisering om att temperaturen har nått tröskelvärdet. Produktions linjen svalnar automatiskt genom OPC. Fabriks operatören meddelas om den nedkylningen.

## <a name="authentication"></a>Autentisering
Du kan använda OPC-delad för enkel autentisering och för en enkel utvecklare.

### <a name="simple-authentication"></a>Enkel autentisering 
OPCs dubbla använder Azure Active Directory (AAD)-baserad autentisering och granskning från slut punkt till slut punkt. Till exempel kan OPC dubbla göra att programmet kan byggas ovanpå OPC, så att du kan avgöra vilken operatör som har utförts på en dator. På dator sidan är det genom OPC UA-granskning. På moln sidan är det genom att lagra en oföränderlig klient Gransknings logg och AAD-autentisering på REST API.

### <a name="simple-developer-experience"></a>Enkel utvecklings upplevelse 
OPCs dubbla kan användas med program som skrivits i programmeringsspråk via REST-API: er. Eftersom utvecklare integrerar en OPC UA-klient i en lösning behövs inte kunskap om OPC UA SDK. OPC skarv kan integreras sömlöst i tillstånds lösa, Server lösa arkitekturer. Till exempel kan en full stack-webbutvecklare som utvecklar ett program för en alarm-och händelse instrument panel skriva logiken för att svara på händelser i Java Script eller TypeScript med OPC dubbla utan kunskap om C, C# eller den fullständiga OPC UA stack-implementeringen. 

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om OPC och dess användnings områden är det föreslagna nästa steg:

[Vad är OPC-valv](overview-opc-vault.md)