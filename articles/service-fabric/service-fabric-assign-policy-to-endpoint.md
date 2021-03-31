---
title: Tilldela åtkomst principer till tjänstens slut punkter
description: Lär dig hur du tilldelar säkerhets åtkomst principer till HTTP-eller HTTPS-slutpunkter i Service Fabrics tjänsten.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614663"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Tilldela en säkerhets åtkomst princip för HTTP-och HTTPS-slutpunkter
Om du använder en kör som-princip och tjänst manifestet deklarerar resurser för HTTP-slutpunkt måste du ange en **SecurityAccessPolicy**.  **SecurityAccessPolicy** garanterar att portarna som allokeras till dessa slut punkter är korrekt begränsade till det användar konto som tjänsten kör som. Annars har **http.sys** inte åtkomst till tjänsten och du får problem med anrop från klienten. I följande exempel används Customer1-kontot till en slut punkt som kallas **EndpointName**, vilket ger fullständig åtkomst behörighet.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

För en HTTPS-slutpunkt anger du även namnet på det certifikat som ska returneras till klienten. Du refererar till certifikatet med **EndpointBindingPolicy**.  Certifikatet definieras i avsnittet **certifikat** i applikations manifestet.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> När du använder HTTPS ska du inte använda samma port och certifikat för olika tjänst instanser (oberoende av programmet) som distribueras till samma nod. Uppgradering av två olika tjänster med samma port i olika program instanser leder till ett uppgraderings haveri. Mer information finns i [uppgradera flera program med https-slutpunkter ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
För nästa steg kan du läsa följande artiklar:
* [Förstå program modellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänst manifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
