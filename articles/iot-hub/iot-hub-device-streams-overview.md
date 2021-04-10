---
title: Azure IoT Hub enhets strömmar | Microsoft Docs
description: Översikt över Azure IoT Hub enhets strömmar, vilket underlättar säker dubbelriktad TCP-tunnlar för en mängd olika kommunikations scenarier med moln till enhet.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 9487fc562fa099d2650aabc8d15fc1449c7fcb5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97825163"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub enhets strömmar (förhands granskning)

Azure IoT Hub *enhets strömmar* gör det enklare att skapa säkra, dubbelriktade TCP-tunnlar för en rad olika kommunikations scenarier med moln till enhet. En enhets ström är underordnad av en IoT Hub *strömnings slut punkt* som fungerar som en proxy mellan din enhet och tjänst slut punkter. Den här installationen, som illustreras i diagrammet nedan, är särskilt användbar när enheter ligger bakom en nätverks brand vägg eller finns inuti ett privat nätverk. Därför kan IoT Hub enhets strömmar hjälpa kundernas behov av att komma åt IoT-enheter på ett användarvänligt sätt och utan att behöva öppna inkommande eller utgående nätverks brand Väggs portar.

![Översikt över IoT Hub enhets strömmar](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Genom att använda IoT Hub enhets strömmar förblir enheterna skyddade och behöver bara öppna utgående TCP-anslutningar till IoT Hub: s strömnings slut punkt via port 443. När en data ström har upprättats har program på Server sidan och enhets sidan samma program mässig åtkomst till ett WebSocket-klientcertifikat för att skicka och ta emot rå byte till varandra. De Tillförlitlighets-och ordnings garantier som tillhandahålls av denna tunnel är i parivärde med TCP.

## <a name="benefits"></a>Fördelar

IoT Hub enhets strömmar ger följande fördelar:

* **Brand Väggs vänlig säker anslutning:** IoT-enheter kan nås från tjänstens slut punkter utan att inkommande brand Väggs port öppnas på enheten eller nätverks omkretserna (endast utgående anslutning till IoT Hub krävs via port 443).

* **Autentisering:** Både enhets-och tjänst sidor i tunneln måste autentiseras med IoT Hub med motsvarande autentiseringsuppgifter.

* **Kryptering:** Som standard använder IoT Hub enhets strömmar TLS-aktiverade anslutningar. Detta säkerställer att trafiken alltid är krypterad oavsett om programmet använder kryptering eller inte.

* **Enkel anslutning:** I många fall eliminerar användningen av enhets strömmar behovet av komplicerad installation av virtuella privata nätverk för att möjliggöra anslutning till IoT-enheter.

* **Kompatibilitet med TCP/IP-stack:** IoT Hub enhets strömmar kan hantera TCP/IP-programtrafik. Det innebär att en mängd olika tillverkarspecifika och standardbaserade protokoll kan utnyttja den här funktionen.

* **Enkel användning i inställningar för privata nätverk:** Tjänsten kan kommunicera med en enhet genom att referera till dess enhets-ID, i stället för enhetens IP-adress. Detta är användbart i situationer där en enhet finns i ett privat nätverk och har en privat IP-adress, eller om dess IP-adress tilldelas dynamiskt och är okänd för tjänst sidan.

## <a name="device-stream-workflows"></a>Enhets Ströms arbets flöden

En enhets ström initieras när tjänsten begär att ansluta till en enhet genom att ange dess enhets-ID. Det här arbets flödet passar särskilt för en klient/server-kommunikations modell, inklusive SSH och RDP, där en användare har för avsikt att fjärrans luta till SSH-eller RDP-servern som körs på enheten med hjälp av ett SSH-eller RDP-klientcertifikat.

Processen för att skapa enhets strömmen omfattar en förhandling mellan enheten, tjänsten, IoT Hub: s huvud-och strömnings slut punkter. Medan IoT Hubs huvud slut punkt dirigerar skapandet av en enhets ström, hanterar streaming-slutpunkten trafiken som flödar mellan tjänsten och enheten.

### <a name="device-stream-creation-flow"></a>Flöde för skapande av enhets ström

Att skapa en enhets ström med hjälp av SDK omfattar följande steg, som också beskrivs i bilden nedan:

![Enhets Ströms hand skaknings process](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Enhets programmet registrerar ett återanrop i förväg för att bli informerad om när en ny enhets ström initieras till enheten. Det här steget inträffar vanligt vis när enheten startas och ansluter till IoT Hub.

2. Programmet på tjänst sidan initierar en enhets ström vid behov genom att ange enhets-ID: t (_inte_ IP-adressen).

3. IoT Hub meddelar programmet på enhets sidan genom att anropa återanropet som registrerades i steg 1. Enheten kan godkänna eller avvisa begäran om data Ströms initiering. Den här logiken kan vara specifik för ditt program scenario. Om Stream-begäran avvisas av enheten, IoT Hub informerar tjänsten enligt detta. i annat fall följer du stegen nedan.

4. Enheten skapar en säker utgående TCP-anslutning till slut punkten för direkt uppspelning via port 443 och uppgraderar anslutningen till en WebSocket. URL: en för direkt uppspelnings slut punkten samt de autentiseringsuppgifter som ska användas för att autentisera båda tillhandahålls till enheten genom IoT Hub som en del av begäran som skickas i steg 3.

5. Tjänsten meddelas om resultatet av enheten som accepterar data strömmen och fortsätter att skapa sin egen WebSocket-klient till slut punkten för direkt uppspelning. På samma sätt får den direkt uppspelnings slut punkts-URL och autentiseringsinformation från IoT Hub.

I hand skaknings processen ovan:

* Hand skaknings processen måste slutföras inom 60 sekunder (steg 2 till 5), annars skulle hand skakningen Miss lyckas med en tids gräns och tjänsten kommer att meddelas därefter.

* När flödet för att skapa data strömmen ovan är klart fungerar slut punkten för direkt uppspelning som en proxy och överför trafiken mellan tjänsten och enheten över sina respektive WebSockets.

* Både enhet och tjänst behöver utgående anslutning till IoT Hubs huvud slut punkt samt slut punkten för direkt uppspelning via port 443. URL: en för dessa slut punkter finns på fliken *Översikt* på IoT Hub Portal.

* Tillförlitlighets-och ordnings garantier för en etablerad ström är i parivärde med TCP.

* Alla anslutningar till IoT Hub och direkt uppspelnings slut punkt använder TLS och är krypterade.

### <a name="termination-flow"></a>Avslutnings flöde

En etablerad data ström avslutas när någon av TCP-anslutningarna till gatewayen kopplas från (av tjänsten eller enheten). Detta kan ske frivilligt genom att du stänger websocketen antingen i enhets-eller tjänst program, eller frivilligt i händelse av en tids gräns för nätverks anslutning eller ett process haveri. Vid avslutning av antingen enhet eller tjänst anslutning till slut punkten för direkt uppspelning kommer den andra TCP-anslutningen också att avbrytas och tjänsten och enheten är ansvarig för att återskapa strömmen vid behov.

## <a name="connectivity-requirements"></a>Anslutnings krav

Både enhets-och tjänst sidorna i en enhets ström måste kunna upprätta TLS-aktiverade anslutningar till IoT Hub och dess slut punkt för direkt uppspelning. Detta kräver utgående anslutning via port 443 till dessa slut punkter. Det värdnamn som är associerat med dessa slut punkter finns på fliken *Översikt* i IoT Hub, som du ser i bilden nedan:

!["Enhets Ströms slut punkter"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Du kan också hämta slut punkts information med Azure CLI under hubbens egenskaps avsnitt, specifikt `property.hostname` och `property.deviceStreams` nycklar.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Utdata är ett JSON-objekt för alla slut punkter som din Hubbs enhet och tjänst kan behöva ansluta till för att upprätta en enhets ström.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Se till att du har installerat Azure CLI-version 2.0.57 eller senare. Du kan ladda ned den senaste versionen från sidan [Installera Azure CLI](/cli/azure/install-azure-cli) .
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Tillåt utgående anslutning till enhetens strömnings slut punkter

Som vi ser i början av den här artikeln skapar enheten en utgående anslutning till IoT Hub strömnings slut punkt under initierings processen för enhets strömmar. Dina brand väggar på enheten eller dess nätverk måste tillåta utgående anslutning till strömmande Gateway via port 443 (Observera att kommunikationen sker via en WebSocket-anslutning som är krypterad med TLS).

Värd namnet för enhetens strömnings slut punkt finns på Azure IoT Hub-portalen under fliken Översikt. ![ " Enhets ström slut punkter "](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Du kan också hitta den här informationen med hjälp av Azure CLI:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Se till att du har installerat Azure CLI-version 2.0.57 eller senare. Du kan ladda ned den senaste versionen från sidan [Installera Azure CLI](/cli/azure/install-azure-cli) .
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>Felsöka via enhets strömmar resurs loggar

Du kan konfigurera Azure Monitor att samla [resurs loggarna för enhets strömmar](monitor-iot-hub-reference.md#device-streams-preview) som genereras av din IoT Hub. Detta kan vara användbart i fel söknings scenarier.

Följ stegen nedan för att skapa en diagnostisk inställning för att skicka loggar för enhets strömmar för IoT Hub till Azure Monitor loggar:

1. I Azure Portal navigerar du till din IoT Hub. I den vänstra rutan, under **övervakning**, väljer du **diagnostikinställningar**. Välj sedan **Lägg till diagnostisk inställning**.

2. Ange ett namn för den diagnostiska inställningen och välj **DeviceStreams** i listan över loggar. Välj sedan **Skicka till Log Analytics**. Du är guidad att välja en befintlig Log Analytics arbets yta eller skapa en ny.

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="Aktivera enhets strömmar loggar":::

3. När du har skapat en diagnostisk inställning för att skicka dina enhets strömmar till en Log Analytics arbets yta kan du komma åt loggarna genom att välja **loggar** under **övervakning** i den vänstra rutan i IoT Hub i Azure Portal. Loggar för enhets strömmar visas i `AzureDiagnostics` tabellen och har `Category=DeviceStreams` . Tänk på att det kan ta flera minuter att följa en åtgärd för loggar som ska visas i tabellen.

   Som du ser nedan är identiteten för mål enheten och resultatet av åtgärden också tillgänglig i loggarna.

   !["Åtkomst till enhets strömmar loggar"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

Mer information om hur du använder Azure Monitor med IoT Hub finns i [övervaka IoT Hub](monitor-iot-hub.md). Information om alla resurs loggar, statistik och tabeller som är tillgängliga för IoT Hub finns i [övervakning av Azure IoT Hub data Reference](monitor-iot-hub-reference.md).

## <a name="regional-availability"></a>Regional tillgänglighet

Under den offentliga för hands versionen är IoT Hub enhets strömmar tillgängliga i de centrala USA, centrala USA-EUAP, Nord Europa och Sydostasien regioner. Se till att du skapar navet i någon av dessa regioner.

## <a name="sdk-availability"></a>SDK-tillgänglighet

Två sidor av varje ström (på enhets-och tjänst sidan) använder IoT Hub SDK för att upprätta tunneln. Under den offentliga för hands versionen kan kunder välja mellan följande SDK-språk:

* C-och C# SDK: s support enhets strömmar på enhets sidan.

* NodeJS och C# SDK stöder enhets strömmar på tjänst sidan.

## <a name="iot-hub-device-stream-samples"></a>Exempel på IoT Hub enhets data ström

Det finns två [snabb starts exempel](./index.yml) som är tillgängliga på sidan IoT Hub. Dessa demonstrerar användningen av enhets strömmar efter program.

* *ECHO* -exemplet demonstrerar användning av enhets strömmar (genom att anropa SDK-API: et direkt).

* Exemplet på den *lokala proxyn* visar tunneln av klient/server-programtrafik (till exempel SSH, RDP eller webb) via enhets strömmar.

De här exemplen beskrivs mer detaljerat nedan.

### <a name="echo-sample"></a>Eko exempel

Exemplet på ekobegäran visar programmerings användning av enhets strömmar för att skicka och ta emot byte mellan tjänst-och enhets program. Observera att du kan använda tjänst-och enhets program på olika språk. Du kan till exempel använda enhets programmet C med C#-tjänst programmet.

Här är eko exemplen:

* [C#-tjänst-och tjänst program](quickstart-device-streams-echo-csharp.md)

* [Node.js tjänst program](quickstart-device-streams-echo-nodejs.md)

* [Enhets program för C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Lokalt proxy-exempel (för SSH eller RDP)

Exemplet på den lokala proxyn visar ett sätt att aktivera tunnlar av ett befintligt programs trafik som inbegriper kommunikation mellan en klient och ett Server program. Den här inställningen fungerar för klient/server-protokoll som SSH och RDP, där tjänst sidan fungerar som en klient (som kör SSH-eller RDP-klientprogram) och enhets sidan fungerar som servern (som kör SSH daemon eller RDP-serverprogram).

I det här avsnittet beskrivs hur du använder enhets strömmar för att ge användaren SSH till en enhet över enhets strömmar (fallet för RDP eller andra klient/server-program på samma sätt som med protokollets motsvarande port).

Installations programmet använder två *lokala proxy* -program som visas i bilden nedan, nämligen *enhets lokal Proxy* och *service-lokal Proxy*. De lokala proxy-programmen är ansvariga för att utföra [enhets strömmens hand skakning](#device-stream-creation-flow) med IoT Hub och INTERAGERA med SSH-klienten och SSH-daemon med hjälp av vanliga klient/server-socketar.

!["Installation av proxy för enhets Ströms-proxy för SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. Användaren kör tjänst-lokal Proxy för att initiera en enhets ström till enheten.

2. Den enhets lokala proxyn accepterar begäran om data Ströms initiering och tunneln upprättas till IoT Hub strömnings slut punkt (enligt beskrivningen ovan).

3. Den enhets lokala proxyn ansluter till den SSH daemon-slutpunkt som lyssnar på port 22 på enheten.

4. Den tjänst-lokala proxyn lyssnar på en utsedd port som väntar på nya SSH-anslutningar från användaren (port 2222 som används i exemplet, men den kan konfigureras till en annan tillgänglig port). Användaren pekar SSH-klienten till den lokala proxyservern på localhost.

### <a name="notes"></a>Kommentarer

* Ovanstående steg slutför en slutpunkt-till-slutpunkt-tunnel mellan SSH-klienten (till höger) i SSH-daemonen (till vänster). En del av den här anslutningen från slut punkt till slut punkt omfattar att skicka trafik via en enhets ström till IoT Hub.

* Pilarna i bilden visar i vilken riktning anslutningarna upprättas mellan slut punkterna. Observera särskilt att det inte finns några inkommande anslutningar till enheten (detta blockeras ofta av en brand vägg).

* Valet att använda port 2222 på den lokala proxyn är ett valfritt alternativ. Proxyn kan konfigureras för att använda någon annan tillgänglig port.

* Valet av port 22 är protokoll beroende och särskilt för SSH i det här fallet. Vid RDP måste port 3389 användas. Detta kan konfigureras i de tillhandahållna exempel programmen.

Använd länkarna nedan för instruktioner om hur du kör lokala proxy-program på önskat språk. Precis som i [eko exemplet](#echo-sample)kan du köra enhets-och tjänst lokala proxy-program på olika språk eftersom de är helt driftskompatibla.

* [C#-tjänst-och tjänst program](quickstart-device-streams-proxy-csharp.md)

* [Node.js tjänst program](quickstart-device-streams-proxy-nodejs.md)

* [Enhets program för C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Nästa steg

Använd länkarna nedan om du vill veta mer om enhets strömmar.

> [!div class="nextstepaction"]
> [Enhets strömmar på IoT Show (kanal 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
