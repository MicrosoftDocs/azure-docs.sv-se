---
title: Köra en Azure Service Fabric-tjänst som en AD-användare eller-grupp
description: Lär dig hur du kör en tjänst som en Active Directory användare eller grupp på ett Service Fabric fristående Windows-kluster.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: d4a7afc2ddb0f39014a7cf0fd006d7fe23673a95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91840735"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Köra en tjänst som Active Directory användare eller grupp
På ett fristående Windows Server-kluster kan du köra en tjänst som en Active Directory användare eller grupp med en RunAs-princip.  Som standard körs Service Fabric-program under det konto som Fabric.exe processen körs under. Att köra program under olika konton, även i en delad värd miljö, gör dem säkrare från varandra. Observera att detta använder Active Directory lokalt i din domän och inte Azure Active Directory (Azure AD).  Du kan också köra en tjänst som ett [grupphanterat tjänst konto (gMSA)](service-fabric-run-service-as-gmsa.md).

Genom att använda en domän användare eller grupp kan du sedan komma åt andra resurser i domänen (till exempel fil resurser) som har beviljats behörigheter.

I följande exempel visas en Active Directory användare som heter *TestUser* med sitt domän lösen ord krypterat med hjälp av ett certifikat som kallas för *certifikat.* Du kan använda `Invoke-ServiceFabricEncryptText` PowerShell-kommandot för att skapa texten hemligt chiffer. Mer information finns i [Hantera hemligheter i Service Fabric-program](service-fabric-application-secret-management.md) .

Du måste distribuera den privata nyckeln för certifikatet för att dekryptera lösen ordet till den lokala datorn med hjälp av en out-of-band-metod (i Azure är detta via Azure Resource Manager). När Service Fabric distribuerar tjänst paketet till datorn kan den dekryptera hemligheten och (tillsammans med användar namnet) autentisera med Active Directory för att köras under dessa autentiseringsuppgifter.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Om du använder en RunAs-princip för en tjänst och tjänst manifestet deklarerar slut punkts resurser med HTTP-protokollet måste du också ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhets åtkomst princip för HTTP-och https-slutpunkter](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
I nästa steg ska du läsa följande artiklar:
* [Förstå program modellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänst manifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
