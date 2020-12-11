---
title: Självstudie – Distribuera Azure våren Cloud i det virtuella nätverket
description: Distribuera Azure våren Cloud i Virtual Network (v-net insprutning).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 2f5c16fce68213b291b970c11921a17b39527270
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032125"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Självstudie: Distribuera Azure våren Cloud i Azure Virtual Network (VNet-insprutning)

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

I den här självstudien beskrivs hur du distribuerar en Azure våren Cloud Service-instans i det virtuella nätverket. Detta kallas ibland VNet-inmatning.  

Distributionen gör det möjligt att:

* Isolering av Azure våren-molnappar och service runtime från Internet i företags nätverket
* Azure våren Cloud-interaktion med system i lokala data Center och/eller Azure-tjänster i andra virtuella nätverk
* Bemyndiga kunder att kontrol lera inkommande och utgående nätverkskommunikation för Azure våren Cloud

## <a name="prerequisites"></a>Förutsättningar
Du måste registrera Azure våren Cloud Resource Provider *Microsoft. AppPlatform* och *Microsoft. container service* enligt anvisningarna [Registrera resurs leverantören på Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) eller genom att köra följande AZ CLI-kommando:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```
## <a name="virtual-network-requirements"></a>Krav för virtuellt nätverk
Det virtuella nätverk som du distribuerar din Azure våren moln tjänst instans till måste uppfylla följande krav:

* **Plats**: det virtuella nätverket måste finnas på samma plats som Azure våren Cloud Service-instansen.
* **Prenumeration**: det virtuella nätverket måste finnas i samma prenumeration som Azure våren Cloud Service-instansen.
* **Undernät**: det virtuella nätverket måste innehålla två undernät som är dedikerade till en Azure våren Cloud Service-instans: 
    * En för service runtime
    * En för dina våren Boot-mikrotjänstprogram. 
    * Det finns en 1-till-1-relation mellan dessa undernät och en Azure våren Cloud Service-instans. Du måste använda ett nytt undernät för varje tjänst instans som du distribuerar och varje undernät kan bara innehålla en enda tjänst instans.
* **Adress utrymme**: CIDR-blocken är upp till **/28** för både service runtime-undernät och våren Boot-program med mikrotjänstprogram.
* **Routningstabell**: under näten får inte ha någon befintlig routningstabell kopplad.

Följande procedurer beskriver hur du installerar det virtuella nätverket som innehåller instansen av Azure våren Cloud.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Om du redan har ett virtuellt nätverk som värd för Azure våren Cloud Service-instansen hoppar du över steg 1, 2 och 3. Du kan börja från steg 4 för att förbereda undernät för det virtuella nätverket.

1. I menyn i Azure-portalen väljer du **Skapa en resurs**. På Azure Marketplace väljer du **nätverk**  >  **virtuellt nätverk**.

1. I dialog rutan **Skapa virtuellt nätverk** anger eller väljer du följande information:

    |Inställning          |Värde                                             |
    |-----------------|--------------------------------------------------|
    |Prenumeration     |Välj din prenumeration.                         |
    |Resursgrupp   |Välj en resurs grupp eller skapa en ny.  |
    |Name             |Ange *Azure-våren-Cloud-VNet*                   |
    |Plats         |Välj **USA, östra**                                |

1. Klicka på **Nästa: IP-adresser >**. 
 
1. För IPv4-adress utrymme anger du 10.1.0.0/16.

1. Välj **Lägg till undernät** och ange sedan *service-runtime-Subnet* för **under nätets namn** och 10.1.0.0/28 för **under nätets adress intervall**. Klicka sedan på **Lägg till**.

1. Välj **Lägg till undernät** igen och ange **under nätets namn** och **under nätets adress intervall**, till exempel *Apps-Subnet* och 10.1.1.0/28.  Klicka på **Lägg till**.

1. Klicka på **Granska + skapa**. Lämna resten som standard och klicka på **skapa**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Bevilja tjänst behörighet till det virtuella nätverket

Välj det virtuella nätverket *Azure-våren-Cloud-VNet* som du skapade tidigare.

1. Välj **åtkomst kontroll (IAM)** och välj sedan **Lägg till > Lägg till roll tilldelning**.

    ![Åtkomst kontroll för v-net](./media/spring-cloud-v-net-injection/access-control.png)

2. I dialog rutan **Lägg till roll tilldelning** anger eller väljer du den här informationen:

    |Inställning  |Värde                                             |
    |---------|--------------------------------------------------|
    |Roll     |Välj **ägare**                                  |
    |Välj   |Ange *Azure våren Cloud Resource Provider*      |

    Välj sedan *Azure våren Cloud Resource Provider* och klicka på **Spara**.

    ![Tilldela Azures moln resurs leverantör till v-net](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Du kan också åstadkomma detta genom att köra följande AZ CLI-kommando

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Distribuera Azure våren Cloud Service-instansen i det virtuella nätverket

1. Öppna Azure Portal med på https://portal.azure.com .

1. Sök efter **Azure våren Cloud** i den översta sökrutan och välj **Azure våren Cloud** från resultatet.

1. På sidan **Azure våren Cloud** väljer du **+ Lägg till**.

1. Fyll i formuläret på sidan Azure våren Cloud **create** . 

1. Välj samma resurs grupp och region som det virtuella nätverket.

1. Som **namn** under **tjänst information** väljer du *Azure-våren-Cloud-VNet*.

1. Välj fliken **nätverk** och välj följande:

    |Inställning                                |Värde                                             |
    |---------------------------------------|--------------------------------------------------|
    |Distribuera i ditt eget virtuella nätverk     |Välj **Ja**                                    |
    |Virtuellt nätverk                        |Välj *Azure-våren-Cloud-VNet*                  |
    |Undernät för service runtime                 |Välj *service-runtime-Subnet*                   |
    |Subnet Boot mikroservice Apps-undernät   |Välj *appar-undernät*                              |

    ![Fliken skapa nätverk](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Klicka på **Granska och skapa**.

1. Kontrol lera dina specifikationer och klicka på **skapa**.

    ![Kontrol lera specifikationer](./media/spring-cloud-v-net-injection/verify-specifications.png)

Efter distributionen skapas två ytterligare resurs grupper i din prenumeration som värd för nätverks resurserna för Azure våren Cloud Service-instansen.  Gå till **Start** och välj **resurs grupper** från de översta meny alternativen för att hitta följande nya resurs grupper.

Resurs gruppen med namnet as *AP-SVC-RT_ {tjänst instans namn} _ {tjänst instans region}* innehåller nätverks resurser för tjänst körningens tjänst körning.

  ![Service runtime](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Resurs gruppen med namnet som *AP-app_ {tjänst instans namn} _ {tjänst instans region}* innehåller nätverks resurser för dina våren Boot-mikrotjänstprogram för tjänst instansen.

  ![Resurs grupp för appar](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Nätverks resurserna är anslutna till ditt virtuella nätverk som du skapade ovan.

  ![V-net med ansluten enhet](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Resurs grupperna hanteras fullständigt av Azure våren Cloud service. Ta inte bort eller ändra någon resurs i den manuellt.

## <a name="limitations"></a>Begränsningar

Litet under näts intervall sparar IP-adresser, men ger begränsningar till det maximala antalet App-instanser som Azure våren-molnet kan hantera. 

| CIDR | Totalt antal IP-adresser | Tillgängliga IP-adresser | Maximalt antal App-instanser                                        |
| ---- | --------- | ------------- | ------------------------------------------------------------ |
| /28  | 16        | 8             | <p> App med 1 kärna: 96 <br/> App med 2 kärnor: 48<br/>  App med 3 kärnor: 32 <br/> App med 4 kärnor: 24 </p> |
| /27  | 32        | 24            | <p> App med 1 kärna: 228<br/> App med 2 kärnor: 144<br/>  App med 3 kärnor: 96 <br/>  App med 4 kärnor: 72</p> |
| /26  | 64        | 56            | <p> App med 1 kärna: 500<br/> App med 2 kärnor: 336<br/>  App med 3 kärnor: 224<br/>  App med 4 kärnor: 168</p> |
| /25  | 128       | 120           | <p> App med 1 kärna: 500<br> App med 2 kärnor: 500<br>  App med 3 kärnor: 480<br>  App med 4 kärnor: 360</p> |
| /24  | 256       | 248           | <p> App med 1 kärna: 500<br/> App med 2 kärnor: 500<br/>  App med 3 kärnor: 500<br/>  App med 4 kärnor: 500</p> |

## <a name="next-steps"></a>Nästa steg

[Distribuera program till Azure våren Cloud i ditt VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Se även

- [Felsöka Azure våren Cloud i VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Kund ansvar för att köra Azure våren Cloud i VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)