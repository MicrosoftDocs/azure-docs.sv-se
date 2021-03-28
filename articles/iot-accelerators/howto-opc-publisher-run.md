---
title: Kör OPC Publisher – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du kör och felsöker OPC-utgivare. Den behandlar också prestanda-och minnes överväganden.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f7d6581a1892ebd74a1adba5c09c0af9d3cf9d43
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646290"
---
# <a name="run-opc-publisher"></a>Köra OPC Publisher

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Den här artikeln beskriver hur du kör AD debug OPC Publisher. Den behandlar också prestanda-och minnes överväganden.

## <a name="command-line-options"></a>Kommandoradsalternativ

Program användningen visas med `--help` kommando rads alternativet enligt följande:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Normalt anger du endast IoT Hub ägarens anslutnings sträng i den första körningen av programmet. Anslutnings strängen krypteras och lagras i plattformens certifikat arkiv. Vid senare körningar läser programmet anslutnings strängen från certifikat arkivet. Om du anger anslutnings strängen för varje körning tas enheten som skapats för programmet i IoT Hub enhets registret bort och återskapas.

## <a name="run-natively-on-windows"></a>Kör internt i Windows

Öppna projektet **opcpublisher. SLN** med Visual Studio, skapa lösningen och publicera den. Du kan starta programmet i den **mål katalog** som du har publicerat enligt följande:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Använd en självgående behållare

Skapa en egen behållare och starta den på följande sätt:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Använd en behållare från Microsoft Container Registry

Det finns en fördefinierad behållare i Microsoft Container Registry. Starta den på följande sätt:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontrol lera [Docker-hubben](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) för att se de operativ system och processor arkitekturer som stöds. Om din operativ system-och CPU-arkitektur stöds väljer Docker dock automatiskt rätt behållare.

## <a name="run-as-an-azure-iot-edge-module"></a>Kör som en Azure IoT Edge modul

OPC Publisher är redo att användas som en [Azure IoT Edge](../iot-edge/index.yml) modul. När du använder OPC-utgivare som IoT Edge-modul, är de enda transport protokoll som stöds **Amqp_Tcp_Only** och **Mqtt_Tcp_Only**.

Om du vill lägga till OPC-utgivare som modul till din IoT Edge-distribution går du till IoT Hub inställningarna i Azure Portal och utför följande steg:

1. Gå till **IoT Edge** och skapa eller välj din IoT Edge-enhet.
1. Välj **Ange moduler**.
1. Välj **Lägg till** under **distributions moduler** och sedan **IoT Edge modul**.
1. I fältet **namn** anger du **utgivare**.
1. I fältet **bild-URI** anger du `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Du kan hitta tillgängliga taggar på [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Klistra in följande JSON i fältet **skapa alternativ för container** :

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Den här konfigurationen konfigurerar IoT Edge för att starta en behållare som heter **Publisher** med hjälp av avbildningen OPC Publisher. Värd namnet för behållarens system har angetts till **utgivare**. OPC Publisher anropas med följande kommando rads argument: `--aa` . Med det här alternativet kan OPC Publisher lita på certifikaten för de OPC UA-servrar som den ansluter till. Du kan använda alla kommando rads alternativ för OPC Publisher. Den enda begränsningen är storleken på **behållar skapande alternativen** som stöds av IoT Edge.

1. Lämna de andra inställningarna som de är och välj **Spara**.
1. Om du vill bearbeta utdata från OPC-utgivaren lokalt med en annan IoT Edge modul går du tillbaka till sidan **Ange moduler** . Gå sedan till fliken **Ange vägar** och Lägg till en ny väg som ser ut som följande JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Gå tillbaka till sidan **Ange moduler** , Välj **Nästa** tills du når den sista sidan i konfigurationen.
1. Välj **Skicka** för att skicka konfigurationen till IoT Edge.
1. När du har startat IoT Edge på din Edge-enhet och Docker-behållar **utgivaren** körs, kan du se logg utmatningen för OPC-utgivaren antingen genom att använda `docker logs -f publisher` eller genom att kontrol lera logg filen. I föregående exempel är logg filen ovanför `d:\iiotegde\publisher-publisher.log` . Du kan också använda [verktyget IoT-Edge-OPC-Publisher-Diagnostics](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Gör konfigurationsfilerna tillgängliga på värden

Om du vill göra IoT Edge modulens konfigurationsfiler tillgängliga i värd fil systemet, använder du följande **behållare skapa alternativ**. Följande exempel är en distribution som använder Linux-behållare för Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Med de här alternativen läser OPC utgivare de noder som ska publiceras från filen `./pn.json` och behållaren arbets katalog är inställd på `/appdata` vid start. Med de här inställningarna läser OPC utgivare filen `/appdata/pn.json` från behållaren för att hämta dess konfiguration. Utan `--pf` alternativet försöker OPC utgivare läsa standard konfigurations filen `./publishednodes.json` .

Logg filen, med standard namnet `publisher-publisher.log` , skrivs till `/appdata` och `CertificateStores` katalogen skapas också i den här katalogen.

För att göra alla dessa filer tillgängliga i värd fil systemet, kräver container konfigurationen en bindnings volym för bindning. `d://iiotedge:/appdata`Bindningen mappar katalogen `/appdata` , som är den aktuella arbets katalogen när behållaren startas, till värd katalogen `d://iiotedge` . Utan det här alternativet sparas inga fildata när behållaren nästa startar.

