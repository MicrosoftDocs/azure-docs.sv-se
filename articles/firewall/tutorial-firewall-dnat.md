---
title: 'Självstudie: filtrera inkommande Internet trafik med Azure Firewall DNAT med portalen'
description: I den här självstudien får du lära dig att distribuera och konfigurera Azure Firewall DNAT via Azure-portalen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a1d3bdae1e870b094472a63d4b808d9df95c129d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741913"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Självstudie: filtrera inkommande Internet trafik med Azure Firewall DNAT med hjälp av Azure Portal

Du kan konfigurera Azure Firewall DNAT (Destination Network Address Translation) att översätta och filtrera inkommande trafik till dina undernät. När du konfigurerar DNAT anges åtgärds uppsättningen för NAT-regler till **DNAt**. Varje regel i insamlings regeln för NAT kan sedan användas för att översätta brand väggens offentliga IP-adress och port till en privat IP-adress och port. DNAT-regler lägger implicit till en motsvarande nätverksregel för att tillåta den översatta trafiken. Av säkerhets skäl är den rekommenderade metoden att lägga till en speciell Internet källa för att ge DNAT åtkomst till nätverket och undvika att använda jokertecken. Mer information om regelbearbetningslogik för Azure Firewall finns i [Regelbearbetningslogik för Azure Firewall](rule-processing.md).

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
4. I fältet **Prenumeration** väljer du din prenumeration.
1. I fältet **Resursgruppsnamn** anger du **RG-DNAT-Test**.
5. För **region** väljer du en region. Alla andra resurser som du skapar måste vara i samma region.
6. Välj **Granska + skapa**.
1. Välj **Skapa**.

## <a name="set-up-the-network-environment"></a>Konfigurera nätverksmiljön

För den här självstudien skapar du två peerkopplade virtuella nätverk:

- **VN-Hub** – brandväggen finns i det här virtuella nätverket.
- **VN-Spoke** – arbetsbelastningsservern finns i det här virtuella nätverket.

Skapa först de virtuella nätverken och peerkoppla dem sedan.

### <a name="create-the-hub-vnet"></a>Skapa det virtuella Hub-nätverket

