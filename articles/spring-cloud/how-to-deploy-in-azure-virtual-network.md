---
title: Distribuera Azure Spring Cloud i ett virtuellt nätverk
description: Distribuera Azure Spring Cloud i ett virtuellt nätverk (VNet-ktion).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b0ea5728618c7b69403fcc4c0a3575b70fac6038
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482610"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Distribuera Azure Spring Cloud i ett virtuellt nätverk

**Den här artikeln gäller för:** ââ"ï ° Java âé"ï â C #

Den här självstudien förklarar hur du distribuerar Azure Spring Cloud en instans i ditt virtuella nätverk. Den här distributionen kallas ibland för VNet- injection.

Distributionen möjliggör:

* Isolering av Azure Spring Cloud-appar och tjänstkörningar från internetâ€i företagsnätverket eller i ditt företagsnätverk.
* Azure Spring Cloud interaktion med system i lokala datacenter â azure-tjänster i andra virtuella nätverk.
* Kunder kan styra inkommande och utgående nätverkskommunikation för Azure Spring Cloud.

> [!Note]
> Du kan bara välja ditt virtuella Azure-nätverk när du skapar en ny Azure Spring Cloud-tjänstinstans. Du kan inte ändra till ett annat virtuellt nätverk när Azure Spring Cloud har skapats.

## <a name="prerequisites"></a>Förutsättningar

Registrera Azure Spring Cloud **microsoft.AppPlatform** och **Microsoft.ContainerService** enligt anvisningarna i Registrera resursprovider på [Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) eller genom att köra följande Azure CLI-kommando:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Krav för virtuellt nätverk

Det virtuella nätverk som du distribuerar din Azure Spring Cloud instansen till måste uppfylla följande krav:

* **Plats:** Det virtuella nätverket måste finnas på samma plats som den Azure Spring Cloud instansen.
* **Prenumeration:** Det virtuella nätverket måste finnas i samma prenumeration som den Azure Spring Cloud instansen.
* **Undernät:** Det virtuella nätverket måste innehålla två undernät som är dedikerade till en Azure Spring Cloud instans:

    * En för tjänstkörningen.
    * Ett för Spring Boot mikrotjänstprogram.
    * Det finns en en-till-en-relation mellan dessa undernät och en Azure Spring Cloud instans. Använd ett nytt undernät för varje tjänstinstans som du distribuerar. Varje undernät kan bara innehålla en enda tjänstinstans.