Om du kör Windows-behållare är syntaxen för `Binds` parametern annorlunda. Vid behållarens start är arbets katalogen `c:\appdata` . Om du vill placera konfigurations filen i katalogen `d:\iiotedge` på värden anger du följande mappning i `HostConfig` avsnittet:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Om du kör Linux-behållare på Linux, är syntaxen för `Binds` parametern återigen annorlunda. Vid behållarens start är arbets katalogen `/appdata` . Om du vill placera konfigurations filen i katalogen `/iiotedge` på värden anger du följande mappning i `HostConfig` avsnittet:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Att tänka på när du använder en behållare

I följande avsnitt listas några saker att tänka på när du använder en behållare:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Åtkomst till OPC Publisher OPC UA-servern

Som standard lyssnar OPC utgivare OPC UA-servern på port 62222. Använd följande kommando för att exponera den inkommande porten i en behållare:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Aktivera namn matchning med namn matchning

Om du vill aktivera namn matchning från behållaren till andra behållare skapar du en användare definiera Docker Bridge Network och ansluter behållaren till det här nätverket med `--network` alternativet. Tilldela även containern ett namn med hjälp av `--name` alternativet enligt följande:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Behållaren kan nu uppnås med hjälp av namnet på `publisher` andra behållare i samma nätverk.

### <a name="access-other-systems-from-within-the-container"></a>Få åtkomst till andra system inifrån behållaren

Andra behållare kan nås med hjälp av de parametrar som beskrivs i föregående avsnitt. Om det operativ system där Docker är värd för är DNS aktiverat, kommer åtkomst till alla system som är kända för DNS att fungera.

I nätverk som använder NetBIOS-namnmatchning aktiverar du åtkomst till andra system genom att starta din behållare med `--add-host` alternativet. Med det här alternativet läggs en post till i behållarens värd fil:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Tilldela ett värdnamn

OPC Publisher använder värd namnet för datorn som den körs på för certifikat och skapande av slut punkt. Docker väljer ett slumpmässigt värdnamn om det inte har angetts med `-h` alternativet. I följande exempel visas hur du anger behållarens interna värdnamn `publisher` :

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Använda bind-monteringar (delat fil system)

I stället för att använda behållar fil systemet kan du välja värd fil systemet för att lagra konfigurations information och loggfiler. Om du vill konfigurera det här alternativet använder du `-v` alternativet `docker run` i bind Mount-läge.

## <a name="opc-ua-x509-certificates"></a>OPC UA X. 509-certifikat

OPC UA använder X. 509-certifikat för att autentisera OPC UA-klienten och-servern när de upprättar en anslutning och krypterar kommunikationen mellan dem. OPC Publisher använder certifikat arkiv som underhålls av OPC UA-stacken för att hantera alla certifikat. Vid start kontrollerar OPC Publisher om det finns ett certifikat för sig självt. Om det inte finns något certifikat i certifikat arkivet, och det inte finns något som skickats in på kommando raden, skapar OPC Publisher ett självsignerat certifikat. Mer information finns i **InitApplicationSecurityAsync** -metoden i `OpcApplicationConfigurationSecurity.cs` .

