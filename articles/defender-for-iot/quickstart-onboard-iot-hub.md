---
title: 'Snabb start: aktivera tjänsten'
description: Lär dig hur du integrerar och aktiverar tjänsten Defender för IoT-säkerhet i Azure-IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e3768ef233c60f1687bc804778c3dabf32666e1d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835167"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>Snabb start: publicera Azure Defender för IoT-tjänsten i IoT Hub

Den här artikeln innehåller en förklaring av hur du aktiverar tjänsten Defender för IoT på din befintliga IoT Hub. Om du för närvarande inte har en IoT Hub kan du läsa [skapa ett IoT Hub med hjälp av Azure Portal](../iot-hub/iot-hub-create-through-portal.md) för att komma igång.

> [!NOTE]
> Defender for IoT stöder för närvarande bara IoT-hubbar på standard nivå.

## <a name="prerequisites-for-enabling-the-service"></a>Krav för att aktivera tjänsten

- Log Analytics-arbetsyta
  - Två typer av information lagras som standard i din Log Analytics-arbetsyta av Defender for IoT; **säkerhets aviseringar** och **rekommendationer**.
  - Du kan välja att lägga till lagring av ytterligare en informations typ, **rå händelser**. Observera att lagring av **rå händelser** i Log Analytics medför ytterligare lagrings kostnader.
- IoT Hub (standard-nivå)
- Uppfyller alla [system krav](quickstart-system-prerequisites.md).

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>Aktivera Defender för IoT på din IoT Hub

Så här aktiverar du säkerhet på IoT Hub:

1. Öppna din **IoT Hub** i Azure Portal.
1. Under menyn **säkerhet** klickar du på **skydda din IoT-lösning**.

Grattis! Du har slutfört aktiveringen av Defender för IoT på din IoT Hub.

### <a name="geolocation-and-ip-address-handling"></a>Hantering av geolokalisering och IP-adresser

För att skydda din IoT-lösning kan IP-adresser för inkommande och utgående anslutningar till och från dina IoT-enheter, IoT Edge och IoT Hub samlas in och lagras som standard. Den här informationen är nödvändig för att identifiera onormal anslutning från misstänkta IP-källor. Till exempel när försök görs att upprätta anslutningar från en IP-källa för en känd botnät eller från en IP-källa utanför din plats. Defender för IoT-tjänsten ger dig möjlighet att aktivera och inaktivera insamling av IP-Datadata när som helst.

Aktivera eller inaktivera insamling av IP-adress data:

1. Öppna din IoT Hub och välj sedan **Inställningar** på menyn **säkerhet** .
1. Välj skärmen **data insamling** och ändra inställningarna för geolokalisering och/eller IP-hantering som du vill.

### <a name="log-analytics-creation"></a>Skapa Log Analytics

När Defender för IoT är aktiverat skapas en standard arbets yta i Azure Log Analytics som lagrar säkerhets händelser, aviseringar och rekommendationer för dina IoT-enheter, IoT Edge och IoT Hub. Varje månad är de första fem (5) GB data som matas in per kund till Azure Log Analytics-tjänsten kostnads fritt. Varje GB data som matas in i Azure Log Analytics-arbetsytan behålls utan kostnad under de första 31 dagarna. Läs mer om [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) prissättning.

Ändra arbets ytans konfiguration för Log Analytics:

1. Öppna din IoT Hub och välj sedan **Inställningar** på menyn **säkerhet** .
1. Välj skärmen **data insamling** och ändra arbets ytans konfiguration för Log Analytics inställningar som du vill.

### <a name="customize-your-iot-security-solution"></a>Anpassa din IoT-säkerhetslösning

Som standard säkrar automatiskt alla IoT-hubbar i Azure-prenumerationen genom att aktivera Defender for IoT-lösningen.

Aktivera och inaktivera Defender för IoT-tjänsten på en bestämd IoT Hub:

1. Öppna din IoT Hub och välj sedan **Inställningar** på menyn **säkerhet** .
1. Välj skärmen **data insamling** och ändra säkerhets inställningarna för alla IoT-hubbar i din Azure-prenumeration som du vill.

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel för att konfigurera din lösning...

> [!div class="nextstepaction"]
> [Konfigurera lösningen](quickstart-configure-your-solution.md)