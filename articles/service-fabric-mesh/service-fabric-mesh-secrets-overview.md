---
title: Lagra och använda Azure Service Fabric nät program hemligheter
description: Service Fabric Mesh stöder hemligheter som Azure-resurser. Så här lagrar och hanterar du hemligheter med Service Fabric nätappar.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625540"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh-programhemligheter

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Service Fabric Mesh stöder hemligheter som Azure-resurser. En Service Fabric Mesh-hemlighet kan vara valfri känslig textinformation, till exempel lagringsanslutningssträngar, lösenord eller andra värden som bör lagras och överföras på ett säkert sätt.

![Översikt över Mesh-hemligheter][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Resurser för Mesh-hemligheter
En Mesh-programhemlighet består av:
* En **hemlighetsresurs**, vilket är en container som lagrar texthemligheter. Hemligheter som ingår i **hemlighetsresursen** lagras och överförs på ett säkert sätt.
* En eller flera **hemlighets-/värderesurser** som lagras i **hemlighetsresurscontainern**. Varje **hemlighets-/värderesurs** identifieras med ett versionsnummer.

## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh-hemligheter finns i följande avsnitt:
- [Hantera Service Fabric Mesh-programhemligheter](service-fabric-mesh-howto-manage-secrets.md)
- [Introduktion till Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
