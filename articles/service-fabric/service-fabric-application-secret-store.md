---
title: Azure Service Fabric centrala hemligheter
description: Den här artikeln beskriver hur du använder Central hemligheter i Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: e9fd435803ad5354b0eb2d4f5de50009a8cbbfe2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88869763"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Centrala hemligheter i Azure Service Fabric 
Den här artikeln beskriver hur du använder den centrala hemligheter Store (CSS) i Azure Service Fabric för att skapa hemligheter i Service Fabric program. CSS är en lokal hemlighet som lagrar känsliga data, till exempel lösen ord, tokens och nycklar, krypterade i minnet.

  > [!NOTE] 
  > När du aktiverar CSS för första gången innan du sa version 7,1. CU3 kan aktive ras och lämna CSS i fel tillstånd permanent om: CSS är aktiverat på ett Windows-autentiserat kluster. CSS aktive ras på ett kluster `EncryptionCertificateThumbprint` , men har deklarerats felaktigt eller motsvarande certifikat är inte installerat/ACL-ed på noder. För Windows auth-kluster kan du komma till 7,1. CU3 innan du fortsätter. För andra kluster måste du kontrol lera dessa invarianter eller komma till 7,1. CU3.
  
## <a name="enable-central-secrets-store"></a>Aktivera Central hemligheter Arkiv
Lägg till följande skript i kluster konfigurationen under `fabricSettings` för att aktivera CSS. Vi rekommenderar att du använder ett annat certifikat än ett kluster certifikat för CSS. Se till att krypterings certifikatet har installerats på alla noder och att det `NetworkService` har Läs behörighet till certifikatets privata nyckel.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Deklarera en hemlig resurs
Du kan skapa en hemlig resurs med hjälp av REST API.
  > [!NOTE] 
  > Om klustret använder Windows-autentisering skickas REST-begäran via oskyddad HTTP-kanal. Rekommendationen är att använda ett X509-baserat kluster med säkra slut punkter.

Om du vill skapa en `supersecret` hemlig resurs med hjälp av REST API gör du en skicka-begäran till `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` . Du behöver ett kluster certifikat eller ett administratörs klient certifikat för att skapa en hemlig resurs.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Ange det hemliga värdet

Använd följande skript för att använda REST API för att ange det hemliga värdet.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Granska det hemliga värdet
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Använd hemligheten i ditt program

Följ dessa steg om du vill använda hemligheten i ditt Service Fabric-program.

1. Lägg till ett avsnitt i **settings.xml** -filen med följande kodfragment. Observera att värdet är i formatet { `secretname:version` }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importera avsnittet i **ApplicationManifest.xml**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   Miljövariabeln `SecretPath` kommer att peka på den katalog där alla hemligheter lagras. Varje parameter som anges under `testsecrets` avsnittet lagras i en separat fil. Programmet kan nu använda hemligheten på följande sätt:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Montera hemligheterna till en behållare. Den enda ändring som krävs för att göra de hemligheter som är tillgängliga i behållaren är till `specify` en monterings punkt i `<ConfigPackage>` .
Följande kodfragment är den ändrade **ApplicationManifest.xml**.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Hemligheter är tillgängliga under monterings punkten i din behållare.

1. Du kan binda en hemlighet till en process miljö variabel genom att ange `Type='SecretsStoreRef` . Följande kodfragment är ett exempel på hur du binder `supersecret` versionen `ver1` till miljövariabeln `MySuperSecret` i **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Nästa steg
Läs mer om [säkerhet för program och tjänster](service-fabric-application-and-service-security.md).
