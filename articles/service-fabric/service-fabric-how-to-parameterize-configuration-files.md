---
title: Parameterisera config-filer i Azure Service Fabric
description: Lär dig hur du Parameterisera konfigurationsfiler i Service Fabric, en användbar teknik vid hantering av flera miljöer.
ms.topic: conceptual
ms.date: 10/09/2018
ms.openlocfilehash: ca376230c427c47e839b2dee96e8daa83ccedf15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576765"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Så här Parameterisera du konfigurationsfiler i Service Fabric

Den här artikeln visar hur du Parameterisera en konfigurations fil i Service Fabric.  Om du inte redan är bekant med de grundläggande begreppen för att hantera program i flera miljöer kan du läsa [hantera program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedur för att parametriserade konfigurationsfiler

I det här exemplet åsidosätter du ett konfigurations värde med hjälp av parametrarna i program distributionen.

1. Öppna *\<MyService>\PackageRoot\Config\Settings.xml* -filen i ditt tjänst projekt.
1. Ange ett namn och värde för konfigurations parametern, till exempel cache-storlek som motsvarar 25, genom att lägga till följande XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Spara och stäng filen.
1. Öppna *\<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* -filen.
1. I ApplicationManifest.xml-filen deklarerar du en parameter och ett standardvärde i `Parameters` elementet.  Vi rekommenderar att parameter namnet innehåller namnet på tjänsten (till exempel "fjärrtjänst").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. I `ServiceManifestImport` avsnittet i ApplicationManifest.xml-filen lägger du till ett `ConfigOverrides` och `ConfigOverride` -element, refererar till konfigurations paketet, avsnittet och parametern.

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> Om du lägger till en ConfigOverride väljer Service Fabric alltid program parametrarna eller standardvärdet som anges i applikations manifestet.
>
>

## <a name="next-steps"></a>Nästa steg
Information om andra hanterings funktioner för appar som är tillgängliga i Visual Studio finns i [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).
