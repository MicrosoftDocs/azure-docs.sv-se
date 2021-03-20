---
title: Azure Service Fabric Docker skapa förhands granskning av distribution
description: Azure Service Fabric accepterar Docker-format för att göra det enklare att dirigera befintliga behållare med hjälp av Service Fabric. Detta stöd är för närvarande en för hands version.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84691296"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Docker skapa distributions stöd i Azure Service Fabric (för hands version)

Docker använder filen [filen Docker. yml](https://docs.docker.com/compose) för att definiera program med flera behållare. För att göra det lättare för kunder att bekanta sig med Docker för att dirigera befintliga behållar program på Azure Service Fabric har vi inkluderat för hands versions stöd för Docker-distribution inbyggt i plattformen. Service Fabric kan acceptera version 3 och senare av `docker-compose.yml` filer. 

Eftersom detta stöds i för hands versionen stöds endast en delmängd av skapa-direktiv.

Om du vill använda den här för hands versionen skapar du klustret med version 5,7 eller senare av Service Fabric körningen genom Azure Portal tillsammans med motsvarande SDK. 

> [!NOTE]
> Den här funktionen är i för hands version och stöds inte i produktion.
> Exemplen nedan baseras på Kör tids version 6,0 och SDK version 2,8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Distribuera en Docker-filfil på Service Fabric

Följande kommandon skapar ett Service Fabric-program (med namnet `fabric:/TestContainerApp` ) som du kan övervaka och hantera precis som andra Service Fabric program. Du kan använda det angivna program namnet för hälso frågor.
Service Fabric identifierar "DeploymentName" som identifierare för Skriv distributionen.

### <a name="use-powershell"></a>Använd PowerShell

Skapa en Service Fabric skapa en distribution från en filen Docker. YML-fil genom att köra följande kommando i PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` och `RegistryPassword` Se användar namn och lösen ord för behållar registret. När du har slutfört distributionen kan du kontrol lera statusen med hjälp av följande kommando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Använd följande kommando för att ta bort Skriv distributionen via PowerShell:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Om du vill starta en uppgraderings uppgradering via PowerShell använder du följande kommando:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Använd följande kommando för att återställa uppgraderingen av Skriv distributionen via PowerShell:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

När uppgraderingen har godkänts kan uppgraderings förloppet spåras med hjälp av följande kommando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Använda Azure Service Fabric CLI (sfctl)

Du kan också använda följande Service Fabric CLI-kommando:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

När du har skapat distributionen kan du kontrol lera dess status med hjälp av följande kommando:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Om du vill ta bort Skriv distributionen använder du följande kommando:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Starta en uppgradering av Skriv distribution med följande kommando:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Använd följande kommando för att återställa uppgraderingen av Skriv distributionen:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

När uppgraderingen har godkänts kan uppgraderings förloppet spåras med hjälp av följande kommando:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Skriv direktiv som stöds

Den här för hands versionen stöder en delmängd av konfigurations alternativen från formatet format version 3, inklusive följande primitiver:

* Tjänster > distribuera > repliker
* Tjänster > distribuera > placering > begränsningar
* Tjänster > distribuera > resurser > gränser
    * – CPU-resurser
    * – minne
    * -minnes växling
* Tjänster > kommandon
* Tjänster >s miljö
* Tjänster > portar
* Avbildning av tjänster >
* Tjänster > isolering (endast för Windows)
* Tjänster > loggning > driv rutin
* Tjänster > loggning > driv rutin > alternativ
* Volym & distribuera > volym

Konfigurera klustret för tvingande resurs gränser enligt beskrivningen i [Service Fabric resurs styrning](service-fabric-resource-governance.md). Alla andra Docker skapa-direktiv stöds inte för den här för hands versionen.

### <a name="ports-section"></a>Port avsnitt

Ange antingen http-eller HTTPS-protokollet i avsnittet portar som ska användas av den Service Fabric tjänst lyssnaren. Detta säkerställer att slut punkts protokollet publiceras korrekt med namngivnings tjänsten för att tillåta omvänd proxy att vidarebefordra begär Anden:
* Ange **/http** för att dirigera till oskyddade Service Fabric Skriv tjänster. Till exempel – **"80:80/http"**.
* Ange **/https** för att dirigera till säkra Service Fabric Skriv tjänster. Till exempel – **"443:443/https"**.

> [!NOTE]
> /Http och/https ports section-syntax är särskilt Service Fabric för att registrera rätt Service Fabric lyssnar-URL.  Om Docker-filsyntaxen är program mässigt verifierad kan det orsaka ett verifierings fel.

## <a name="servicednsname-computation"></a>ServiceDnsName-beräkning

Om tjänst namnet som du anger i en Skriv fil är ett fullständigt kvalificerat domän namn (det innehåller en punkt [.]), är DNS-namnet som registreras av Service Fabric `<ServiceName>` (inklusive punkten). Om inte, blir varje Sök vägs segment i program namnet en domän etikett i tjänstens DNS-namn, där det första Sök vägs segmentet blir den översta domän etiketten.

Om det angivna program namnet till exempel är `fabric:/SampleApp/MyComposeApp` , är `<ServiceName>.MyComposeApp.SampleApp` det registrerade DNS-namnet.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Skriv distribution (instans definition) jämfört med Service Fabric app Model (typ definition)

En filen Docker. YML-fil beskriver en distributions uppsättning behållare, inklusive deras egenskaper och konfigurationer.
Filen kan till exempel innehålla miljövariabler och portar. Du kan också ange distributions parametrar, till exempel placerings begränsningar, resurs gränser och DNS-namn i filen filen Docker. yml.

[Service Fabric program modellen](service-fabric-application-model.md) använder tjänst typer och program typer, där du kan ha många program instanser av samma typ. Du kan till exempel ha en program instans per kund. Den här typen-baserade modellen stöder flera versioner av samma program typ som är registrerad i körnings miljön.

Kund A kan till exempel ha ett program instansierat med typ 1,0 av AppTypeA, och kund B kan ha ett annat program instansierat med samma typ och version. Du definierar program typerna i program manifesten och anger program namnet och distributions parametrarna när du skapar programmet.

Även om den här modellen erbjuder flexibilitet, planerar vi också att stödja en enklare, instans-baserad distributions modell där typer är implicita från manifest filen. I den här modellen hämtar varje program sitt eget oberoende manifest. Vi för hands Grans kar den här ansträngningen genom att lägga till stöd för filen Docker. yml, som är ett domänbaserat distributions format.

## <a name="next-steps"></a>Nästa steg

* Läs mer i [Service Fabric program modellen](service-fabric-application-model.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)
