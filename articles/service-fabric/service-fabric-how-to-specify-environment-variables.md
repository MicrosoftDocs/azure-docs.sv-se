---
title: Ange miljövariabler för tjänster
description: Visar hur du använder miljövariabler för program i Service Fabric
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 610e44ac98b8b8a2ce5a91fdbcfda145ae36a94b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576748"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Så här anger du miljövariabler för tjänster i Service Fabric

Den här artikeln visar hur du anger miljövariabler för en tjänst eller behållare i Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedur för att ange miljövariabler för tjänster

I det här exemplet anger du en miljö variabel för en behållare. Artikeln förutsätter att du redan har ett program-och tjänst manifest.

1. Öppna ServiceManifest.xml-filen.
2. I `CodePackage` elementet lägger du till ett nytt `EnvironmentVariables` element och ett- `EnvironmentVariable` element för varje miljö variabel.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Miljövariabler kan åsidosättas i applikations manifestet.

3. Om du vill åsidosätta miljövariablerna i applikations manifestet använder du- `EnvironmentOverrides` elementet.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Ange miljövariabler dynamiskt med Docker Compose

Service Fabric har stöd för möjligheten att [använda Docker Compose för distribution](service-fabric-docker-compose.md#supported-compose-directives). Skapa filer kan käll miljövariabler från gränssnittet. Det här beteendet kan användas för att ersätta önskade miljö värden dynamiskt:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Nästa steg
Mer information om några av de grundläggande begreppen som beskrivs i den här artikeln finns i artikeln [hantera program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra hanterings funktioner för appar som är tillgängliga i Visual Studio finns i [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).
