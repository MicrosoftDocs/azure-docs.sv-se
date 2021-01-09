---
title: 'Självstudie: filtrera inkommande Internet trafik med Azure Firewall DNAT med portalen'
description: I den här självstudien får du lära dig att distribuera och konfigurera Azure Firewall DNAT via Azure-portalen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 281d0587ca4c041c7149e49aad6227f6dc0b7fbf
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050875"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Självstudie: filtrera inkommande Internet trafik med Azure Firewall DNAT med hjälp av Azure Portal

Du kan konfigurera Azure Firewall DNAT (Destination Network Address Translation) att översätta och filtrera inkommande trafik till dina undernät. När du konfigurerar DNAT anges åtgärds uppsättningen för NAT-regler till **DNAt**. Varje regel i NAT-regelsamlingen kan sedan användas till att översätta brandväggens offentliga IP-adress och port till en privat IP-adress och port. DNAT-regler lägger implicit till en motsvarande nätverksregel för att tillåta den översatta trafiken. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Mer information om regelbearbetningslogik för Azure Firewall finns i [Regelbearbetningslogik för Azure Firewall](rule-processing.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera en DNAT-regel
> * testa brandväggen.

## <a name="prerequisites"></a>Krav

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.



## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **resurs grupper** på Azure Portal start sida och välj sedan **Lägg till**.
3. I fältet **Resursgruppsnamn** anger du **RG-DNAT-Test**.
4. I fältet **Prenumeration** väljer du din prenumeration.
5. I fältet **Resursgruppsplats** väljer du en plats. Alla efterföljande resurser du skapar måste finnas på samma plats.
6. Välj **Skapa**.

## <a name="set-up-the-network-environment"></a>Konfigurera nätverksmiljön

För den här självstudien skapar du två peerkopplade virtuella nätverk:

- **VN-Hub** – brandväggen finns i det här virtuella nätverket.
- **VN-Spoke** – arbetsbelastningsservern finns i det här virtuella nätverket.

Skapa först de virtuella nätverken och peerkoppla dem sedan.

### <a name="create-the-hub-vnet"></a>Skapa det virtuella Hub-nätverket

1. På Start sidan för Azure Portal väljer du **alla tjänster**.
2. Under **nätverk** väljer du **virtuella nätverk**.
3. Välj **Lägg till**.
4. Som **Namn** anger du **VN-Hub**.
5. I fältet **Adressutrymme** skriver du **10.0.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer sedan **RG-DNAT-Test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **Undernät**, i fältet **Namn**, skriver du **AzureFirewallSubnet**.

     Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
     > [!NOTE]
     > Storleken på AzureFirewallSubnet-undernätet är/26. Mer information om under näts storleken finns i [vanliga frågor och svar om Azure Firewall](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. För **adress intervall** skriver du **10.0.1.0/26**.
11. Använd de andra standardinställningarna och välj sedan **skapa**.

### <a name="create-a-spoke-vnet"></a>Skapa ett virtuellt spoke-nätverk

1. På Start sidan för Azure Portal väljer du **alla tjänster**.
2. Under **nätverk** väljer du **virtuella nätverk**.
3. Välj **Lägg till**.
4. I fältet **Namn** anger du **VN-Spoke**.
5. I fältet **Adressutrymme** skriver du **192.168.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer sedan **RG-DNAT-Test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **Undernät**, i fältet **Namn** anger du **SN-Workload**.

    Servern kommer att finnas i det här undernätet.
10. I fältet **Adressintervall** skriver du **192.168.1.0/24**.
11. Använd de andra standardinställningarna och välj sedan **skapa**.

### <a name="peer-the-vnets"></a>Peerkoppla de virtuella nätverken

Peerkoppla nu de två virtuella nätverken.

1. Välj det virtuella nätverket **VN-Hub** .
2. Under **Inställningar** väljer du **peering**.
3. Välj **Lägg till**.
4. Skriv **peer-HubSpoke** som **namn på peer koppling från VN-Hub till VN-eker**.
5. Välj **VN-Spoke** för det virtuella nätverket.
6. Skriv **peer-SpokeHub** för peering- **namnet från VN-Spoke till VN-Hub**.
7. För **att tillåta vidarebefordrad trafik från VN-Spoke till VN-hubb väljer du** **aktive rad**.
8. Välj **OK**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell arbetsbelastningsdator och placera den i undernätet **SN-Workload**.

1. I menyn i Azure-portalen väljer du **Skapa en resurs**.
2. Under **populär** väljer du **Windows Server 2016 Data Center**.

**Grunder**

1. I fältet **Prenumeration** väljer du din prenumeration.
1. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer sedan **RG-DNAT-Test**.
1. För **namn på virtuell dator** skriver du **SRV-arbets belastning**.
1. För **region** väljer du samma plats som du använde tidigare.
1. Ange ett användarnamn och lösenord.
1. Välj **Nästa: diskar**.

**Diskar**
1. Välj **Nästa: Nätverk**.

**Nätverk**

1. För **virtuellt nätverk** väljer du **VN-ekrar**.
2. I fältet **Undernät** väljer du **SN-Workload**.
3. Välj **ingen** för **offentlig IP-adress** .
4. För **offentliga inkommande portar** väljer du **ingen**. 
2. Lämna de andra standardinställningarna och välj **Nästa: hantering**.

**Hantering**

1. För **startdiagnostik** väljer du **av**.
1. Välj **Granska + skapa**.

**Granska + skapa**

Granska sammanfattningen och välj sedan **skapa**. Det här kan ta några minuter.

När distributionen är klar antecknar du den privata IP-adressen för den virtuella datorn. Den används senare när du konfigurerar brandväggen. Välj namnet på den virtuella datorn och under **Inställningar** väljer du **nätverk** för att hitta den privata IP-adressen.

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

1. På portalens start sida väljer du **skapa en resurs**.
2. Välj **nätverk** och efter **aktuell** väljer du **Visa alla**.
3. Välj **brand vägg** och välj sedan **skapa**. 
4. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:

   |Inställning  |Värde  |
   |---------|---------|
   |Namn     |FW-DNAT-test|
   |Prenumeration     |\<your subscription\>|
   |Resursgrupp     |**Använd befintlig**: RG-DNAT-Test |
   |Plats     |Välj samma plats som tidigare|
   |Välj ett virtuellt nätverk     |**Använd befintlig**: VN-Hub|
   |Offentlig IP-adress     |**Skapa nytt**. Den offentliga IP-adressen måste vara Standard SKU-typen.|

5. Välj **Granska + skapa**.
6. Granska sammanfattningen och välj sedan **skapa** för att skapa brand väggen.

   Distributionen kan ta några minuter.
7. När distributionen är klar går du till resurs gruppen **RG-DNAt-test** och väljer **VB-DNAt-test-** brandväggen.
8. Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-a-default-route"></a>Skapa en standardväg

För undernätet **SN-Workload** ställer du in att den utgående standardvägen ska gå via brandväggen.

1. På Start sidan för Azure Portal väljer du **alla tjänster**.
2. Under **nätverk** väljer du **routningstabeller**.
3. Välj **Lägg till**.
4. I fältet **Namn** skriver du **RT-FWroute**.
5. I fältet **Prenumeration** väljer du din prenumeration.
6. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer **RG-DNAT-Test**.
7. Välj samma plats som tidigare i fältet **Plats**.
8. Välj **Skapa**.
9. Välj **Uppdatera** och välj sedan tabellen **RT-FWroute** väg.
10. Välj **undernät** och välj sedan **associera**.
11. Välj **virtuellt nätverk** och välj sedan **VN-eker**.
12. I fältet **Undernät** väljer du **SN-Workload**.
13. Välj **OK**.
14. Välj **vägar** och välj sedan **Lägg till**.
15. I fältet **Vägnamn** skriver du **FW-DG**.
16. I fältet **Adressprefix** skriver du **0.0.0.0/0**.
17. I fältet **Nästa hopptyp** väljer du **Virtuell installation**.

    Azure Firewall är egentligen en hanterad tjänst, men en virtuell installation fungerar i det här fallet.
18. I fältet **Nästa hoppadress** skriver du brandväggens privata IP-adress som du skrev ned tidigare.
19. Välj **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurera en NAT-regel

1. Öppna **RG-DNAt-test** och välj **VB-DNAt-test-** brandväggen. 
2. På sidan **VB-DNAt-test** går du till **Inställningar** och väljer **regler**. 
3. Välj **Lägg till NAT-regel samling**. 
4. I fältet **Namn** skriver du **RC-DNAT-01**. 
5. I fältet **Prioritet** skriver du **200**. 
6. Under **Regler**, i fältet **Namn**, skriver du **RL-01**.
7. I fältet **Protokoll** väljer du **TCP**.
8. I fältet **Källadresser** skriver du *. 
9. I fältet **Måladresser** skriver du brandväggens offentliga IP-adress. 
10. I fältet **Målportar** skriver du **3389**. 
11. I fältet **Översatt adress** skriver du den privata IP-adressen för den virtuella datorn Srv-Workload. 
12. I fältet **Översatt port** skriver du **3389**. 
13. Välj **Lägg till**. 

## <a name="test-the-firewall"></a>testa brandväggen.

1. Anslut ett fjärrskrivbord till brandväggens offentliga IP-adress. Du ska vara ansluten till den virtuella datorn **Srv-Workload**.
2. Stäng fjärrskrivbordet.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **RG-DNAT-Test** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera en DNAT-regel
> * testa brandväggen.

Därefter kan du övervaka Azure Firewall-loggarna.

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./firewall-diagnostics.md)