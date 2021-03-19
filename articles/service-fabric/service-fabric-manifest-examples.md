---
title: Exempel på manifest för Azure Service Fabric-program
description: Lär dig hur du konfigurerar inställningar för program-och tjänst manifest för ett Service Fabric-program.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75451649"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Exempel på Service Fabric program-och tjänst manifest
Det här avsnittet innehåller exempel på program-och tjänst manifest. De här exemplen är inte avsedda att Visa viktiga scenarier, men för att visa de olika inställningar som är tillgängliga och hur de används. 

Följande är ett index över de funktioner som visas och de exempel-manifest som de är en del av.

|Funktion|Manifest|
|---|---|
|[Resursstyrning](service-fabric-resource-governance.md)|[Reliable Services applikations manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [manifest för container applikation](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Kör en tjänst som ett lokalt administratörs konto](service-fabric-application-runas-security.md)|[Reliable Services applikations manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Tillämpa en standard princip för alla service kod paket](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services applikations manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Skapa användar-och grupp objekt](service-fabric-application-runas-security.md)|[Reliable Services applikations manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|Dela ett data paket mellan tjänst instanser|[Reliable Services applikations manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Åsidosätt tjänst slut punkter](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services applikations manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Kör ett skript när tjänsten startas](service-fabric-run-script-at-service-startup.md)|[VotingWeb-tjänst manifest](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definiera en HTTPS-slutpunkt](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb-tjänst manifest](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Deklarera ett konfigurations paket](service-fabric-application-and-service-manifests.md)|[VotingData-tjänst manifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklarera ett data paket](service-fabric-application-and-service-manifests.md)|[VotingData-tjänst manifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Åsidosätt miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest för container applikation](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurera mappning av container port-till-värd](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifest för container applikation](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurera autentisering av containerregister](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifest för container applikation](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ange isolerings läge](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifest för container applikation](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ange avbildningar av operativ system version-specifika behållare](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifest för container applikation](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ange miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[FrontEndService tjänst manifest](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [container BackEndService service manifest](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Konfigurera en slut punkt](service-fabric-get-started-containers.md#configure-communication)|[Container FrontEndService service manifest](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [container BackEndService service manifest](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData service manifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|skicka kommandon till behållaren|[Tjänst manifest för container FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importera ett certifikat till en behållare](service-fabric-securing-containers.md)|[Tjänst manifest för container FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Konfigurera volym driv rutin](service-fabric-containers-volume-logging-drivers.md)|[Tjänst manifest för container BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

