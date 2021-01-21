---
title: Arbeta med Defender for IoT-API:er
description: Använd en extern REST API för att komma åt data som upptäckts av sensorer och hanterings konsoler och utföra åtgärder med dessa data.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 44ea6e8343203a9cb18947f31f45aa0b023178b0
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624582"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender för IoT sensor-och Management Console-API: er

Använd en extern REST API för att komma åt data som upptäckts av sensorer och hanterings konsoler och utföra åtgärder med dessa data.

Anslutningarna skyddas via SSL.

## <a name="getting-started"></a>Komma igång

I allmänhet måste du generera en åtkomsttoken när du använder ett externt API på Azure Defender för IoT-sensorn eller den lokala hanterings konsolen. Tokens krävs inte för API: er för autentisering som du använder på sensorn och den lokala hanterings konsolen.

Så här genererar du en token:

1. Välj **åtkomsttoken** i fönstret **system inställningar** .
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Skärm bild av fönstret system inställningar som markerar knappen åtkomsttoken.":::

2. Välj **generera ny token**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Välj knappen för att generera en ny token.":::

3. Beskriv syftet med den nya token och välj **Nästa**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Generera en ny token och ange namnet på den integrering som är kopplad till den.":::

4. Åtkomsttoken visas. Kopiera den, eftersom den inte visas igen.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Kopiera din åtkomsttoken för din integrering.":::

5. Välj **Slutför**. De token som du skapar visas **i dialog rutan åtkomsttoken.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Skärm bild av dialog rutan token för enhet med fyllda token":::

   **Används** anger sista gången ett externt anrop med denna token togs emot.

   Om **N/A** visas i fältet **används** för denna token fungerar inte anslutningen mellan sensorn och den anslutna servern.

6. Lägg till ett HTTP-huvud med namnet **auktorisering** till din begäran och ange värdet till den token som du skapade.

## <a name="sensor-api-specifications"></a>Sensor-API-specifikationer

I det här avsnittet beskrivs följande sensor-API: er:

- /api/v1/devices

- /api/v1/devices/connections

- /api/v1/devices/cves

- /api/v1/alerts

- /api/v1/events

- /api/v1/reports/vulnerabilities/devices

- /api/v1/reports/vulnerabilities/security

- /api/v1/reports/vulnerabilities/operational

- /api/external/authentication/validation

- /External/Authentication/set_password

- /External/Authentication/set_password_by_admin

### <a name="retrieve-device-information"></a>Hämta enhets information

Använd detta API för att begära en lista över alla enheter som en Defender for IoT-sensor har identifierat.

#### <a name="apiv1devices"></a>/api/v1/devices

#### <a name="method"></a>Metod

**TA**

Begär en lista över alla enheter som Defender for IoT-sensorn har identifierat.

#### <a name="query-parameters"></a>Frågeparametrar

- **auktoriserad**: filtrera endast auktoriserade och oauktoriserade enheter.

  **Exempel**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

Matris med JSON-objekt som representerar enheter.

#### <a name="device-fields"></a>Enhets fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **id** | Numerisk | No | - |
| **IP-adresser** | JSON-matris | Yes | IP-adresser (kan vara mer än en adress i fråga om Internet adresser eller en enhet med dubbla nätverkskort) |
| **name** | Sträng | No | - |
| **bastyp** | Sträng | No | Okänd, teknik Station, PLC, HMI, historian, domänkontrollant, DB-server, trådlös åtkomst punkt, router, växel, Server, arbets Station, IP-kamera, skrivare, brand vägg, Terminal Station, VPN Gateway, Internet eller multicast och sändning |
| **macAddresses** | JSON-matris | Yes | MAC-adresser (kan vara mer än en adress i händelse av en enhet med dubbla nätverkskort) |
| **operatingSystem** | Sträng | Yes | - |
| **engineeringStation** | Boolesk | No | Sant eller falskt |
| **läser** | Boolesk | No | Sant eller falskt |
| **auktorisation** | Boolesk | No | Sant eller falskt |
| **leverantörsspecifika** | Sträng | Yes | - |
| **protokoll** | JSON-matris | Yes | Protokoll objekt |
| **inbyggd program vara** | JSON-matris | Yes | Firmware-objekt |

#### <a name="protocol-fields"></a>Protokoll fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **Namn** | Sträng | No |  |
| **Adresser** | JSON-matris | Yes | Huvud-eller numeriska värden |

#### <a name="firmware-fields"></a>Fält för inbyggd program vara

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **nummer** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **förlag** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **versionInbyggdProgramvara** | Double | No | Ej tillämpligt eller det faktiska värdet |
| **additionalData** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **moduleAddress** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **räck** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **stack** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **adresspool** | Sträng | No | Ej tillämpligt eller det faktiska värdet |

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

### <a name="retrieve-device-connection-information"></a>Hämta anslutnings information för enhet

Använd det här API: et för att begära en lista över alla anslutningar per enhet.

#### <a name="apiv1devicesconnections"></a>/api/v1/devices/connections

#### <a name="method"></a>Metod

**TA**

#### <a name="query-parameters"></a>Frågeparametrar

