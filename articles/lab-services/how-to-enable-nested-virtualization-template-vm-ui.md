---
title: Aktivera kapslad virtualisering på en virtuell mall i Azure Lab Services (UI) | Microsoft Docs
description: Lär dig hur du skapar en mall för virtuella datorer med flera virtuella datorer i.  Med andra ord aktiverar du kapslad virtualisering på en virtuell mall i Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f8135e11fb7b7ddb588ab3a8ed01227712072fd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647927"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Aktivera kapslad virtualisering på en virtuell mall i Azure Lab Services manuellt

Kapslad virtualisering gör att du kan skapa en miljö med flera virtuella datorer i en labbs mall för virtuella datorer. Genom att publicera mallen får du varje användare i labbet med en virtuell dator som är konfigurerad med flera virtuella datorer i den.  Mer information om kapslad virtualisering och Azure Lab Services finns i [Aktivera kapslad virtualisering på en mall virtuell dator i Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Den här artikeln beskriver hur du konfigurerar kapslad virtualisering på en mallfil i Azure Lab Services att använda Windows-roller och-verktyg direkt.  Det finns några saker som behövs för att en klass ska kunna använda kapslad virtualisering.  Stegen nedan beskriver hur du manuellt konfigurerar en mall för labb tjänsters datorer med Hyper-V.  Stegen är avsedda för Windows Server 2016 eller Windows Server 2019.  

>[!IMPORTANT]
>Välj **stor (kapslad virtualisering)** eller **medium (kapslad virtualisering)** för den virtuella dator storleken när du skapar labbet.  Kapslad virtualisering kommer inte att fungera på annat sätt.  

## <a name="enable-hyper-v-role"></a>Aktivera Hyper-V-roll

Följande steg beskriver åtgärder som krävs för att aktivera Hyper-V på Windows Server med hjälp av antingen Serverhanteraren.  När installationen är klar är Hyper-V Manager tillgänglig för att lägga till, ändra och ta bort virtuella klient datorer.

1. I **Serverhanteraren** klickar du på **Lägg till roller och funktioner** på instrument panelens sida.
2. Klicka på **Nästa** på sidan **Innan du börjar**.
3. På sidan **Välj Installations typ** behåller du standard valet av rollbaserad eller funktions baserad installation och klickar sedan på **Nästa**.
4. På sidan **Välj mål server** väljer du Välj en server från serverpoolen.   Den aktuella servern är redan markerad.  Klicka på Nästa.
5. På sidan **Välj Server roller** väljer du **Hyper-V**.  
6. Popup-fönstret **guiden Lägg till roller och funktioner** visas.  Välj **ta med hanterings verktyg (om tillämpligt)**.  Klicka på knappen **Lägg till funktioner** .
7. På sidan **Välj serverroller**, klickar du på **Nästa**.
8. På **sidan Välj funktioner** klickar du på **Nästa**.
9. På sidan **Hyper-V** klickar du på **Nästa**.
10. På sidan **skapa virtuella växlar** godkänner du standardinställningarna och klickar på **Nästa**.
11. På sidan **migrera virtuell dator** accepterar du standardinställningarna och klickar på **Nästa**.
12. På sidan **standard butiker** accepterar du standardinställningarna och klickar på **Nästa**.
13. På sidan **Bekräfta installations inställningarna** väljer **du starta om mål servern automatiskt om det behövs**.
14. När popup- **guiden Lägg till roller och funktioner** visas klickar du på **Ja**.
15. Klicka på **Installera**.
16. Vänta tills sidan **installations förlopp** visar att Hyper-V-rollen är slutförd.  Datorn kan starta om mitt i installationen.
17. Klicka på **Stäng**.

## <a name="enable-dhcp-role"></a>Aktivera DHCP-roll

Alla virtuella Hyper-V-klienter som skapas, behöver en IP-adress i NAT-nätverket.  Vi kommer att skapa NAT-nätverket senare.  Ett sätt att tilldela IP-adresser är att konfigurera värden, i det här fallet mallen för virtuella labb datorer som en DHCP-server.  Nedan visas de steg som krävs för att aktivera DHCP-rollen.

1. I **Serverhanteraren** klickar du på **Lägg till roller och funktioner** på **instrument panelens** sida.
2. Klicka på **Nästa** på sidan **Innan du börjar**.
3. På sidan **Välj Installations typ** väljer du **rollbaserad eller funktions baserad installation** och klickar sedan på **Nästa**.
4. På sidan **Välj mål server** väljer du den aktuella servern från serverpoolen och klickar sedan på **Nästa**.
5. På sidan **Välj Server roller** väljer du **DHCP-server**.  
6. Popup-fönstret **guiden Lägg till roller och funktioner** visas.  Välj **ta med hanterings verktyg (om tillämpligt)**.  Klicka på **Lägg till funktioner**.

    >[!NOTE]
    >Du kan se ett verifierings fel som anger att inga statiska IP-adresser hittades.  Den här varningen kan ignoreras för vårt scenario.

7. På sidan **Välj serverroller**, klickar du på **Nästa**.
8. På sidan **Välj funktioner** klickar du på **Nästa**.
9. På sidan **DHCP-server** klickar du på **Nästa**.
10. Klicka på **Installera** på sidan **Bekräfta installationsval**.
11. Vänta tills **installations förloppet** visar att DHCP-rollen har slutförts.
12. Klicka på Stäng.

## <a name="enable-routing-and-remote-access-role"></a>Aktivera rollen Routning och fjärråtkomst

1. I **Serverhanteraren** klickar du på **Lägg till roller och funktioner** på **instrument panelens** sida.
2. Klicka på **Nästa** på sidan **Innan du börjar**.
3. På sidan **Välj Installations typ** väljer du **rollbaserad eller funktions baserad installation** och klickar sedan på **Nästa**.
4. På sidan **Välj mål server** väljer du den aktuella servern från serverpoolen och klickar sedan på **Nästa**.
5. På sidan **Välj Server roller** väljer du **fjärråtkomst**. Klicka på **OK**.
6. På sidan **Välj funktioner** klickar du på **Nästa**.
7. På sidan **fjärråtkomst** klickar du på **Nästa**.
8. På sidan **roll tjänster** väljer du **routning**.
9. Popup-fönstret **guiden Lägg till roller och funktioner** visas.  Välj **ta med hanterings verktyg (om tillämpligt)**.  Klicka på **Lägg till funktioner**.
10. Klicka på **Nästa**.
11. Klicka på **Nästa** på sidan **Rollen Webbserver (IIS)**.
12. Klicka på **Nästa** på sidan **Välj rolltjänster**.
13. Klicka på **Installera** på sidan **Bekräfta installationsval**.
14. Vänta tills sidan **installations förlopp** visar att fjärråtkomstrollen är slutförd.  
15. Klicka på **Stäng**.

## <a name="create-virtual-nat-network"></a>Skapa virtuellt NAT-nätverk

Nu när alla nödvändiga roller har installerats, är det dags att skapa NAT-nätverket.  Skapande processen innebär att du skapar en växel och NAT-nätverket.  Ett NAT-nätverk (Network Address Translation) tilldelar en offentlig IP-adress till en grupp med virtuella datorer i ett privat nätverk för att tillåta anslutning till Internet.  I vårt fall blir gruppen med privata virtuella datorer de kapslade virtuella datorerna.  NAT-nätverket gör att de kapslade virtuella datorerna kan kommunicera med varandra. En växel är en nätverks enhet som hanterar mottagning och routning av trafik i ett nätverk.

### <a name="create-a-new-virtual-switch"></a>Skapa en ny virtuell växel

1. Öppna **Hyper-V Manager** från administrations verktyg för Windows.
2. Välj den aktuella servern i den vänstra navigerings menyn.
3. Klicka på **hanteraren för virtuella växlar...** från menyn **åtgärder** på höger sida av **Hyper-V Manager**.
4. Välj **intern** för den typ av växel som ska skapas på popup-fönstret för **hanteraren för virtuella växlar** .  Klicka på **Skapa virtuell växel**.
5. För den nya virtuella växeln ställer du in namnet på något minnes värt.  I det här exemplet ska vi använda ' LabServicesSwitch '.  Klicka på **OK**.
6. Ett nytt nätverkskort kommer att skapas.  Namnet kommer att likna "vEthernet (LabServicesSwitch)".  Verifiera att öppna **kontroll panelen** genom att klicka på **nätverk och Internet**, klicka på **Visa nätverks status och uppgifter**.  Klicka på **ändra inställningar för nätverkskort** till vänster.

### <a name="create-a-nat-network"></a>Skapa ett NAT-nätverk

1. Öppna verktyget **Routning och fjärråtkomst** från administrativa verktyg i Windows.
2. Välj den lokala servern på den vänstra navigerings sidan.
3. Välj **åtgärd**  ->  **Konfigurera och aktivera Routning och fjärråtkomst**.
4. Klicka på **Nästa** när **guiden Konfigurera server för Routning och fjärråtkomst** visas.
5. På sidan **konfiguration** väljer du **NAT-konfiguration (Network Address Translation)** .  Klicka på **Nästa**.

    >[!WARNING]
    >Välj inte alternativet för åtkomst till virtuellt privat nätverk (VPN) och NAT.

6. På sidan **NAT-anslutning** väljer du "Ethernet".  Välj inte anslutningen "vEthernet (LabServicesSwitch)" som vi skapade i Hyper-V Manager. Klicka på **Nästa**.
7. Klicka på **Slutför** på den sista sidan i guiden.
8. När dialog rutan **starta tjänsten** visas klickar du på **Starta tjänst**.
9. Vänta tills tjänsten har startats.

## <a name="update-network-adapter-settings"></a>Uppdatera inställningarna för nätverkskort

Nätverkskortet kommer att associeras med den IP-adress som används för standardgateway-IP för det NAT-nätverk som skapades tidigare.  I det här exemplet skapar vi en IP-adress för 192.168.0.1 med nät masken 255.255.255.0.  Vi kommer att använda den virtuella växel som skapades tidigare.

1. Öppna **kontroll panelen**, klicka på **nätverk och Internet**, klicka på **Visa nätverks status och aktiviteter**.
2. Klicka på **ändra inställningar för nätverkskort** till vänster.  
3. I fönstret **nätverks anslutningar** dubbelklickar du på "VEthernet (LabServicesSwitch)" för att Visa dialog rutan **status information för vEthernet (LabServicesSwitch)** .
4. Klicka på knappen **Egenskaper** .
5. Välj **Internet Protocol version 4 (TCP/IPv4)** och klicka på knappen **Egenskaper** .
6. I dialog rutan **Egenskaper för Internet Protocol version 4 (TCP/IPv4)** väljer **du Använd följande IP-adress**.  För IP-adressen anger du 192.168.0.1. För nät masken anger du 255.255.255.0.  Lämna standard-gatewayen tom.  Låt även DNS-servrarna vara tomma.

    >[!NOTE]
    > Vårt utbud av vårt NAT-nätverk kommer att vara i CIDR-notation, 192.168.0.0/24.  Detta skapar ett utbud av användbara IP-adresser från 192.168.0.1 till 192.168.0.254.  Per konvention har gatewayer den första IP-adressen i ett under näts intervall.

7. Klicka på OK.

## <a name="create-dhcp-scope"></a>Skapa DHCP-omfång

Följande steg är instruktioner för att lägga till DHCP-scope.  I den här artikeln är vårt NAT-nätverk 192.168.0.0/24 i CIDR-format.  Detta skapar ett utbud av användbara IP-adresser från 192.168.0.1 till 192.168.0.254.  Det område som skapats måste vara inom det tillåtna intervallet för användbara adresser exklusive den redan skapade IP-adressen.

1. Öppna **administrations verktyg** och öppna administrations verktyget för **DHCP** .
2. I **DHCP-** verktyget expanderar du noden för den aktuella servern och väljer **IPv4**.
3. I åtgärd-menyn väljer du **nytt omfång...**
4. När **guiden Nytt scope** visas klickar du på **Nästa** på **välkomst** sidan.
5. På sidan **omfångs namn** anger du ' LabServicesDhcpScope ' eller något annat minnes värt för namnet.  Klicka på **Nästa**.
6. Ange följande värden på sidan **IP-adressintervall** .

    - 192.168.0.100 för den första IP-adressen
    - 192.168.0.200 för den sista IP-adressen
    - 24 för längd
    - 255.255.255.0 för nät masken

7. Klicka på **Nästa**.
8. På sidan **Lägg till undantag och fördröjning** klickar du på **Nästa**.
9. På sidan **låne tid** klickar du på **Nästa**.
10. På sidan **Konfigurera DHCP-alternativ** väljer du **Ja, jag vill konfigurera dessa alternativ nu**. Klicka på **Nästa**.
11. På **routern (standard-gateway)**
12. Lägg till 192.168.0.1, om du inte redan gjort det. Klicka på **Nästa**.
13. På sidan **domän namn och DNS-servrar** lägger du till 168.63.129.16 som en IP-adress för DNS-server, om du inte redan gjort det.  168.63.129.16 är IP-adressen för en statisk Azure-DNS-server. Klicka på **Nästa**.
14. På sidan **WINS-servrar** klickar du på **Nästa**.
15. På sidan **Aktivera omfattning** väljer du **Ja, jag vill aktivera det här omfånget nu**.  Klicka på **Nästa**.
16. Klicka på **Slutför** på sidan **Slutför guiden Nytt scope** .

## <a name="conclusion"></a>Slutsats

Nu är din mall maskin redo att skapa virtuella Hyper-V-datorer.   Instruktioner om hur du skapar virtuella Hyper-V-datorer finns i [skapa en virtuell dator i Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) .  Se även [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) för att ta en titt på tillgängliga operativ system och program.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)