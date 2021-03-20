---
title: 'Självstudie: belastningsutjämna virtuella datorer i tillgänglighets zoner – Azure Portal'
titleSuffix: Azure Load Balancer
description: Den här kursen visar hur du skapar en Standard Load Balancer med zonredundant klientdel för att lastbalansera virtuella datorer i flera tillgänglighetszoner med hjälp av Azure Portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 4e07285eca0fd10b73b386fcf139cdad5b94ddc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696412"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Självstudiekurs: Lastbalansera virtuella datorer i flera tillgänglighetszoner med Standard Load Balancer med hjälp av Azure Portal

Med belastningsutjämning får du högre tillgänglighet genom att inkommande begäranden sprids över flera virtuella datorer. Den här kursen går stegvis igenom hur du skapar en offentlig Standard Load Balancer som lastbalanserar virtuella datorer i flera tillgänglighetszoner. Det här hjälper till att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter. Med zonredundans kan en eller flera tillgänglighetszoner misslyckas och datasökvägen överleva så länge en zon i regionen har god status. Lär dig att:

> [!div class="checklist"]
> * Skapa en Standard Load Balancer
> * skapa nätverkssäkerhetsgrupper för att definiera regler för inkommande trafik
> * Skapa zonredundanta virtuella datorer över flera zoner och ansluta till en lastbalanserare
> * skapa hälsoavsökning för lastbalanseraren
> * skapa trafikregler för lastbalanseraren
> * skapa en grundläggande IIS-webbplats
> * visa en lastbalanserare i praktiken

Mer information om hur du använder tillgänglighetszoner med standardlastbalanserare finns i [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).

Om du vill kan du slutföra den här självstudien med [Azure CLI](./quickstart-load-balancer-standard-public-cli.md).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Skapa en Standard Load Balancer

En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en ny offentlig IP-adress medan du skapar lastbalanseraren konfigureras den automatiskt som en standard-SKU-version och blir också automatiskt zonredundant.

1. Klicka på **skapa en resurs**  >  **nätverk**  >  **Load Balancer** på den övre vänstra sidan av skärmen.
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och skriv *MyResourceGroupLBAZ* i textrutan.|
    | Name                   | *myLoadBalancer*                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **standard**.                          |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   |
    |Tillgänglighetszon| Välj **Zonredundant**.    |
   

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du ett virtuellt nätverk, virtuella datorer i olika zoner för regionen och installerar sedan IIS på de virtuella datorerna för att testa den zonredundanta lastbalanseraren. Om en zon misslyckas så misslyckas därför hälsoavsökningen för virtuell dator i samma zon och trafiken fortsätter att fungera med virtuella datorer i andra zoner.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLBAZ (Välj en befintlig resurs grupp) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa, västra      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp så att du kan definiera inkommande anslutningar till det virtuella nätverket.

1. Högst upp till vänster på skärmen klickar du på **Skapa en resurs**, i sökrutan skriver du *Nätverkssäkerhetsgrupp*, och på nätverkssäkerhetsgruppens sidan klickar du på **Skapa**.
2. Ange dessa värden på sidan Skapa nätverkssäkerhetsgrupp:
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen.
    - *myResourceGroupLB* – för namnet på den befintliga resursgruppen.
   