Självsignerade certifikat ger ingen säkerhet eftersom de inte har signerats av en betrodd certifikat utfärdare.

OPC Publisher tillhandahåller kommando rads alternativ för att:

- Hämta CSR-information om det aktuella program certifikatet som används av OPC-utgivaren.
- Etablera OPC-utgivare med ett CA-signerat certifikat.
- Etablera OPC-utgivare med ett nytt nyckel par och matchande CA-signerat certifikat.
- Lägg till certifikat i ett betrott peer-eller certifikat Arkiv för betrodda certifikat utfärdare.
- Lägg till en CRL.
- Ta bort ett certifikat från certifikat arkivet Betrodda peer-eller betrodda utfärdare.

Med de här alternativen kan du skicka parametrar med hjälp av filer eller Base64-kodade strängar.

Standard lagrings typen för alla certifikat arkiv är fil systemet, som du kan ändra med hjälp av kommando rads alternativ. Eftersom behållaren inte tillhandahåller beständigt lagrings utrymme i fil systemet måste du välja en annan lagrings typ. Använd Docker- `-v` alternativet för att spara certifikat arkiven i värd fil systemet eller i en Docker-volym. Om du använder en Docker-volym kan du skicka certifikat med base64-kodade strängar.

Körnings miljön påverkar hur certifikat sparas. Undvik att skapa nya certifikat Arkiv varje gången du kör programmet:

