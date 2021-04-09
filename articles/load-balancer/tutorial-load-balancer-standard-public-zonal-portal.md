---
title: 'Självstudie: Load Balancer virtuella datorer inom en zon – Azure Portal'
titleSuffix: Azure Load Balancer
description: Den här kursen visar hur du skapar en Standard Load Balancer med zonindelad klientdel för att lastbalansera virtuella datorer i en tillgänglighetszon med hjälp av Azure Portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: f91c9c0f401a455543b12af81eed48bd1a3349bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696446"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Självstudiekurs: Lastbalansera virtuella datorer i en tillgänglighetszon med Standard Load Balancer med hjälp av Azure Portal

I den här kursen skapar du en offentlig [Azure Standard Load Balancer-instans](https://aka.ms/azureloadbalancerstandard) med en zonindelad klientdel som använder en offentlig IP-standardadress med Azure Portal. I det här scenariot anger du en viss zon för dina klientdels- och serverdelsinstanser för att justera din datasökväg och dina resurser med en viss zon. Du lär dig hur du använder följande funktioner:

> [!div class="checklist"]
> * Skapa en Standard Load Balancer med zonindelad klientdel.
> * Skapa nätverkssäkerhetsgrupper för att definiera regler för inkommande trafik.
> * Skapa zonindelade virtuella datorer och anslut dem till en lastbalanserare.
> * Skapa en hälsoavsökning för lastbalanserare.
> * Skapa trafikregler för lastbalanserare.
> * Skapa en enkel webbplats för Internet Information Services (IIS).
> * Visa en lastbalanserare i praktiken.

Mer information om hur du använder tillgänglighets zoner med Standard Load Balancer finns [standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md).

Om du vill kan du använda [Azure CLI](./quickstart-load-balancer-standard-public-cli.md) till att slutföra den här kursen.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-public-standard-load-balancer-instance"></a>Skapa en offentlig Standard Load Balancer-instans

Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en ny offentlig IP-adress medan du skapar lastbalanseraren konfigureras den automatiskt som en standard-SKU-version. Den är också automatiskt zonredundant.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och skriv *MyResourceGroupZLB* i textrutan.|
    | Name                   | *myLoadBalancer*                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **standard**.                          |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   |
    |Tillgänglighetszon| Välj **1**.    |
3. På fliken **Granska + skapa** klickar du på **Skapa**.   

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du ett virtuellt nätverk. Du kan också skapa två virtuella datorer i samma zon (dvs zon 1) för den region som ska läggas till i lastbalanserarens serverdelspool. Sedan installerar du IIS på de virtuella datorerna så att du enklare kan testa den zonredundanta lastbalanseraren. Om en virtuell dator misslyckas så misslyckas även hälsoavsökningen för den virtuella datorn i samma zon. Trafiken fortsätter att hanteras av andra virtuella datorer i samma zon.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupZLB (Välj en befintlig resurs grupp) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa, västra      |
| **\<IPv4-address-space>**   | 10.0.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.0.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Välj **Skapa en resurs** längst upp till vänster på skärmen. Ange **Nätverkssäkerhetsgrupp** i sökrutan. Välj **Skapa** på nätverkssäkerhetsgruppssidan.
2. Ange följande värden på sidan **skapa nätverks säkerhets grupp** :
   - **myNetworkSecurityGroup** för nätverkssäkerhetsgruppens namn.
   - **myResourceGroupLB** för den befintliga resursgruppens namn.
   
     ![Skapa en nätverkssäkerhetsgrupp](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Skapa nätverkssäkerhetsgruppsregler

I det här avsnittet skapar du nätverkssäkerhetsgruppsregler som tillåter att inkommande anslutningar som använder HTTP och Microsoft Remote Desktop Protocol (RDP) med Azure Portal.

1. Välj **Alla resurser** på menyn längst till vänster i Azure Portal. Sök sedan efter och välj **myNetworkSecurityGroup**. Det finns i resursgruppen **myResourceGroupZLB**.
2. Under **Inställningar** väljer du **inkommande säkerhets regler**. Välj **Lägg till**.
3. Ange följande värden för den inkommande säkerhetsregeln med namnet **myHTTPRule** så att den tillåter inkommande HTTP-anslutningar som använder port 80:
    - **Tjänstetagg** för **Källa**.
    - **Internet** för **Källtjänsttagg**.
    - **80** för **Målportsintervall**.
    - **TCP** för **Protokoll**.
    - **Tillåt** för **Åtgärd**.
    - **100**, som **prioritet**.
    - **myHTTPRule**, som **namn**.
    - **Tillåt HTTP** för **Beskrivning**.
4. Välj **OK**.
 
   ![Skapa nätverkssäkerhetsgruppsregler](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Upprepa steg 2 till 4 om du vill skapa en annan regel med namnet **myRDPRule**. Den här regeln tillåter en inkommande RDP-anslutning som använder port 3389 med följande värden:
    - **Tjänstetagg** för **Källa**.
    - **Internet** för **Källtjänsttagg**.
    - **3389** för **Målportsintervall**.
    - **TCP** för **Protokoll**.
    - **Tillåt** för **Åtgärd**.
    - **200**, som **prioritet**.
    - **myRDPRule**, som **namn**.
    - **Tillåt HTTP** för **Beskrivning**.

      ![Skapa en RDP-regel](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **Compute**  >  **Windows Server 2016 Data Center**. Ange följande värden för den virtuella datorn:
    - **myVM1** för den virtuella datorns namn.        
    - **azureuser** för administratörens användarnamn.    
    - **myResourceGroupLB** för **Resursgrupp**. Välj **Använd befintlig** och sedan **myResourceGroupZLB**.
2. Välj **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn. Välj **Välj**.
4. Ange dessa värden för VM-inställningarna:
    - **zon 1** för den tillgänglighetszon där du placerar den virtuella datorn.
    -  **myVNet**. Se till att detta har markerats som det virtuella nätverket.
    - **myVM1PIP** för den offentliga standard-IP-adress du skapar. Välj **Skapa ny**. Välj **myVM1PIP** som namntyp. Välj **1** som **zon**. IP-adressens SKU är standard.
    - **myBackendSubnet**. Se till att den har markerats som undernät.
    - **myNetworkSecurityGroup** för namnet på den nätverkssäkerhetsgrupp (brandvägg) som redan finns.
5. Inaktivera startdiagnostikinställningar genom att välja **Inaktiverad**.
6. Välj **OK**. Granska inställningarna på sammanfattningssidan. Välj sedan **Skapa**.
7. Upprepa steg 1 till 6 om du vill skapa en andra virtuell dator med namnet **myVM2** i zon 1. Gör **myVnet** till det virtuella nätverket. Gör **myVM2PIP** till den offentliga IP-standardadressen. Gör **myBackendSubnet** till undernät. Och gör **myNetworkSecurityGroup** till nätverkssäkerhetsgrupp.

    ![Skapa virtuella datorer](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Installera IIS på virtuella datorer

1. Välj **Alla resurser** på menyn längst till vänster. Välj sedan **myVM1** i resurslistan. Det finns i resursgruppen **myResourceGroupZLB**.
2. Välj **Anslut** på sidan **Översikt** och anslut till den virtuella datorn med RDP.
3. Logga in till den virtuella datorn och ange det användarnamn och lösenord du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** om du vill ange autentiseringsuppgifterna som du angav när du skapade den virtuella datorn. Välj **Använd ett annat konto**. Och välj sedan **OK**. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen.
4. Gå till **Windows administrations verktyg**  >  **Windows PowerShell** på server Skriv bordet.
6. Installera IIS-servern genom att köra följande kommandon i **PowerShell**-fönstret. De här kommandona tar även bort standardfilen iisstart.htm och lägger till en ny iisstart.htm-fil som visar den virtuella datorn namn:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Stäng RDP-sessionen med **myVM1**.
8. Upprepa steg 1 till 7 för att installera IIS på **myVM2**.

## <a name="create-load-balancer-resources"></a>Skapa resurser för lastbalansering

I det här avsnittet konfigurerar du inställningar för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning. Du kan också ange lastbalanserare och nätverk adress translation regler för inläsning.


### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till lastbalanseraren. Skapa serverdelsadresspoolen **myBackendPool** så att den omfattar **VM1** och **VM2**.

1. Välj **Alla resurser** på menyn längst till vänster. Välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar** väljer du **backend-pooler**. Välj **Lägg till**.
3. Gör följande på sidan **Lägg till en serverdelspool**:
    - Ange **myBackEndPool** som serverdelspoolens namn.
    - Välj **myVNet** som **Virtuellt nätverk** i den nedrullningsbara menyn. 
    - För **virtuell dator** och **IP-adress** lägger du till **myVM1** och **myVM2** och deras motsvarande offentliga IP-adresser.
4. Välj **Lägg till**.
5. Kontrollera att inställningen för serverdelspoolens lastbalanserare visar båda de virtuella datorerna: **myVM1** och **myVM2**.
 
    ![Skapa en serverdelspool](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Använd hälsoavsökningen så att lastbalanseraren kan övervaka din apps status. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning **myHealthProbe** så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **Alla resurser** på menyn längst till vänster. Välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar** väljer du **Hälsoavsökningar**. Välj **Lägg till**.
3. Använd följande värden när du skapar hälsoavsökningen:
    - **myHealthProbe** för hälsoavsökningens namn.
    - **HTTP** för protokolltypen.
    - **80** för portnumret.
    - **15** för antalet **intervall** i sekunder mellan avsökningsförsöken.
    - **2** för antalet **tröskelvärden för ohälsosamt värde** eller antalet avsökningsfel i följd som måste inträffa innan en virtuell dator anses vara felaktig.
4. Välj **OK**.

   ![Lägga till en hälsoavsökning](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel definierar hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa en lastbalanseringsregel med namnet **myLoadBalancerRuleWeb** som ska avlyssna port 80 i klientdelen **FrontendLoadBalancer**. Regeln skickar belastningsutjämnad nätverkstrafik till serverdelsadresspoolen **myBackEndPool**, också det med port 80. 

1. Välj **Alla resurser** på menyn längst till vänster. Välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar** väljer du **Belastningsutjämningsregler**. Välj **Lägg till**.
3. Använd följande värden när du konfigurerar belastningsutjämningsregeln:
    - **myHTTPRule** för belastningsutjämningsregelns namn.
    - **TCP** för protokolltypen.
    - **80** för portnumret.
    - **80** för serverdelsporten.
    - **myBackendPool** för serverdelspoolens namn.
    - **myHealthProbe** för hälsoavsökningens namn.
4. Välj **OK**.
    
    ![Lägga till en belastningsutjämningsregel](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren
1. Hitta den offentliga IP-adressen för belastningsutjämnaren på **översikts** skärmen. Välj **Alla resurser**. Välj sedan **myPublicIP**. 

2. Kopiera den offentliga IP-adressen. Klistra sedan in den i webbläsarens adressfält. Sidan som innehåller namnet på webbserversidan visas i webbläsaren.

      ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Om du vill se lastbalanseraren i arbete kan du tvinga fram ett stopp av den virtuella dator som visas. Uppdatera webbläsaren om du vill se det andra servernamn som visas i webbläsaren.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. Markera den resursgrupp som innehåller lastbalanseraren. Välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig att belastningsutjämna virtuella datorer i tillgänglighets zoner.
> [!div class="nextstepaction"]
> [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](tutorial-load-balancer-standard-public-zone-redundant-portal.md)