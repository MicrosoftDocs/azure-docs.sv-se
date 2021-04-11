---
title: 'Självstudie: Distribuera & konfigurera Azure-brandväggen med hjälp av Azure Portal'
description: I den här självstudien får du lära dig att distribuera och konfigurera Azure Firewall via Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ac9eaea70de67e67ef690c280b30bae22517ac21
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228001"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal

En viktig del av en övergripande säkerhetsplan för nätverket är att kontrollera utgående nätverksåtkomst. Du kanske till exempel vill begränsa åtkomsten till webbplatser. Eller så kanske du vill begränsa de utgående IP-adresserna och portarna som kan nås.

Med Azure Firewall kan du kontrollera åtkomsten till utgående nätverk från ett Azure-undernät. Med Azure Firewall kan du konfigurera:

* Programreglerna som definierar fullständigt kvalificerade domännamn (FQDN) kan nås från ett undernät.
* Nätverksregler som definierar källadress, protokoll, målport och måladress.

Nätverkstrafiken måste följa konfigurerade brandväggsregler när du vidarebefordrar den till brandväggen som standardgateway för undernätet.

I den här självstudien skapar du ett förenklat enda VNet med två undernät för enkel distribution.

För produktions distributioner rekommenderas en [nav-och eker-modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) , där brand väggen finns i ett eget VNet. Arbets belastnings servrarna finns i peer-virtuella nätverk i samma region med ett eller flera undernät.

* **AzureFirewallSubnet** – brandväggen ligger i det här undernätet.
* **Workload-SN** – arbetsbelastningsservern ligger i det här undernätet. Det här undernätets nätverkstrafik går genom brandväggen.

