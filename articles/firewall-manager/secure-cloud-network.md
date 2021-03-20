---
title: 'Självstudie: skydda din virtuella hubb med Azure Firewall Manager'
description: I den här självstudien får du lära dig hur du skyddar din virtuella hubb med Azure Firewall Manager med hjälp av Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: victorh
ms.openlocfilehash: 9d1e2d257074555e7a2e78930e1f9be6cd4d90fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89536011"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Självstudie: skydda din virtuella hubb med Azure Firewall Manager

Med hjälp av Azure Firewall Manager kan du skapa säkra virtuella hubbar för att skydda din moln nätverks trafik till privata IP-adresser, Azure PaaS och Internet. Trafik dirigering till brand väggen är automatiserad, så du behöver inte skapa användardefinierade vägar (UDR).

![skydda moln nätverket](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager stöder också en hubb för virtuella nätverk. En jämförelse av arkitektur typerna för den säkra virtuella hubben och hubben för virtuella nätverk finns i [Vad är arkitektur alternativ för Azure Firewall Manager?](vhubs-and-vnets.md)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa det virtuella ekernätverket
> * Skapa en säker virtuell hubb
> * Anslut hubben och ekrarna virtuella nätverk
> * Dirigera trafik till hubben
> * Distribuera servrarna
> * Skapa en brand Väggs princip och skydda hubben
> * testa brandväggen.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-hub-and-spoke-architecture"></a>Skapa en hubb och eker-arkitektur

Skapa först ekrar virtuella nätverk där du kan placera dina servrar.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Skapa två ekrar virtuella nätverk och undernät

De två virtuella nätverken kommer att ha en arbets belastnings Server och skyddas av brand väggen.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Välj **virtuellt nätverk** under **nätverk**.
2. I fältet **Prenumeration** väljer du din prenumeration.
1. För **resurs grupp** väljer du **Skapa ny** och skriver **VB-Manager** som namn och väljer **OK**.
2. Som **namn** skriver du **ekrar-01**.
3. För **region** väljer du **(US) USA, östra**.
4. Välj **Nästa: IP-adresser**.
1. Skriv **10.1.0.0/16** för **adress utrymme**.
3. Välj **Lägg till undernät**.
4. Skriv **arbets belastning – 01-SN**.
5. Skriv **10.1.1.0/24** för **under nätets adress intervall**.
6. Välj **Lägg till**.
1. Välj **Granska + skapa**.
2. Välj **Skapa**.

Upprepa proceduren för att skapa ett annat liknande virtuellt nätverk:

Namn: **ekrar-02**<br>
Adress utrymme: **10.2.0.0/16**<br>
Under näts namn: **arbets belastning-02-SN**<br>
Adress intervall för under nätet: **10.2.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Skapa den säkra virtuella hubben

Skapa din säkra virtuella hubb med hjälp av brand Väggs hanteraren.

1. På Start sidan för Azure Portal väljer du **alla tjänster**.
2. I rutan Sök skriver du **Firewall Manager** och väljer **brand Väggs hanteraren**.
3. På sidan **Firewall Manager** väljer du **Visa skyddade virtuella nav**.
4. I **brand Väggs hanteraren | Sidan skyddade virtuella hubbar** väljer du **Skapa ny säker virtuell hubb**.
5. För **resurs grupp** väljer du **VB-Manager**.
7. För **region** väljer du **östra USA**.
1. För namnet på den **säkra virtuella hubben** skriver du **Hub-01**.
2. För **nav adress utrymme** skriver du **10.0.0.0/16**.
3. För det nya vWAN-namnet skriver du **vWAN-01**.
4. Lämna kryss rutan **Inkludera VPN-gateway för att aktivera betrodda säkerhets partner** avmarkerad.
5. Välj **Nästa: Azure-brandvägg**.
6. Godkänn standard inställningen för **Azure Firewall** som **är aktive rad** och välj sedan **Nästa: betrodd säkerhets partner**.
7. Godkänn standardinställningen för **inaktiverade** **betrodda säkerhets partner** och välj **Nästa: granska + skapa**.
8. Välj **Skapa**. Det tar cirka 30 minuter att distribuera.

Nu kan du hämta brand väggens offentliga IP-adress.

1. När distributionen är klar väljer du **alla tjänster** på Azure Portal.
1. Skriv **Firewall Manager** och välj sedan **Firewall Manager**.
2. Välj **skyddade virtuella hubbar**.
3. Välj **hubb – 01**.
7. Välj **offentlig IP-konfiguration**.
8. Anteckna den offentliga IP-adressen som ska användas senare.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Anslut hubben och ekrarna virtuella nätverk

Nu kan du peer-koppla hubben och Ekers virtuella nätverk.

1. Välj resurs gruppen **VB-Manager** och välj sedan det virtuella WAN **-nätverket Vwan-01** .
2. Under **anslutning** väljer du **virtuella nätverks anslutningar**.
3. Välj **Lägg till anslutning**.
4. För **anslutnings namn** skriver du **Hub-eker-01**.
5. För **hubbar** väljer du **hubb-01**.
6. För **resurs grupp** väljer du **VB-Manager**.
7. För **virtuellt nätverk** väljer du **eker-01**.
8. Välj **Skapa**.

Upprepa för att ansluta det **ekrar-02** virtuella nätverket: anslutnings namn – **hubb-eker-02**

## <a name="deploy-the-servers"></a>Distribuera servrarna

1. Välj **Skapa en resurs** på Azure-portalen.
2. Välj **Windows Server 2016 Data Center** i listan **populär** .
3. Ange följande värden för den virtuella datorn:

   |Inställning  |Värde  |
   |---------|---------|
   |Resursgrupp     |**VB-chef**|
   |Namn på virtuell dator     |**SRV-arbets belastning – 01**|
   |Region     |**USA USA, östra)**|
   |Administratörens användar namn     |Ange ett användar namn|
   |Lösenord     |Ange ett lösen ord|

