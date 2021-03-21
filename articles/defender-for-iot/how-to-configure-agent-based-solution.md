---
title: Konfigurera Azure Defender för IoT agent-baserad lösning
description: Lär dig hur du konfigurerar data insamling i Azure Defender för IoT agent-baserad lösning
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e32b9c690db70baeda7dec2f9f1270f82c6881bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733303"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Konfigurera Azure Defender för IoT agent-baserad lösning  

Den här artikeln beskriver hur du konfigurerar data insamling i Azure Defender för IoT agent-baserad lösning.

## <a name="configure-data-collection"></a>Konfigurera datainsamling

Så här konfigurerar du data insamling i Azure Defender för IoT agent-baserad lösning: 

1. Navigera till Azure Portal och välj IoT Hub som Defender for IoT är kopplat till 

1. Under menyn  **säkerhet**   väljer du **Inställningar**. 

1. Välj **data insamling**. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Välj data insamling i säkerhets meny inställningarna.":::

## <a name="geolocation-and-ip-address-handling"></a>Hantering av geolokalisering och IP-adresser 

För att skydda din IoT-lösning, samlas IP-adresserna för de inkommande och utgående anslutningarna för dina IoT-enheter, IoT Edge och IoT Hub (ar) in och lagras som standard. Den här informationen är viktig och används för att identifiera onormal anslutning från misstänkta IP-adresser. Till exempel när försök görs att upprätta anslutningar från en IP-adress källa för en känd botnät, eller från en IP-datakälla utanför din plats. Tjänsten Defender for IoT erbjuder flexibiliteten att aktivera och inaktivera insamling av IP-Datadata när som helst. 

Aktivera eller inaktivera samlingen av IP-adress data: 

1. Öppna din IoT Hub och välj sedan **Inställningar**   på menyn **säkerhet**   . 

1. Välj skärmen **data insamling**   och ändra geolokalisering och inställningar för IP-adress hantering så att de passar dina behov. 

## <a name="log-analytics-creation"></a>Skapa Log Analytics 

Med Defender för IoT kan du lagra säkerhets aviseringar, rekommendationer och rå säkerhets data i din Log Analytics-arbetsyta. Log Analytics inmatning i IoT Hub är inställt på **av som standard** i Defender for IoT-lösningen. Det är möjligt att bifoga Defender för IoT till en logg analys arbets yta och att lagra säkerhets data där. 

Det finns två typer av information som lagras som standard i din Log Analytics-arbetsyta av Defender för IoT:
 
- Säkerhets aviseringar.

- Rekommenderade. 

Du kan välja att lägga till lagring av ytterligare en informations typ som `raw events` . 

> [!Note] 
> Lagring  `raw events`   i Log Analytics medför ytterligare lagrings kostnader. 

Så här aktiverar du Log Analytics att arbeta med Micro agent: 

1. Navigera till **konfigurations**  >  **data insamling** för arbets yta och växla till  **på**. 

1. Skapa en ny Log Analytics arbets yta eller bifoga en befintlig. 

1. Kontrol lera att alternativet **åtkomst till säkerhets data för RAW**   är markerat.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Se till att åtkomsten till rå data är markerad.":::

1. Välj **Spara**.

Varje månad är de första 5 gigabyte data som matas in per kund till Azure Log Analytics-tjänsten kostnads fritt. Varje gigabyte data som matas in i din Azure Log Analytics-arbetsyta behålls utan kostnad under de första 31 dagarna. Mer information om priser finns i [Log Analytics prissättning](https://azure.microsoft.com/pricing/details/monitor/). 

Ändra arbets ytans konfiguration för Log Analytics: 

1. Välj  **Inställningar** i menyn **säkerhet** i IoT Hub. 

1. Välj skärmen **data insamling**   och ändra arbets ytans konfiguration för Log Analytics inställningarna så att de passar dina behov. 

Så här får du åtkomst till aviseringar i arbets ytan Log Analytics efter konfigurationen:

1. Välj en avisering i Defender för IoT.

1. Välj **Undersök aviseringar i Log Analytics arbets ytan**.

Så här får du åtkomst till aviseringar i arbets ytan Log Analytics efter konfigurationen:

1. Välj en rekommendation i Defender för IoT.

1. Välj **Undersök rekommendationer i Log Analytics arbets yta**. 
 
Mer information om att köra frågor mot data från Log Analytics finns i [Kom igång med frågor i Log Analytics](../azure-monitor/logs/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>Inaktivera Defender för IoT 

Aktivera en Defender for IoT-tjänst på eller av på en bestämd IoT Hub: 

1. Välj  **Inställningar** i menyn **säkerhet** i IoT Hub.

1. Välj skärmen **data insamling**   och ändra arbets ytans konfiguration för Log Analytics inställningarna så att de passar dina behov.

## <a name="next-steps"></a>Nästa steg 

Fortsätt till nästa artikel för att [Konfigurera din lösning](quickstart-configure-your-solution.md).