* **Adressutrymme:** CIDR blockerar upp till */28* för både undernätet för tjänstkörning och Spring Boot undernätet för mikrotjänstprogram.
* **Vägtabell:** Som standard behöver inte undernäten befintliga associerade vägtabeller. Du kan [ta med din egen vägtabell](#bring-your-own-route-table).

Följande procedurer beskriver konfigurationen av det virtuella nätverket som ska innehålla instansen av Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Om du redan har ett virtuellt nätverk som värd för Azure Spring Cloud instans hoppar du över steg 1, 2 och 3. Du kan börja från steg 4 för att förbereda undernät för det virtuella nätverket.

1. I menyn i Azure-portalen väljer du **Skapa en resurs**. Från Azure Marketplace väljer du **Nätverk**  >  **Virtuellt nätverk**.

1. I **dialogrutan Skapa virtuellt** nätverk anger eller väljer du följande information:

    |Inställning          |Värde                                             |
    |-----------------|--------------------------------------------------|
    |Prenumeration     |Välj din prenumeration.                         |
    |Resursgrupp   |Välj din resursgrupp eller skapa en ny.  |
    |Name             |Ange **azure-spring-cloud-vnet**.                 |
    |Location         |Välj **USA, östra.**                               |

1. Välj **Nästa: IP-adresser.**

1. För IPv4-adressutrymmet anger du **10.1.0.0/16**.

1. Välj **Lägg till undernät.** Ange sedan **service-runtime-subnet** som undernätsnamn och **ange 10.1.0.0/28** som **adressintervall för undernätet.**  Välj **Lägg till**.

1. Välj **Lägg till undernät** igen och ange sedan **Undernätsnamn och** **Adressintervall för undernätet.** Ange till exempel **apps-subnet och** **10.1.1.0/28**. Välj **Lägg till**.

1. Välj **Granska + skapa**. Lämna resten som standard och välj **Skapa.**

## <a name="grant-service-permission-to-the-virtual-network"></a>Bevilja tjänstbehörighet till det virtuella nätverket
Azure Spring Cloud kräver **ägarbehörighet** till ditt virtuella nätverk för att ge ett dedikerat och dynamiskt huvudnamn för tjänsten i det virtuella nätverket för ytterligare distribution och underhåll.

Välj det virtuella nätverket **azure-spring-cloud-vnet som** du skapade tidigare.

1. Välj **Åtkomstkontroll (IAM)** och välj sedan Lägg till **lägg till**  >  **rolltilldelning.**

    ![Skärmbild som visar skärmen Åtkomstkontroll.](./media/spring-cloud-v-net-injection/access-control.png)

1. I dialogrutan **Lägg till** rolltilldelning anger eller väljer du följande information:

    |Inställning  |Värde                                             |
    |---------|--------------------------------------------------|
    |Roll     |Välj **Ägare.**                                 |
    |Välj   |Ange **Azure Spring Cloud resursprovider**.   |

    Välj sedan **Azure Spring Cloud resursprovider** och välj **Spara**.

    ![Skärmbild som visar val Azure Spring Cloud resursprovidern.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Du kan också göra det här steget genom att köra följande Azure CLI-kommando:

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

## <a name="deploy-an-azure-spring-cloud-instance"></a>Distribuera en Azure Spring Cloud instans

Så här distribuerar Azure Spring Cloud instans i det virtuella nätverket:

1. Öppna [Azure-portalen](https://portal.azure.com).

1. I den övre sökrutan söker du efter **Azure Spring Cloud**. Välj **Azure Spring Cloud** i resultatet.

1. På sidan **Azure Spring Cloud** väljer du **+ Lägg till**.

1. Fyll i formuläret på sidan Azure Spring Cloud **Skapa.**

1. Välj samma resursgrupp och region som det virtuella nätverket.

1. För **Namn** under **Tjänstinformation** väljer du **azure-spring-cloud-vnet**.

1. Välj **fliken Nätverk** och välj följande värden:

    |Inställning                                |Värde                                             |
    |---------------------------------------|--------------------------------------------------|
    |Distribuera i ditt eget virtuella nätverk     |Välj **Ja**.                                   |
    |Virtuellt nätverk                        |Välj **azure-spring-cloud-vnet**.               |
    |Undernät för tjänstkörning                 |Välj **service-runtime-subnet**.                |
    |Spring Boot undernät för mikrotjänstappar   |Välj **apps-subnet**.                           |

    ![Skärmbild som visar fliken Nätverk på sidan Azure Spring Cloud Skapa.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Välj **Granska och skapa**.

1. Verifiera dina specifikationer och välj **Skapa.**

    ![Skärmbild som visar verifiering av specifikationer.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Efter distributionen skapas ytterligare två resursgrupper i din prenumeration som värd för nätverksresurserna för Azure Spring Cloud instansen. Gå till **Start** och välj sedan **Resursgrupper från** de översta menyalternativen för att hitta följande nya resursgrupper.

Resursgruppen med namnet **ap-svc-rt_{service instance name}_{service instance region}** innehåller nätverksresurser för tjänstkörningen för tjänstinstansen.

  ![Skärmbild som visar tjänstkörningen.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Resursgruppen med namnet **ap-app_{service instance name}_{service instance region}** innehåller nätverksresurser för dina Spring Boot-mikrotjänstprogram för tjänstinstansen.

  ![Skärmbild som visar appresursgruppen.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Dessa nätverksresurser är anslutna till det virtuella nätverk som skapades i föregående avbildning.

  ![Skärmbild som visar det virtuella nätverket med anslutna enheter.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Resursgrupperna hanteras fullständigt av Azure Spring Cloud tjänsten. Ta *inte* bort eller ändra någon resurs i den manuellt.

## <a name="using-smaller-subnet-ranges"></a>Använda mindre undernätsintervall

Den här tabellen visar det maximala antalet appinstanser som Azure Spring Cloud har stöd för med mindre undernätsintervall.

| Appundernät CIDR | Totalt antal IP-adresser | Tillgängliga IP-adresser | Maximalt antal appinstanser                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> App med 1 kärna: 96 <br/> App med 2 kärnor: 48<br/>  App med 3 kärnor: 32 <br/> App med 4 kärnor: 24 </p> |
| /27             | 32        | 24            | <p> App med 1 kärna: 228<br/> App med 2 kärnor: 144<br/>  App med 3 kärnor: 96 <br/>  App med 4 kärnor: 72</p> |
| /26             | 64        | 56            | <p> App med 1 kärna: 500<br/> App med 2 kärnor: 336<br/>  App med 3 kärnor: 224<br/>  App med 4 kärnor: 168</p> |
| /25             | 128       | 120           | <p> App med 1 kärna: 500<br> App med 2 kärnor: 500<br>  App med 3 kärnor: 480<br>  App med 4 kärnor: 360</p> |
| /24             | 256       | 248           | <p> App med 1 kärna: 500<br/> App med 2 kärnor: 500<br/>  App med 3 kärnor: 500<br/>  App med 4 kärnor: 500</p> |

För undernät reserveras fem IP-adresser av Azure och minst fyra adresser krävs av Azure Spring Cloud. Minst nio IP-adresser krävs, så /29 och /30 är icke-verksamhetsfria.

För ett undernät för tjänstkörning är den minsta storleken /28. Den här storleken påverkar inte antalet appinstanser.

## <a name="bring-your-own-route-table"></a>Bring your own route table

Azure Spring Cloud stöder användning av befintliga undernät och vägtabeller.

Om dina anpassade undernät inte innehåller vägtabeller skapar Azure Spring Cloud dem för vart och ett av undernäten och lägger till regler i dem under instanslivscykeln. Om dina anpassade undernät innehåller vägtabeller bekräftar Azure Spring Cloud befintliga vägtabeller under instansåtgärder och lägger till/uppdaterar och/eller regler för åtgärder.

> [!Warning] 
> Anpassade regler kan läggas till i de anpassade vägtabellerna och uppdateras. Regler läggs dock till av Azure Spring Cloud och dessa får inte uppdateras eller tas bort. Regler som 0.0.0.0/0 måste alltid finnas på en viss vägtabell och mappas till målet för din Internet-gateway, till exempel en NVA eller en annan utgående gateway. Var försiktig när du uppdaterar regler när endast dina anpassade regler ändras.


### <a name="route-table-requirements"></a>Krav för vägtabell

De vägtabeller som ditt anpassade vnet är associerat med måste uppfylla följande krav:

* Du kan bara associera dina Azure-vägtabeller med ditt virtuella nätverk när du skapar en ny Azure Spring Cloud-tjänstinstans. Du kan inte ändra till en annan vägtabell när Azure Spring Cloud har skapats.
* Både undernätet för mikrotjänstprogrammet och undernätet för tjänstkörning måste associeras med olika vägtabeller eller ingen av dem.
* Behörigheter måste tilldelas innan instansen skapas. Se till att ge Azure *Spring Cloud behörigheten Ägare* till dina vägtabeller.
* Den associerade resurs för vägtabellen kan inte uppdateras när klustret har skapats. Resurs för vägtabellen kan inte uppdateras, men anpassade regler kan ändras i vägtabellen.
* Du kan inte återanvända en routningstabell med flera instanser på grund av potentiellt motstridiga routningsregler.

## <a name="next-steps"></a>Nästa steg

[Distribuera program till Azure Spring Cloud i ditt VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Se även

- [Felsöka Azure Spring Cloud i VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Kundens ansvar för att köra Azure Spring Cloud i VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
