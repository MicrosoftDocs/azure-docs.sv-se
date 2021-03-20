---
title: Variabler för Azure Service Fabric-miljövariabler
description: Lär dig mer om miljövariabler i Azure Service Fabric. Innehåller en referens till en fullständig lista över variabler och deras användning.
ms.topic: reference
ms.date: 12/07/2017
ms.openlocfilehash: b70249daa439b5a631b5a84b10c47f082ce75985
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574589"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric miljövariabler

Service Fabric har inbyggda miljövariabler som är inställda för varje tjänst instans. Den fullständiga listan över miljövariabler är nedan:

| Miljö variabel                         | Beskrivning                                                            | Exempel                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Infrastrukturens URI-namn för programmet                                 | infrastruktur resurser:/mina program                                                |
| Fabric_CodePackageName                       | Namnet på det kod paket som processen tillhör              | Kod                                                                 |
| Fabric_Endpoint \_ IPOrFQDN \_ *ServiceEndpointName*     | Slut punktens IP-adress eller fullständiga domän namn                                 | 10.0.0.1                                                     |
| ServiceEndpointName för infrastruktur resurs \_ slut punkt \_               | Port nummer för slut punkten                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Loggmappen                                                             | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12 \\ \\ logg      |
| Fabric_Folder_App_Temp                       | Temp-mapp                                                            | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12 \\ \\ Temp     |
| Fabric_Folder_App_Work                       | Arbetsmapp                                                            | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12 \\ \\ arbete     |
| Fabric_Folder_Application                    | Programmets arbetsmapp                                           | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12             |
| Fabric_IsContainerHost                       | En bool som anger om processen är en behållare                   | falskt                                                                |
| Fabric_NodeId                                | Node-ID för noden som kör processen                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Nodens IP eller FQDN, enligt vad som anges i kluster manifest filen. | localhost eller 10.0.0.1                                                |
| Fabric_NodeName                              | Nodnamn för noden som kör processen                          | _Node_0                                                              |
| Fabric_ServiceName                           | Infrastrukturens URI-namn för tjänsten, om tjänsten finns i ExclusiveProcess-läge. Detta variabel värde är bara tillgängligt om du skapar tjänsten med ServicePackageActivationMode ExclusiveProcess.  | infrastruktur resurser:/mina program/mina tjänster                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId                                         | ETT GUID                                                               |
| Fabric_ServicePackageName                    | Namnet på det tjänst paket som processen tillhör                     | Web1Pkg                                                              |

Interna miljövariabler som används av Service Fabric Runtime:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
