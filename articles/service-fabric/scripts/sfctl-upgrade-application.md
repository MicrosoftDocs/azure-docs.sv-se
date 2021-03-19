---
title: Uppdatera ett program i ett kluster i sfctl
description: Skriptexempel för Service Fabric CLI – Uppdatera ett program med en ny version. Det här exemplet uppgraderar också ett distribuerat program med de nya bitarna.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 34f2ae6f3a2ff3adc35794d6e7dfd682640c646b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75614731"
---
# <a name="update-an-application-using-the-service-fabric-cli"></a>Uppdatera ett program med hjälp av Service Fabric CLI

Exempelskriptet laddar upp en ny version av ett befintligt program och uppgraderar sedan ett distribuerat program med de nya bitarna.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Service Fabric CLI-dokumentationen](../service-fabric-cli.md).

Fler Service Fabric CLI-exempel för Azure Service Fabric finns i [Service Fabric CLI-exempel](../samples-cli.md).