4. Under **regler för inkommande port** för **offentliga inkommande portar** väljer du **ingen**.
6. Godkänn de andra standardinställningarna och välj **Nästa: diskar**.
7. Acceptera standardvärdena för disken och välj **Nästa: nätverk**.
8. Välj **ekrar – 01** för det virtuella nätverket och välj **arbets belastning – 01-SN** för under nätet.
9. För **offentlig IP-adress** väljer du **ingen**.
11. Godkänn övriga standardvärden och välj **Nästa: hantering**.
12. Välj **av** om du vill inaktivera startdiagnostik. Godkänn de andra standardinställningarna och välj **Granska + skapa**.
13. Granska inställningarna på sidan Sammanfattning och välj sedan **skapa**.

Använd informationen i följande tabell för att konfigurera en annan virtuell dator med namnet **SRV-arbetsbelastning-02**. Resten av konfigurationen är samma som den virtuella datorn för **SRV-arbetsbelastnings 01** .

|Inställning  |Värde  |
|---------|---------|
|Virtuellt nätverk|**Eker-02**|
|Undernät|**Arbets belastning-02-SN**|

När servrarna har distribuerats väljer du en server resurs och i **nätverk** noterar du den privata IP-adressen för varje server.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Skapa en brand Väggs princip och skydda hubben

En brand Väggs princip definierar samlingar av regler för att dirigera trafik på ett eller flera skyddade virtuella nav. Du skapar brand Väggs principen och skyddar sedan hubben.

1. I brand Väggs hanteraren väljer du **Visa Azure Firewall-principer**.
2. Välj **Skapa Azure Firewall-princip**.
3. Under **princip information**, för **namn** typ **policy-01** och för **region** väljer du **USA, östra**.
4. Välj **Nästa: DNS-inställningar (förhands granskning)**.
1. Välj **Nästa: regler**.
2. På fliken **regler** väljer du **Lägg till en regel samling**.
3. På sidan **Lägg till regel samling** skriver du **app-RC-01** som **namn**.
4. För **regel samlings typ** väljer du **program**.
5. För **prioritet**, Skriv **100**.
6. Se till att **regel samlings åtgärden** är **tillåten**.
7. För regel **namn** typen **Allow-MSFT**.
8. För **typ av källa** väljer du **IP-adress**.
9. Som **källa** skriver du **\*** .
10. För **protokoll**, Skriv **http, https**.
11. Se till att **måltypen** är ett **fullständigt domän namn**.
12. För **mål** skriver du **\* . Microsoft.com**.
13. Välj **Lägg till**.

Lägg till en DNAT-regel så att du kan ansluta ett fjärr skrivbord till den virtuella **SRV-arbets belastnings 01-** datorn.

