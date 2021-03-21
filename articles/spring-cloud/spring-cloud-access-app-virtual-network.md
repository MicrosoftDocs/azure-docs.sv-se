---
title: Azure våren Cloud Access-app i virtuellt nätverk
description: Få åtkomst till appen i ett Azure våren-moln i det virtuella nätverket.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576539"
---
# <a name="access-your-application-in-a-private-network"></a>Få åtkomst till ditt program i ett privat nätverk

Det här dokumentet beskriver hur du får åtkomst till en slut punkt för ditt program i ett privat nätverk.  För att få åtkomst måste du skapa en **Azure privat DNS-zon** i din prenumeration för att översätta/lösa det privata fullständigt kvalificerade domän namnet (FQDN) till dess IP-adress.

När **tilldelnings slut punkten** för program i en Azure våren Cloud-tjänstinstans distribueras i det virtuella nätverket, är slut punkten ett privat fullständigt domän namn. Domänen är bara tillgänglig i det privata nätverket. Appar och tjänster använder program slut punkten. De innehåller **test slut punkten** som beskrivs i [Visa appar och distributioner](spring-cloud-howto-staging-environment.md#view-apps-and-deployments). **Logg strömning**, som beskrivs i [Stream Azure våren Cloud App-loggar i real tid](spring-cloud-howto-log-streaming.md), fungerar också bara inom det privata nätverket.

## <a name="create-a-private-dns-zone"></a>Skapa en privat DNS-zon

Följande procedur skapar en privat DNS-zon för ett program i det privata nätverket.

1. Öppna Azure-portalen. I den övre sökrutan söker du efter **privat DNS zoner** och väljer **privat DNS zoner** från resultatet.

2. På sidan **privat DNS zoner** väljer du **+ Lägg till**.

3. Fyll i formuläret på sidan **skapa privat DNS zon** . Ange **<span>Private.azuremicroservices.io</span>** som **namn** på zonen.

4. Välj **Granska + skapa**.

5. Välj **Skapa**.

Det kan ta några minuter att skapa zonen.

## <a name="link-the-virtual-network"></a>Länka det virtuella nätverket

Om du vill länka den privata DNS-zonen till det virtuella nätverket måste du skapa en länk till ett virtuellt nätverk.

1. Välj den privata DNS-zon som skapades ovan: **<span>Private.azuremicroservices.io</span>** 

2. I det vänstra fönstret väljer du **länkar till virtuella nätverk**.

3. Välj **Lägg till**.

4. Ange **Azure-våren-Cloud-DNS-Link** som **namn på länk**.

5. För **virtuellt nätverk** väljer du det virtuella nätverk som du skapade enligt beskrivningen i [Distribuera Azure våren Cloud i ditt virtuella Azure-nätverk (VNet-insprutning)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

    ![Lägg till virtuellt nätverks länk](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. Klicka på **OK**.

## <a name="create-dns-record"></a>Skapa DNS-post

Om du vill använda den privata DNS-zonen för att översätta/matcha DNS måste du skapa en post av typen "A" i zonen.

1. Välj den virtuella nätverks resurs som du skapade enligt beskrivningen i [Distribuera Azure våren Cloud i ditt virtuella Azure-nätverk (VNet-inmatning)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

2. I sökrutan **anslutna enheter** anger du *Kubernetes-Internal*.

3. I det filtrerade resultatet letar du reda på **enheten** som är ansluten till service runtime- **undernätet** för tjänst instansen och kopierar dess **IP-adress**. I det här exemplet är IP-adressen *10.1.0.7*.

    [![Skapa DNS-post ](media/spring-cloud-access-app-vnet/create-dns-record.png)](media/spring-cloud-access-app-vnet/create-dns-record.png)

Du kan också hämta IP-adressen med följande AZ CLI-kommando:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Välj den privata DNS-zon som skapades ovan: **<span>Private.azuremicroservices.io</span>**.

5. Välj **+ Postuppsättning**.

6. I **Lägg till post uppsättning** anger eller väljer du den här informationen:

    |Inställning     |Värde                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Namn        |Går *\**                                                                 |
    |Typ        |Välj **en**                                                               |
    |TTL         |Ange *1*                                                                  |
    |TTL-enhet    |Välj **timmar**                                                           |
    |IP-adress  |Ange den IP-adress som kopierades i steg 3. I exemplet anger du *10.1.0.7*.    |

    Välj sedan **OK**.

    ![Lägg till post för privat DNS-zon](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Tilldela ett privat fullständigt domän namn för ditt program

När du har använt proceduren i [skapa och distribuera program för mikrotjänster](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)kan du tilldela ett privat fullständigt domän namn för ditt program.

1. Välj moln tjänst instansen Azure våren som distribueras i det virtuella nätverket och öppna fliken **appar** på menyn till vänster.

2. Välj programmet för att visa **översikts** sidan.

3. Välj **tilldela slut punkt** för att tilldela ett privat FQDN till ditt program. Det kan ta några minuter.

    ![Tilldela privat slut punkt](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. Det tilldelade privata domän namnet (märkt **URL**) är nu tillgängligt. Det går bara att komma åt i det privata nätverket, men inte på Internet.

## <a name="access-application-private-fqdn"></a>Åtkomst till program privat FQDN

Efter tilldelningen kan du komma åt det privata domän namnet för ditt program i privata nätverk. Du kan till exempel skapa en hoppsida i samma virtuella nätverk, eller ett peer-kopplat virtuellt nätverk, och på den datorn i den lokala datorn med det privata domän namnet är tillgängligt.

![Åtkomst till privat slut punkt i VNet](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Nästa steg

- [Exponera program till Internet – använda Application Gateway och Azure-brandväggen](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Se även

- [Felsöka Azure våren Cloud i VNET](spring-cloud-troubleshooting-vnet.md)
- [Kund ansvar för att köra Azure våren Cloud i VNET](spring-cloud-vnet-customer-responsibilities.md)