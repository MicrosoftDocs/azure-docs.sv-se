---
title: Azure Service Fabric – konfigurera autentiseringsuppgifter för container-lagringsplatsen
description: Konfigurera autentiseringsuppgifter för lagrings plats för att ladda ned bilder från container Registry
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0c6421fed88a3909db717c13a6b3faf51c4491cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574827"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Konfigurera autentiseringsuppgifter för databasen för ditt program för att ladda ned behållar avbildningar

Konfigurera autentisering av behållar registret genom att lägga till `RepositoryCredentials` i `ContainerHostPolicies` avsnittet i program manifestet. Lägg till kontot och lösen ordet för behållar registret (*myregistry.azurecr.io* i exemplet nedan), vilket gör att tjänsten kan ladda ned behållar avbildningen från lagrings platsen.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Vi rekommenderar att du krypterar lösen ordet för databasen med hjälp av ett krypterings certifikat som distribueras till alla noder i klustret. När Service Fabric distribuerar tjänstpaketet till klustret används chiffreringscertifikatet för att avkryptera chiffertexten. Cmdleten Invoke-ServiceFabricEncryptText används för att skapa chiffertexten för lösenordet, som läggs till i filen ApplicationManifest.xml.
Se [Secret Management](service-fabric-application-secret-management.md) för mer information om certifikat och semantiskt kryptering.

## <a name="configure-cluster-wide-credentials"></a>Konfigurera autentiseringsuppgifter för hela klustret

Med Service Fabric kan du konfigurera autentiseringsuppgifter för hela klustret som kan användas som standard inloggnings uppgifter för alla program.

Den här funktionen kan aktive ras eller inaktive ras genom att `UseDefaultRepositoryCredentials` attributet läggs till `ContainerHostPolicies` i ApplicationManifest.xml med ett `true` eller- `false` värde.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric använder sedan standard-autentiseringsuppgifterna för lagring som kan anges i ClusterManifest under `Hosting` avsnittet.  Om `UseDefaultRepositoryCredentials` är `true` Service Fabric läser följande värden från ClusterManifest:

* DefaultContainerRepositoryAccountName (sträng)
* DefaultContainerRepositoryPassword (sträng)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (sträng)

Här är ett exempel på vad som kan läggas till i `Hosting` avsnittet i ClusterManifestTemplate.jsi filen. `Hosting`Avsnittet kan läggas till när klustret skapas eller senare i en konfigurations uppgradering. Mer information finns i [Ändra kluster inställningar för azure Service Fabric](service-fabric-cluster-fabric-settings.md) och [Hantera Azure Service Fabric program hemligheter](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Använd tokens som autentiseringsuppgifter för registret

Service Fabric stöder token som autentiseringsuppgifter för att ladda ned bilder för dina behållare.  Den här funktionen utnyttjar den *hanterade identiteten* för den underliggande skalnings uppsättningen för virtuella datorer för att autentisera till registret, vilket eliminerar behovet av att hantera användarautentiseringsuppgifter.  Mer information finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) .  Följande steg krävs för att använda den här funktionen:

1. Se till att *systemtilldelad hanterad identitet* är aktive rad för den virtuella datorn.

    ![Azure Portal: skapa identitets alternativ för skal uppsättning för virtuell dator](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

> [!NOTE]
> Hoppa över det här steget för användare som tilldelats hanterad identitet. De återstående stegen nedan fungerar på samma sätt, så länge skalnings uppsättningen bara är associerad med en enda användardefinierad hanterad identitet.

2. Ge behörigheter till skalnings uppsättningen för den virtuella datorn för att hämta/läsa avbildningar från registret. Lägg till en *roll tilldelning* för din virtuella dator från bladet Access Control (IAM) i Azure Container Registry i Azure Portal:

    ![Lägg till VM-huvudobjekt i ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Ändra sedan applikations manifestet. I `ContainerHostPolicies` avsnittet lägger du till attributet `‘UseTokenAuthenticationCredentials=”true”` .

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > Flaggan `UseDefaultRepositoryCredentials` inställt på sant när `UseTokenAuthenticationCredentials` värdet är true orsakar ett fel under distributionen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [behållar registrets autentisering](../container-registry/container-registry-authentication.md).
