---
title: Köra en Azure Service Fabric-tjänst under ett gMSA-konto
description: Lär dig hur du kör en tjänst som ett grupphanterat tjänst konto (gMSA) i ett Service Fabric fristående Windows-kluster.
ms.topic: how-to
ms.date: 03/29/2018
ms.openlocfilehash: 9750042764306c5df7a391429cc6926704db05ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91838916"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Köra tjänster som grupphanterade tjänstkonton

På ett fristående Windows Server-kluster kan du köra en tjänst som ett *grupphanterat tjänst konto* (gMSA) med hjälp av en *runas* -princip.  Som standard körs Service Fabric-program under det konto som `Fabric.exe` processen körs under. Att köra program under olika konton, även i en delad värd miljö, gör dem säkrare från varandra. Genom att använda en gMSA finns det inget lösen ord eller krypterat lösen ord som lagras i applikations manifestet.  Du kan också köra en tjänst som [Active Directory användare eller grupp](service-fabric-run-service-as-ad-user-or-group.md).

I följande exempel visas hur du skapar ett gMSA-konto med namnet *SVC-test $*, hur du distribuerar det hanterade tjänst kontot till klusternoderna och hur du konfigurerar användarens huvud namn.

> [!NOTE]
> Att använda en gMSA med ett fristående Service Fabric-kluster kräver Active Directory lokalt i din domän (i stället för Azure Active Directory (Azure AD)).

Förutsättningar:

- Domänen behöver en KDS-rot nyckel.
- Det måste finnas minst en DOMÄNKONTROLLANT med Windows Server 2012 (eller R2) i domänen.

1. Be en Active Directory domän administratör skapa ett grupphanterat tjänst konto med hjälp av `New-ADServiceAccount` cmdleten och se till att `PrincipalsAllowedToRetrieveManagedPassword` alla Service Fabric-klusternoderna inkluderas. `AccountName`, `DnsHostName` , och `ServicePrincipalName` måste vara unika.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. På var och en av Service Fabric klusternoder (till exempel `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$` ) installerar och testar du gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Konfigurera användarens huvud namn och konfigurera `RunAsPolicy` för att referera till [användaren](./service-fabric-cluster-fabric-settings.md#runas).
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE]
> Om du använder en RunAs-princip för en tjänst och tjänst manifestet deklarerar slut punkts resurser med HTTP-protokollet måste du ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhets åtkomst princip för HTTP-och https-slutpunkter](service-fabric-assign-policy-to-endpoint.md).
>

I följande artiklar får du hjälp med nästa steg:

- [Förstå program modellen](service-fabric-application-model.md)
- [Ange resurser i ett tjänst manifest](service-fabric-service-manifest-resources.md)
- [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
