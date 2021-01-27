---
title: Azure Service Fabric – använda referenser för Service Fabric-program för nyckel valv
description: Den här artikeln förklarar hur du använder Service Fabric KeyVaultReference-stöd för program hemligheter.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898604"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>KeyVaultReference-stöd för Azure-distribuerade Service Fabric program

En vanlig utmaning när du bygger moln program är hur du säkert distribuerar hemligheter till dina program på ett säkert sätt. Till exempel kanske du vill distribuera en databas nyckel till ditt program utan att exponera nyckeln under pipelinen eller till operatorn. Service Fabric KeyVaultReference-support gör det enkelt att distribuera hemligheter till dina program genom att referera till URL: en för hemligheten som lagras i Key Vault. Service Fabric hanterar hämtningen av hemligheten för programmets hanterade identitet och aktiverar programmet med hemligheten.

> [!NOTE]
> KeyVaultReference-stöd för Service Fabric program är GA (för hands version) från och med Service Fabric version 7,2 CU5. Vi rekommenderar att du uppgraderar till den här versionen innan du använder den här funktionen.

> [!NOTE]
> KeyVaultReference-stöd för Service Fabric program har endast stöd för [versions](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) hemligheter. Det finns inte stöd för versions hanterings hemligheter. Key Vault måste finnas i samma prenumeration som Service Fabric-klustret. 

## <a name="prerequisites"></a>Förutsättningar

- Hanterad identitet för Service Fabric program

    Service Fabric KeyVaultReference support använder ett programs hanterade identitet för att hämta hemligheter för programmets räkning, så att ditt program måste distribueras via och tilldelas en hanterad identitet. Följ det här [dokumentet](concepts-managed-identity.md) för att aktivera hanterad identitet för ditt program.

- Centrala hemligheter lagras (CSS).

    Den centrala hemligheter Store (CSS) är Service Fabrics krypterade lokala hemligheter. Den här funktionen använder CSS för att skydda och bevara hemligheter när de har hämtats från Key Vault. Att aktivera den här valfria system tjänsten måste också använda den här funktionen. Följ det här [dokumentet](service-fabric-application-secret-store.md) om du vill aktivera och konfigurera CSS.

- Bevilja programmets åtkomst behörighet för hanterad identitet till nyckel valvet

    Referera till det här [dokumentet](how-to-grant-access-other-resources.md) för att se hur du beviljar hanterad identitets åtkomst till nyckel valv. Observera också om du använder systemtilldelad hanterad identitet skapas den hanterade identiteten endast efter program distributionen. Detta kan skapa tävlings villkor där programmet försöker få åtkomst till hemligheten innan identiteten kan tilldelas till valvet. Den tilldelade identitetens namn är `{cluster name}/{application name}/{service name}` .
    
## <a name="use-keyvaultreferences-in-your-application"></a>Använda KeyVaultReferences i ditt program
KeyVaultReferences kan användas på flera olika sätt
- [Som en miljö variabel](#as-an-environment-variable)
- [Monteras som en fil i din behållare](#mounted-as-a-file-into-your-container)
- [Som referens till ett lösen ord för container databasen](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>Som en miljö variabel

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Monteras som en fil i din behållare

- Lägg till ett avsnitt i settings.xml

    Definiera `MySecret` parameter med typ `KeyVaultReference` och värde `<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- Referera till det nya avsnittet i ApplicationManifest.xml i `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Använda hemligheterna från Service koden

    Varje parameter som anges under är `<Section  Name=MySecrets>` en fil under mappen som EnvironmentVariable SecretPath. I det här C#-kodfragmentet visas hur du läser hemligheter från ditt program.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > Monterings punkt styr mappen där filerna som innehåller hemliga värden ska monteras.

### <a name="as-a-reference-to-a-container-repository-password"></a>Som referens till ett lösen ord för container databasen

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om Azure-valv](../key-vault/index.yml)
* [Läs mer om Central Secret](service-fabric-application-secret-store.md)
* [Lär dig mer om hanterad identitet för Service Fabric program](concepts-managed-identity.md)
