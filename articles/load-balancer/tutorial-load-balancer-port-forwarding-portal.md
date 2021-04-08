---
title: 'Självstudie: Konfigurera port vidarebefordring – Azure Portal'
titleSuffix: Azure Load Balancer
description: Den här kursen visar hur du konfigurerar vidarebefordrade portar med Azure Load Balancer för att skapa en anslutning till virtuella datorer i Azure-nätverk.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 63d1a08dc588f0303ccb1ae13bd4c28af2a393c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92043661"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Självstudie: Konfigurera vidarebefordran av portar i Azure Load Balancer med hjälp av portalen

Med portvidarebefordran kan du ansluta till virtuella datorer (VM) i Azure-nätverk med hjälp av en offentliga IP-adress och ett offentligt port nummer för Azure Load Balancer. 

I den här självstudien får du ställa in portvidarebefordran i Azure Load Balancer. Lär dig att:

> [!div class="checklist"]
> * Skapa en offentlig Standard-lastbalanserare för att balansera nätverkstrafik över virtuella datorer. 
> * Skapa ett virtuellt nätverk och virtuella datorer med en nätverkssäkerhetsgruppregel. 
> * Lägg till de virtuella datorerna i lastbalanserarens adresspool för serverdelen.
> * Skapa en hälsoavsökning och trafikregler för lastbalanseraren.
> * Skapa inkommande NAT-portvidarebefordringsregler för en lastbalanserare.
> * Installera och konfigurera IIS på de virtuella datorerna för att visa belastningsutjämning och portvidarebefordran i praktiken.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

