---
title: Arbeta med Defender for IoT-API:er
description: Använd en extern REST API att komma åt data som identifierats av sensorer och hanteringskonsoler och utföra åtgärder med dessa data.
ms.date: 12/14/2020
ms.topic: reference
ms.openlocfilehash: e7833a20d4f708ecb5b80394fae2c56fc07c9489
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752740"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>API:er för Defender for IoT-sensor och hanteringskonsol

Använd en extern REST API att komma åt data som identifierats av sensorer och hanteringskonsoler och utföra åtgärder med dessa data.

Anslutningar skyddas via SSL.

## <a name="getting-started"></a>Komma igång

När du använder ett externt API på den Azure Defender for IoT eller lokala hanteringskonsolen måste du i allmänhet generera en åtkomsttoken. Token krävs inte för autentiserings-API:er som du använder på sensorn och den lokala hanteringskonsolen.

Så här genererar du en token:

1. I fönstret **Systeminställningar** väljer du **Åtkomsttoken.**
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Skärmbild av fönstret Systeminställningar med knappen Åtkomsttoken framhävd.":::

2. Välj **Generera ny token**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Välj knappen för att generera en ny token.":::

3. Beskriv syftet med den nya token och välj **Nästa.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Generera en ny token och ange namnet på den integrering som är associerad med den.":::

4. Åtkomsttoken visas. Kopiera den eftersom den inte visas igen.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Kopiera din åtkomsttoken för integreringen.":::

5. Välj **Slutför**. De token som du skapar visas i **dialogrutan Åtkomsttoken.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Skärmbild av dialogrutan Enhetstoken med ifyllda token":::

   **Används** anger den senaste gången ett externt anrop med denna token togs emot.

   Om **ej a** visas i fältet Används **för** denna token fungerar inte anslutningen mellan sensorn och den anslutna servern.

6. Lägg till ett HTTP-huvud **med namnet Authorization** (Auktorisering) i din begäran och ange dess värde till den token som du genererade.

## <a name="sensor-api-specifications"></a>Sensor API-specifikationer

I det här avsnittet beskrivs följande sensor-API:er:

