---
title: Hantera Azure Service Fabric program hemligheter
description: Lär dig hur du skyddar hemliga värden i ett Service Fabric program (oberoende).
ms.topic: conceptual
ms.date: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a11869c3b606ed9e74ce4f598109139fa1bb4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89012831"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Hantera krypterade hemligheter i Service Fabric program
Den här guiden vägleder dig genom stegen för att hantera hemligheter i ett Service Fabric program. Hemligheter kan vara vilken känslig information som helst, till exempel lagrings anslutnings strängar, lösen ord eller andra värden som inte ska hanteras i oformaterad text.

Att använda krypterade hemligheter i ett Service Fabric-program omfattar tre steg:
* Konfigurera ett krypterings certifikat och kryptera hemligheter.
* Ange krypterade hemligheter i ett program.
* Dekryptera krypterade hemligheter från Service koden.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Konfigurera ett krypterings certifikat och kryptera hemligheter
Att konfigurera ett krypterings certifikat och använda det för att kryptera hemligheter varierar mellan Windows och Linux.
* [Konfigurera ett krypterings certifikat och kryptera hemligheter på Windows-kluster.][secret-management-windows-specific-link]
* [Konfigurera ett krypterings certifikat och kryptera hemligheter på Linux-kluster.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Ange krypterade hemligheter i ett program
I föregående steg beskrivs hur du krypterar en hemlighet med ett certifikat och skapar en Base64 64-kodad sträng för användning i ett program. Den här Base-64-kodade strängen kan anges som en krypterad [parameter][parameters-link] i en tjänsts Settings.xml eller som en krypterad [miljö variabel][environment-variables-link] i en tjänsts ServiceManifest.xml.

Ange en krypterad [parameter][parameters-link] i tjänstens Settings.xml konfigurations fil med `IsEncrypted` attributet angivet till `true` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Ange en krypterad [miljö variabel][environment-variables-link] i tjänstens ServiceManifest.xml fil med `Type` attributet angivet till `Encrypted` :
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Hemligheterna bör också inkluderas i ditt Service Fabric-program genom att ange ett certifikat i applikations manifestet. Lägg till ett **SecretsCertificate** -element i **ApplicationManifest.xml** och inkludera det önskade certifikatets tumavtryck.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> När du aktiverar ett program som anger en SecretsCertificate hittar Service Fabric det matchande certifikatet och tilldelar identiteten att programmet körs med fullständig behörighet till certifikatets privata nyckel. Service Fabric övervakar också certifikatet efter ändringar och tillämpar behörigheterna på nytt. Om du vill identifiera ändringar för certifikat som har deklarerats med ett eget namn kör Service Fabric en regelbunden uppgift som hittar alla matchande certifikat och jämför den med en cachelagrad lista över tumavtrycken. När ett nytt tumavtryck identifieras innebär det att ett certifikat från det ämnet har förnyats. Aktiviteten körs en gång per minut på varje nod i klustret.
>
> Medan SecretsCertificate tillåter ämnesbaserade deklarationer noterar du att de krypterade inställningarna är knutna till nyckel paret som användes för att kryptera inställningen på klienten. Du måste se till att det ursprungliga krypterings certifikatet (eller motsvarande) matchar den ämnes baserade deklarationen och att det är installerat, inklusive dess motsvarande privata nyckel, på varje nod i klustret som kan vara värd för programmet. Alla tidsgiltiga certifikat som matchar den ämnesbaserade deklarationen och som bygger på samma nyckel par som det ursprungliga krypterings certifikatet betraktas som motsvarigheter.
>

### <a name="inject-application-secrets-into-application-instances"></a>Mata in program hemligheter i program instanser
Vi rekommenderar att distribution till olika miljöer är så automatiserat som möjligt. Detta kan åstadkommas genom att utföra hemlig kryptering i en build-miljö och tillhandahålla krypterade hemligheter som parametrar när du skapar program instanser.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Använd åsidosättningsbar-parametrar i Settings.xml
Settings.xml konfigurations filen tillåter åsidosättningsbar-parametrar som kan tillhandahållas när programmet skapas. Använd `MustOverride` attributet i stället för att ange ett värde för en parameter:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Om du vill åsidosätta värden i Settings.xml, deklarera en parameter för åsidosättning för tjänsten i ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Du kan nu ange värdet som en *program parameter* när du skapar en instans av programmet. Att skapa en program instans kan skriptas med PowerShell eller skrivas i C# för enkel integrering i en build-process.

Med PowerShell anges parametern till `New-ServiceFabricApplication` kommandot som en [hash-tabell](/previous-versions/windows/it-pro/windows-powershell-1.0/ee692803(v=technet.10)):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Med C# anges program parametrar i en `ApplicationDescription` som `NameValueCollection` :

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Dekryptera krypterade hemligheter från Service koden
API: erna för åtkomst till [parametrar][parameters-link] och [miljövariabler][environment-variables-link] möjliggör enkel dekryptering av krypterade värden. Eftersom den krypterade strängen innehåller information om certifikatet som används för kryptering behöver du inte ange certifikatet manuellt. Certifikatet behöver bara installeras på den nod där tjänsten körs.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Nästa steg
* Service Fabric [hemligheter](service-fabric-application-secret-store.md) 
* Läs mer om [säkerhet för program och tjänster](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md