1. På Start sidan för Azure Portal väljer du **alla tjänster**.
2. Under **nätverk** väljer du **virtuella nätverk**.
3. Välj **Lägg till**.
7. För **resurs grupp** väljer du **RG-DNAt-test**.
1. Som **Namn** anger du **VN-Hub**.
1. För **region** väljer du samma region som du använde tidigare.
1. Välj **Nästa: IP-adresser**.
1. Acceptera standardvärdet **10.0.0.0/16** för **IPv4-adress utrymme**.
1. Under **undernäts namn** väljer du standard.
1. Redigera **under näts namnet** och skriv **AzureFirewallSubnet**.

     Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
     > [!NOTE]
     > Storleken på AzureFirewallSubnet-undernätet är/26. Mer information om under näts storleken finns i [vanliga frågor och svar om Azure Firewall](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. Skriv **10.0.1.0/26** för **under nätets adress intervall**.
11. Välj **Spara**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

### <a name="create-a-spoke-vnet"></a>Skapa ett virtuellt spoke-nätverk

1. På Start sidan för Azure Portal väljer du **alla tjänster**.
2. Under **nätverk** väljer du **virtuella nätverk**.
3. Välj **Lägg till**.
1. För **resurs grupp** väljer du **RG-DNAt-test**.
1. I fältet **Namn** anger du **VN-Spoke**.
1. För **region** väljer du samma region som du använde tidigare.
1. Välj **Nästa: IP-adresser**.
1. För **IPv4-adress utrymme** redigerar du standard och skriver **192.168.0.0/16**.
1. Välj **Lägg till undernät**.
1. För **under nätets namn** skriver du **SN-arbets belastning**.
10. Skriv **192.168.1.0/24** för **under nätets adress intervall**.
11. Välj **Lägg till**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

### <a name="peer-the-vnets"></a>Peerkoppla de virtuella nätverken

Peerkoppla nu de två virtuella nätverken.

1. Välj det virtuella nätverket **VN-Hub** .
2. Under **Inställningar** väljer du **peering**.
3. Välj **Lägg till**.
4. Under **det här virtuella nätverket** skriver du **peer-HubSpoke** för **peering-länkens namn**.
5. Under **fjärran slutet virtuellt nätverk**, för **peering-länkens namn**, skriver du **peer-SpokeHub**. 
1. Välj **VN-Spoke** för det virtuella nätverket.
1. Godkänn alla andra standardvärden och välj sedan **Lägg till**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell arbetsbelastningsdator och placera den i undernätet **SN-Workload**.

1. I menyn i Azure-portalen väljer du **Skapa en resurs**.
2. Under **populär** väljer du **Windows Server 2016 Data Center**.

**Grundläggande inställningar**

1. I fältet **Prenumeration** väljer du din prenumeration.
1. För **resurs grupp** väljer du **RG-DNAt-test**.
1. För **namn på virtuell dator** skriver du **SRV-arbets belastning**.
1. För **region** väljer du samma plats som du använde tidigare.
1. Ange ett användarnamn och lösenord.
1. Välj **Nästa: diskar**.

**Diskar**
1. Välj **Nästa: Nätverk**.

**Nätverk**

1. För **virtuellt nätverk** väljer du **VN-ekrar**.
2. I fältet **Undernät** väljer du **SN-Workload**.
3. För **offentlig IP-adress** väljer du **ingen**.
4. För **offentliga inkommande portar** väljer du **ingen**. 
2. Lämna de andra standardinställningarna och välj **Nästa: hantering**.

**Hantering**

1. För **startdiagnostik** väljer du **inaktivera**.
1. Välj **Granska + skapa**.

**Granska + skapa**

Granska sammanfattningen och välj sedan **skapa**. Det här kan ta några minuter.

När distributionen är klar antecknar du den privata IP-adressen för den virtuella datorn. Den används senare när du konfigurerar brandväggen. Välj namnet på den virtuella datorn och under **Inställningar** väljer du **nätverk** för att hitta den privata IP-adressen.

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

1. På portalens start sida väljer du **skapa en resurs**.
1. Sök efter **brand vägg** och välj sedan **brand vägg**.
1. Välj **Skapa**. 
1. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:

   |Inställning  |Värde  |
   |---------|---------|
   |Prenumeration     |\<your subscription\>|
   |Resursgrupp     |Välj **RG-DNAt-test** |
   |Name     |**FW-DNAT-test**|
   |Region     |Välj samma plats som tidigare|
   |Brand Väggs hantering|**Använd brand Väggs regler (klassisk) för att hantera den här brand väggen**|
   |Välj ett virtuellt nätverk     |**Använd befintlig**: VN-Hub|
   |Offentlig IP-adress     |**Lägg till nytt**, namn: **VB-pip**.|

5. Godkänn de andra standardinställningarna och välj sedan **Granska + skapa**.
6. Granska sammanfattningen och välj sedan **skapa** för att skapa brand väggen.

   Distributionen kan ta några minuter.
7. När distributionen är klar går du till resurs gruppen **RG-DNAt-test** och väljer **VB-DNAt-test-** brandväggen.
8. Anteckna brand väggens privata och offentliga IP-adresser. Du kommer att använda dem senare när du skapar standard väg och NAT-regel.

## <a name="create-a-default-route"></a>Skapa en standardväg

För undernätet **SN-Workload** ställer du in att den utgående standardvägen ska gå via brandväggen.

1. På Start sidan för Azure Portal väljer du **alla tjänster**.
2. Under **nätverk** väljer du **routningstabeller**.
3. Välj **Lägg till**.
5. I fältet **Prenumeration** väljer du din prenumeration.
1. För **resurs grupp** väljer du **RG-DNAt-test**.
1. För **region** väljer du samma region som du använde tidigare.
1. I fältet **Namn** skriver du **RT-FWroute**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.
1. Välj **Gå till resurs**.
1. Välj **undernät** och välj sedan **associera**.
1. För **virtuellt nätverk** väljer du **VN-ekrar**.
1. I fältet **Undernät** väljer du **SN-Workload**.
1. Välj **OK**.
1. Välj **vägar** och välj sedan **Lägg till**.
1. I fältet **Vägnamn** skriver du **FW-DG**.
1. I fältet **Adressprefix** skriver du **0.0.0.0/0**.
1. I fältet **Nästa hopptyp** väljer du **Virtuell installation**.

    Azure Firewall är egentligen en hanterad tjänst, men en virtuell installation fungerar i det här fallet.
18. I fältet **Nästa hoppadress** skriver du brandväggens privata IP-adress som du skrev ned tidigare.
19. Välj **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurera en NAT-regel

1. Öppna resurs gruppen **RG-DNAt-test** och välj brand väggen **VB-DNAt-test** . 
2. På sidan **VB-DNAt-test** går du till **Inställningar** och väljer **regler (klassisk)**. 
3. Välj **Lägg till NAT-regel samling**. 
4. I fältet **Namn** skriver du **RC-DNAT-01**. 
5. I fältet **Prioritet** skriver du **200**. 
6. Under **Regler**, i fältet **Namn**, skriver du **RL-01**.
7. I fältet **Protokoll** väljer du **TCP**.
1. I **typ av källa** väljer du **IP-adress**.
1. Skriv * i **källa**. 
1. För **mål adresser** anger du brand väggens offentliga IP-adress. 
1. I fältet **Målportar** skriver du **3389**. 
1. I fältet **Översatt adress** skriver du den privata IP-adressen för den virtuella datorn Srv-Workload. 
1. I fältet **Översatt port** skriver du **3389**. 
1. Välj **Lägg till**. Det här kan ta några minuter.

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