För alla steg i den här självstudien loggar du in på Azure Portal på [https://portal.azure.com](https://portal.azure.com) .

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration.

## <a name="create-a-standard-load-balancer"></a>Skapa en standardlastbalanserare

Börja med att skapa en offentlig standardlastbalanserare som kan balansera trafik över virtuella datorer. En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en Standard Load Balancer, och även måste skapa en ny offentlig IP-standardadress som är konfigurerad som klientdelen med namnet **LoadBalancerFrontend** som standard. 

1. Klicka på **skapa en resurs**  >  **nätverk**  >  **Load Balancer** på den övre vänstra sidan av skärmen.
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och skriv *MyResourceGroupLB* i textrutan.|
    | Name                   | *myLoadBalancer*                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **standard**.                          |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   |
    |Tillgänglighetszon| Välj **Zonredundant**.    |
     
    >[!NOTE]
     >Se till att skapa en Load Balancer och alla resurser för den på en plats som har stöd för tillgänglighetszoner. Mer information finns i [regioner som har stöd för tillgänglighetszoner](../availability-zones/az-region.md). 

3. På fliken **Granska + skapa** klickar du på **Skapa**.  
  
## <a name="create-and-configure-back-end-servers"></a>Skapa och konfigurera serverdelsservrar

Skapa ett virtuellt nätverk med två virtuella datorer för serverdelspoolen för din lastbalanserare. 

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB (Välj en befintlig resurs grupp) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa, västra      |
| **\<IPv4-address-space>**   | 10.3.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Skapa virtuella datorer och lägg till dem i lastbalanseraren för serverdelsadresspoolen

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **Compute**  >  **Windows Server 2016 Data Center**. 
   
1. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Prenumeration**  >  **Resurs grupp**: list rutan och välj **MyResourceGroupLB**.
   - **Namn på virtuell dator**: Skriv *MyVM1*.
   - **Region**: Välj **Europa, västra**. 
   - **Användar namn**: Skriv *azureuser*.
   - **Lösen ord**: Skriv *Azure1234567*. 
     Skriv lösenordet på nytt i fältet **Bekräfta lösenord**.
   
1. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**. 
   
   Kontrollera att följande har valts:
   - **Virtuellt nätverk**: **MyVNet**
   - **Undernät**: **MyBackendSubnet**
   
1. Välj **Skapa ny** och sedan **Standard** för **Offentlig IP-adress** på sidan **Skapa offentlig IP-adress** och välj sedan **OK**. 
   
1. För att skapa en ny nätverkssäkerhetsgrupp (NSG), en typ av brandvägg, går du till **Nätverkssäkerhetsgrupp** och väljer **Avancerat**. 
   1. I fältet **Konfigurera nätverkssäkerhetsgrupp** väljer du **Skapa ny**. 
   1. Skriv *MyNetworkSecurityGroup* och välj **OK**. 
   
   >[!NOTE]
   >Observera att som standard har NSG redan en inkommande regel för att öppna port 3389, fjärrporten för fjärrskrivbordet.
   
1. Lägg till den virtuella datorn till en lastbalanserares serverdelpool som du har skapat:
   
   1. Under **belastnings utjämning**  >  **placerar du den virtuella datorn bakom en befintlig belastnings Utjämnings lösning?** väljer du **Ja**. 
   1. Välj **Azure-lastbalanserare** i listrutan för **lastbalanseringsalternativ**. 
   1. För **Välj lastbalanserare** väljer du **MyLoadBalancer** i listrutan. 
   1. Under **Välj en serverdelspool** väljer du **Skapa nytt** och skriver *MyBackendPool*. Välj **Skapa**. 
   
   ![Skapa ett virtuellt nätverk](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Välj fliken **Hantering** eller **Nästa** > **Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
   
1. Välj **Granska + skapa**.
   
1. Granska inställningarna och när verifieringen är klar väljer du **Skapa**. 

1. Följ stegen för att skapa en andra virtuell dator med namnet *MyVM2*, med alla andra inställningar på samma sätt som MyVM1. 
   
   För **Nätverkssäkerhetsgrupp**: när du har valt **Avancerat** väljer du gruppen **MyNetworkSecurityGroup** som du redan skapat i listrutan. 
   
   Under **Välj en serverdelspool** kontrollerar du att **MyBackendPool** har valts. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Skapa en NSG-regel för de virtuella datorerna

Skapa en nätverkssäkerhetsgruppregel för de virtuella datorerna så att inkommande internetanslutningar med HTTP tillåts.

>[!NOTE]
>Somstandard har NSG redan en inkommande regel för att öppna port 3389, fjärrporten för fjärrskrivbordet.

1. Välj **Alla resurser** på menyn till vänster. I resurslistan väljer du **MyNetworkSecurityGroup** i resursgruppen **MyResourceGroupLB**.
   
1. Under **Inställningar** väljer du **Inkommande säkerhetsregler** och sedan **Lägg till**.
   
1. I dialogrutan **Lägg till ingående säkerhetsregel** skriver eller väljer du följande:
   
   - **Källa**: Välj **Tjänsttagg**.  
   - **Källtjänsttagg**: Välj **Internet**. 
   - **Målportintervall**: Skriv *80*.
   - **Protokoll**: Välj **TCP**. 
   - **Åtgärd**: Välj **Tillåt**.  
   - **Prioritet**: Skriv *100*. 
   - **Namn**: Skriv *MyHTTPRule*. 
   - **Beskrivning**: Skriv *Tillåt HTTP*. 
   
1. Välj **Lägg till**. 
   
   ![Skapa en NSG-regel](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Skapa resurser för lastbalansering

I det här avsnittet inspekterar du serverdelpoolen för lastbalanseraren och konfigurerar trafikregler och en hälsoavsökning för lastbalanseraren.

### <a name="view-the-back-end-address-pool"></a>Visa serverdelsadresspoolen

För att distribuera trafik till de virtuella datorerna använder belastningsutjämnaren en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till lastbalanseraren. 

Du skapade din serverdelspool för lastbalanseraren och lade till virtuella datorer i den när du skapade de virtuella datorerna. Du kan också skapa serverdelsadresspooler och lägga till eller ta bort virtuella datorer från lastbalanseraren på sidan **Serverdelspooler**. 

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **backend-pooler**.
   
1. På sidan **Serverdelspooler** expanderar du **MyBackendPool** och kontrollerar att både **VM1** och **VM2** visas.

1. Välj **MyBackendPool**. 
   
   På sidan **MyBackendPool** under **VIRTUELL DATOR** och **IP-ADRESS** kan du ta bort eller lägga till tillgängliga virtuella datorer i poolen.

DU kan skapa nya serverdelspooler genom att välja **Lägg till** på sidan **Serverdelspooler**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att lastbalanseraren ska övervaka VM-status använder du en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. 

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Hälsoavsökningar** och sedan **Lägg till**.
   
1. På sidan **Lägg till en hälsoavsökning** skriver eller väljer du följande värden:
   
   - **Namn**: Skriv *MyHealthProbe*.
   - **Protokoll**: I listrutan väljer du **HTTP**. 
   - **Port**: Skriv *80*. 
   - **Sökväg**: Acceptera */* för standard-URI. Du kan ersätta det här värdet med valfri URI. 
   - **Intervall**: Skriv *15*. Intervallet är antalet sekunder mellan avsökningsförsök.
   - **Tröskelvärde för Ej felfri**: Skriv *2*. Det här värdet är det antal avsökningsfel i följd som ska inträffa innan en virtuell dator anses vara felaktig.
   
1. Välj **OK**.
   
   ![Lägga till en avsökning](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel definierar hur trafiken ska distribueras till de virtuella datorerna. Regeln definierar IP-konfigurationen på klientdelen för inkommande trafik, serverdels-IP-poolen för att ta emot trafik samt nödvändiga käll- och målportar. 

Lastbalanserarregeln med namnet **MyLoadBalancerRule** avlyssnar port 80 i klientdelen **LoadBalancerFrontEnd**. Regeln skickar nätverkstrafik till serverdelsadresspoolen **MyBackendPool**, även det med port 80. 

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Belastningsutjämningsregler** och väljer sedan **Lägg till**.
   
1. På sidan **Lägg till belastningsutjämningsregel** skriver eller väljer du följande värden:
   
   - **Namn**: Skriv *MyLoadBalancerRule*.
   - **Protokoll**: Välj **TCP**.
   - **Port**: Skriv *80*.
   - **Serverdelsport**: Skriv *80*.
   - **Serverdelspool**: Välj **MyBackendPool**.
   - **Hälsoavsökning**: Välj **MyHealthProbe**. 
   
1. Välj **OK**.
   
   ![Lägga till en regel för lastbalanseraren](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Skapa inkommande NAT-portvidarebefordringsregel

Skapa en inkommande NAT-regel för lastbalanseraren för att vidarebefordra trafik från en viss port på klientdelens IP-adress till en specifik port för en virtuell serverdelsdator.

1. Välj **Alla resurser** i den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Inkommande NAT-regler** och sedan **Lägg till**. 
   
1. På sidan **Lägg till inkommande NAT-regel** skriver eller väljer du följande värden:
   
   - **Namn**: Skriv *MyNATRuleVM1*.
   - **Port**: typ *4221*.
   - **Virtuell mål dator**: Välj **MyVM1** i list rutan.
   - **IP-konfiguration för nätverk**: Välj **ipconfig1** i list rutan.
   - **Port mappning**: Välj **anpassad**.
   - **Målport**: typ *3389*.
   
1. Välj **OK**.
   
1. Upprepa stegen för att lägga till en inkommande NAT-regel med namnet *MyNATRuleVM2*, med hjälp av **porten**: *4222* och den **virtuella mål datorn**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

I det här avsnittet ska du installera Internet Information Services (IIS) på serverdelsservrarna och anpassa standardwebbsidan för att visa namnet på datorn. Du använder den sedan lastbalanserarens offentliga IP-adress för att testa lastbalanseraren. 

Varje virtuella serverdelsdator arbetar med olika versioner av IIS-standardwebbplatsen, så du kan se när lastbalanseraren distribuerar begäranden mellan de två virtuella datorerna.

### <a name="connect-to-the-vms-with-rdp"></a>Ansluta till de virtuella datorerna med RDP

Ansluta till varje virtuell dator till fjärrskrivbord (RDP). 

1. På portalen väljer du **Alla resurser** på menyn till vänster. I resurslistan väljer du varje virtuell dator i resursgruppen **MyResourceGroupLB**.
   
1. På sidan **Översikt** väljer du **Anslut** och sedan **Ladda ned RDP-fil**. 
   
1. Öppna den RDP-fil som du laddade ned och välj **Anslut**.
   
1. På skärmen Windows-säkerhet väljer du **Fler alternativ** och sedan **Använd ett annat konto**. 
   
   Ange användarnamnet *azureuser* och lösenordet *Azure1234567* och välj **OK**.
   
1. Svara **Ja** på eventuella certifikatfrågor. 
   
   Den virtuella datorns skrivbord öppnas i ett nytt fönster. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Installera IIS och ersätta IIS-standardsidan 

Använd PowerShell för att installera IIS och ersätter IIS-standardwebbplatsen med en anpassad sida som visar varje virtuell dators namn.

1. På MyVM1 och MyVM2 startar du **Windows PowerShell** från **Start-menyn**. 

2. Kör följande kommandon för att installera IIS och ersätta IIS-standardwebbplatsen:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Stäng RDP-anslutningarna med MyVM1 och MyVM2 genom att välja **Koppla från**. Stäng inte av de virtuella datorerna.

### <a name="test-load-balancing"></a>Testbelastningsutjämning

1. På portalen går du till sidan **Översikt** för **MyLoadBalancer** och kopierar dess offentliga IP-adress under **Offentlig IP-adress**. Hovra över adressen och kopiera den genom att välja ikonen **Kopiera**. I det här exemplet är den **40.67.218.235**. 
   
1. Klistra in eller skriv lastbalanserarens privata IP-adress (*40.67.218.235*) i webbläsarens adressfält. 
   
   Den anpassade IIS-webbserverns standardsida visas i webbläsaren. I meddelandet står det antingen **Hello World from MyVM1** (Hej världen från MyVM1) eller **Hello World from MyVM2**.
   
   ![Ny IIS-standardsida](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Uppdatera webbläsaren om du vill se hur lastbalanseraren distribuerar trafik över virtuella datorer. Ibland visas sidan **MyVM1**, och vid andra tillfällen visas sidan **MyVM2** när lastbalanseraren distribuerar begäranden till varje virtuell serverdelsdator.
   
   >[!NOTE]
   >Du kan behöva rensa webbläsarens cache eller öppna ett nytt webbläsarfönster mellan försöken.

## <a name="test-port-forwarding"></a>Testportvidarebefordran

Med portvidarebefordran kan du använda fjärrskrivbord med en virtuell serverdelsdator med hjälp av IP-adressen för lastbalanseraren och värdet för klientdelsporten som definieras i NAT-regeln. 

1. På portalen går du till sidan **Översikt** för **MyLoadBalancer** och letar upp dess offentliga IP-adress under offentlig IP-adress. Hovra över adressen och kopiera den genom att välja ikonen **Kopiera**. I det här exemplet är den **40.67.218.235**. 
   
1. Öppna en kommandotolk och använd följande kommando för att skapa en fjärrskrivbordssession med MyVM2 med hjälp av lastbalanserarens offentliga IP-adress och klientdelsporten du definierade i den virtuella datorns NAT-regel. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Öppna den nedladdade RDP-filen och välj **Anslut**.
   
1. På skärmen Windows-säkerhet väljer du **Fler alternativ** och sedan **Använd ett annat konto**. 
   
   Ange användarnamnet *azureuser* och lösenordet *Azure1234567* och välj **OK**.
   
1. Svara **Ja** på eventuella certifikatfrågor. 
   
   Skrivbordet på MyVM2 öppnas i ett nytt fönster. 

RDP-anslutningen lyckas eftersom den inkommande NAT-regeln **MyNATRuleVM2** dirigerar trafik från lastbalanserarens klientdelsport 4222 till MyVM2:s port 3389 (RDP-porten).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort lastbalanseraren och alla relaterade resurser när du inte längre behöver dem öppnar du resursgruppen **MyResourceGroupLB** och väljer **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en intern standardlastbalanserare. Du skapade och konfigurerade nätverksresurser, serverdelsservrar, en hälsoavsökning och regler för lastbalanseraren. Du installerade IIS på virtuella serverdelsdatorer och använde en lastbalanserarens offentliga IP-adress för att testa lastbalanseraren. Du har skapat och testat portvidarebefordran från en angiven port på lastbalanseraren till en port på en virtuell serverdelsdator. 

Om du vill läsa mer om Azure Load Balancer kan du hämta fler självstudier.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