- Som körs internt i Windows kan du inte använda ett program certifikat Arkiv av typen `Directory` eftersom åtkomsten till den privata nyckeln Miss lyckas. I det här fallet använder du alternativet `--at X509Store` .
- Kör som Linux Docker-behållare, du kan mappa certifikat arkiven till värd fil systemet med alternativet Docker-körning `-v <hostdirectory>:/appdata` . Det här alternativet gör att certifikatet beständigt mellan program körs.
- Körs som Linux Docker-behållare och du vill använda ett X509-Arkiv för program certifikatet använder du alternativet Docker-körning `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` och alternativet program `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Överväganden för prestanda och minne

I det här avsnittet beskrivs alternativ för att hantera minne och prestanda:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Kommando rads parametrar för att kontrol lera prestanda och minne

När du kör OPC-Utgivare måste du vara medveten om dina prestanda krav och de minnes resurser som är tillgängliga på värden.

Minnes-och prestanda är beroende av varandra och båda beror på konfigurationen av hur många noder som du konfigurerar att publicera. Se till att följande parametrar uppfyller dina krav:

- IoT Hub skickar intervall: `--si`
- IoT Hub meddelande storlek (standard `1` ): `--ms`
- Kapacitet för övervakade objekt i kö: `--mq`

`--mq`Parametern styr den övre kanten av kapaciteten för den interna kön, som buffrar alla OPC ändrings meddelanden för Node-noder. Om OPC Publisher inte kan skicka meddelanden till IoT Hub tillräckligt snabbt så buffrar den här kön aviseringarna. Parametern anger antalet meddelanden som kan buffras. Om du ser hur många objekt i den här kön som ökar i test körningarna, så att du undviker att förlora meddelanden:

- Minska sändnings intervallet för IoT Hub
- Öka storleken på IoT Hubs meddelandet

`--si`Parametern TVINGAR OPC-utgivare att skicka meddelanden till IoT Hub med det angivna intervallet. OPC Publisher skickar ett meddelande så snart som den meddelande storlek som anges av `--ms` parametern har nåtts, eller så snart som det intervall som anges av `--si` parametern har nåtts. Om du vill inaktivera alternativet för meddelande storlek använder du `--ms 0` . I det här fallet använder OPC Publisher den största möjliga IoT Hub meddelande storleken på 256 kB för att köra batch-data.

`--ms`Parametern låter dig batch-meddelanden som skickas till IoT Hub. Protokollet som du använder avgör om överföringen av ett meddelande till IoT Hub är hög jämfört med den faktiska tiden för att skicka nytto lasten. Om ditt scenario tillåter svars tid när data matas in av IoT Hub konfigurerar du OPC Publisher att använda den största meddelande storleken på 256 kB.

Innan du använder OPC Publisher i produktions scenarier kan du testa prestanda-och minnes användningen under produktions förhållanden. Du kan använda `--di` parametern för att ange intervallet, i sekunder, som OPC-utgivare skriver diagnostikinformation.

### <a name="test-measurements"></a>Test mätningar

Följande exempel på diagnostik visar mätningar med olika värden för `--si` och `--ms` parametrar som publicerar 500-noder med ett OPC publicerings intervall på 1 sekund.  Testet använde en fel söknings version av OPC Publisher i Windows 10 inbyggt i 120 sekunder. Det IoT Hub protokollet är standard protokollet för MQTT.

#### <a name="default-configuration---si-10---ms-262144"></a>Standard konfiguration (--SI 10--MS 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Standard konfigurationen skickar data till IoT Hub var 10: e sekund, eller när 256 kB data är tillgängliga för IoT Hub inmatning. Den här konfigurationen lägger till en måttlig fördröjning på cirka 10 sekunder, men har lägsta sannolikhet för att förlora data på grund av den stora meddelande storleken. Den diagnostiska utdata visar att det inte finns några borttappade OPC: `monitored item notifications enqueue failure: 0` .

#### <a name="constant-send-interval---si-1---ms-0"></a>Konstant överförings intervall (--si 1--MS 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

När meddelande storleken är inställd på 0, skapar OPC Publisher internt data med den största IoT Hub meddelande storlek som stöds, som är 256 kB. De diagnostiska utdata som visar den genomsnittliga meddelande storleken är 115 019 byte. I den här konfigurationen förlorar utgivaren inte några OPC-OPC, och jämförs med standard den har kortare svars tid.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Skicka varje OPC uppdatering av Node-värdet (--SI 0--MS 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Den här konfigurationen skickar för varje OPC-Node-värde ändra ett meddelande till IoT Hub. Diagnostiken visar den genomsnittliga meddelande storleken är 234 byte, vilket är litet. Fördelen med den här konfigurationen är att OPC Publisher inte lägger till någon svars tid. Antalet förlorade OPC-uppdateringar ( `monitored item notifications enqueue failure: 44624` ) är högt, vilket gör att den här konfigurationen är olämplig för scenarier med stora mängder telemetri som ska publiceras.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximal batching (--SI 0--MS 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Den här konfigurationen är lika många uppdateringar av OPC Node-värdet som möjligt. Den största IoT Hub meddelande storleken är 256 kB, som konfigureras här. Det finns inget begärt överförings intervall, vilket innebär att mängden data som IoT Hub för inmatning bestämmer svars tiden. Den här konfigurationen har minst sannolikheten att förlora eventuella OPC Node-värden och är lämplig för att publicera ett stort antal noder. När du använder den här konfigurationen bör du se till att scenariot inte har några villkor där hög latens har införts om meddelande storleken på 256 kB inte har uppnåtts.

## <a name="debug-the-application"></a>Felsök programmet

Om du vill felsöka programmet öppnar du lösnings filen **opcpublisher. SLN** med Visual Studio och använder fel söknings verktygen i Visual Studio.

Om du behöver åtkomst till OPC UA-servern i OPC-utgivaren kontrollerar du att brand väggen tillåter åtkomst till porten som servern lyssnar på. Standard porten är: 62222.

## <a name="control-the-application-remotely"></a>Fjärrstyra programmet

Du kan konfigurera noderna som ska publiceras genom att använda IoT Hub direkta metoder.

OPC Publisher implementerar ytterligare IoT Hub direkta metod anrop för att läsa:

- Allmän information.
- Diagnostisk information om OPC-sessioner, prenumerationer och övervakade objekt.
- Diagnostisk information om IoT Hub meddelanden och händelser.
- Start loggen.
- De sista 100 raderna i loggen.
- Stäng programmet.

Följande GitHub-databaser innehåller verktyg för att [Konfigurera noderna för att publicera](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) och [läsa diagnostikinformation](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Båda verktygen är också tillgängliga som behållare i Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Använd ett exempel på en OPC UA-Server

Om du inte har en riktig OPC UA-Server kan du använda [EXEMPLET OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) för att komma igång. Det här exemplet är också tillgängligt i Docker Hub.

Det implementerar ett antal taggar, som genererar slumpmässiga data och taggar med avvikelser. Du kan utöka exemplet om du behöver simulera ytterligare tagg värden.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kör OPC-utgivare, är de rekommenderade nästa stegen att lära dig mer om [OPC](overview-opc-twin.md) -och [OPC-valvet](overview-opc-vault.md).