![Skärm bild som visar fönstret Skapa nätverks säkerhets grupp.](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Skapa regler för nätverkssäkerhetsgrupp

I det här avsnittet skapar du nätverkssäkerhetsgruppsregler som tillåter att inkommande anslutningar använder HTTP och RDP med Azure Portal.

1. I Azure Portal klickar du på **Alla resurser** i den vänstra menyn. Sök sedan och klicka på **myNetworkSecurityGroup** som finns i resursgruppen **myResourceGroupLBAZ**.
2. Klicka på **Ingående säkerhetsregler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Ange dessa värden för den ingående säkerhetsregeln *myHTTPRule* så att inkommande HTTP-anslutningar som använder port 80 tillåts:
    - *Tjänstetagg* – för **Källa**.
    - *Internet* – för **Källtjänsttagg**
    - *80* – för **målportsintervall**
    - *TCP* – för **Protokoll**
    - *Tillåt* – för **Åtgärd**
    - *100* för **Prioritet**
    - *myHTTPRule* för lastbalanseringsregelns namn.
    - *Tillåt HTTP* – för beskrivning av lastbalanseringsregeln.
4. Klicka på **OK**.
 
   ![Skärm bild som visar fönstret Lägg till inkommande säkerhets regel.](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Skapa, genom att upprepa steg 2 till 4, en annan regel med namnet *myRDPRule* som tillåter att en inkommande RDP-anslutning använder port 3389 med följande värden:
    - *Tjänstetagg* – för **Källa**.
    - *Internet* – för **Källtjänsttagg**
    - *3389* – för **målportsintervall**
    - *TCP* – för **Protokoll**
    - *Tillåt* – för **Åtgärd**
    - *200* för **Prioritet**
    - *myRDPRule* för namn
    - *Tillåt HTTP* – för beskrivning

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa virtuella datorer i olika zoner (zon 1, zon 2 och zon 3) för den region som kan fungera som serverdelsservrar för lastbalanseraren.

1. Klicka på **skapa en resurs**  >  **Compute**  >  **Windows Server 2016 Data Center** på den övre vänstra sidan av skärmen och ange följande värden för den virtuella datorn:
    - *myVM1* – för den virtuella datorns namn.        
    - *azureuser* – för administratörens användarnamn.    
    - *myResourceGroupLBAZ* – för **Resursgrupp** väljer du **Använd befintlig**, och väljer sedan *myResourceGroupLBAZ*.
2. Klicka på **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
    - *zon 1* – för den zon där du placerar den virtuella datorn.
    -  *myVNet* – se till att detta har markerats som det virtuella nätverket.
    - *myBackendSubnet* – kontrollera att det har markerats som undernätet.
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen (brandvägg).
5. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
6. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.
7. Skapa en andra virtuell dator med namnet *VM2* i Zon 2 och en tredje virtuell dator i Zon 3, och med *myVnet* som virtuellt nätverk, *myBackendSubnet* som undernät och **myNetworkSecurityGroup* som nätverkssäkerhetsgrupp med steg 1–6.

### <a name="install-iis-on-vms"></a>Installera IIS på virtuella datorer

1. Klicka på **Alla resurser** på den vänstra menyn och klicka sedan i resurslistan på **myVM1** som finns i resursgruppen *myResourceGroupLBAZ*.
2. Klicka på **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. Logga in på den virtuella datorn med användarnamnet *azureuser*.
4. Gå till **Windows administrations verktyg** > **Windows PowerShell** på server Skriv bordet.
5. I PowerShell-fönstret kör du följande kommandon för att installera IIS.servern, ta bort standardfilen iisstart.htm och lägga till en ny iisstart.htm-fil som visar namnet på den virtuella datorn:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. Stäng RDP-sessionen med *myVM1*.
7. Upprepa steg 1 till 6 för att installera IIS och den uppdaterade filen iisstart.htm på *myVM2* och *myVM3*.

## <a name="create-load-balancer-resources"></a>Skapa resurser för lastbalansering

I det här avsnittet ska du konfigurera lastbalanseringsinställningarna för en serverdelsadresspool och en hälsoavsökning, och ange lastbalanserare och NAT-regler.


### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till lastbalanseraren. Skapa serverdelsadresspoolen *myBackendPool* så att den omfattar *VM1*, *VM2* och *VM3*.

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i listan resurser.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Gör följande på sidan **Lägg till en serverdelspool**:
    - Ange *myBackEndPool* som namn på serverdelspoolen.
    - För **Virtuellt nätverk** i den nedrullningsbara menyn klickar du på **myVNet**
    - För **Virtuell dator** klickar du i den nedrullningsbara menyn på **myVM1**.
    - För **IP-adress** klickar du i den nedrullningsbara menyn på IP-adressen för myVM1.
4. Klicka på **Lägg till ny serverdelsresurs** för att lägga till varje virtuella dator (*myVM2* och *myVM3*) för att lägga till i serverdelspoolen för lastbalanseraren.
5. Klicka på **Lägg till**.

    ![Lägga till i backend-adresspoolen (serverdelspoolen)](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Kontrollera att inställningen för lastbalanserarens serverdelspool visar alla tre virtuella datorer – **myVM1**, **myVM2** och **myVM3**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att lastbalanseraren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i listan resurser.
2. Klicka på **Hälsoavsökningar** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd följande värden när du skapar hälsoavsökningen:
    - *myHealthProbe* – för hälsoavsökningens namn.
    - **HTTP** – för protokolltypen.
    - *80* – för portnumret.
    - *15* – för antalet **intervall** i sekunder mellan avsökningsförsöken.
    - *2* – för antalet **tröskelvärden för ohälsosamt värde** eller antalet avsökningsfel i följd som måste inträffa innan en virtuell dator anses vara felaktig.
4. Klicka på **OK**.

   ![Lägga till en avsökning](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen för den inkommande trafiken och IP-poolen i serverdelen som ska ta emot trafiken, samt nödvändiga käll- och målportar. Skapa lastbalanseringsregeln *myLoadBalancerRuleWeb* så att du kan lyssna på port 80 i klientdelen *FrontendLoadBalancer* och skicka lastbalanserad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80. 

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i listan resurser.
2. Klicka på **Belastningsutjämningsregler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd följande värden när du konfigurerar belastningsutjämningsregeln:
    - *myHTTPRule* – för belastningsutjämningsregelns namn.
    - **TCP** – för protokolltypen.
    - *80* – för portnumret.
    - *80* – för serverdelsporten.
    - *myBackendPool* – för serverdelspoolens namn.
    - *myHealthProbe* – för hälsoavsökningens namn.
4. Klicka på **OK**.
    
    
    ![Lägga till en belastningsutjämningsregel](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Klicka på **Alla resurser** och sedan på **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

      ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se lastbalanseraren fördela trafik på de virtuella datorer som är distribuerade i zonen kan du tvångsuppdatera webbläsaren.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. Det gör du genom att markera den resurs grupp som innehåller belastningsutjämnaren och välja **ta bort**.

## <a name="next-steps"></a>Nästa steg

Läs mer om belastnings utjämning för en virtuell dator i en bestämd tillgänglighets zon..
> [!div class="nextstepaction"]
> [Belastningsutjämna virtuella datorer i en tillgänglighets zon](tutorial-load-balancer-standard-public-zonal-portal.md)