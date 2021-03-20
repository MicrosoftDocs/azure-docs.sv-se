---
title: Konfigurera nätverks lägen för container tjänster
description: Lär dig hur du konfigurerar olika nätverks lägen som stöds av Azure Service Fabric.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: e6174f35bd54b3ca0b2c5240a663369350b30ce8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86241904"
---
# <a name="service-fabric-container-networking-modes"></a>Nätverks lägen för Service Fabric behållare

Ett Azure Service Fabric-kluster för container tjänster använder **NAT** -nätverks läge som standard. Om fler än en behållar tjänst lyssnar på samma port och NAT-läge används kan det uppstå distributions fel. För att stödja flera behållar tjänster som lyssnar på samma port, Service Fabric erbjuda **öppen** nätverks läge (version 5,7 och senare). I öppet läge har varje behållar tjänst en intern, dynamiskt tilldelad IP-adress som stöder flera tjänster som lyssnar på samma port.  

Om du har en container service med en statisk slut punkt i tjänst manifestet kan du skapa och ta bort nya tjänster med hjälp av öppet läge utan distributions fel. Samma filen Docker. YML-fil kan också användas med statiska port mappningar för att skapa flera tjänster.

När en behållar tjänst startas om eller flyttas till en annan nod i klustret ändras IP-adressen. Därför rekommenderar vi inte att du använder den dynamiskt tilldelade IP-adressen för att identifiera behållar tjänster. Endast Service Fabric Naming Service eller DNS-tjänsten ska användas för tjänst identifiering. 

>[!WARNING]
>Azure ger totalt 65 356 IP-adresser per virtuellt nätverk. Summan av antalet noder och antalet behållar tjänst instanser (som använder öppet läge) får inte överstiga 65 356 IP-adresser i ett virtuellt nätverk. För scenarier med hög densitet rekommenderar vi NAT-nätverkets läge. Dessutom kommer andra beroenden som belastningsutjämnaren att ha andra [begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md) att överväga. För närvarande är upp till 50 IP-adresser per nod testat och beprövat stabilt. 
>

## <a name="set-up-open-networking-mode"></a>Konfigurera öppet nätverks läge

1. Konfigurera Azure Resource Manager-mallen. I avsnittet **fabricSettings** i kluster resursen aktiverar du DNS-tjänsten och IP-providern: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Konfigurera nätverks profil avsnittet för den virtuella datorns skalnings uppsättnings resurs. Detta gör att flera IP-adresser kan konfigureras på varje nod i klustret. I följande exempel konfigureras fem IP-adresser per nod för ett Windows/Linux Service Fabric-kluster. Du kan ha fem tjänst instanser som lyssnar på porten på varje nod. Om du vill att de fem IP-adresserna ska vara tillgängliga från Azure Load Balancer registrerar du de fem IP-adresserna i den Azure Load Balancer backend-adresspoolen som visas nedan.  Du måste också lägga till variablerna överst i mallen i avsnittet Variables.

    Lägg till det här avsnittet i variabler:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Lägg till det här avsnittet i den virtuella datorns skalnings uppsättnings resurs:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. För Windows-kluster konfigurerar du en regel för Azure nätverks säkerhets grupp (NSG) som öppnar port UDP/53 för det virtuella nätverket med följande värden:

   |Inställning |Värde |
   | --- | --- |
   |Prioritet |2000 |
   |Name |Custom_Dns  |
   |Källa |VirtualNetwork |
   |Mål | VirtualNetwork |
   |Tjänst | DNS (UDP/53) |
   |Åtgärd | Tillåt  |

4. Ange Nätverks läget i applikations manifestet för varje tjänst: `<NetworkConfig NetworkType="Open">` . **Öppna** nätverks läge resultat i tjänsten hämtar en dedikerad IP-adress. Om ett läge inte har angetts använder tjänsten **NAT** -läge som standard. I följande manifest-exempel kan- `NodeContainerServicePackage1` och- `NodeContainerServicePackage2` tjänsterna varje lyssning på samma port (båda tjänsterna lyssnar på `Endpoint1` ). När du har angett öppna nätverks läge `PortBinding` kan du inte ange konfigurationer.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Du kan blanda och matcha olika nätverks lägen mellan tjänster i ett program för ett Windows-kluster. Vissa tjänster kan använda öppet läge medan andra använder NAT-läge. När en tjänst har kon figurer ATS för att använda NAT-läge måste porten som tjänsten lyssnar på vara unik.

    >[!NOTE]
    >I Linux-kluster stöds inte blandade nätverks lägen för olika tjänster. 
    >

5. När du har valt **öppet** läge ska **slut punkts** definitionen i tjänst manifestet uttryckligen peka mot det kod paket som motsvarar slut punkten, även om tjänst paketet bara har ett kod paket i det. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. För Windows kommer en omstart av den virtuella datorn leda till att det öppna nätverket återskapas. Detta är att minimera ett underliggande problem i nätverks stacken. Standard beteendet är att återskapa nätverket. Om det här beteendet måste inaktive ras kan följande konfiguration användas följt av en konfigurations uppgradering.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Nästa steg
* [Förstå Service Fabric-programmodellen](service-fabric-application-model.md)
* [Läs mer om resurser för Service Fabric tjänst manifest](./service-fabric-service-manifest-resources.md)
* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en Docker-behållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