![Självstudie om nätverksinfrastruktur](media/tutorial-firewall-deploy-portal/tutorial-network.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera en program regel för att tillåta åtkomst till www.google.com
> * Konfigurera en nätverksregel för att tillåta åtkomst till externa DNS-servrar
> * Konfigurera en NAT-regel för att tillåta fjärr skrivbord till test servern
> * testa brandväggen.

Om du vill kan du utföra den här självstudien med [Azure PowerShell](deploy-ps.md).

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="set-up-the-network"></a>Konfigurera nätverket

Skapa först en resursgrupp som ska innehålla de resurser som behövs till att distribuera brandväggen. Skapa sedan ett VNet, undernät och en test Server.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Resursgruppen innehåller alla resurser för den här självstudien.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. På Azure Portal-menyn väljer du **resurs grupper** eller söker efter och väljer *resurs grupper* från vilken sida som helst. Välj **Lägg till**.
4. I fältet **Prenumeration** väljer du din prenumeration.
1. För **resurs grupp namn** anger du *test-VB-RG*.
1. I fältet **Resursgruppsplats** väljer du en plats. Alla andra resurser som du skapar måste finnas på samma plats.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Det virtuella nätverket kommer att ha tre undernät.

> [!NOTE]
> Storleken på AzureFirewallSubnet-undernätet är/26. Mer information om under näts storleken finns i [vanliga frågor och svar om Azure Firewall](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
1. Välj **nätverk**  >  **virtuellt nätverk**.
1. Välj **Skapa**.
1. I fältet **Prenumeration** väljer du din prenumeration.
1. För **resurs grupp** väljer du **test-VB-RG**.
1. I fältet **Namn** skriver du **Test-FW-VN**.
1. För **region** väljer du samma plats som du använde tidigare.
1. Välj **Nästa: IP-adresser**.
1. För **IPv4-adress utrymme**, Skriv **10.0.0.0/16**.
1. Under **undernät** väljer du **standard**.
1. För **under näts namn** skriver du **AzureFirewallSubnet**. Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
1. För **adress intervall** skriver du **10.0.1.0/26**.
1. Välj **Spara**.

   Skapa sedan ett undernät för arbets belastnings servern.

1. Välj **Lägg till undernät**.
4. Skriv **arbets belastning – SN** för **under näts namn**.
5. Skriv **10.0.2.0/24** för **under nätets adress intervall**.
6. Välj **Lägg till**.
7. Välj **Granska + skapa**.
8. Välj **Skapa**.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Nu ska du skapa den virtuella arbets belastnings datorn och placera den i **arbets belastningen SN-** undernät.

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
2. Välj **Windows Server 2016 Data Center**.
4. Ange följande värden för den virtuella datorn:

   |Inställning  |Värde  |
   |---------|---------|
   |Resursgrupp     |**Test-VB-RG**|
   |Namn på virtuell dator     |**SRV-arbete**|
   |Region     |Samma som föregående|
   |Avbildning|Windows Server 2016 Datacenter|
   |Administratörens användar namn     |Ange ett användar namn|
   |Lösenord     |Ange ett lösen ord|

4. Under **regler för inkommande port**, **offentliga inkommande portar** väljer du **ingen**.
6. Godkänn de andra standardinställningarna och välj **Nästa: diskar**.
7. Acceptera standardvärdena för disken och välj **Nästa: nätverk**.
8. Kontrol lera att **test-VB-VN** är valt för det virtuella nätverket och att under nätet är **arbets belastning – SN**.
9. För **offentlig IP-adress** väljer du **ingen**.
11. Godkänn övriga standardvärden och välj **Nästa: hantering**.
12. Välj **inaktivera** om du vill inaktivera startdiagnostik. Godkänn de andra standardinställningarna och välj **Granska + skapa**.
13. Granska inställningarna på sidan Sammanfattning och välj sedan **skapa**.

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

Distribuera brandväggen till det virtuella nätverket.

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
2. Skriv **brand väggen** i sökrutan och tryck på **RETUR**.
3. Välj **brand vägg** och välj sedan **skapa**.
4. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:

   |Inställning  |Värde  |
   |---------|---------|
   |Prenumeration     |\<your subscription\>|
   |Resursgrupp     |**Test-VB-RG** |
   |Name     |**Test-FW01**|
   |Region     |Välj samma plats som tidigare|
   |Brand Väggs hantering|**Använd brand Väggs regler (klassisk) för att hantera den här brand väggen**|
   |Välj ett virtuellt nätverk     |**Använd befintlig**: **test-VB-VN**|
   |Offentlig IP-adress     |**Lägg till ny**<br>**Namn**:  **VB-pip**|

5. Godkänn de andra standardvärdena och välj sedan **Granska + skapa**.
6. Granska sammanfattningen och välj sedan **skapa** för att skapa brand väggen.

   Distributionen kan ta några minuter.
7. När distributionen är klar går du till resurs gruppen **test-VB-RG** och väljer **test-FW01** brand väggen.
8. Observera brand väggens privata och offentliga IP-adresser. Du kommer att använda de här adresserna senare.

## <a name="create-a-default-route"></a>Skapa en standardväg

För undernätet **Workload-SN** ställer du in att den utgående standardvägen ska gå via brandväggen.

1. Välj **alla tjänster** på Azure Portal-menyn eller Sök efter och välj *alla tjänster* från vilken sida som helst.
2. Under **nätverk** väljer du **routningstabeller**.
3. Välj **Lägg till**.
5. I fältet **Prenumeration** väljer du din prenumeration.
6. För **resurs grupp** väljer du **test-VB-RG**.
7. För **region** väljer du samma plats som du använde tidigare.
4. I fältet **Namn** skriver du **Firewall-route**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

När distributionen är klar väljer **du gå till resurs**.

1. På sidan brand Väggs väg väljer du **undernät** och väljer sedan **associera**.
1. Välj **Virtual Network**  >  **test-VB-VN**.
1. För **undernät** väljer du **arbets belastning-SN**. Se till att du bara väljer **arbets belastningen – SN** under nätet för den här vägen, annars fungerar inte brand väggen korrekt.

13. Välj **OK**.
14. Välj **vägar** och välj sedan **Lägg till**.
15. För **väg namn** skriver du **VB-GD**.
16. I fältet **Adressprefix** skriver du **0.0.0.0/0**.
17. I fältet **Nästa hopptyp** väljer du **Virtuell installation**.

    Azure Firewall är egentligen en hanterad tjänst, men en virtuell installation fungerar i det här fallet.
18. I fältet **Nästa hoppadress** skriver du brandväggens privata IP-adress som du skrev ned tidigare.
19. Välj **OK**.

## <a name="configure-an-application-rule"></a>Konfigurera en programregel

Det här är den program regel som tillåter utgående åtkomst till `www.google.com` .

1. Öppna mappen **test-VB-RG** och välj **test-FW01** -brandväggen.
2. På sidan **test-FW01** under **Inställningar** väljer du **regler (klassisk)**.
3. Välj fliken **samling av program regel** .
4. Välj **Lägg till program regel samling**.
5. I fältet **Namn** skriver du **App-Coll01**.
6. I fältet **Prioritet** skriver du **200**.
7. I fältet **Åtgärd** väljer du **Tillåt**.
8. Under **regler**, **mål-FQDN**, som **namn**, skriver du **Allow-Google**.
9. I **typ av källa** väljer du **IP-adress**.
10. För **källa** skriver du **10.0.2.0/24**.
11. I fältet **Protokoll: port** skriver du **http, https**.
12. För **mål-FQDN** skriver du **`www.google.com`**
13. Välj **Lägg till**.

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. Mer information finns i [Infrastruktur-FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurera en nätverksregel

Det här är nätverksregel som tillåter utgående åtkomst till två IP-adresser på port 53 (DNS).

1. Välj fliken **nätverks regel samling** .
2. Välj **Lägg till regel samling för nätverk**.
3. I fältet **Namn** skriver du **Net-Coll01**.
4. I fältet **Prioritet** skriver du **200**.
5. I fältet **Åtgärd** väljer du **Tillåt**.
6. Under **regler**, **IP-adresser**, för **namn**, skriver du **Allow-DNS**.
7. I fältet **Protokoll** väljer du **UDP**.
9. I **typ av källa** väljer du **IP-adress**.
1. För **källa** skriver du **10.0.2.0/24**.
2. För **måltyp** väljer du **IP-adress**.
3. För **mål adress** skriver du **209.244.0.3, 209.244.0.4**

   Detta är offentliga DNS-servrar som hanteras av CenturyLink.
1. I fältet **Målportar** skriver du **53**.
2. Välj **Lägg till**.

## <a name="configure-a-dnat-rule"></a>Konfigurera en DNAT-regel

Med den här regeln kan du ansluta ett fjärr skrivbord till den Srv-Work virtuella datorn via brand väggen.

1. Välj fliken **insamling av NAT-regel** .
2. Välj **Lägg till NAT-regel samling**.
3. I **namn** skriver du **RDP**.
4. I fältet **Prioritet** skriver du **200**.
5. Under **regler**, för **namn**, skriver du **RDP-NAT**.
6. I fältet **Protokoll** väljer du **TCP**.
7. I **typ av källa** väljer du **IP-adress**.
8. Som **källa** skriver du **\*** .
9. Ange den offentliga IP-adressen för brand väggen för **mål adress**.
10. För **mål portar** skriver du **3389**.
11. För **översatt adress** anger du den privata IP-adressen för **SRV-arbete** .
12. I fältet **Översatt port** skriver du **3389**.
13. Välj **Lägg till**.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Ändra den primära och sekundära DNS-adressen för nätverksgränssnittet **Srv-Work**

I test syfte i den här självstudien konfigurerar du serverns primära och sekundära DNS-adresser. Detta är inte ett allmänt krav för Azure-brandvägg.

1. På Azure Portal-menyn väljer du **resurs grupper** eller söker efter och väljer *resurs grupper* från vilken sida som helst. Välj resurs gruppen **test-VB-RG** .
2. Välj nätverks gränssnitt för den virtuella **SRV-Work-** datorn.
3. Under **Inställningar** väljer du **DNS-servrar**.
4. Under **DNS-servrar** väljer du **anpassad**.
5. Skriv **209.244.0.3** i textrutan **Lägg till DNS-server** och **209.244.0.4** i nästa textruta.
6. Välj **Spara**.
7. Starta om den virtuella datorn **Srv-Work**.

## <a name="test-the-firewall"></a>testa brandväggen.

Testa nu brand väggen för att bekräfta att den fungerar som förväntat.

1. Anslut ett fjärr skrivbord till brand väggens offentliga IP-adress och logga in på den virtuella datorn för **SRV-arbete** . 
3. Öppna Internet Explorer och navigera till `https://www.google.com`.
4. Välj **OK**  >  **Stäng** i Internet Explorers säkerhets aviseringar.

   Du bör se Googles start sida.

5. Gå till `https://www.microsoft.com`.

   Du bör blockeras av brandväggen.

Nu har du verifierat att brand Väggs reglerna fungerar:

* Du kan bläddra till en tillåten FQDN, men inte till andra.
* Du kan omvandla DNS-namn med hjälp av den konfigurerade externa DNS-servern.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **Test-FW-RG** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./firewall-diagnostics.md)