- [Hämta enhetsinformation – /api/v1/devices](#retrieve-device-information---apiv1devices)

- [Hämta information om enhetsanslutning – /api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Hämta information om CVE:er – /api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [Hämta aviseringsinformation – /api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [Hämta tidslinjehändelser – /api/v1/events](#retrieve-timeline-events---apiv1events)

- [Hämta sårbarhetsinformation – /api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Hämta säkerhetsrisker – /api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Hämta sårbarheter i verksamheten – /api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Verifiera autentiseringsuppgifter för användare – /api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Ändra lösenord – /external/authentication/set_password](#change-password---externalauthenticationset_password)

- [Uppdatering av användarlösenord av systemadministratör – /extern/autentisering/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Hämta enhetsinformation – /api/v1/devices

Använd det här API:et för att begära en lista över alla enheter som en Defender for IoT-sensor har identifierat.

#### <a name="method"></a>Metod

**Få**

Begär en lista över alla enheter som Defender for IoT-sensorn har identifierat.

#### <a name="query-parameters"></a>Frågeparametrar

- **auktoriserat:** Filtrera endast auktoriserade och obehöriga enheter.

  **Exempel:**

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Matris med JSON-objekt som representerar enheter.

#### <a name="device-fields"></a>Enhetsfält

| Namn | Typ | Kan ha värdet null | Lista över värden |
|--|--|--|--|
| **id** | Numerisk | No | - |
| **ipAddresses** | JSON-matris | Yes | IP-adresser (kan vara mer än en adress om det gäller Internetadresser eller en enhet med dubbla nätverkskort) |
| **name** | Sträng | No | - |
| **Typ** | Sträng | No | Unknown, Engineering Station, WORKSTATION, HMI, Domain Controller, DB Server, Wireless Access Point, Router, Switch, Server, Workstation, IP Camera, Printer, Firewall, Terminal station, VPN Gateway, Internet eller Multicast och Broadcast |
| **macAddresses** | JSON-matris | Yes | MAC-adresser (kan vara mer än en adress om en enhet har dubbla nätverkskort) |
| **operatingSystem** | Sträng | Yes | - |
| **engineeringStation** | Boolesk | No | Sant eller falskt |
| **Scanner** | Boolesk | No | Sant eller falskt |
| **Auktoriserad** | Boolesk | No | Sant eller falskt |
| **Leverantör** | Sträng | Yes | - |
| **Protokoll** | JSON-matris | Yes | Protokollobjekt |
| **Firmware** | JSON-matris | Yes | Objekt för inbyggd programvara |

#### <a name="protocol-fields"></a>Protokollfält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **Namn** | Sträng | No |  |
| **Adresser** | JSON-matris | Yes | Huvudvärden eller numeriska värden |

#### <a name="firmware-fields"></a>Fält för inbyggd programvara

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **Seriell** | Sträng | No | Ej aktuellt eller faktiskt värde |
| **Modell** | Sträng | No | Ej aktuellt eller faktiskt värde |
| **versionInbyggdProgramvara** | Double | No | Ej aktuellt eller faktiskt värde |
| **additionalData** | Sträng | No | Ej aktuellt eller faktiskt värde |
| **moduleAddress** | Sträng | No | Ej A, eller det faktiska värdet |
| **Rack** | Sträng | No | Ej A, eller det faktiska värdet |
| **Slot** | Sträng | No | Ej A, eller det faktiska värdet |
| **Adress** | Sträng | No | Ej A, eller det faktiska värdet |

#### <a name="response-example"></a>Exempel på svar

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https: <span> //127 <span> .0.0.1/api/v1/devices?authorized=true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Hämta information om enhetsanslutning – /api/v1/devices/connections

Använd det här API:et för att begära en lista över alla anslutningar per enhet.

#### <a name="method"></a>Metod

**Få**

#### <a name="query-parameters"></a>Frågeparametrar

Om du inte anger frågeparametrarna returneras alla enhetsanslutningar.

**Exempel**:

`/api/v1/devices/connections`

- **deviceId:** Filtrera efter ett specifikt enhets-ID för att visa dess anslutningar.

  **Exempel**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes:** Tidsram från och med nu bakåt, efter minut, under vilken anslutningarna var aktiva.

  **Exempel**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore:** Filtrera endast anslutningar som identifierades före en viss tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter:** Filtrera endast anslutningar som har identifierats efter en viss tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Matris med JSON-objekt som representerar enhetsanslutningar.

#### <a name="fields"></a>Fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **firstDeviceId** | Numerisk | No | - |
| **secondDeviceId** | Numerisk | No | - |
| **lastSeen** | Numerisk | No | Epok (UTC) |
| **Upptäckte** | Numerisk | No | Epok (UTC) |
| **Portar** | Talmatris | No | - |
| **Protokoll** | JSON-matris | No | Protokollfält |

#### <a name="protocol-field"></a>Protokollfält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **name** | Sträng | No | - |
| **Kommandon** | Strängmatris | No | - |

#### <a name="response-example"></a>Exempel på svar

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Curl-kommando

> [!div class="mx-tdBreakAll"]
> | Typ | API:er | Exempel |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/connections | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/connections |
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/devices/ <deviceId> /connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/api/v1/devices/2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=1594550986000' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Hämta information om CVE:er – /api/v1/devices/cves

Använd det här API:et för att begära en lista över alla kända CRE:er som identifierats på enheter i nätverket.

#### <a name="method"></a>Metod

**Få**

#### <a name="query-parameters"></a>Frågeparametrar

Som standard innehåller det här API:et en lista över alla enhets-IP-adresser med CRE:er, upp till 100 topppoängade CRE:er för varje IP-adress.

**Exempel**:

`/api/v1/devices/cves`

- **deviceId:** Filtrera efter en specifik enhets IP-adress för att få upp till 100 topppoängiga CRE:er som identifierats på den specifika enheten.

  **Exempel**:

  `/api/v1/devices/<ipAddress>/cves`

- **top**: Hur många CRE:er med högst poäng som ska hämtas för varje enhets IP-adress.

  **Exempel**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Matris med JSON-objekt som representerar CRE:er som identifierats på IP-adresser.

#### <a name="fields"></a>Fält

| Namn | Typ | Kan ha värdet null | Lista över värden |
|--|--|--|--|
| **cveId** | Sträng | No | - |
| **Ip** | Sträng | No | IP-adress |
| **Poäng** | Sträng | No | 0.0 - 10.0 |
| **attackVector** | Sträng | No | Nätverk, angränsande nätverk, lokalt eller fysiskt |
| **Beskrivning** | Sträng | No | - |

#### <a name="response-example"></a>Exempel på svar

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/cves | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/cves |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/ <deviceIpAddress> /cves?top= | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Hämta aviseringsinformation – /api/v1/alerts

Använd det här API:et för att begära en lista över alla aviseringar som Defender for IoT-sensorn har identifierat.

#### <a name="method"></a>Metod

**Få**

#### <a name="query-parameters"></a>Frågeparametrar

- **tillstånd:** Filtrera endast hanterade eller ohanterade aviseringar.

  **Exempel**:

  `/api/v1/alerts?state=handled`

- **fromTime:** För att filtrera aviseringar som skapats från en viss tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** Om du bara vill filtrera aviseringar som skapats före en viss tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/alerts?toTime=<epoch>`

- **type**: Om du vill filtrera aviseringar efter en viss typ. Befintliga typer att filtrera efter: oväntade nya enheter, frånkopplingar.

  **Exempel**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Matris med JSON-objekt som representerar aviseringar.

#### <a name="alert-fields"></a>Aviseringsfält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **ID** | Numerisk | No | - |
| **Tid** | Numerisk | No | Epok (UTC) |
| **title** | Sträng | No | - |
| **Meddelande** | Sträng | No | - |
| **Svårighetsgrad** | Sträng | No | Varning, Mindre, Större eller Kritisk |
| **Motor** | Sträng | No | Protokollöverträdelse, principöverträdelse, skadlig kod, avvikelse eller drift |
| **sourceDevice** | Numerisk | Yes | Enhets-ID |
| **destinationDevice** | Numerisk | Yes | Enhets-ID |
| **sourceDeviceAddress** | Numerisk | Yes | IP, MAC, Null |
| **destinationDeviceAddress** | Numerisk | Yes | IP, MAC, Null |
| **remediationSteps** | Sträng | Yes | Reparationssteg som beskrivs i aviseringen |
| **ytterligare information** | Ytterligare informationsobjekt | Yes | - |

Observera att /api/v2/ krävs för följande information:

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps

#### <a name="additional-information-fields"></a>Ytterligare informationsfält

| Namn | Typ | Kan ha värdet null | Lista över värden |
|--|--|--|--|
| **Beskrivning** | Sträng | No | - |
| **Information** | JSON-matris | Inga | Sträng |

#### <a name="response-example"></a>Exempel på svar

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Curl-kommando

> [!div class="mx-tdBreakAll"]
> | Typ | API:er | Exempel |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/alerts?state=&fromTime=&toTime=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/api/v1/alerts?state=ohanterad&fromTime=1594550986000&toTime=1594550986001&type=disconnections' |

### <a name="retrieve-timeline-events---apiv1events"></a>Hämta tidslinjehändelser – /api/v1/events

Använd det här API:et för att begära en lista över händelser som rapporterats till händelsetidslinjen.

#### <a name="method"></a>Metod

**Få**

#### <a name="query-parameters"></a>Frågeparametrar

- **minutesTimeFrame:** Tidsramen från nu bakåt, efter minut, då händelserna rapporterades.

  **Exempel**:

  `/api/v1/events?minutesTimeFrame=20`

- **skriv**: Om du vill filtrera händelselistan efter en viss typ.

  **Exempel:**

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Matris med JSON-objekt som representerar aviseringar.

#### <a name="event-fields"></a>Händelsefält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|--|
| **Tidsstämpel** | Numerisk | No | Epok (UTC) |
| **title** | Sträng | No | - |
| **Svårighetsgrad** | Sträng | No | INFO, MEDDELANDE eller AVISERING |
| **Ägare** | Sträng | Yes | Om händelsen skapades manuellt innehåller det här fältet det användarnamn som skapade händelsen |
| **innehåll** | Sträng | No | - |

#### <a name="response-example"></a>Exempel på svar

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/events?minutesTimeFrame=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Hämta sårbarhetsinformation – /api/v1/reports/vulnerabilities/devices

Använd det här API:et för att begära sårbarhetsbedömningsresultat för varje enhet.

#### <a name="method"></a>Metod

**Få**

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Matris med JSON-objekt som representerar utvärderade enheter.

Enhetsobjektet innehåller:

- Allmänna data

- Utvärderingspoäng

- Sårbarheter

#### <a name="device-fields"></a>Enhetsfält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **name** | Sträng | No | - |
| **ipAddresses** | JSON-matris | No | - |
| **securityScore** | Numerisk | No | - |
| **Leverantör** | Sträng | Yes |  |
| **versionInbyggdProgramvara** | Sträng | Yes | - |
| **Modell** | Sträng | Yes | - |
| **isWirelessAccessPoint** | Boolesk | No | Sant eller falskt |
| **operatingSystem** | Operativsystemobjekt | Yes | - |
| **Sårbarheter** | Sårbarhetsobjekt | Yes | - |

#### <a name="operating-system-fields"></a>Operativsystemfält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **Namn** | Sträng | Yes | - |
| **Typ** | Sträng | Yes | - |
| **Version** | Sträng | Yes | - |
| **latestVersion** | Sträng | Yes | - |

#### <a name="vulnerabilities-fields"></a>Fält för säkerhetsrisker
 
| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **Antivirusprogram** | JSON-matris | Yes | Antivirusnamn |
| **plainTextPasswords** | JSON-matris | Yes | Lösenordsobjekt |
| **Remoteaccess** | JSON-matris | Yes | Fjärråtkomstobjekt |
| **isBackupServer** | Boolesk | No | Sant eller falskt |
| **openedPorts** | JSON-matris | Yes | Portobjekt öppnades |
| **isEngineeringStation** | Boolesk | No | Sant eller falskt |
| **isKnownScanner** | Boolesk | No | Sant eller falskt |
| **cves** | JSON-matris | Yes | CVE-objekt |
| **isUnauthorized** | Boolesk | No | Sant eller falskt |
| **malwareIndicationsDetected** | Boolesk | No | Sant eller falskt |
| **weakAuthentication** | JSON-matris | Yes | Identifierade program som använder svag autentisering |

#### <a name="password-fields"></a>Lösenordsfält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **lösenord** | Sträng | No | - |
| **Protokollet** | Sträng | No | - |
| **Styrka** | Sträng | No | Mycket svag, svag, medel eller stark |

#### <a name="remote-access-fields"></a>Fjärråtkomstfält

| Namn | Typ | Kan ha värdet null | Lista över värden |
|--|--|--|--|
| **Port** | Numerisk | No | - |
| **Transport** | Sträng | No | TCP eller UDP |
| **Klient** | Sträng | No | IP-adress |
| **clientSoftware** | Sträng | No | SSH, VNC, Fjärranslutning eller Team Viewer |

#### <a name="open-port-fields"></a>Öppna portfält

| Namn | Typ | Kan ha värdet null | Lista över värden |
|--|--|--|--|
| **Port** | Numerisk | No | - |
| **Transport** | Sträng | No | TCP eller UDP |
| **Protokollet** | Sträng | Yes | - |
| **isConflictingWithFirewall** | Boolesk | No | Sant eller falskt |

#### <a name="cve-fields"></a>CVE-fält

| Namn | Typ | Kan ha värdet null | Lista över värden |
|--|--|--|--|
| **ID** | Sträng | No | - |
| **Poäng** | Numerisk | No | Double |
| **Beskrivning** | Sträng | No | - |

#### <a name="response-example"></a>Exempel på svar

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Hämta säkerhetsrisker – /api/v1/reports/vulnerabilities/security

Använd det här API:et för att begära resultat av en allmän sårbarhetsbedömning. Den här utvärderingen ger inblick i systemets säkerhetsnivå.

Den här utvärderingen baseras på allmän nätverks- och systeminformation och inte på en specifik enhetsutvärdering.

#### <a name="method"></a>Metod

**Få**

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

JSON-objekt som representerar utvärderade resultat. Varje nyckel kan ha värdet null. Annars innehåller den ett JSON-objekt med nycklar som inte kan ha värdet null.

### <a name="result-fields"></a>Resultatfält

**Nycklar**

**unauthorizedDevices**

| Fältnamn | Typ | Lista över värden |
| ---------- | ---- | -------------- |
| **Adress** | Sträng | IP-adress |
| **name** | Sträng | - |
| **firstDetectionTime** | Numerisk | Epok (UTC) |
| lastSeen | Numerisk | Epok (UTC) |

**illegalTrafficByFirewallRules**

| Fältnamn | Typ | Lista över värden |
| ---------- | ---- | -------------- |
| **Server** | Sträng | IP-adress |
| **Klient** | Sträng | IP-adress |
| **Port** | Numerisk | - |
| **Transport** | Sträng | TCP, UDP eller ICMP |

**weakFirewallRules**

| Fältnamn | Typ | Lista över värden |
| ---------- | ---- | -------------- |
| **Källor** | JSON-matris med källor. Varje källa kan ha något av fyra format. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Destinationer** | JSON-matris med mål. Varje mål kan ha något av fyra format. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Portar** | JSON-matris med portar i något av tre format | "Any", "port (protocol, if detected)", "from port-to port (protocol, if detected)" |

**accessPoints**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **macAddress** | Sträng | MAC-adress |
| **Leverantör** | Sträng | Leverantörsnamn |
| **Ip** | Sträng | IP-adress eller ej a |
| **name** | Sträng | Enhetsnamn eller ej |
| **Trådlös** | Sträng | Nej, Misstänkt eller Ja |

**connectionsBetweenSubnets**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **Server** | Sträng | IP-adress |
| **Klient** | Sträng | IP-adress |

**industrialMalwareIndicators**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **detectionTime** | Numerisk | Epok (UTC) |
| **alertMessage** | Sträng | - |
| **Beskrivning** | Sträng | - |
| **Enheter** | JSON-matris | Enhetsnamn | 

**internetConnections**

| Fältnamn | Typ | Lista över värden |
| ---------- | ---- | -------------- |
| **internalAddress** | Sträng | IP-adress |
| **Auktoriserad** | Boolesk | Ja eller nej | 
| **externalAddresses** | JSON-matris | IP-adress |

#### <a name="response-example"></a>Exempel på svar

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/security | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Hämta sårbarheter i verksamheten – /api/v1/reports/vulnerabilities/operational

Använd det här API:et för att begära resultat av en allmän sårbarhetsbedömning. Den här utvärderingen ger inblick i nätverkets driftstatus. Den baseras på allmän nätverks- och systeminformation och inte på en specifik enhetsutvärdering.

#### <a name="method"></a>Metod

**Få**

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

JSON-objekt som representerar utvärderade resultat. Varje nyckel innehåller en JSON-matris med resultat.

#### <a name="result-fields"></a>Resultatfält

**Nycklar**

**backupServer**

| Fältnamn | Typ | Lista med värden |
|--|--|--|
| **Källkod** | Sträng | IP-adress |
| **Destination** | Sträng | IP-adress |
| **Port** | Numerisk | - |
| **Transport** | Sträng | TCP eller UDP |
| **backupMaximalInterval** | Sträng | - |
| **lastSeenBackup** | Numerisk | Epok (UTC) |

**ipNetworks**

| Fältnamn | Typ | Lista med värden |
|--|--|--|
| **addresse** s | Numerisk | - |
| **Nätverk** | Sträng | IP-adress |
| **mask** | Sträng | Nätmask |

**protocolProblems**

| Fältnamn | Typ | Lista med värden |
|--|--|--|
| **Protokollet** | Sträng | - |
| **Adresser** | JSON-matris | IP-adresser |
| **Alert** | Sträng | - |
| **reportTime** | Numerisk | Epok (UTC) |

**protocolDataVolumes**

| Fältnamn | Typ | Lista över värden |
|--|--|--|
| Protokollet | Sträng | - |
| volym | Sträng | "volymnummer MB" |

**Frånkopplingar**

| Fältnamn | Typ | Lista över värden |
|--|--|--|
| **assetAddress** | Sträng | IP-adress |
| **assetName** | Sträng | - |
| **lastDetectionTime** | Numerisk | Epok (UTC) |
| **backToNormalTime** | Numerisk | Epok (UTC) |     

#### <a name="response-example"></a>Exempel på svar

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/operational | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Verifiera autentiseringsuppgifter för användare – /api/extern/autentisering/validering

Använd det här API:et för att verifiera ett användarnamn och lösenord för Defender för IoT. Alla Defender for IoT-användarroller kan fungera med API:et.

Du behöver inte en Defender for IoT-åtkomsttoken för att använda det här API:et.

#### <a name="method"></a>Metod

**POST**

#### <a name="request-type"></a>Typ av begäran

**JSON**

#### <a name="query-parameters"></a>Frågeparametrar

| **Namn** | **Typ** | **Kan ha värdet Null** |
|--|--|--|
| **Användarnamn** | Sträng | No |
| **lösenord** | Sträng | No |

#### <a name="request-example"></a>Exempel på begäran

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Meddelandesträng med information om åtgärdsstatus:

- **Lyckades – msg:** Autentiseringen lyckades

- **Fel – fel:** Verifieringen av autentiseringsuppgifter misslyckades

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/external/authentication/validation | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/external/authentication/validation |

### <a name="change-password---externalauthenticationset_password"></a>Ändra lösenord – /external/authentication/set_password

Använd det här API:et för att låta användarna ändra sina egna lösenord. Alla Defender for IoT-användarroller kan arbeta med API:et. Du behöver inte en Defender for IoT-åtkomsttoken för att använda det här API:et.

#### <a name="method"></a>Metod

**POST**

#### <a name="request-type"></a>Typ av begäran

**JSON**

#### <a name="request-example"></a>Exempel på begäran

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Meddelandesträng med information om åtgärdsstatus:

- **Lyckades – msg:** Lösenordet har ersatts

- **Fel – fel:** Användarautentiseringsfel

- **Fel – fel:** Lösenordet matchar inte säkerhetsprincipen

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Enhetsfält

| **Namn** | **Typ** | **Kan ha värdet Null** |
|--|--|--|
| **Användarnamn** | Sträng | No |
| **lösenord** | Sträng | No |
| **new_password** | Sträng | No |

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/api/external/authentication/set_password | curl -k -d '{"username": "myUser","password": 1234@abcd " ","new_password": abcd@1234 " "}' -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Uppdatering av användarlösenord per systemadministratör – /extern/autentisering/set_password_by_admin

Använd det här API:et för att låta systemadministratörer ändra lösenord för angivna användare. Användarroller för Defender for IoT-administratörer kan fungera med API:et. Du behöver inte en Defender for IoT-åtkomsttoken för att använda det här API:et.

#### <a name="method"></a>Metod

**POST**

#### <a name="request-type"></a>Typ av begäran

**JSON**

#### <a name="request-example"></a>Exempel på begäran

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Meddelandesträng med information om åtgärdsstatus:

- **Lyckades – msg:** Lösenordet har ersatts

- **Fel – fel:** Autentiseringsfel för användare

- **Fel – fel:** Användaren finns inte

- **Fel – fel:** Lösenordet matchar inte säkerhetsprincipen

- **Fel – fel:** Användaren har inte behörighet att ändra lösenord

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Enhetsfält

| **Namn** | **Typ** | **Kan ha värdet Null** |
|--|--|--|
| **admin_username** | Sträng | No |
| **admin_password** | Sträng | No |
| **Användarnamn** | Sträng | No |
| **new_password** | Sträng | No |

#### <a name="curl-command"></a>Curl-kommando

> [!div class="mx-tdBreakAll"]
> | Typ | API:er | Exempel |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/api/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": 1234@abcd " ","username": "myUser","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>API-specifikationer för den lokala hanteringskonsolen ##

I det här avsnittet beskrivs API:er för den lokala hanteringskonsolen för:
- Avisering exkludering
- Enhetsinformation
- Aviseringsinformation

### <a name="alert-exclusions"></a>Aviserings undantag ###

Definiera villkor under vilka aviseringar inte ska skickas. Du kan till exempel definiera och uppdatera stopp- och starttider, enheter eller undernät som ska undantas när aviseringar utlöses, eller Defender för IoT-motorer som ska undantas. Under en underhållsfönstret kanske du till exempel vill stoppa leveransen av alla aviseringar, förutom aviseringar om skadlig kod på kritiska enheter. De objekt som du definierar här visas i fönstret Avisering uteslutningar i den lokala **hanteringskonsolen** som skrivskyddade undantagsregler.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Svarsexempel**

- **Svar:**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Ändra lösenord – /external/authentication/set_password 

Använd det här API:et för att låta användarna ändra sina egna lösenord. Alla Defender for IoT-användarroller kan arbeta med API:et. Du behöver inte en Defender for IoT-åtkomsttoken för att använda det här API:et.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Uppdatering av användarlösenord av systemadministratör – /extern/autentisering/set_password_by_admin 

Använd det här API:et för att låta systemadministratörer ändra lösenord för specifika användare. Defender for IoT-administratörsanvändarroller kan arbeta med API:et. Du behöver inte en Defender for IoT-åtkomsttoken för att använda det här API:et.

### <a name="retrieve-device-information---externalv1devices"></a>Hämta enhetsinformation – /external/v1/devices ###

Det här API:et begär en lista över alla enheter som identifieras av Defender for IoT-sensorer som är anslutna till en lokal hanteringskonsol.

#### <a name="method"></a>Metod

**Få**

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Matris med JSON-objekt som representerar enheter.

#### <a name="query-parameters"></a>Frågeparametrar

- **auktoriserat:** Filtrera endast auktoriserade och obehöriga enheter.

- **siteId:** Filtrera endast enheter som är relaterade till specifika platser.

- **zoneId:** Filtrera endast enheter som är relaterade till specifika zoner. [1](#1)

- **sensorId:** Om du bara vill filtrera enheter som identifieras av specifika sensorer. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *Du kanske inte har plats- och zon-ID:t. Om så är fallet frågar du alla enheter för att hämta plats- och zon-ID:t.*

#### <a name="query-parameters-example"></a>Exempel på frågeparametrar

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Enhetsfält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **sensorId** | Numerisk | No | - |
| **Zonid** | Numerisk | Yes | - |
| **siteId** | Numerisk | Yes | - |
| **ipAddresses** | JSON-matris | Yes | IP-adresser (kan vara mer än en adress i händelse av Internet-adresser eller en enhet med dubbla nätverkskort) |
| **name** | Sträng | No | - |
| **Typ** | Sträng | No | Unknown, Engineering Station,ROU, HMI, Domain Controller, DB Server, Wireless Access Point, Router, Switch, Server, Workstation, IP Camera, Printer, Firewall, Terminal station, VPN Gateway, Internet eller Multicast och Broadcast |
| **macAddresses** | JSON-matris | Yes | MAC-adresser (kan vara mer än en adress om en enhet har dubbla nätverkskort) |
| **operatingSystem** | Sträng | Yes | - |
| **engineeringStation** | Boolesk | No | Sant eller falskt |
| **Scanner** | Boolesk | No | Sant eller falskt |
| **Auktoriserad** | Boolesk | No | Sant eller falskt |
| **Leverantör** | Sträng | Yes | - |
| **Protokoll** | JSON-matris | Yes | Protokollobjekt |
| **Firmware** | JSON-matris | Yes | Objekt för inbyggd programvara |

#### <a name="protocol-fields"></a>Protokollfält

| Namn | Typ | Kan ha värdet null | Lista över värden |
|--|--|--|--|
| Name | Sträng | No | - |
| Adresser | JSON-matris | Yes | Huvudvärden eller numeriska värden |

#### <a name="firmware-fields"></a>Fält för inbyggd programvara

| Namn | Typ | Kan ha värdet null | Lista över värden |
|--|--|--|--|
| **Seriell** | Sträng | No | Ej A, eller det faktiska värdet |
| **Modell** | Sträng | No | Ej A, eller det faktiska värdet |
| **versionInbyggdProgramvara** | Double | No | Ej A, eller det faktiska värdet |
| **additionalData** | Sträng | No | Ej A, eller det faktiska värdet |
| **moduleAddress** | Sträng | No | Ej A, eller det faktiska värdet |
| **Rack** | Sträng | No | Ej A, eller det faktiska värdet |
| **Slot** | Sträng | No | Ej aktuellt eller faktiskt värde |
| **Adress** | Sträng | No | Ej aktuellt eller faktiskt värde |

#### <a name="response-example"></a>Exempel på svar

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true' |

### <a name="retrieve-alert-information---externalv1alerts"></a>Hämta aviseringsinformation – /external/v1/alerts

Använd det här API:et för att hämta alla eller filtrerade aviseringar från en lokal hanteringskonsol.

#### <a name="method"></a>Metod

**Få**

#### <a name="query-parameters"></a>Frågeparametrar

- **tillstånd:** Filtrera endast hanterade och ohanterade aviseringar.

  **Exempel**:

  `/api/v1/alerts?state=handled`

- **fromTime:** För att filtrera aviseringar som skapats från en viss tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** Om du bara vill filtrera aviseringar som skapats före en viss tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteId:** Den plats där aviseringen identifierades.
- **zoneId:** Zonen där aviseringen identifierades.
- **sensor**: Sensorn som aviseringen identifierades på.

*Du kanske inte har plats- och zon-ID:t. Om så är fallet frågar du alla enheter för att hämta plats- och zon-ID:t.*

#### <a name="alert-fields"></a>Aviseringsfält 

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **ID** | Numerisk | No | - |
| **Tid** | Numerisk | No | Epok (UTC) |
| **title** | Sträng | No | - |
| **Meddelande** | Sträng | No | - |
| **Svårighetsgrad** | Sträng | No | Varning, Mindre, Större eller Kritisk |
| **Motor** | Sträng | No | Protokollöverträdelse, principöverträdelse, skadlig kod, avvikelse eller drift |
| **sourceDevice** | Numerisk | Yes | Enhets-ID |
| **destinationDevice** | Numerisk | Yes | Enhets-ID |
| **sourceDeviceAddress** | Numerisk | Yes | IP, MAC, Null |
| **destinationDeviceAddress** | Numerisk | Yes | IP, MAC, Null |
| **remediationSteps** | Sträng | Yes | Åtgärdssteg som visas i aviseringen|
| **sensorName** | Sträng | Yes | Namnet på sensorn som definierats av användaren i konsolen|
|**Zonnamn** | Sträng | Yes | Namnet på zonen som är associerad med sensorn i konsolen|
| **Platsnamn** | Sträng | Yes | Namnet på den plats som är associerad med sensorn i konsolen |
| **ytterligare information** | Ytterligare informationsobjekt | Yes | - |

Observera att /api/v2/ krävs för följande information:

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps
- sensorName
- Zonnamn
- Platsnamn

#### <a name="additional-information-fields"></a>Ytterligare informationsfält

| Namn | Typ | Kan ha värdet null | Lista över värden |
|--|--|--|--|
| **Beskrivning** | Sträng | No | - |
| **Information** | JSON-matris | Inga | Sträng |

#### <a name="response-example"></a>Exempel på svar

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

#### <a name="curl-command"></a>Curl-kommando

> [!div class="mx-tdBreakAll"]
> | Typ | API:er | Exempel |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/external/v1/alerts?state=unhandled&zoneId=1&fromTime=0&toTime=1594551777000&siteId=1&sensor=1' |

### <a name="qradar-alerts"></a>QRadar-aviseringar

QRadar-integrering med Defender for IoT hjälper dig att identifiera de aviseringar som genereras av Defender for IoT och utföra åtgärder med dessa aviseringar. QRadar tar emot data från Defender för IoT och kontaktar sedan komponenten för den offentliga API:et för den lokala hanteringskonsolen.

Om du vill skicka data som identifieras av Defender for IoT till QRadar definierar  du en vidarespeeringsregel i Defender for IoT-systemet och väljer alternativet Fjärrsupportaviseringshantering.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Redigera vidare vidarebefordransregler så att de matchar dina behov.":::

När du väljer det här alternativet när du konfigurerar vidare vidarebefordransregler visas följande ytterligare fält i QRadar:

- **UUID:** Unik aviseringsidentifierare, till exempel 1-1555245116250.

- **Plats:** Platsen där aviseringen identifierades.

- **Zon:** Zonen där aviseringen identifierades.

Exempel på nyttolasten som skickas till QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/ &lt; UUID&gt;

#### <a name="method"></a>Metod

**PUT**

#### <a name="request-type"></a>Typ av begäran

**JSON**

#### <a name="request-content"></a>Begära innehåll

JSON-objekt som representerar åtgärden som ska utföras på aviseringen som innehåller UUID.

#### <a name="action-fields"></a>Åtgärdsfält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **åtgärd** | Sträng | No | handle eller handleAndLearn |

#### <a name="request-example"></a>Exempel på begäran

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Matris med JSON-objekt som representerar enheter.

#### <a name="response-fields"></a>Svarsfält


| Namn | Typ | Kan ha värdet null | Description |
|--|--|--|--|
| **innehåll/fel** | Sträng | No | Om begäran lyckas visas innehållsegenskapen. Annars visas felegenskapen. |

#### <a name="possible-content-values"></a>Möjliga innehållsvärden

| Statuskod | Innehållsvärdet | Description |
|--|--|--|
| 200 | Aviseringsuppdateringsbegäran slutfördes. | Uppdateringsbegäran slutfördes. Inga kommentarer. |
| 200 | Aviseringen hanterades redan (**handle**). | Aviseringen hanterades redan när en hanteringsbegäran för aviseringen togs emot.<br />Aviseringen förblir **hanterad.** |
| 200 | Aviseringen har redan hanterats och lärts in (**handleAndLearn**). | Aviseringen hanterades redan och lärdes in när en begäran **om att hanteraAndLearn** togs emot.<br />Aviseringen finns kvar i **statusen handledAndLearn.** |
| 200 | Aviseringen hanterades redan (**hanterades**).<br />Handle and learn (**handleAndLearn**) utfördes på aviseringen. | Aviseringen hanterades redan när en begäran om **att hanteraAndLearn** togs emot.<br />Aviseringen blir **handleAndLearn**. |
| 200 | Aviseringen har redan hanterats och lärts in (**handleAndLearn**). Ignorerad referensbegäran. | Aviseringen hanterades **redanAndLearn när** en begäran om att hantera aviseringen togs emot. Aviseringen förblir **handleAndLearn**. |
| 500 | Ogiltig åtgärd. | Den åtgärd som skickades är inte en giltig åtgärd att utföra på aviseringen. |
| 500 | Ett oväntat fel inträffade. | Det uppstod ett oväntat fel. Lös problemet genom att kontakta teknisk support. |
| 500 | Det gick inte att köra begäran eftersom ingen avisering hittades för detta UUID. | Det angivna aviserings-UUID:t hittades inte i systemet. |

#### <a name="response-example"></a>Exempel på svar

**Framgångsrika**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Misslyckade**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| PUT | curl -k -X PUT -d '{"action": " <ACTION> "}' -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/alerts/<UUID> | curl -k -X PUT -d '{"action": "handle"}' -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Aviserings undantag (underhållsfönstret) – /external/v1/maintenanceWindow

Definiera villkor under vilka aviseringar inte ska skickas. Du kan till exempel definiera och uppdatera stopp- och starttider, enheter eller undernät som ska undantas när aviseringar utlöses, eller Defender för IoT-motorer som ska undantas. Under en underhållsfönstret kanske du till exempel vill stoppa aviseringsleveransen av alla aviseringar, förutom aviseringar om skadlig kod på kritiska enheter.

DE API:er som du definierar här visas i fönstret Aviserings undantag i den lokala hanteringskonsolen som en skrivskyddade **undantagsregel.**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Fönstret Aviserings exkludering visar en lista över alla undantagsregler. ":::

#### <a name="method---post"></a>Metod – POST

#### <a name="query-parameters"></a>Frågeparametrar

- **ticketId:** Definierar underhållsbiljett-ID:t i användarens system.

- **ttl:** Definierar TTL-värdet (time to live), som är underhållsperiodens varaktighet i minuter. Efter den tidsperiod som den här parametern definierar börjar systemet automatiskt skicka aviseringar.

- **engines**: Definierar från vilken säkerhetsmotor som ska förhindra aviseringar under underhållsprocessen:

   - Anomali

   - Malware

   - Operativa

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds:** Definierar från vilken Defender for IoT-sensor som aviseringar ska förhindras under underhållsprocessen. Det är samma ID som hämtades från /api/v1/appliances (GET).

- **undernät:** Definierar från vilket undernät som aviseringar ska förhindras under underhållsprocessen. Undernätet skickas i följande format: 192.168.0.0/16.

#### <a name="error-codes"></a>Felkoder

- **201 (skapad):** Åtgärden slutfördes.

- **400 (felaktig begäran):** Visas i följande fall:

   - Parametern **ttl** är inte numerisk eller inte positiv.

   - Parametern **för undernät** definierades med fel format.

   - Parametern **ticketId** saknas.

   - **Motorparametern** matchar inte de befintliga säkerhetsmotorerna.

- **404 (hittades inte):** En av sensorerna finns inte.

- **409 (konflikt):** Biljett-ID:t är länkat till en annan öppen underhållsfönstret.

- **500 (internt serverfel):** Annat oväntat fel.

> [!NOTE]
> Kontrollera att biljett-ID:t inte är länkat till ett befintligt öppet fönster. Följande undantagsregel genereras: Maintenance-{token name}-{ticket ID}.

#### <a name="method---put"></a>Metod – PUT

Tillåter uppdatering av underhållsperiodens varaktighet efter att du har börjat underhållsprocessen genom att ändra **ttl-parametern.** Den nya varaktighetsdefinitionen åsidosätter den tidigare.

Den här metoden är användbar när du vill ange en längre varaktighet än den för närvarande konfigurerade varaktigheten.

#### <a name="query-parameters"></a>Frågeparametrar

- **ticketId:** Definierar underhållsbiljett-ID:t i användarens system.

- **ttl:** Definierar varaktigheten för fönstret i minuter.

#### <a name="error-code"></a>Felkod

- **200 (OK):** Åtgärden har slutförts.

- **400 (felaktig begäran):** Visas i följande fall:

   - Parametern **ttl** är inte numerisk eller inte positiv.

   - Parametern **ticketId** saknas.

   - Parametern **ttl** saknas.

- **404 (hittades inte):** Biljett-ID:t är inte länkat till en öppen underhållsfönstret.

- **500 (internt serverfel):** Andra oväntade fel.

> [!NOTE]
> Kontrollera att biljett-ID:t är länkat till ett befintligt öppet fönster.

#### <a name="method---delete"></a>Metod – DELETE

Stänger en befintlig underhållsfönstret.

#### <a name="query-parameters"></a>Frågeparametrar

- **ticketId:** Loggar underhållsbiljett-ID:t i användarens system.

#### <a name="error-code"></a>Felkod

- **200 (OK):** Åtgärden har slutförts.

- **400 (felaktig begäran):** **Parametern ticketId** saknas.

- **404 (hittades inte):** Biljett-ID:t är inte länkat till en öppen underhållsfönstret.

- **500 (internt serverfel):** Annat oväntat fel.

> [!NOTE]
> Kontrollera att biljett-ID:t är länkat till ett befintligt öppet fönster.

#### <a name="method---get"></a>Metod – GET

Hämta en logg över alla öppna, stäng och uppdatera åtgärder som utfördes i systemet under underhållet. Du kan bara hämta en logg för underhållsfönster som har varit aktiva tidigare och har stängts.

#### <a name="query-parameters"></a>Frågeparametrar

- **fromDate:** Filtrerar loggarna från det fördefinierade datumet och senare. Formatet är 2019-12-30.

- **toDate:** Filtrerar loggarna fram till det fördefinierade datumet. Formatet är 2019-12-30.

- **ticketId:** Filtrerar loggarna som är relaterade till ett specifikt biljett-ID.

- **tokenName:** Filtrerar loggarna som är relaterade till ett specifikt tokennamn.

#### <a name="error-code"></a>Felkod 

- **200 (OK):** Åtgärden slutfördes.

- **400 (felaktig begäran):** Datumformatet är fel.

- **204 (inget innehåll):** Det finns inga data att visa.

- **500 (internt serverfel):** Annat oväntat fel.

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Matris med JSON-objekt som representerar underhållsfönstrets åtgärder.

#### <a name="response-structure"></a>Svarsstruktur

| Namn | Typ | Kommentar | Kan ha värdet null |
|--|--|--|--|
| **Datetime** | Sträng | Exempel: "2012-04-23T18:25:43.511Z" | nej |
| **ticketId** | Sträng | Exempel: "9a5fe99c-d914-4bda-9332-307384fe40bf" | nej |
| **tokenName** | Sträng | - | nej |
| **Motorer** | Strängmatris | - | ja |
| **sensorIds** | Strängmatris | - | ja |
| **Undernät** | Strängmatris | - | ja |
| **ttl** | Numerisk | - | ja |
| **operationType** | Sträng | Värdena är "OPEN", "UPDATE" och "CLOSE" | nej |

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| POST | curl -k -X POST -d '{"ticketId": "<TICKET_ID>",ttl": <TIME_TO_LIVE>,"engines": [<ENGINE1, ENGINE2... ENGINEn>],"sensorIds": [<SENSOR_ID1, SENSOR_ID2... SENSOR_IDn>],"subnets": [<SUBNET1, SUBNET2.... SUBNETn>]}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20","engines": ["ANOMALY"],"sensorIds": ["5","3 "],"subnets": ["10.0.0.3"]}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| PUT | curl -k -X PUT -d '{"ticketId": "<TICKET_ID>",ttl": "<TIME_TO_LIVE>"}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| DELETE | curl -k -X DELETE -d '{"ticketId": "<TICKET_ID>"}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId=&tokenName=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/ <span> /127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-01-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Autentisera autentiseringsuppgifter för användare – /external/authentication/validation

Använd det här API:et för att verifiera användarautentiseringsuppgifter. Alla Defender for IoT-användarroller kan arbeta med API:et. Du behöver inte en Defender for IoT-åtkomsttoken för att använda det här API:et.

#### <a name="method"></a>Metod

**POST**

#### <a name="request-type"></a>Typ av begäran

**JSON**

#### <a name="request-example"></a>Exempel på begäran

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Meddelandesträng med information om åtgärdsstatus:

- **Lyckades – msg:** Autentiseringen lyckades

- **Fel – fel:** Verifieringen av autentiseringsuppgifter misslyckades

#### <a name="device-fields"></a>Enhetsfält

| **Namn** | **Typ** | **Kan ha värdet Null** |
|--|--|--|
| **Användarnamn** | Sträng | No |
| **lösenord** | Sträng | No |

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| POST | curl -k -d '{"username":"<USER_NAME>","password":"PASSWORD"}' 'https://<IP_ADDRESS>/external/authentication/validation' | curl -k -d '{"username":"myUser","password":" 1234@abcd "}' 'https:/ <span> /127.0.0.1/external/authentication/validation' |

### <a name="change-password---externalauthenticationset_password"></a>Ändra lösenord – /external/authentication/set_password

Använd det här API:et för att låta användarna ändra sina egna lösenord. Alla Defender for IoT-användarroller kan arbeta med API:et. Du behöver inte en Defender for IoT-åtkomsttoken för att använda det här API:et.

#### <a name="method"></a>Metod

**POST**

#### <a name="request-type"></a>Typ av begäran

**JSON**

#### <a name="request-example"></a>Exempel på begäran

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Meddelandesträng med information om åtgärdsstatus:

- **Lyckades – msg:** Lösenordet har ersatts

- **Fel – fel:** Autentiseringsfel för användare

- **Fel – fel:** Lösenordet matchar inte säkerhetsprincipen

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Enhetsfält

| **Namn** | **Typ** | **Kan ha värdet Null** |
|--|--|--|
| **Användarnamn** | Sträng | No |
| **lösenord** | Sträng | No |
| **new_password** | Sträng | No |

#### <a name="curl-command"></a>Curl-kommando

| Typ | API:er | Exempel |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password | curl -k -d '{"username": "myUser","password": 1234@abcd " ","new_password": abcd@1234 " "}' -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Uppdatering av användarlösenord per systemadministratör – /extern/autentisering/set_password_by_admin

Använd det här API:et för att låta systemadministratörer ändra lösenord för angivna användare. Defender for IoT-administratörsanvändarroller kan fungera med API:et. Du behöver inte en Defender for IoT-åtkomsttoken för att använda det här API:et.

#### <a name="method"></a>Metod

**POST**

#### <a name="request-type"></a>Typ av begäran

**JSON**

#### <a name="request-example"></a>Exempel på begäran

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svarsinnehåll

Meddelandesträng med information om åtgärdsstatus:

- **Lyckades – msg:** Lösenordet har ersatts

- **Fel – fel:** Autentiseringsfel för användare

- **Fel – fel:** Användaren finns inte

- **Fel – fel:** Lösenordet matchar inte säkerhetsprincipen

- **Fel – fel:** Användaren har inte behörighet att ändra lösenord

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Enhetsfält

| **Namn** | **Typ** | **Kan ha värdet Null** |
|--|--|--|
| **admin_username** | Sträng | No |
| **admin_password** | Sträng | No |
| **Användarnamn** | Sträng | No |
| **new_password** | Sträng | No |

#### <a name="curl-command"></a>Curl-kommando

> [!div class="mx-tdBreakAll"]
> | Typ | API:er | Exempel |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}" -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": 1234@abcd " ","username": "myUser","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password_by_admin |

## <a name="next-steps"></a>Nästa steg

- [Undersök identifieringar av sensorer i en enhetsinventering](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Undersök alla identifieringar av företagssensorer i en enhetsinventering](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