1. Välj **Lägg till en regel samling**.
2. I **namn** skriver du **DNAt-RDP**.
3. För **regel samlings typ** väljer du **DNAt**.
4. För **prioritet**, Skriv **100**.
5. Som regel **namn** skriver du **Tillåt-RDP**.
6. För **typ av källa** väljer du **IP-adress**.
7. Som **källa** skriver du **\*** .
8. I fältet **Protokoll** väljer du **TCP**.
9. För **mål portar** skriver du **3389**.
10. För **måltyp** väljer du **IP-adress**.
11. För **mål** anger du den offentliga IP-adress för brand väggen som du antecknade tidigare.
12. För **översatt adress** anger du den privata IP-adressen för **SRV-arbetsbelastning – 01** som du antecknade tidigare.
13. I fältet **Översatt port** skriver du **3389**.
14. Välj **Lägg till**.

Lägg till en nätverks regel så att du kan ansluta ett fjärr skrivbord från **SRV-arbetsbelastning – 01** till **SRV-arbets belastning-02**.

1. Välj **Lägg till en regel samling**.
2. För **namn** skriver du **VNet-RDP**.
3. För **regel samlings typ** väljer du **nätverk**.
4. För **prioritet**, Skriv **100**.
5. För regel **namn** skriver du **Allow-VNet**.
6. För **typ av källa** väljer du **IP-adress**.
7. Som **källa** skriver du **\*** .
8. I fältet **Protokoll** väljer du **TCP**.
9. För **mål portar** skriver du **3389**.
9. För **måltyp** väljer du **IP-adress**.
10. För **mål** anger du den **SRV-arbetsbelastnings 02-** privata IP-adress som du antecknade tidigare.
11. Välj **Lägg till**.
1. Välj **Nästa: Hot information**.
2. Välj **Nästa: hubbar**.
3. På fliken **hubbar** väljer du **associera virtuella nav**.
4. Välj **hubb – 01** och välj sedan **Lägg till**.
5. Välj **Granska + skapa**.
6. Välj **Skapa**.

Det kan ta ungefär fem minuter eller mer att slutföra.

## <a name="route-traffic-to-your-hub"></a>Dirigera trafik till hubben

Nu måste du se till att nätverks trafiken dirigeras genom brand väggen.

1. I brand Väggs hanteraren väljer du **skyddade virtuella nav**.
2. Välj **hubb – 01**.
3. Under **Inställningar** väljer du **säkerhets konfiguration**.
4. Under **Internet trafik** väljer du **Azure-brandvägg**.
5. Under **privat trafik** väljer du **Skicka via Azure-brandvägg**.
10. Kontrol lera att **Hub-eker-** anslutningen visar **Internet trafik** som **skyddad**.
11. Välj **Spara**.


## <a name="test-your-firewall"></a>Testa din brand vägg

För att testa brand Väggs reglerna ansluter du en fjärr skrivbord med hjälp av den offentliga brand Väggs-IP-adressen, som är NATed till **SRV-arbetsbelastning – 01**. Därifrån använder du en webbläsare för att testa program regeln och ansluta ett fjärr skrivbord till **SRV-arbets belastnings 02** för att testa nätverks regeln.

### <a name="test-the-application-rule"></a>Testa program regeln

Testa nu brand Väggs reglerna för att bekräfta att den fungerar som förväntat.

1. Anslut ett fjärr skrivbord till brand väggens offentliga IP-adress och logga in.

3. Öppna Internet Explorer och navigera till https://www.microsoft.com.
4. Välj **OK**  >  **Stäng** i Internet Explorers säkerhets aviseringar.

   Du bör se Microsofts hem sida.

5. Gå till https://www.google.com.

   Du bör blockeras av brandväggen.

Nu har du verifierat att brand Väggs program regeln fungerar:

* Du kan bläddra till en tillåten FQDN, men inte till andra.

### <a name="test-the-network-rule"></a>Testa nätverks regeln

Testa nu nätverks regeln.

- Öppna ett fjärr skrivbord till **SRV-arbets belastningen 02** privat IP-adress.

   Ett fjärr skrivbord bör ansluta till **SRV-arbets belastning-02**.

Nu har du verifierat att brand Väggs nätverks regeln fungerar:
* Du kan ansluta ett fjärr skrivbord till en server som finns i ett annat virtuellt nätverk.

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med att testa brand Väggs resurserna tar du bort resurs gruppen **VB-Manager** för att ta bort alla brand Väggs resurser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om betrodda säkerhets partner](trusted-security-partners.md)