Om du inte anger frågeparametrar returneras alla enhets anslutningar.

**Exempel**:

`/api/v1/devices/connections`

- **deviceId**: Filtrera efter ett angivet enhets-ID för att visa dess anslutningar.

  **Exempel**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: tids ramen från nu bakåt, efter minut, då anslutningarna var aktiva.

  **Exempel**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: filtrera endast anslutningar som har identifierats före en angiven tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: filtrera endast anslutningar som har identifierats efter en angiven tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

Matris med JSON-objekt som representerar enhets anslutningar.

#### <a name="fields"></a>Fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **firstDeviceId** | Numerisk | No | - |
| **secondDeviceId** | Numerisk | No | - |
| **lastSeen** | Numerisk | No | Epok (UTC) |
| **Hitta** | Numerisk | No | Epok (UTC) |
| **hamnarna** | Nummer mat ris | No | - |
| **protokoll** | JSON-matris | No | Protokoll fält |

#### <a name="protocol-field"></a>Protokoll fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **name** | Sträng | No | - |
| **kommandon** | Strängmatris | No | - |

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

### <a name="retrieve-information-on-cves"></a>Hämta information om CVEs

Använd det här API: et för att begära en lista över alla kända CVEs som identifierats på enheter i nätverket.

#### <a name="apiv1devicescves"></a>/api/v1/devices/cves

#### <a name="method"></a>Metod

**TA**

#### <a name="query-parameters"></a>Frågeparametrar

Som standard tillhandahåller detta API listan över alla enhets-IP: er med CVEs, upp till 100 översta CVEs för varje IP-adress.

**Exempel**:

`/api/v1/devices/cves`

- **deviceId**: Filtrera efter en speciell enhets-IP-adress för att få upp till 100 CVEs som identifieras på den aktuella enheten.

  **Exempel**:

  `/api/v1/devices/<ipAddress>/cves`

- **överkant**: hur många CVEs som ska hämtas för varje ENHETs IP-adress.

  **Exempel**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

Matris med JSON-objekt som representerar CVEs som identifieras på IP-adresser.

#### <a name="fields"></a>Fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **cveId** | Sträng | No | - |
| **Adresser** | Sträng | No | IP-adress |
| **resultat** | Sträng | No | 0,0 – 10,0 |
| **attackVector** | Sträng | No | Nätverk, intilliggande nätverk, lokalt eller fysiskt |
| **beteckning** | Sträng | No | - |

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

### <a name="retrieve-alert-information"></a>Hämta aviserings information

Använd detta API för att begära en lista över alla aviseringar som Defender for IoT-sensorn har identifierat.

#### <a name="apiv1alerts"></a>/api/v1/alerts

#### <a name="method"></a>Metod

**TA**

#### <a name="query-parameters"></a>Frågeparametrar

- **status**: om du bara vill filtrera hanterade eller ohanterade aviseringar.

  **Exempel**:

  `/api/v1/alerts?state=handled`

