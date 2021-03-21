---
title: Hantera appar för flera miljöer
description: Azure Service Fabric-program kan köras på kluster som har ett storleks intervall från en dator till tusentals datorer. I vissa fall vill du konfigurera ditt program på olika sätt för de varierande miljöerna. Den här artikeln beskriver hur du definierar olika program parametrar per miljö.
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: c907540c03788ab5f4087a96e301f18ab7ced4ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787985"
---
# <a name="manage-applications-for-multiple-environments"></a>Hantera program för flera miljöer

Med Azure Service Fabric-kluster kan du skapa kluster med var som helst från en till många tusentals datorer. I de flesta fall kan du själv se att du behöver distribuera programmet över flera klusterkonfigurationer: ditt lokala utvecklings kluster, ett delat utvecklings kluster och ditt produktions kluster. Alla dessa kluster anses vara olika miljöer som din kod måste köra i. Programbinärfiler kan köras utan ändringar i det här breda spektrumet, men du vill ofta konfigurera programmet på olika sätt.

Överväg två enkla exempel:
  - tjänsten lyssnar på en definierad port, men du behöver den porten för att vara en annan i alla miljöer
  - Du måste ange olika autentiseringsuppgifter för bindning för en databas i alla miljöer

## <a name="specifying-configuration"></a>Anger konfiguration

Konfigurationen du anger kan delas upp i två kategorier:

- Konfiguration som gäller för hur dina tjänster körs
  - Till exempel port numret för en slut punkt eller antalet instanser av en tjänst
  - Den här konfigurationen anges i program-eller tjänst manifest filen
- Konfiguration som gäller för din program kod
  - Till exempel bindnings information för en databas
  - Den här konfigurationen kan antingen tillhandahållas via konfigurationsfiler eller miljövariabler

> [!NOTE]
> Alla attribut i program-och tjänst manifest filen stöder inte parametrar.
> I dessa fall måste du förlita dig på att ersätta strängar som en del av distributions arbets flödet. I Azure-DevOps kan du använda ett tillägg som ersättnings-token: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens eller i Jenkins kan du köra en skript aktivitet för att ersätta värdena.
>

## <a name="specifying-parameters-during-application-creation"></a>Ange parametrar när programmet skapas

När du skapar en namngiven program instans i Service Fabric kan du välja att skicka parametrarna. Hur du gör det beror på hur du skapar program instansen.

  - I PowerShell [`New-ServiceFabricApplication`](/powershell/module/servicefabric/new-servicefabricapplication) använder cmdleten program parametrarna som en hash-modul.
  - Med hjälp av sfctl [`sfctl application create`](./service-fabric-sfctl-application.md#sfctl-application-create) tar kommandot parametrar som en JSON-sträng. Install.sh-skriptet använder sfctl.
  - Med Visual Studio får du en uppsättning parameter-filer i mappen parametrar i programprojektet. Dessa parameter-filer används vid publicering från Visual Studio med hjälp av Azure DevOps Services eller Azure DevOps Server. I Visual Studio skickas parametervärdena till Deploy-FabricApplication.ps1-skriptet.

## <a name="next-steps"></a>Nästa steg
I följande artiklar visas hur du använder några av de begrepp som beskrivs här:

- [Så här anger du miljövariabler för tjänster i Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Så här anger du Port numret för en tjänst med hjälp av parametrar i Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Så här Parameterisera du konfigurationsfiler](service-fabric-how-to-parameterize-configuration-files.md)

- [Miljö variabel referens](service-fabric-environment-variables-reference.md)
