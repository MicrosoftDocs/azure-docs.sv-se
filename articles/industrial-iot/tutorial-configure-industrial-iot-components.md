---
title: Konfigurera Azures industriella IoT-komponenter
description: I den här självstudien får du lära dig hur du ändrar standardvärdena för konfigurationen.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787751"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Självstudie: Konfigurera industriella IoT-komponenter

Distributions skriptet konfigurerar automatiskt alla komponenter så att de fungerar med hjälp av standardvärden. Inställningarna för standardvärdena kan dock ändras för att uppfylla dina krav.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Anpassa konfigurationen av komponenterna


Här följer några av de mer relevanta anpassnings inställningarna för komponenterna:
* IoT Hub
    * Nätverk → offentlig åtkomst: Konfigurera Internet åtkomst, till exempel IP-filter
    * Nätverk → anslutningar för privata slut punkter: skapa en slut punkt som inte är tillgänglig via Internet och som kan förbrukas internt av andra Azure-tjänster eller lokala enheter (till exempel via en VPN-anslutning)
    * IoT Edge: hantera konfigurationen av gräns enheterna som är anslutna till OPC UA-servrar 
* Cosmos DB
    * Replikera data globalt: Konfigurera data-redundans
    * Brand vägg och virtuella nätverk: Konfigurera Internet-och VNET-åtkomst och IP-filter
    * Anslutningar för privata slut punkter: skapa en slut punkt som inte är tillgänglig via Internet 
* Key Vault
    * Hemligheter: hantera plattforms inställningar
    * Åtkomst principer: hantera vilka program och användare som kan komma åt data i Key Vault och vilka åtgärder (till exempel läsa, skriva, lista, ta bort) som de får utföra i nätverket, brand väggen, VNET och privata slut punkter
* Azure Active Directory (AAD) → Appregistreringar
    * <APP_NAME>-Web → autentisering: hantera svars-URI: er, som är en lista över URI: er som kan användas som landnings sidor när autentiseringen har slutförts. Distributions skriptet kanske inte kan konfigurera detta automatiskt under vissa scenarier, t. ex. utebliven AAD admin-rättigheter. Du kanske vill lägga till eller ändra URI: er när du ändrar värd namnet för webbappen, till exempel port numret som används av localhost för fel sökning
* App Service
    * Konfiguration: hantera de miljövariabler som styr tjänsterna eller användar gränssnittet
* Virtuell dator
    * Nätverk: Konfigurera nätverk och brand Väggs regler som stöds
    * Seriell konsol: SSH-åtkomst för att få insikter eller för fel sökning, hämta autentiseringsuppgifterna från utdata från distributions skript eller Återställ lösen ordet
* IoT Hub → IoT Edge
    * Hantera identiteterna för de IoT Edge enheter som kan komma åt hubben, konfigurera vilka moduler som är installerade och vilken konfiguration de använder, till exempel kodnings parametrar för OPC-utgivaren
* IoT Hub → IoT Edge → \<DEVICE> → set modules → OpcPublisher (endast för fristående OPC Publisher-åtgärder)


## <a name="configuration-options"></a>Konfigurationsalternativ

|Konfigurations alternativ (kort skrift/fullständigt namn)    |    Beskrivning   |
|----------------------------------------------|------------------|
PF/publishfile |Fil namnet för att konfigurera noderna som ska publiceras. Om det här alternativet anges placeras OPC-utgivare i fristående läge.
LF/logfile |Fil namnet för den loggfil som ska användas.
lla/LogLevel |Den loggnings nivå som ska användas (tillåts: allvarligt, fel, Varna, info, debug, verbose).
mig/MessageEncoding |Meddelande kodningen för utgående meddelanden tillåtna värden: JSON, UADP
mm/messagingmode |Meddelande läge för tillåtna värden för utgående meddelanden: PubSub, exempel
FM/fullfeaturedmessage |Fullständigt aktuellt läge för meddelanden (alla fält ifyllda). Standardvärdet är "true" för äldre kompatibilitet "falskt"
AA/AutoAccept |Utgivarens betrodda alla servrar är en anslutning till
BS/batchSize |Antalet OPC UA-data – ändra meddelanden som ska cachelagras för batchering.
Si/iothubsendinterval |Utlösarens batch-intervall i sekunder.
MS/iothubmessagesize |Maximal storlek för meddelandet (IoT D2C).
om/maxoutgressmessages |Den maximala storleken för meddelandet (IoT D2C), utgående buffert.
di/diagnosticsinterval |Visar information om utgivarens diagnostikinformation vid det angivna intervallet i sekunder (information om loggnings nivå krävs). -1 inaktiverar fjärran sluten diagnostikloggar och diagnostiska utdata
lt/logflugtimespan |TimeSpan i sekunder när logg filen ska tömmas.
ih/iothubprotocol |Protokoll som ska användas för kommunikation med hubben. Tillåtna värden: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, AMQP, MQTT, TCP, WebSocket, any
hb/heartbeatinterval |Utgivaren använder detta som standardvärde i sekunder för inställningen för pulsslags intervallet för noder utan inställningen för pulsslags intervall.
OperationTimeout |Åtgärds tids gränsen för utgivarens OPC UA-klient i MS.
OL/opcmaxstringlen |Den maximala längden för en sträng OPC kan skicka/ta emot.
OI/opcsamplinginterval |Standardvärde i millisekunder för att begära servrarna till exempel värden
OP/opcpublishinginterval |Standardvärdet i millisekunder för publicerings intervalls inställningen för prenumerationerna mot OPC UA-servern.
CT/createsessiontimeout |Intervallet som utgivaren skickar Keep Alive-meddelanden på några sekunder till OPC-servrarna på de slut punkter som den är ansluten till.
kt/keepalivethresholt |Ange antalet Keep Alive-paket som en server kan sakna, innan sessionen kopplas från.
TM/trustmyself |Utgivar certifikatet placeras automatiskt i det betrodda arkivet.
vid/appcertstoretype |Egen lagrings typ för program certifikat (tillåts: katalog, X509Store).


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du ändrar standardvärdena för konfigurationen kan du 

> [!div class="nextstepaction"]
> [Hämta IIoT-data till ADX](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Visualisera och analysera data med hjälp av Time Series Insights](tutorial-visualize-data-time-series-insights.md)
