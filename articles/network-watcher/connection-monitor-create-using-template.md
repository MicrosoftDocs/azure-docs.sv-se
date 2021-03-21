---
title: Skapa anslutnings övervakare – ARM-mall
titleSuffix: Azure Network Watcher
description: Lär dig hur du skapar anslutnings övervakaren med hjälp av ARMClient.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 46bdaf932d4224bf97b46e7713d49d815ca1bcdd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833005"
---
# <a name="create-a-connection-monitor-using-the-arm-template"></a>Skapa en anslutnings övervakare med ARM-mallen

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta i Övervakare av nätverksprestanda. Du kommer inte heller att kunna lägga till nya anslutnings övervakare i anslutnings övervakaren (klassisk). Du kan fortsätta att använda de tester och anslutnings Övervakare som skapades före den 1 juli 2021. [Migrera dina tester från övervakare av nätverksprestanda](migrate-to-connection-monitor-from-network-performance-monitor.md) eller [Migrera från anslutnings övervakaren (klassisk)](migrate-to-connection-monitor-from-connection-monitor-classic.md) till den nya anslutnings övervakaren i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.

Lär dig hur du skapar anslutnings övervakaren för att övervaka kommunikationen mellan dina resurser med hjälp av ARMClient. Det stöder hybrid-och Azure Cloud-distributioner.


## <a name="before-you-begin"></a>Innan du börjar 

I anslutnings Övervakare som du skapar i anslutnings övervakaren kan du lägga till både lokala datorer och virtuella Azure-datorer som källor. Dessa anslutnings övervakare kan också övervaka anslutningar till slut punkter. Slut punkterna kan vara på Azure eller någon annan URL eller IP-adress.

Anslutnings övervakaren innehåller följande entiteter:

