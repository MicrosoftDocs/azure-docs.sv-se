---
title: 'Snabb start: Konfigurera Azure-skydds och ansluta till en virtuell dator via privat IP-adress och en webbläsare'
titleSuffix: Azure Bastion
description: I den här snabb starts artikeln lär du dig hur du skapar en Azure skydds-värd från en virtuell dator och ansluter till den virtuella datorn via din webbläsare via privat IP-adress.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 02/18/2021
ms.author: cherylmc
ms.openlocfilehash: 8aeba13954283ca35c3eb0060a0e588ba6a7adbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101707179"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Snabb start: ansluta till en virtuell dator på ett säkert sätt via en webbläsare via privat IP-adress

Du kan ansluta till en virtuell dator (VM) via webbläsaren med hjälp av Azure Portal och Azure-skydds. Den här snabb starts artikeln visar hur du konfigurerar Azure-skydds baserat på dina VM-inställningar och sedan ansluter till den virtuella datorn via portalen. Den virtuella datorn behöver inte ha någon offentlig IP-adress, klient program vara, agent eller en särskild konfiguration. När tjänsten har tillhandahållits är RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Mer information om Azure skydds finns i [Vad är Azure skydds?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. Om du inte har ett kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). För att kunna ansluta till en virtuell dator via webbläsaren med skydds måste du kunna logga in på den Azure Portal.

* En virtuell Windows-dator i ett virtuellt nätverk. Om du inte har en virtuell dator skapar du en med [snabb start: skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md).

  * Om du behöver exempel värden kan du se de tillhandahållna [exempel värdena](#values).
  * Om du redan har ett virtuellt nätverk, se till att välja det på fliken nätverk när du skapar din virtuella dator.
  * Om du inte redan har ett virtuellt nätverk kan du skapa ett på samma tidpunkt som du skapar den virtuella datorn.
  * Du behöver inte ha en offentlig IP-adress för den här virtuella datorn för att kunna ansluta via Azure skydds.

* Nödvändiga VM-roller:
  * Rollen läsare på den virtuella datorn.
  * Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress.
  
* Obligatoriska VM-portar:
  * Inkommande portar: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Exempelvärden

Du kan använda följande exempel värden när du skapar den här konfigurationen, eller så kan du ersätta din egen.

**Grundläggande VNet-och VM-värden:**

|**Namn** | **Värde** |
| --- | --- |
| Virtuell dator| TestVM |
| Resursgrupp | TestRG1 |
| Region | East US |
| Virtuellt nätverk | VNet1 |
| Adressutrymme | 10.1.0.0/16 |
| Undernät | Klient del: 10.1.0.0/24 |

**Azure skydds-värden:**

|**Namn** | **Värde** |
| --- | --- |
| Name | VNet1 – skydds |
| + Under näts namn | AzureBastionSubnet |
| AzureBastionSubnet-adresser | Ett undernät inom ditt VNet-adressutrymme med en/27-nätmask. Till exempel 10.1.1.0/27.  |
| Offentlig IP-adress |  Skapa ny |
| Namn på offentlig IP-adress | VNet1-IP  |
| SKU för offentlig IP-adress |  Standard  |
| Tilldelning  | Statisk |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Skapa en skydds-värd

Det finns flera olika sätt att konfigurera en skydds-värd. I följande steg ska du skapa en skydds-värd i Azure Portal direkt från din virtuella dator. När du skapar en värd från en virtuell dator fylls olika inställningar automatiskt i som motsvarar den virtuella datorn och/eller det virtuella nätverket.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till den virtuella dator som du vill ansluta till och välj sedan **Anslut**.

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="Skärm bild av inställningar för virtuella datorer." lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. Välj **skydds** i list rutan.

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="Skärm bild av list rutan skydds." lightbox="./media/quickstart-host-portal/bastion.png":::
1. På **testvm | Sidan Anslut** väljer du **Använd skydds**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Skärm bild av Använd skydds.":::

1. Konfigurera värdena på sidan **Anslut med Azure-skydds** .

   * **Steg 1:** Värdena fylls i i förväg eftersom du skapar skydds-värden direkt från den virtuella datorn.

   * **Steg 2:** Adress utrymmet är i förväg ifyllt med ett föreslaget adress utrymme. AzureBastionSubnet måste ha ett adress utrymme på/27 eller större (/26,/25 osv.).

   :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Skärm bild av skapa skydds-undernätet.":::

1. Skapa AzureBastionSubnet genom att klicka på **skapa undernät** .
1. När under nätet har skapats flyttar sidan automatiskt till **steg 3**. I steg 3 använder du följande värden:

   * **Namn:** Namnge skydds-värden.
   * **Offentlig IP-adress:** välj **Skapa ny**.
   * **Namn på offentlig IP-adress:** Namnet på den offentliga IP-adressresursen.
   * **SKU för offentlig IP-adress:** Förkonfigurerad som **standard**
   * **Tilldelning:** Förkonfigurerad på **statisk**. Du kan inte använda en dynamisk tilldelning för Azure-skydds.
   * **Resurs grupp**: samma resurs grupp som den virtuella datorn.

   :::image type="content" source="./media/quickstart-host-portal/create-bastion.png" alt-text="Skärm bild av steg 3.":::
1. När du har fyllt i värdena väljer du **Skapa Azure-skydds med standardinställningar**. Azure verifierar inställningarna och skapar sedan värden. Värden och dess resurser tar ungefär 5 minuter att skapa och distribuera.

## <a name="connect"></a><a name="connect"></a>Gå

När skydds har distribuerats till det virtuella nätverket ändras skärmen till sidan Anslut.

1. Ange användar namn och lösen ord för den virtuella datorn. Välj sedan **Anslut**.

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="Skärm bild som visar dialog rutan Anslut med Azure skydds.":::
1. RDP-anslutningen till den virtuella datorn öppnas direkt i Azure Portal (via HTML5) med port 443 och skydds-tjänsten.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="RDP-anslutning":::

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resurs gruppen och alla resurser den innehåller:

1. Ange namnet på din resurs grupp i rutan **Sök** högst upp i portalen och välj den från Sök resultaten.

1. Välj **Ta bort resursgrupp**.

1. Ange resurs gruppen för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en skydds-värd för ditt virtuella nätverk och anslöt sedan till en virtuell dator på ett säkert sätt via skydds. Sedan kan du fortsätta med följande steg om du vill ansluta till en skalnings uppsättning för virtuella datorer.

> [!div class="nextstepaction"]
> [Ansluta till en skalnings uppsättning för virtuella datorer med hjälp av Azure skydds](bastion-connect-vm-scale-set.md)
