---
title: Aktivera webb läsar anslutning på Azure DevTest Labs virtuella datorer
description: DevTest Labs integreras nu med Azure skydds som en ägare till labbet som du kan använda för att få åtkomst till alla virtuella labb datorer via en webbläsare.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6d9d631c79c22f1f713cfc4ee7cdd766a4ad8f06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341180"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>Aktivera webb läsar anslutning på Azure DevTest Labs virtuella datorer 
DevTest Labs integreras med [Azure skydds](../bastion/index.yml), vilket gör att du kan ansluta till dina virtuella datorer via en webbläsare. Du måste först aktivera webb läsar anslutning på virtuella labb datorer.

Som ägare till ett labb kan du aktivera åtkomst till alla virtuella labb datorer via en webbläsare. Du behöver inte ytterligare en klient, en agent eller en program varu enhet. Azure skydds ger säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure Portal via TLS. När du ansluter via Azure skydds behöver inte de virtuella datorerna någon offentlig IP-adress. Mer information finns i [Vad är Azure skydds?](../bastion/bastion-overview.md)


Den här artikeln visar hur du aktiverar webb läsar anslutning på virtuella labb datorer.

## <a name="prerequisites"></a>Förutsättningar 
- Distribuera en skydds-värd i det befintliga Labbets virtuella nätverk **(eller)** Anslut ditt labb med ett skydds konfigurerat virtuellt nätverk.
Information om hur du distribuerar en skydds-värd i ett virtuellt nätverk finns i  [skapa en Azure skydds-värd](../bastion/tutorial-create-host-portal.md). När du skapar skydds-värden väljer du Labbets virtuella nätverk. 
- Labb användaren måste ha en **läsar** roll på skydds-värden och det virtuella nätverk som har skydds konfigurerat. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Skapa ett andra under nät i det virtuella skydds-nätverket
Först måste du skapa ett andra undernät i det virtuella skydds-nätverket eftersom AzureBastionSubnet inte tillåter att icke-skydds resurser skapas. Skapa ett annat undernät i det virtuella skydds-nätverket som det visas i följande bild:

![Andra undernät i Azure skydds Virtual Network](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Aktivera skapande av virtuell dator i under nätet
Nu ska du aktivera skapandet av virtuella datorer i det här under nätet genom att följa dessa steg: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **alla tjänster** i den vänstra navigerings menyn. 
1. Välj **DevTest Labs** från listan. 
1. I listan med labb väljer *du ditt labb*. 

    > [!NOTE]
    > Azure skydds är nu allmänt tillgängligt i följande regioner: västra USA, östra USA, Västeuropa, södra centrala USA, östra Australien och Östra Japan. Skapa ett labb i något av de här regionerna om ditt labb inte finns i något av dem. 
    
1. Välj **konfiguration och principer** i avsnittet **Inställningar** på den vänstra menyn. 
1. Välj **virtuella nätverk**.
1. Välj **Lägg till** i verktygsfältet. 
1. Välj det **virtuella nätverk** som har skydds-värden distribuerad. 
1. Välj undernät för virtuella datorer, inte **AzureBastionSubnet**, det andra som du skapade tidigare. Stäng sidan och öppna den igen om du inte ser under nätet i listan längst ned. 

    ![Aktivera skapande av virtuell dator i under nätet](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Välj alternativet **Använd vid skapande av virtuell dator** . 
1. Välj **Spara** i verktygsfältet. 
1. Om du har ett gammalt virtuellt nätverk för labbet tar du bort det genom att välja **...*  och **ta bort**. 

## <a name="enable-browser-connection"></a>Aktivera webb läsar anslutning 

När du har ett skydds konfigurerat virtuellt nätverk i labbet, som labb ägare, kan du aktivera webbläsaren Anslut på virtuella labb datorer.

Följ dessa steg om du vill aktivera webbläsaren Connect på virtuella labb datorer:

1. I Azure Portal navigerar du till *labbet*.
1. Välj **konfiguration och principer**.
1. I **Inställningar** väljer du **webbläsare Connect**. Om du inte ser det här alternativet stänger du sidan **konfigurations principer** och öppnar den igen. 

    ![Aktivera webb läsar anslutning](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Nästa steg
I följande artikel finns information om hur du ansluter till dina virtuella datorer med hjälp av en webbläsare: [Anslut till dina virtuella datorer via en webbläsare](connect-virtual-machine-through-browser.md)