* **Anslutnings övervaknings resurs** – en regions-/regionsspecifika Azure-resurs. Alla följande entiteter är egenskaper för en anslutnings övervaknings resurs.
* **Slut punkt** – en källa eller ett mål som deltar i anslutnings kontroller. Exempel på slut punkter är virtuella Azure-datorer, lokala agenter, URL: er och IP-adresser.
* **Test konfiguration** – en plattformsspecifik konfiguration för ett test. Utifrån det protokoll du väljer kan du definiera port, tröskelvärden, test frekvens och andra parametrar.
* **Test grupp** – gruppen som innehåller käll slut punkter, mål slut punkter och testkonfigurationer. En anslutnings övervakare kan innehålla fler än en test grupp.
* **Test** – kombinationen av en käll slut punkt, mål slut punkt och test konfiguration. Ett test är den mest detaljerade nivån där övervaknings data är tillgängliga. Övervaknings data innehåller procent andelen kontroller som misslyckades och tur och retur-tiden.

    ![Diagram som visar en anslutnings Övervakare som definierar relationen mellan test grupper och tester](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Steg för att skapa med exempel ARM-mall

Använd följande kod för att skapa en anslutnings övervakare med hjälp av ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Här är distributions kommandot:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Beskrivning av egenskaper

* connectionMonitorName – namnet på anslutnings övervaknings resursen

* Underordnat prenumerations-ID för den prenumeration där du vill skapa anslutnings övervakaren

* NW-Network Watcher resurs-ID där CM kommer att skapas 

* plats-region där anslutnings övervakaren ska skapas

* Slutpunkter
    * namn – unikt namn för varje slut punkt
    * resourceId – för Azure-slutpunkter refererar resurs-ID till Azure Resource Manager resurs-ID för virtuella datorer. För icke-Azure-slutpunkter refererar resurs-ID till Azure Resource Manager resurs-ID för arbets ytan Log Analytics som är länkad till icke-Azure-agenter.
    * adress – endast tillämpligt när inget resurs-ID har angetts eller om resurs-ID: t är Log Analytics arbets ytan. Om det används med Log Analytics resurs-ID, refererar detta till det fullständiga domän namnet för den agent som kan användas för övervakning. Om det används utan resurs-ID kan detta vara webb adressen eller IP-adressen för en offentlig slut punkt.
    * Filtrera – för icke-Azure-slutpunkter använder du filter för att välja agenter från Log Analytics arbets ytan som ska användas för övervakning i anslutnings övervaknings resursen. Om inga filter anges kan alla agenter som hör till Log Analytics arbets ytan användas för övervakning
        * typ – ange typ som "agent adress"
        * adress – Ange adress som FQDN för din lokala agent

* Test grupper
    * namn – ge test gruppen ett namn.
    * testConfigurations – testa konfigurationer baserat på vilka käll slut punkter ansluter till mål slut punkter
    * källor – Välj från slut punkter som skapats ovan. Azure-baserade käll slut punkter måste ha Azure Network Watcher-tillägget installerat och Azure-baserade käll slut punkter måste ha haveAzure Log Analytics-agenten installerad. Information om hur du installerar en agent för din källa finns i [Installera övervaknings agenter](./connection-monitor-overview.md#install-monitoring-agents).
    * destinationer – Välj från slut punkter som skapats ovan. Du kan övervaka anslutningar till virtuella Azure-datorer eller en slut punkt (en offentlig IP-adress, URL eller FQDN) genom att ange dem som mål. I en enda test grupp kan du lägga till virtuella Azure-datorer, Office 365-URL: er, Dynamics 365-URL: er och anpassade slut punkter.
    * Inaktivera – Använd det här fältet om du vill inaktivera övervakning för alla källor och mål som test gruppen anger.

* Testa konfigurationer
    * Namn – namnet på test konfigurationen.
    * testFrequencySec – ange hur ofta källor ska pinga mål på det protokoll och den port som du har angett. Du kan välja 30 sekunder, 1 minut, 5 minuter, 15 minuter eller 30 minuter. Källorna kommer att testa anslutningen till destinationer utifrån det värde som du väljer. Om du till exempel väljer 30 sekunder kommer källorna att kontrol lera anslutningen till målet minst en gång under en 30-sekunders period.
    * protokoll – du kan välja TCP, ICMP, HTTP eller HTTPS. Beroende på protokollet kan du göra vissa protokolls par konfigurations information
    
        * preferHTTPS – ange om du vill använda HTTPS över HTTP, när porten som används är varken 80 eller 443
        * Port – ange vilken målport du önskar.
        * disableTraceRoute – detta gäller för test av konfigurationer vars protokoll är TCP eller ICMP. Den stoppar källor från identifiering av topologi och sökning efter hopp.
        * metod – detta används för att testa konfigurationer vars protokoll är HTTP. Välj metoden HTTP-begäran – antingen GET eller POST
        * Sökväg – Ange Sök vägs parametrar som ska läggas till i URL
        * validStatusCodes – Välj tillämpliga status koder. Om svars koden inte matchar den här listan visas ett diagnostiskt meddelande
        * requestHeaders – ange anpassade rubrik strängar för begäran som kommer att skickas till målet
        
    * successThreshold – du kan ange tröskelvärden för följande nätverks parametrar:
        * checksFailedPercent – ange procent andelen kontroller som kan avbrytas när källor kontrollerar anslutningen till destinationer genom att använda de villkor som du har angett. Procent andelen misslyckade kontroller i TCP-eller ICMP-protokollet kan likställas med procent andelen av paket förlusten. För HTTP-protokoll representerar det här fältet den procent andel av HTTP-begäranden som inte fick något svar.
        * roundTripTimeMs – ange önskad söktext i millisekunder för hur lång tid det tar att ansluta till målet via test konfigurationen.

## <a name="scale-limits"></a>Skalnings gränser

Anslutnings övervakare har följande skalnings gränser:

* Högsta antal anslutnings övervakare per prenumeration per region: 100
* Högsta antal test grupper per anslutnings Övervakare: 20
* Högsta antal källor och mål per anslutnings Övervakare: 100
* Högsta antal testkonfigurationer per anslutnings Övervakare: 20 via ARMClient

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du analyserar övervaknings data och ställer in aviseringar](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Lär dig [hur du diagnostiserar problem i nätverket](./connection-monitor-overview.md#diagnose-issues-in-your-network)
