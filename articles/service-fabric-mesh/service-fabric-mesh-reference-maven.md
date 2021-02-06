---
title: Maven-referens för Azure Service Fabric nät
description: Innehåller referensen för att använda maven-plugin-programmet för Service Fabric nät
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.custom: devx-track-java
ms.openlocfilehash: dd42752dc7ef2c7686073b7a2d780fe9356425c7
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625591"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven-plugin-program för Service Fabric nät

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

## <a name="prerequisites"></a>Förutsättningar

- Java SDK
- Maven
- Azure CLI med tillägg i nätet
- Service Fabric CLI

## <a name="goals"></a>Mål

### `azure-sfmesh:init`
- Skapar en `servicefabric` mapp som innehåller en `appresources` mapp som innehåller `application.yaml` filen. 

### `azure-sfmesh:addservice`
- Skapar en mapp i en `servicefabric` mapp med tjänst namnet och skapar tjänstens yaml-fil. 

### `azure-sfmesh:addnetwork`
- Genererar en `network` yaml med det angivna nätverks namnet i `appresources` mappen 

### `azure-sfmesh:addgateway`
- Genererar en `gateway` yaml med det tillhandahållna Gateway-namnet i `appresources` mappen 

#### `azure-sfmesh:addvolume`
- Genererar en `volume` yaml med det angivna volym namnet i `appresources` mappen.

### `azure-sfmesh:addsecret`
- Genererar en `secret` yaml med det angivna hemliga namnet i `appresources` mappen 

### `azure-sfmesh:addsecretvalue`
- Genererar en `secretvalue` yaml med angivet hemligt och hemligt värde namn i `appresources` mappen 

### `azure-sfmesh:deploy`
- Sammanfogar yamls från `servicefabric` mappen och skapar en JSON för Azure Resource Manager-mallar i den aktuella mappen.
- Distribuerar alla resurser till Azure Service Fabric nät miljön 

### `azure-sfmesh:deploytocluster`
- Skapar en mapp ( `meshDeploy` ) som innehåller distributions-JSON som genereras från yamls som är tillämpliga för Service Fabric kluster
- Distribuerar alla resurser till Service Fabric-klustret
 

## <a name="usage"></a>Användning

Om du vill använda Maven-pluginprogrammet i Maven Java-appen lägger du till följande kodavsnitt i filen pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Gemensam konfiguration

Maven-plugin-programmet stöder för närvarande inte vanliga konfigurationer av maven-plugin-program för Azure.

## <a name="how-to"></a>Instruktioner

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Initiera Maven-projekt för Azure Service Fabric nät
Kör följande kommando för att skapa program resursens YAML-fil.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Skapar en mapp `servicefabric->appresources` som kallas i rotmappen som innehåller ett program yaml med namnet `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Lägg till resurs i ditt program

#### <a name="add-a-new-network-to-your-application"></a>Lägg till ett nytt nätverk i programmet
Kör kommandot nedan för att skapa en nätverks resurs yaml. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Skapar en nätverks-YAML i mappen `servicefabric->appresources` med namnet `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Lägg till en ny tjänst i programmet
Kör kommandot nedan för att skapa en tjänst yaml. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Skapar en tjänst YAML i mappen `servicefabric->helloworldservice` med namnet `service_helloworldservice` som refererar `helloworldservicenetwork` & `helloworldserver` appen
- Tjänsten lyssnar på Port 8080
- Tjänsten använder ***helloworldserver: senaste*** som behållar avbildning.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Lägg till en ny gateway-resurs i programmet
Kör kommandot nedan för att skapa en gateway-resurs yaml. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Skapar en ny Gateway-YAML i mappen `servicefabric->appresources` med namnet `gateway_helloworldgateway`
- Referenser `helloworldservicelistener` som tjänst lyssnare som lyssnar på anrop från denna gateway. Refererar också till `helloworldservice` som-tjänsten, `helloworldservicenetwork` som nätverk och `helloworldserver` som program. 
- Lyssnar efter begär Anden på port 80

#### <a name="add-a-new-volume-to-your-application"></a>Lägg till en ny volym i programmet
Kör kommandot nedan för att skapa en volym resurs yaml. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Skapar en volym YAML i mappen `servicefabric->appresources` med namnet `volume_vol1`
- Anger egenskaper för obligatoriska parametrar, `volumeAccountKey` och `volumeShareName` som ovan
- Mer information om hur du refererar till den här skapade volymen finns i följande [distribuera app med Azure Files volym](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Lägg till en ny hemlig resurs i programmet
Kör kommandot nedan för att skapa en hemlig resurs yaml. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Skapar en hemlig YAML i mappen `servicefabric->appresources` med namnet `secret_secret1`
- Mer information om hur du refererar till den här skapade hemligheten finns på följande, [Hantera hemligheter](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Lägg till en ny secretvalue-resurs i ditt program
Kör kommandot nedan för att skapa en secretvalue-resurs yaml. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Skapa en secretvalue-YAML i mappen `servicefabric->appresources` med namnet `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Köra appen lokalt

Med hjälp av målet `azure-sfmesh:deploytocluster` kan du köra programmet lokalt med hjälp av kommandot nedan:

```cmd
mvn azure-sfmesh:deploytocluster
```

Som standard distribuerar det här målet resurser till det lokala klustret. Om du distribuerar till lokalt kluster förutsätter vi att du har ett lokalt Service Fabric kluster igång. Lokala Service Fabrics kluster för resurser stöds för närvarande endast på [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Skapar JSON-fel från yamls som är tillämpliga för Service Fabric kluster
- Distribueras sedan till kluster slut punkten

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Distribuera program till Azure Service Fabric-nät

Med hjälp av målet `azure-sfmesh:deploy` kan du distribuera till Service Fabric nät miljö genom att köra kommandot nedan:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Skapar en resurs grupp `todoapprg` som heter om den inte finns.
- Skapar en JSON för Azure Resource Manager-mall genom att sammanfoga YAMLs. 
- Distribuerar JSON till Azure Service Fabric nät miljön.
