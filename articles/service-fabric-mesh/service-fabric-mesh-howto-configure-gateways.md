---
title: Konfigurera en gateway för att dirigera begär Anden
description: Lär dig hur du konfigurerar den gateway som hanterar inkommande trafik för dina program som körs på Service Fabric nät.
author: georgewallace
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 40bba03c35568c013e383c08f0a13f765c1cc8bd
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625862"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurera en gateway-resurs för att dirigera begär Anden

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

En gateway-resurs används för att dirigera inkommande trafik till nätverket som är hus ditt program. Konfigurera den för att ange regler via vilka begär Anden dirigeras till vissa tjänster eller slut punkter baserat på strukturen för begäran. Mer information om nätverk och gatewayer i nät finns i [Introduktion till nätverk i Service Fabric nät](service-fabric-mesh-networks-and-gateways.md) . 

Gateway-resurser måste deklareras som en del av distributions mal len (JSON eller yaml) och är beroende av en nätverks resurs. Det här dokumentet beskriver de olika egenskaperna som kan ställas in för din gateway och omfattar en exempel på en gateway-konfiguration.

## <a name="options-for-configuring-your-gateway-resource"></a>Alternativ för att konfigurera en gateway-resurs

Eftersom Gateway-resursen fungerar som en bro mellan ditt programs nätverk och den underliggande infrastrukturens nätverk ( `open` nätverket). Du behöver bara konfigurera en (i för hands versionen av nät, det finns en gräns på en gateway per app). Deklarationen för resursen består av två huvud delar: resursens metadata och egenskaperna. 

### <a name="gateway-resource-metadata"></a>Metadata för gateway-resurs

En gateway deklareras med följande metadata:
* `apiVersion` – måste anges till "2018-09-01-Preview" (eller senare i framtiden)
* `name` – ett sträng namn för denna gateway
* `type` -"Microsoft. ServiceFabricMesh/gateways"
* `location` – bör anges till platsen för appen/nätverket. är vanligt vis en referens till plats parametern i distributionen
* `dependsOn` – nätverket som denna gateway fungerar som en ingångs punkt för

Så här ser det ut i en distributions mall för en Azure Resource Manager (JSON): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Gateway-egenskaper

Avsnittet Properties används för att definiera de nätverk mellan vilka gatewayen ligger och reglerna för att dirigera begär Anden. 

#### <a name="source-and-destination-network"></a>Käll-och mål nätverk 

Varje gateway kräver en `sourceNetwork` och `destinationNetwork` . Käll nätverket definieras som det nätverk från vilket appen tar emot inkommande begär Anden. Egenskapen namn ska alltid vara inställd på "öppen". Mål nätverket är det nätverk som begär Anden riktar sig till. Name-värdet för detta ska anges till resurs namnet för appens lokala nätverk (bör innehålla fullständig referens till resursen). Nedan visas en exempel konfiguration av vad detta ser ut för en distribution i ett nätverk som kallas "" nätverket ".

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Regler 

En gateway kan ha flera regler för routning som anger hur inkommande trafik ska hanteras. En regel för routning definierar relationen mellan lyssnings porten och mål slut punkten för ett angivet program. För regler för TCP-routning finns en 1:1-mappning mellan Port: Endpoint. För regler för HTTP-routning kan du ange mer komplexa routningsregler som kontrollerar sökvägen till begäran och eventuellt rubriker, för att bestämma hur begäran ska vidarebefordras. 

Routningsregler anges per port. Varje ingress-Port har sin egen sträng mat ris i avsnittet Egenskaper i Gateway-konfigurationen. 

#### <a name="tcp-routing-rules"></a>Regler för TCP-routning 

En regel för TCP-routning består av följande egenskaper: 
* `name` – referera till regeln som kan vara valfri valfri valfri sträng 
* `port` -Port att lyssna efter inkommande begär Anden 
* `destination` – slut punkts specifikation som innehåller `applicationName` , `serviceName` och `endpointName` , för var begär Anden måste dirigeras till

Här är ett exempel på en TCP-routningsprincip:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>Regler för HTTP-routning 

En regel för HTTP-routning består av följande egenskaper: 
* `name` – referera till regeln som kan vara valfri valfri valfri sträng 
* `port` -Port att lyssna efter inkommande begär Anden 
* `hosts` – en uppsättning principer som gäller för begär Anden som kommer till de olika "värdarna" på den port som anges ovan. Värdar är den uppsättning program och tjänster som kan köras i nätverket och som kan hantera inkommande begär Anden, d.v.s. en webbapp. Värd principer tolkas i ordning, så du bör skapa följande i fallande nivåer av specificitet
    * `name` – DNS-namnet på värden som följande routningsregler anges för. Om du använder "*" här skapas regler för routning för alla värdar.
    * `routes` – en uppsättning principer för den här värden
        * `match` – specifikation av den inkommande begär ande strukturen för den här regeln som ska tillämpas, baserat på en `path`
            * `path` -innehåller en `value` (inkommande URI) `rewrite` (hur du vill att begäran ska vidarebefordras) och en `type` (kan för närvarande endast vara "prefix")
            * `header` – är en valfri matris med rubrik värden som ska matcha i rubriken på begäran om begäran matchar Sök vägs specifikationen (ovan).
              * varje post innehåller `name` (sträng namnet på rubriken som ska matchas), `value` (sträng värde för rubriken i begäran) och en `type` (kan för närvarande bara vara "exakt")
        * `destination` – om begäran matchar skickas den till den här destinationen, som anges med hjälp av ett `applicationName` , `serviceName` , och `endpointName`

Här är ett exempel på en regel för HTTP-routning som gäller för begär Anden som kommer från port 80 till alla värdar som hanteras av appar i det här nätverket. Om URL: en för begäran har en struktur som matchar Sök vägs specifikationen, dvs., `<IPAddress>:80/pickme/<requestContent>` kommer den att dirigeras till `myListener` slut punkten.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Exempel på konfiguration för en gateway-resurs 

Så här ser en fullständig gateway-resurs config ut (detta är anpassat från ingångs exemplet som är tillgängligt i [nätlagrings platsen](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Denna gateway har kon figurer ATS för ett Linux-program, "meshAppLinux", som består av minst två tjänster, "helloWorldService" och "counterService", som lyssnar på port 80. Beroende på URL-strukturen för den inkommande begäran dirigerar den begäran till någon av dessa tjänster. 
* " \<IPAddress> : 80/HelloWorld/ \<request\> " resulterar i att en begäran dirigeras till "helloWorldListener" i helloWorldService. 
* " \<IPAddress> : 80/Counter/ \<request\> " skulle leda till att en begäran dirigeras till "counterListener" i counterService. 

## <a name="next-steps"></a>Nästa steg
* Distribuera ingångs [exemplet](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) för att se gateways i praktiken