- **fromTime**: filtrera aviseringar som skapats från en angiven tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: för att filtrera aviseringar som skapats före en angiven tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/alerts?toTime=<epoch>`

- **typ**: filtrera aviseringar efter en speciell typ. Befintliga typer att filtrera efter: oväntade nya enheter, från kopplingar.

  **Exempel**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

Matris med JSON-objekt som representerar aviseringar.

#### <a name="alert-fields"></a>Aviserings fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **ID** | Numerisk | No | - |
| **tid** | Numerisk | No | Epok (UTC) |
| **title** | Sträng | No | - |
| **meddelande** | Sträng | No | - |
| **allvarlighets grad** | Sträng | No | Varning, mindre, större eller kritiskt |
| **motorn** | Sträng | No | Protokoll överträdelse, princip överträdelse, skadlig kod, avvikelse eller drift |
| **sourceDevice** | Numerisk | Yes | Enhets-ID |
| **destinationDevice** | Numerisk | Yes | Enhets-ID |
| **additionalInformation** | Ytterligare informations objekt | Yes | - |

#### <a name="additional-information-fields"></a>Ytterligare informations fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **beteckning** | Sträng | No | - |
| **Mer** | JSON-matris | Nej | Sträng |

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

### <a name="retrieve-timeline-events"></a>Hämta tids linje händelser

Använd det här API: et för att begära en lista över händelser som rapporter ATS till händelsens tids linje.

#### <a name="apiv1events"></a>/api/v1/events

#### <a name="method"></a>Metod

**TA**

#### <a name="query-parameters"></a>Frågeparametrar

- **minutesTimeFrame**: tids ramen från nu bakåt, efter minut, där händelserna rapporterades.

  **Exempel**:

  `/api/v1/events?minutesTimeFrame=20`

- **typ**: filtrera händelse listan efter en speciell typ.

  **Exempel**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

Matris med JSON-objekt som representerar aviseringar.

#### <a name="event-fields"></a>Händelse fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|--|
| **tidsstämpel** | Numerisk | No | Epok (UTC) |
| **title** | Sträng | No | - |
| **allvarlighets grad** | Sträng | No | INFORMATION, meddelande eller AVISERING |
| **innehavare** | Sträng | Yes | Om händelsen skapades manuellt, kommer det här fältet innehålla det användar namn som skapade händelsen |
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

### <a name="retrieve-vulnerability-information"></a>Hämta sårbarhets information

Använd det här API: et för att begära sårbarhets bedömnings resultat för varje enhet.

#### <a name="apiv1reportsvulnerabilitiesdevices"></a>/api/v1/reports/vulnerabilities/devices

#### <a name="method"></a>Metod

**TA**

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

Matris med JSON-objekt som representerar utvärderade enheter.

Objektet enhet innehåller:

- Allmänna data

- Bedömnings Poäng

- Sårbarheter

#### <a name="device-fields"></a>Enhets fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **name** | Sträng | No | - |
| **IP-adresser** | JSON-matris | No | - |
| **securityScore** | Numerisk | No | - |
| **leverantörsspecifika** | Sträng | Yes |  |
| **versionInbyggdProgramvara** | Sträng | Yes | - |
| **förlag** | Sträng | Yes | - |
| **isWirelessAccessPoint** | Boolesk | No | Sant eller falskt |
| **operatingSystem** | Operativ system objekt | Yes | - |
| **sårbarheter** | Sårbarhets-objekt | Yes | - |

#### <a name="operating-system-fields"></a>Fält för operativ system

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **Namn** | Sträng | Yes | - |
| **Typ** | Sträng | Yes | - |
| **Version** | Sträng | Yes | - |
| **latestVersion** | Sträng | Yes | - |

#### <a name="vulnerabilities-fields"></a>Sårbarhets fält
 
| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **Virus** | JSON-matris | Yes | Antivirus namn |
| **plainTextPasswords** | JSON-matris | Yes | Lösen ords objekt |
| **remoteAccess** | JSON-matris | Yes | Fjärråtkomst-objekt |
| **isBackupServer** | Boolesk | No | Sant eller falskt |
| **openedPorts** | JSON-matris | Yes | Öppna port objekt |
| **isEngineeringStation** | Boolesk | No | Sant eller falskt |
| **isKnownScanner** | Boolesk | No | Sant eller falskt |
| **cves** | JSON-matris | Yes | CVE-objekt |
| **isUnauthorized** | Boolesk | No | Sant eller falskt |
| **malwareIndicationsDetected** | Boolesk | No | Sant eller falskt |
| **weakAuthentication** | JSON-matris | Yes | Identifierade program som använder svag autentisering |

#### <a name="password-fields"></a>Fält för lösen ord

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **lösenord** | Sträng | No | - |
| **protokollhanterare** | Sträng | No | - |
| **styrka** | Sträng | No | Mycket svaga, svaga, medel eller starka |

#### <a name="remote-access-fields"></a>Fält för fjärråtkomst

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **lastning** | Numerisk | No | - |
| **källtransportadr** | Sträng | No | TCP eller UDP |
| **klientsession** | Sträng | No | IP-adress |
| **clientSoftware** | Sträng | No | SSH, VNC, fjärr skrivbord eller team Viewer |

#### <a name="open-port-fields"></a>Öppna port fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **lastning** | Numerisk | No | - |
| **källtransportadr** | Sträng | No | TCP eller UDP |
| **protokollhanterare** | Sträng | Yes | - |
| **isConflictingWithFirewall** | Boolesk | No | Sant eller falskt |

#### <a name="cve-fields"></a>CVE-fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **ID** | Sträng | No | - |
| **resultat** | Numerisk | No | Double |
| **beteckning** | Sträng | No | - |

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

### <a name="retrieve-security-vulnerabilities"></a>Hämta säkerhets risker

Använd detta API för att begära resultat från en allmän sårbarhets bedömning. Den här utvärderingen ger inblick i systemets säkerhets nivå.

Den här utvärderingen baseras på allmän nätverks-och system information och inte för en speciell enhets utvärdering.

#### <a name="apiv1reportsvulnerabilitiessecurity"></a>/api/v1/reports/vulnerabilities/security

#### <a name="method"></a>Metod

**TA**

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

JSON-objekt som representerar utvärderade resultat. Varje nyckel kan vara null. Annars kommer den att innehålla ett JSON-objekt med nycklar som inte kan ha värdet null.

### <a name="result-fields"></a>Resultat fält

**Nycklar**

**unauthorizedDevices**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **adresspool** | Sträng | IP-adress |
| **name** | Sträng | - |
| **firstDetectionTime** | Numerisk | Epok (UTC) |
| lastSeen | Numerisk | Epok (UTC) |

**illegalTrafficByFirewallRules**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **servernamn** | Sträng | IP-adress |
| **klientsession** | Sträng | IP-adress |
| **lastning** | Numerisk | - |
| **källtransportadr** | Sträng | TCP, UDP eller ICMP |

**weakFirewallRules**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **ursprung** | JSON-matris med källor. Varje källa kan vara i något av fyra format. | "Any", "IP-adress (värd)", "från IP-till-IP (intervall)", "IP-adress, nätmask (nätverk)" |
| **bestämmelser** | JSON-matris med mål. Varje mål kan vara i något av fyra format. | "Any", "IP-adress (värd)", "från IP-till-IP (intervall)", "IP-adress, nätmask (nätverk)" |
| **hamnarna** | JSON-matris av portar i något av tre format | "Any", "port (protokoll, om identifierat)", "från Port-till-port (protokoll, om identifierat)" |

**accessPoints**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **macAddress** | Sträng | MAC-adress |
| **leverantörsspecifika** | Sträng | Leverantörs namn |
| **Adresser** | Sträng | IP-adress eller ej tillämpligt |
| **name** | Sträng | Enhets namn eller ej tillämpligt |
| **trådlösa** | Sträng | Nej, misstänkt eller Ja |

**connectionsBetweenSubnets**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **servernamn** | Sträng | IP-adress |
| **klientsession** | Sträng | IP-adress |

**industrialMalwareIndicators**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **detectionTime** | Numerisk | Epok (UTC) |
| **Alertmessage som** | Sträng | - |
| **beteckning** | Sträng | - |
| **enhet** | JSON-matris | Enhets namn | 

**internetConnections**

| Fältnamn | Typ | Lista med värden |
| ---------- | ---- | -------------- |
| **internalAddress** | Sträng | IP-adress |
| **auktorisation** | Boolesk | Ja eller nej | 
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

### <a name="retrieve-operational-vulnerabilities"></a>Hämta drifts sårbarheter

Använd detta API för att begära resultat från en allmän sårbarhets bedömning. Den här utvärderingen ger inblick i nätverkets drift status. Den baseras på allmän nätverks-och system information och inte på en speciell enhets utvärdering.

#### <a name="apiv1reportsvulnerabilitiesoperational"></a>/api/v1/reports/vulnerabilities/operational

#### <a name="method"></a>Metod

**TA**

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

JSON-objekt som representerar utvärderade resultat. Varje nyckel innehåller en JSON-matris med resultat.

#### <a name="result-fields"></a>Resultat fält

**Nycklar**

**backupServer**

| Fältnamn | Typ | Lista med värden |
|--|--|--|
| **källicensservern** | Sträng | IP-adress |
| **mål** | Sträng | IP-adress |
| **lastning** | Numerisk | - |
| **källtransportadr** | Sträng | TCP eller UDP |
| **backupMaximalInterval** | Sträng | - |
| **lastSeenBackup** | Numerisk | Epok (UTC) |

**ipNetworks**

| Fältnamn | Typ | Lista med värden |
|--|--|--|
| **mottagare** s | Numerisk | - |
| **nätverks** | Sträng | IP-adress |
| **mask** | Sträng | Nätmask |

**protocolProblems**

| Fältnamn | Typ | Lista med värden |
|--|--|--|
| **protokollhanterare** | Sträng | - |
| **adresser** | JSON-matris | IP-adresser |
| **varningar** | Sträng | - |
| **reportTime** | Numerisk | Epok (UTC) |

**protocolDataVolumes**

| Fältnamn | Typ | Lista med värden |
|--|--|--|
| protokollhanterare | Sträng | - |
| volym | Sträng | "volym nummer MB" |

**från kopplingar**

| Fältnamn | Typ | Lista med värden |
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

### <a name="validate-user-credentials"></a>Verifiera användarautentiseringsuppgifter

Använd detta API för att verifiera ett användar namn och lösen ord för Defender för IoT. Alla användar roller för Defender för IoT kan fungera med API: et.

Du behöver ingen Defender för IoT-åtkomsttoken för att använda detta API.

#### <a name="apiexternalauthenticationvalidation"></a>/api/external/authentication/validation

#### <a name="method"></a>Metod

**POST**

#### <a name="request-type"></a>Typ av begäran

**JSON**

#### <a name="query-parameters"></a>Frågeparametrar

| **Namn** | **Typ** | **Nullable** |
|--|--|--|
| **användar** | Sträng | No |
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

#### <a name="response-content"></a>Svars innehåll

Meddelande sträng med åtgärds status information:

- **Lyckades-MSG**: autentiseringen lyckades

- **Fel**: det gick inte att verifiera autentiseringsuppgifter

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

### <a name="change-password"></a>Ändra lösenord

Använd det här API: et för att låta användare ändra sina egna lösen ord. Alla användar roller för Defender för IoT kan fungera med API: et. Du behöver ingen Defender för IoT-åtkomsttoken för att använda detta API.

#### <a name="externalauthenticationset_password"></a>/External/Authentication/set_password

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

#### <a name="response-content"></a>Svars innehåll

Meddelande sträng med åtgärds status information:

- **Lyckades – MSG**: lösen ordet har bytts ut

- **Fel – fel**: autentisering av användar fel

- **Fel – fel**: lösen ordet matchar inte säkerhets principen

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Enhets fält

| **Namn** | **Typ** | **Nullable** |
|--|--|--|
| **användar** | Sträng | No |
| **lösenord** | Sträng | No |
| **new_password** | Sträng | No |

### <a name="user-password-update-by-system-admin"></a>Uppdatering av lösen ord för användare av system administratören

Använd detta API för att låta system administratörer ändra lösen ord för angivna användare. Användar rollerna Defender för IoT-administratörer kan arbeta med API: et. Du behöver ingen Defender för IoT-åtkomsttoken för att använda detta API.

#### <a name="externalauthenticationset_password_by_admin"></a>/External/Authentication/set_password_by_admin

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

#### <a name="response-content"></a>Svars innehåll

Meddelande sträng med åtgärds status information:

- **Lyckades – MSG**: lösen ordet har bytts ut

- **Fel – fel**: autentisering av användar fel

- **Fel – fel**: användaren finns inte

- **Fel – fel**: lösen ordet matchar inte säkerhets principen

- **Fel – fel**: användaren har inte behörighet att ändra lösen ord

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

#### <a name="device-fields"></a>Enhets fält

| **Namn** | **Typ** | **Nullable** |
|--|--|--|
| **admin_username** | Sträng | No |
| **admin_password** | Sträng | No |
| **användar** | Sträng | No |
| **new_password** | Sträng | No |

## <a name="on-premises-management-console-api-specifications"></a>API-specifikationer för lokal hanterings konsol

I det här avsnittet beskrivs följande lokala API: er för hanterings konsolen:

- **/external/v1/alerts/<UUID>**

- **Varnings undantag (underhålls fönster)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Fönstret varnings undantag visar aktiva regler.":::

Definiera villkor under vilka aviseringar inte ska skickas. Du kan till exempel definiera och uppdatera stopp-och start tider, enheter eller undernät som ska undantas när du utlöser aviseringar eller Defender för IoT-motorer som ska uteslutas. Under en underhålls period kanske du till exempel vill stoppa leveransen av alla aviseringar, förutom varningar om skadlig kod på kritiska enheter.

De API: er som du definierar här visas i den lokala hanterings konsolens fönster för **aviserings undantag** som en skrivskyddad undantags regel.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Svars exempel**

- **svarade**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password"></a>Ändra lösenord

Använd det här API: et för att låta användare ändra sina egna lösen ord. Alla användar roller för Defender för IoT kan fungera med API: et. Du behöver ingen Defender för IoT-åtkomsttoken för att använda detta API.

- **/External/Authentication/set_password**

#### <a name="user-password-update-by-system-admin"></a>Uppdatering av lösen ord för användare av system administratören

Använd detta API för att låta system administratörer ändra lösen ord för vissa användare. Användar rollerna Defender för IoT-administratörer kan arbeta med API: et. Du behöver ingen Defender för IoT-åtkomsttoken för att använda detta API.

- **/External/Authentication/set_password_by_admin**

### <a name="retrieve-device-information"></a>Hämta enhets information

Detta API begär en lista över alla enheter som identifierats av Defender för IoT-sensorer som är anslutna till en lokal hanterings konsol.

- **/external/v1/devices**

#### <a name="method"></a>Metod

**TA**

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

Matris med JSON-objekt som representerar enheter.

#### <a name="query-parameters"></a>Frågeparametrar

- **auktoriserad**: filtrera endast auktoriserade och oauktoriserade enheter.

- plats- **ID**: om du bara vill filtrera enheter som är relaterade till vissa platser.

- **ZonID**: om du bara vill filtrera enheter som är relaterade till vissa zoner. [1](#1)

- **sensorId**: filtrera endast enheter som identifieras av vissa sensorer. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *du kanske inte har plats-och zon-ID. Om så är fallet frågar du alla enheter för att hämta plats-och zon-ID.*

#### <a name="query-parameters-example"></a>Exempel på frågeparametrar

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Enhets fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **sensorId** | Numerisk | No | - |
| **ZonID** | Numerisk | Yes | - |
| **siteId** | Numerisk | Yes | - |
| **IP-adresser** | JSON-matris | Yes | IP-adresser (kan vara mer än en adress i fråga om Internet adresser eller en enhet med dubbla nätverkskort) |
| **name** | Sträng | No | - |
| **bastyp** | Sträng | No | Okänd, teknik Station, PLC, HMI, historian, domänkontrollant, DB-server, trådlös åtkomst punkt, router, växel, Server, arbets Station, IP-kamera, skrivare, brand vägg, Terminal Station, VPN Gateway, Internet eller multicast och sändning |
| **macAddresses** | JSON-matris | Yes | MAC-adresser (kan vara mer än en adress i händelse av en enhet med dubbla nätverkskort) |
| **operatingSystem** | Sträng | Yes | - |
| **engineeringStation** | Boolesk | No | Sant eller falskt |
| **läser** | Boolesk | No | Sant eller falskt |
| **auktorisation** | Boolesk | No | Sant eller falskt |
| **leverantörsspecifika** | Sträng | Yes | - |
| **Protokoll** | JSON-matris | Yes | Protokoll objekt |
| **inbyggd program vara** | JSON-matris | Yes | Firmware-objekt |

#### <a name="protocol-fields"></a>Protokoll fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| Name | Sträng | No | - |
| Adresser | JSON-matris | Yes | Huvud-eller numeriska värden |

#### <a name="firmware-fields"></a>Fält för inbyggd program vara

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **nummer** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **förlag** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **versionInbyggdProgramvara** | Double | No | Ej tillämpligt eller det faktiska värdet |
| **additionalData** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **moduleAddress** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **räck** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **stack** | Sträng | No | Ej tillämpligt eller det faktiska värdet |
| **adresspool** | Sträng | No | Ej tillämpligt eller det faktiska värdet |

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

### <a name="retrieve-alert-information"></a>Hämta aviserings information

Använd detta API för att hämta alla eller filtrerade aviseringar från en lokal hanterings konsol.

#### <a name="externalv1alerts"></a>/external/v1/alerts

#### <a name="method"></a>Metod

**TA**

#### <a name="query-parameters"></a>Frågeparametrar

- **status**: om du bara vill filtrera hanterade och ohanterade aviseringar.

  **Exempel**:

  `/api/v1/alerts?state=handled`

- **fromTime**: filtrera aviseringar som skapats från en angiven tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: för att filtrera aviseringar som skapats före en angiven tid (i millisekunder, UTC).

  **Exempel**:

  `/api/v1/alerts?toTime=<epoch>`

- plats- **ID**: den plats där aviseringen upptäcktes. [2](#2)

- **ZonID**: den zon som aviseringen identifierades på. [2](#2)

- **sensor**: sensorn som aviseringen identifierades på.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *du kanske inte har plats-och zon-ID. Om så är fallet frågar du alla enheter för att hämta plats-och zon-ID.*

#### <a name="alert-fields"></a>Aviserings fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **ID** | Numerisk | No | - |
| **tid** | Numerisk | No | Epok (UTC) |
| **title** | Sträng | No | - |
| **meddelande** | Sträng | No | - |
| **allvarlighets grad** | Sträng | No | Varning, mindre, större eller kritiskt |
| **motorn** | Sträng | No | Protokoll överträdelse, princip överträdelse, skadlig kod, avvikelse eller drift |
| **sourceDevice** | Numerisk | Yes | Enhets-ID |
| **destinationDevice** | Numerisk | Yes | Enhets-ID |
| **additionalInformation** | Ytterligare informations objekt | Yes | - |

#### <a name="additional-information-fields"></a>Ytterligare informations fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **beteckning** | Sträng | No | - |
| **Mer** | JSON-matris | Nej | Sträng |

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

### <a name="qradar-alerts"></a>QRadar aviseringar

QRadar-integrering med Defender för IoT hjälper dig att identifiera de aviseringar som genererats av Defender för IoT och utföra åtgärder med dessa aviseringar. QRadar tar emot data från Defender för IoT och kontaktar sedan den offentliga API-komponenten för hanterings konsolen.

Om du vill skicka data som identifieras av Defender för IoT till QRadar, definierar du en vidarebefordrings regel i Defender för IoT-systemet och väljer alternativet **Fjärrstöd för aviserings hantering** .

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Redigera vidarebefordrings reglerna så att de passar dina behov.":::

När du väljer det här alternativet när du konfigurerar regler för vidarebefordran visas följande ytterligare fält i QRadar:

- **UUID**: unik aviserings identifierare, till exempel 1-1555245116250.

- **Plats**: den plats där aviseringen identifierades.

- **Zon**: zonen där aviseringen identifierades.

Exempel på nytto lasten som skickas till QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/External/v1/Alerts/- &lt; uuid&gt;

#### <a name="method"></a>Metod

**PUT**

#### <a name="request-type"></a>Typ av begäran

**JSON**

#### <a name="request-content"></a>Begär innehåll

JSON-objekt som representerar åtgärden som ska utföras på den avisering som innehåller UUID.

#### <a name="action-fields"></a>Åtgärds fält

| Namn | Typ | Kan ha värdet null | Lista med värden |
|--|--|--|--|
| **tgärd** | Sträng | No | hantera eller handleAndLearn |

#### <a name="request-example"></a>Exempel på begäran

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

Matris med JSON-objekt som representerar enheter.

#### <a name="response-fields"></a>Svars fält


| Namn | Typ | Kan ha värdet null | Description |
|--|--|--|--|
| **innehåll/fel** | Sträng | No | Om begäran lyckas visas innehålls egenskapen. Annars visas fel-egenskapen. |

#### <a name="possible-content-values"></a>Möjliga innehålls värden

| Statuskod | Innehålls värde | Description |
|--|--|--|
| 200 | Begäran om aviserings uppdatering har slutförts. | Uppdateringsbegäran har slutförts. Inga kommentarer. |
| 200 | Aviseringen hanterades redan (**handle**). | Aviseringen hanterades redan när en hanterings förfrågan togs emot för aviseringen.<br />Aviseringen förblir **hanterad**. |
| 200 | Aviseringen har redan hanterats och lärts (**handleAndLearn**). | Aviseringen har redan hanterats och lärts när en begäran till **handleAndLearn** togs emot.<br />Aviseringen har kvar statusen **handledAndLearn** . |
| 200 | Aviseringen hanterades redan (**hanterad**).<br />Hantera och lär (**handleAndLearn**) utfördes på aviseringen. | Aviseringen hanterades redan när en begäran till **handleAndLearn** togs emot.<br />Aviseringen blir **handleAndLearn**. |
| 200 | Aviseringen har redan hanterats och lärts (**handleAndLearn**). Ignorerad referens förfrågan. | Aviseringen hade redan **handleAndLearn** när en förfrågan om att hantera aviseringen togs emot. Aviseringen förblir **handleAndLearn**. |
| 500 | Ogiltig åtgärd. | Åtgärden som skickades är inte en giltig åtgärd att utföra på aviseringen. |
| 500 | Ett oväntat fel inträffade. | Det uppstod ett oväntat fel. Kontakta teknisk support för att lösa problemet. |
| 500 | Det gick inte att köra begäran eftersom ingen avisering hittades för denna UUID. | Det gick inte att hitta den angivna varnings-UUID: n i systemet. |

#### <a name="response-example"></a>Exempel på svar

**Lyckad**

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

### <a name="alert-exclusions-maintenance-window"></a>Varnings undantag (underhålls fönster)

Definiera villkor under vilka aviseringar inte ska skickas. Du kan till exempel definiera och uppdatera stopp-och start tider, enheter eller undernät som ska undantas när du utlöser aviseringar eller Defender för IoT-motorer som ska uteslutas. Under en underhålls period kanske du till exempel vill stoppa aviserings leveransen av alla aviseringar, förutom varningar om skadlig kod på kritiska enheter.

De API: er som du definierar här visas i den lokala hanterings konsolens fönster för **aviserings undantag** som en skrivskyddad undantags regel.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Fönstret aviserings undantag visar en lista över alla undantags regler. ":::

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

#### <a name="method---post"></a>Metod-POST

#### <a name="query-parameters"></a>Frågeparametrar

- **ticketId**: definierar ID för underhålls biljett i användarens system.

- **TTL**: definierar TTL (Time to Live), vilket är varaktigheten för underhålls perioden på några minuter. Efter den tids period som den här parametern definierar börjar systemet automatiskt att skicka aviseringar.

- **motorer**: definierar från vilken säkerhets motor som ska förhindra aviseringar under underhålls processen:

   - AVVIKELSE

   - SKADLIG kod

   - VERKSAMHETSRELATERADE

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**: definierar från vilken Defender för IoT-sensorn som ska ignorera aviseringar under underhålls processen. Det har samma ID som hämtats från/API/v1/Appliances (GET).

- **undernät**: definierar från vilket undernät som aviseringar ska undertryckas under underhålls processen. Under nätet skickas i följande format: 192.168.0.0/16.

#### <a name="error-codes"></a>Felkoder

- **201 (skapat)**: åtgärden har slutförts.

- **400 (felaktig begäran)**: visas i följande fall:

   - **TTL** -parametern är inte numerisk eller inte positiv.

   - **Under näts** parametern definierades med fel format.

   - **TicketId** -parametern saknas.

   - **Motor** parametern matchar inte befintliga säkerhets motorer.

- **404 (hittades inte)**: en av sensorerna finns inte.

- **409 (konflikt)**: biljett-ID: t är länkat till ett annat öppet underhålls fönster.

- **500 (internt Server fel)**: andra oväntade fel.

> [!NOTE]
> Kontrol lera att biljett-ID: t inte är länkat till ett befintligt öppet fönster. Följande undantags regel genereras: underhåll-{token}-{biljett-ID}.

#### <a name="method---put"></a>Metod-placering

Tillåter uppdatering av underhålls periodens varaktighet när du har startat underhålls processen genom att ändra **TTL** -parametern. Den nya varaktighets definitionen åsidosätter den tidigare.

Den här metoden är användbar när du vill ange en längre varaktighet än den för tillfället konfigurerade varaktigheten.

#### <a name="query-parameters"></a>Frågeparametrar

- **ticketId**: definierar ID för underhålls biljett i användarens system.

- **TTL**: definierar varaktigheten för fönstret på några minuter.

#### <a name="error-code"></a>Felkod

- **200 (OK)**: åtgärden har slutförts.

- **400 (felaktig begäran)**: visas i följande fall:

   - **TTL** -parametern är inte numerisk eller inte positiv.

   - **TicketId** -parametern saknas.

   - **TTL** -parametern saknas.

- **404 (hittades inte)**: biljett-ID: t är inte länkat till ett öppet underhålls fönster.

- **500 (internt Server fel)**: andra oväntade fel.

> [!NOTE]
> Kontrol lera att biljett-ID: t är länkat till ett befintligt öppet fönster.

#### <a name="method---delete"></a>Metod-ta bort

Stänger ett befintligt underhålls fönster.

#### <a name="query-parameters"></a>Frågeparametrar

- **ticketId**: loggar underhålls biljett-ID: t i användarens system.

#### <a name="error-code"></a>Felkod

- **200 (OK)**: åtgärden har slutförts.

- **400 (felaktig begäran)**: **ticketId** -parametern saknas.

- **404 (hittades inte)**: biljett-ID: t är inte länkat till ett öppet underhålls fönster.

- **500 (internt Server fel)**: andra oväntade fel.

> [!NOTE]
> Kontrol lera att biljett-ID: t är länkat till ett befintligt öppet fönster.

#### <a name="method---get"></a>Metod – Hämta

Hämta en logg över alla öppna, Stäng-och uppdatera-åtgärder som utfördes i systemet under underhållet. Du kan bara hämta en logg för underhålls fönster som var aktiva tidigare och som har stängts.

#### <a name="query-parameters"></a>Frågeparametrar

- **fromDate**: filtrerar loggarna från det fördefinierade datumet och senare. Formatet är 2019-12-30.

- **todate**: filtrerar loggarna upp till det fördefinierade datumet. Formatet är 2019-12-30.

- **ticketId**: filtrerar loggarna som är relaterade till ett angivet biljett-ID.

- **tokenName**: filtrerar loggarna som är relaterade till ett angivet token-namn.

#### <a name="error-code"></a>Felkod

- **200 (OK)**: åtgärden har slutförts.

- **400 (felaktig begäran)**: datum formatet är felaktigt.

- **204 (inget innehåll)**: det finns inga data att visa.

- **500 (internt Server fel)**: andra oväntade fel.

#### <a name="response-type"></a>Svarstyp

**JSON**

#### <a name="response-content"></a>Svars innehåll

Matris med JSON-objekt som representerar underhålls fönster åtgärder.

#### <a name="response-structure"></a>Svars struktur

| Namn | Typ | Kommentar | Kan ha värdet null |
|--|--|--|--|
| **dateTime** | Sträng | Exempel: "2012-04-23T18:25:43.511 Z" | nej |
| **ticketId** | Sträng | Exempel: "9a5fe99c-d914-4bda-9332-307384fe40bf" | nej |
| **tokenName** | Sträng | - | nej |
| **motor** | Strängmatris | - | ja |
| **sensorIds** | Strängmatris | - | ja |
| **undernät** | Strängmatris | - | ja |
| **ttl** | Numerisk | - | ja |
| **operationType** | Sträng | Värdena är "öppna", "UPDATE" och "CLOSE" | nej |

### <a name="authenticate-user-credentials"></a>Autentisera användarautentiseringsuppgifter

Använd det här API: et för att verifiera användarautentiseringsuppgifter. Alla användar roller för Defender för IoT kan fungera med API: et. Du behöver ingen Defender för IoT-åtkomsttoken för att använda detta API.

#### <a name="externalauthenticationvalidation"></a>/external/authentication/validation

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

#### <a name="response-content"></a>Svars innehåll

Meddelande sträng med åtgärds status information:

- **Lyckades – MSG**: autentiseringen lyckades

- **Fel – fel**: det gick inte att verifiera autentiseringsuppgifter

#### <a name="device-fields"></a>Enhets fält

| **Namn** | **Typ** | **Nullable** |
|--|--|--|
| **användar** | Sträng | No |
| **lösenord** | Sträng | No |

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

### <a name="change-password"></a>Ändra lösenord

Använd det här API: et för att låta användare ändra sina egna lösen ord. Alla användar roller för Defender för IoT kan fungera med API: et. Du behöver ingen Defender för IoT-åtkomsttoken för att använda detta API.

#### <a name="externalauthenticationset_password"></a>/External/Authentication/set_password

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

#### <a name="response-content"></a>Svars innehåll

Meddelande sträng med åtgärds status information:

- **Lyckades – MSG**: lösen ordet har bytts ut

- **Fel – fel**: autentisering av användar fel

- **Fel – fel**: lösen ordet matchar inte säkerhets principen

#### <a name="response-example"></a>Exempel på svar

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Enhets fält

| **Namn** | **Typ** | **Nullable** |
|--|--|--|
| **användar** | Sträng | No |
| **lösenord** | Sträng | No |
| **new_password** | Sträng | No |

### <a name="user-password-update-by-system-admin"></a>Uppdatering av lösen ord för användare av system administratören

Använd detta API för att låta system administratörer ändra lösen ord för angivna användare. Användar rollerna Defender för IoT-administratörer kan arbeta med API: et. Du behöver ingen Defender för IoT-åtkomsttoken för att använda detta API.

#### <a name="externalauthenticationset_password_by_admin"></a>/External/Authentication/set_password_by_admin

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

#### <a name="response-content"></a>Svars innehåll

Meddelande sträng med åtgärds status information:

- **Lyckades – MSG**: lösen ordet har bytts ut

- **Fel – fel**: autentisering av användar fel

- **Fel – fel**: användaren finns inte

- **Fel – fel**: lösen ordet matchar inte säkerhets principen

- **Fel – fel**: användaren har inte behörighet att ändra lösen ord

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

#### <a name="device-fields"></a>Enhets fält

| **Namn** | **Typ** | **Nullable** |
|--|--|--|
| **admin_username** | Sträng | No |
| **admin_password** | Sträng | No |
| **användar** | Sträng | No |
| **new_password** | Sträng | No |

## <a name="see-also"></a>Se även
[Undersök sensor identifieringar i en enhets inventering](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 [Undersök alla Enterprise sensor-identifieringar i en enhets inventering](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
