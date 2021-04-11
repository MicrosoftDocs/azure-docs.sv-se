---
title: Aktivera slut punkt till slut punkt Transport Layer Security
titleSuffix: Azure Spring Cloud
description: Så här aktiverar du Transport Layer Security slut punkt till slut punkt för ett program.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227814"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>Aktivera end-to-end-TLS för ett program

Det här avsnittet visar hur du aktiverar SSL/TLS från slut punkt till slut punkt för att skydda trafik från en ingångs kontroll till program som stöder HTTPS. När du har aktiverat end-to-end-TLS och läst in ett certifikat från ett nyckel valv, skyddas all kommunikation i Azure våren Cloud med TLS.

   ![Diagram över kommunikation som skyddas av TLS.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Förutsättningar 

- En distribuerad Azure våren Cloud-instans. Kom igång [genom att följa snabb starten för att distribuera via Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) .
- Om du inte är bekant med end-to-end-TLS, se det fullständiga [TLS-exemplet](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl).
- Om du vill läsa in de nödvändiga certifikaten i våren Boot-appar på ett säkert sätt kan du använda nyckel [valv fjäder start starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates).


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>Aktivera end-to-end-TLS i en befintlig app

Använd kommandot `az spring-cloud app update --enable-end-to-end-tls` för att aktivera eller inaktivera TLS från slut punkt till slut punkt för en app.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>Aktivera TLS från slut punkt till slut punkt när du binder anpassad domän

Använd kommandot `az spring-cloud app custom-domain update --enable-end-to-end-tls` eller `az spring-cloud app custom-domain bind --enable-end-to-end-tls` om du vill aktivera eller inaktivera TLS från slut punkt till slut punkt för en app.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>Verifiera TLS-status från slut punkt till slut punkt

Använd kommandot `az spring-cloud app show` för att kontrol lera värdet för `enableEndToEndTls` .
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Nästa steg
* [Åtkomst till konfigurations Server och tjänst register](how-to-access-data-plane-azure-ad-rbac.md)
