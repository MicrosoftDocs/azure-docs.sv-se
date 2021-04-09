---
title: Nätverks isolering i Azure DevTest Labs
description: Lär dig om nätverks isolering i Azure DevTest Labs.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: 28bfa048a8a6666deb58a8ecfff38779c91d95b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96352940"
---
# <a name="network-isolation-in-devtest-labs"></a>Nätverks isolering i DevTest Labs

Ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md) fungerar som en säkerhets gränser som isolerar dina Azure-resurser från det offentliga Internet. Du kan också ansluta ett virtuellt Azure-nätverk till ditt lokala nätverk för att kunna ansluta säkert till dina lokal-resurser. I DevTest Labs kan du välja att [isolera alla labb virtuella datorer](devtest-lab-configure-vnet.md) och [miljöer till nätverket](connect-environment-lab-virtual-network.md) så att Lab-resurserna följer organisationens nätverks principer. 

Som labb ägare kan du också välja att helt isolera labbet, vilket innebär tillsammans med att isolera virtuella datorer och miljöer till det valda nätverket. du kan också isolera labb lagrings konto och nyckel valv som skapats i din prenumeration. Den här artikeln vägleder dig genom att skapa en isolerad test miljö. 

Läs även följande artiklar:

- [Hur DevTest Labs använder labb lagrings konto](encrypt-storage.md)
- [Hur DevTest Labs använder nyckel valv](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> Nätverks isolering stöds för närvarande endast för nya labb skapande.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Steg för att aktivera nätverks isolering under labb skapande

1. När du skapar labbet går du till fliken **nätverk** .
1. Du kan antingen välja ett **standard** nätverk som labbet ska skapa åt dig eller välja ett befintligt nätverk i list rutan. Du kan bara välja nätverk i samma region och prenumeration som i labbet. 

    > [!div class="mx-imgBorder"]
    > ![Skapa labb](./media/network-isolation/create-lab.png)
1. Välj ett undernät.

    > [!div class="mx-imgBorder"]
    > ![Skapa ett undernät](./media/network-isolation/create-lab-subnet.png)
1. Om du väljer att isolera labb resurser (labb lagrings konto och nyckel valv) till standard nätverket krävs ingen ytterligare åtgärd och labbet kommer att hantera isolerade resurser som går framåt.
 
    > [!div class="mx-imgBorder"]
    > ![Nätverksisolering](./media/network-isolation/isolate-lab-resources.png)
1. Om du väljer att isolera labb resurser (labb lagrings konto och nyckel valv) till ett befintligt nätverk som du har valt, måste du utföra följande steg för att se hur labbet fortsätter att fungera i isolerat läge. 
 
    > [!div class="mx-imgBorder"]
    > ![Isolerad omkälla](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > Labb ägaren måste utföra de här stegen innan du konfigurerar och/eller skapar några resurser i labbet.

### <a name="steps-to-follow-post-lab-creation"></a>Steg för att följa steget efter att skapa labb

1. På Start sidan för labbet väljer du **resurs gruppen** på sidan **Översikt** . Du bör se sidan **resurs grupp** för resurs gruppen som innehåller labbet. 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso-labb](./media/network-isolation/contoso-lab.png)
1. Välj Azure Storage-kontot för labbet. Namn konventionen för labb lagrings kontot är: ett<*labNameWithoutInvalidCharacters* > *<4-siffriga*>. Om Lab-namnet exempelvis är contosolab, kan namnet på lagrings kontot vara acontosolab1234.
 
   > [!div class="mx-imgBorder"]
   > ![Contoso-test](./media/network-isolation/contoso-test.png)
1. På lagrings kontot går du till brand väggar och virtuella nätverk och kontrollerar att kryss rutan Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot är markerad. Eftersom [DevTest Labs är en betrodd Microsoft-tjänst](../storage/common/storage-network-security.md#trusted-microsoft-services)gör det här alternativet att labbet fungerar normalt i ett isolerat nätverk. 

   > [!div class="mx-imgBorder"]
   > ![Contoso Lab-brandväggar](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Klicka sedan på **+ Lägg till befintligt virtuellt nätverk**, Välj det virtuella nätverk och undernät som du valde när du skapade labbet och klicka på **Aktivera**. 

   > [!div class="mx-imgBorder"]
   > ![Contoso mitt VNet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  När tjänstens slut punkt har Aktiver ATS för det valda virtuella nätverket klickar du på **Lägg till**. 

   > [!div class="mx-imgBorder"]
   > ![Lägg till](./media/network-isolation/contoso-firewall-add.png)
 
Med det här alternativet tillåter Azure Storage inkommande anslutningar från det tillagda virtuella nätverket och gör att labbet kan köras i ett isolerat läge i nätverket. 

Du kan också välja att automatisera de här stegen för att konfigurera den här inställningen för flera labb. 

[Läs mer om hur du hanterar standard åtkomst regler för nätverk för Azure Storage med PowerShell och CLI](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Saker att komma ihåg när du använder ett labb i ett isolerat nätverk

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Komma åt Labbets lagrings konto utanför labbet 

I ett isolerat nätverk, för åtgärder som att ladda upp en virtuell hård disk till Labbets lagrings konto för att skapa anpassade avbildningar, behöver Lab-ägare uttryckligen aktivera åtkomst till lagrings kontot från en tillåten slut punkt. Du kan göra detta genom att skapa en virtuell dator och på ett säkert sätt komma åt Labbets lagrings konto från den virtuella datorn. 

[Lär dig mer om att komma åt ett lagrings konto privat från en virtuell dator](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Exportera användnings data från labbet 

I ett isolerat nätverk för att [Exportera personliga användnings data för labbet](personal-data-delete-export.md)måste labb ägaren uttryckligen tillhandahålla lagrings konto och generera en BLOB i kontot för att lagra data. 

Om ett lagrings konto inte anges kommer den här åtgärden inte att fungera i nätverket isolerat läge eftersom Labbets lagrings konto inte är tillgängligt för labbet att använda det om inget lagrings konto har angetts av kunden. 

[Läs mer om att exportera användnings data för labbet i ett angivet lagrings konto](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Nästa steg

[Skapa eller ändra labb automatiskt med hjälp av Azure Resource Manager mallar och PowerShell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)