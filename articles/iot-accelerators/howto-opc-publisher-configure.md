---
title: Konfigurera OPC Publisher – Azure | Microsoft Docs
description: I den här artikeln beskrivs hur du konfigurerar OPC-utgivare för att ange OPC data ändringar för UA-noder, OPC UA-händelser som ska publiceras och även telemetri-formatet.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 405fa04a3b6af7f0c87888aa48aba9bdffeaf282
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646444"
---
# <a name="configure-opc-publisher"></a>Konfigurera OPC Publisher

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Du kan konfigurera OPC-utgivare för att ange:

- OPC UA-Node-data ändras för publicering.
- OPC UA-händelser att publicera.
- Telemetri-formatet.

Du kan konfigurera OPC-utgivare med konfigurationsfiler eller med hjälp av metod anrop.

## <a name="use-configuration-files"></a>Använda konfigurationsfiler

I det här avsnittet beskrivs alternativ för att konfigurera OPC UA Node Publishing with konfigurationsfilerna.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Använd en konfigurations fil för att konfigurera publicerings data ändringar

Det enklaste sättet att konfigurera OPC UA-noder att publicera är med en konfigurations fil. Konfigurations fil formatet är dokumenterat i [publishednodes.jspå](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) i lagrings platsen.

Syntaxen för konfigurations filen har ändrats med tiden. OPC Publisher läser fortfarande gamla format, men konverterar dem till det senaste formatet När konfigurationen är kvar.

I följande exempel visas formatet för konfigurations filen:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Använd en konfigurations fil för att konfigurera publicerings händelser

Om du vill publicera OPC UA-händelser använder du samma konfigurations fil som för data ändringar.

I följande exempel visas hur du konfigurerar publicering av händelser som genereras av [SimpleEvents-servern](https://github.com/OPCFoundation/UA-.NETStandard-Samples/tree/master/Workshop/SimpleEvents/Server). Du hittar SimpleEvents-servern i [OPC Foundation-lagringsplatsen](https://github.com/OPCFoundation/UA-.NETStandard-Samples) :

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Använd metod anrop

I det här avsnittet beskrivs de metod anrop som du kan använda för att konfigurera OPC-utgivare.

### <a name="configure-using-opc-ua-method-calls"></a>Konfigurera med OPC UA-metod anrop

OPC Publisher innehåller en OPC UA-server som kan nås på port 62222. Om värd namnet är **utgivare** är slut punktens URI: `opc.tcp://publisher:62222/UA/Publisher` .

Den här slut punkten visar följande fyra metoder:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT-HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Konfigurera med hjälp av anrop till IoT Hub Direct-metoden

OPC Publisher implementerar följande IoT Hub direkta metod anrop:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

Formatet på JSON-nyttolasten för metod begär Anden och svar definieras i [opcpublisher/HubMethodModel. cs](https://github.com/Azure/iot-edge-opc-publisher/tree/master/opcpublisher).

Om du anropar en okänd metod i modulen svarar den med en sträng som säger att metoden inte har implementerats. Du kan anropa en okänd metod som ett sätt att pinga modulen.

### <a name="configure-username-and-password-for-authentication"></a>Konfigurera användar namn och lösen ord för autentisering

Autentiseringsläget kan ställas in via ett IoT Hub Direct-metod anrop. Nytto lasten måste innehålla egenskapen **OpcAuthenticationMode** och användar namn och lösen ord:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

Lösen ordet krypteras av IoT Hub arbets belastnings klient och lagras i utgivarens konfiguration. Om du vill ändra tillbaka autentiseringen till anonym använder du metoden med följande nytto last:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Om egenskapen **OpcAuthenticationMode** inte har angetts i nytto lasten förblir autentiseringsinställningarna oförändrade i konfigurationen.

## <a name="configure-telemetry-publishing"></a>Konfigurera telemetri-publicering

När OPC Publisher får ett meddelande om en värde ändring i en publicerad nod, genererar den ett JSON-formaterat meddelande som skickas till IoT Hub.

Du kan konfigurera innehållet i det här JSON-formaterade meddelandet med hjälp av en konfigurations fil. Om ingen konfigurations fil anges med `--tc` alternativet används en standard konfiguration som är kompatibel med den [anslutna fabriks lösnings acceleratorn](https://github.com/Azure/azure-iot-connected-factory).

Om OPC Publisher har kon figurer ATS för batch-meddelanden skickas de som en giltig JSON-matris.

Telemetrin härleds från följande källor:

- OPC för nodens publicerings nod
- **MonitoredItem** -objektet för OPC UA-stacken som OPC-utgivaren fick ett meddelande till.
- Argumentet som skickades till det här meddelandet, som innehåller information om data värdes ändringen.

Telemetrin som placeras i JSON-formaterat meddelande är ett urval av viktiga egenskaper för dessa objekt. Om du behöver fler egenskaper måste du ändra OPC utgivarens kodbas.

Konfigurations filens syntax är följande:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is omitted (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar OPC Publisher är det föreslagna nästa steg att lära dig hur du [Kör OPC Publisher](howto-opc-publisher-run.md).
