---
title: Exponera program för Internet med hjälp av Application Gateway och Azure-brandväggen
description: Hur du exponerar program på Internet med hjälp av Application Gateway och Azure-brandväggen
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878585"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Exponera program för Internet med hjälp av Application Gateway och Azure-brandväggen

Det här dokumentet beskriver hur du exponerar program på Internet med hjälp av Application Gateway och Azure-brandväggen. När en Azure våren Cloud Service-instans distribueras i det virtuella nätverket, är program på tjänst instansen bara tillgängliga i det privata nätverket. Om du vill göra programmen tillgängliga på Internet måste du integrera med **Azure Application Gateway** och eventuellt med **Azure-brandväggen**.

## <a name="prerequisites"></a>Förutsättningar

- [Azure CLI version 2.0.4 eller senare](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Definiera variabler

Definiera variabler för resurs gruppen och det virtuella nätverk som du skapade enligt anvisningarna i [Distribuera Azure våren Cloud i Azure Virtual Network (VNet-insprutning)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md). Anpassa värdena baserat på din riktiga miljö.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Logga in till Azure

Logga in på Azure CLI och välj din aktiva prenumeration.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

**Azure Application Gateway** som ska skapas ansluter till samma virtuella nätverk som-eller peer-kopplade virtuella nätverk till--Azure våren Cloud Service-instansen. Skapa först ett nytt undernät för Application Gateway i det virtuella nätverket med `az network vnet subnet create` , och skapa också en offentlig IP-adress som klient del för Application Gateway med hjälp av `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Skapa Application Gateway

Skapa en Application Gateway med `az network application-gateway create` och ange programmets privata fullständigt kvalificerade domän namn (FQDN) som servrar i backend-poolen. Uppdatera sedan HTTP-inställningen med `az network application-gateway http-settings update` och Använd värd namnet från backend-poolen.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Det kan ta upp till 30 minuter för Azure att skapa programgatewayen. När den har skapats kontrollerar du Server dels hälsan med `az network application-gateway show-backend-health` .  Detta undersöker om programgatewayen når ditt program via dess privata fullständiga domän namn.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

Utdata anger den felfria statusen för backend-poolen.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Få åtkomst till ditt program med den offentliga IP-adressen för programgatewayen

Hämta den offentliga IP-adressen för programgatewayen med hjälp av `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Kopiera och klistra in den offentliga IP-adressen i webbläsarens adressfält.

  ![App i offentlig IP](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Se även

- [Felsöka Azure våren Cloud i VNET](spring-cloud-troubleshooting-vnet.md)
- [Kund ansvar för att köra Azure våren Cloud i VNET](spring-cloud-vnet-customer-responsibilities.md)