---
title: Principer för Azure API Management-autentisering | Microsoft Docs
description: Lär dig mer om de autentiseringsprinciper som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/27/2021
ms.author: apimpm
ms.openlocfilehash: 22d2960801cac2222f868c384a55b4bf436bc75b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492611"
---
# <a name="api-management-authentication-policies"></a>Principer för API Management-autentisering
Det här avsnittet innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](./api-management-policies.md).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Autentiseringsprinciper

-   [Autentisera med Basic](api-management-authentication-policies.md#Basic) -autentisera med en backend-tjänst med grundläggande autentisering.

-   [Autentisera med klient certifikat](api-management-authentication-policies.md#ClientCertificate) – autentisera med en backend-tjänst med hjälp av klient certifikat.

-   [Autentisera med hanterad identitet](api-management-authentication-policies.md#ManagedIdentity) – autentisera med den [hanterade identiteten](../active-directory/managed-identities-azure-resources/overview.md) för den API Management tjänsten.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Autentisera med Basic
 Använd `authentication-basic` principen för att autentisera med en backend-tjänst med grundläggande autentisering. Den här principen anger att HTTP-Authorization-huvudet ska vara det värde som motsvarar de autentiseringsuppgifter som anges i principen.

### <a name="policy-statement"></a>Princip kommentar

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Exempel

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|autentisering – grundläggande|Rot element.|Ja|

### <a name="attributes"></a>Attribut

|Name|Beskrivning|Krävs|Standardvärde|
|----------|-----------------|--------------|-------------|
|användarnamn|Anger användar namnet för den grundläggande autentiseringsuppgiften.|Ja|Ej tillämpligt|
|password|Anger lösen ordet för grundläggande autentiseringsuppgifter.|Ja|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

-   **Princip avsnitt:** inkommande

-   **Princip omfattningar:** alla omfattningar

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Autentisera med klient certifikat
 Använd `authentication-certificate` principen för att autentisera med en backend-tjänst med ett klient certifikat. Certifikatet måste [installeras i API Management](./api-management-howto-mutual-certificates.md) först och identifieras av sitt tumavtryck eller certifikat-ID (resurs namn). 

> [!CAUTION]
> Om certifikatet refererar till ett certifikat som lagras i Azure Key Vault kan du identifiera det med hjälp av certifikat-ID: t. När ett Key Vault-certifikat roteras kommer dess tumavtryck i API Management att ändras, och principen kommer inte att matcha det nya certifikatet om det identifieras av tumavtrycket.

### <a name="policy-statement"></a>Princip kommentar

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exempel

I det här exemplet identifieras klient certifikatet av certifikat-ID: t:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

I det här exemplet identifieras klient certifikatet med tumavtrycket:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
I det här exemplet anges klient certifikatet i principen i stället för att hämtas från det inbyggda certifikat arkivet:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|autentisering-certifikat|Rot element.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Name|Beskrivning|Krävs|Standardvärde|  
|----------|-----------------|--------------|-------------|  
|begäran|Tumavtryck för klient certifikatet.|Antingen `thumbprint` eller `certificate-id` måste finnas.|Ej tillämpligt|
|certifikat-ID|Certifikat resursens namn.|Antingen `thumbprint` eller `certificate-id` måste finnas.|Ej tillämpligt|
|body|Klient certifikat som en byte mat ris.|Inga|Ej tillämpligt|
|password|Lösen ordet för klient certifikatet.|Används om certifikatet som anges i `body` är lösenordsskyddat.|Ej tillämpligt|
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).  
  
-   **Princip avsnitt:** inkommande  
  
-   **Princip omfattningar:** alla omfattningar  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> Autentisera med hanterad identitet  
 Använd `authentication-managed-identity` principen för att autentisera med en backend-tjänst med hjälp av den hanterade identiteten. Den här principen använder i princip den hanterade identiteten för att hämta en åtkomsttoken från Azure Active Directory för åtkomst till den angivna resursen. När token har hämtats ställer principen in värdet för token i `Authorization` rubriken med `Bearer` schemat.

Både systemtilldelad identitet och någon av de flera användare som tilldelats identiteter kan användas för att begära token. Om `client-id` inte anges används en tilldelad identitet. Om `client-id` variabeln har angetts begärs token för den användarens tilldelade identitet från Azure Active Directory
  
### <a name="policy-statement"></a>Princip kommentar  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exempel  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Använd hanterad identitet för att autentisera med en server dels tjänst
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Använd hanterad identitet och ange sidhuvudet manuellt

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>Använd hanterad identitet i skicka begär ande princip
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|autentisering-hanterad-identitet |Rot element.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Name|Beskrivning|Krävs|Standardvärde|  
|----------|-----------------|--------------|-------------|  
|resource|Sträng. App-ID för mål webb-API (säker resurs) i Azure Active Directory.|Ja|Ej tillämpligt|
|klient-ID|Sträng. App-ID: t för den användarspecifika identiteten i Azure Active Directory.|Inga|systemtilldelad identitet|
|output-token-variabel-namn|Sträng. Namnet på den Sammanhangs variabel som kommer att ta emot token-värde som en objekt typ `string` . |Inga|Ej tillämpligt|  
|Ignorera-fel|Booleskt. Om detta är inställt på `true` , fortsätter princip pipelinen att köras även om en åtkomsttoken inte har hämtats.|Inga|falskt|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).  
  
-   **Princip avsnitt:** inkommande  
  
-   **Princip omfattningar:** alla omfattningar

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med principer finns i:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Princip referens](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar
+ [Principexempel](./policy-reference.md)