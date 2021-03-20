---
title: Konfigurera routning av under näts trafik – Azure Traffic Manager
description: Den här artikeln förklarar hur du konfigurerar Traffic Manager att dirigera trafik från vissa undernät.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: duau
ms.openlocfilehash: b1901ddce2eb9c8ff5ec9ac90a56379e74c11aa6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994901"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Dirigera trafik till specifika slutpunkter baserat på användares undernät med Traffic Manager

Den här artikeln beskriver hur du konfigurerar trafikroutningsmetoden för undernät. Med trafikroutningsmetoden för **undernät** kan du mappa en uppsättning IP-adressintervall till specifika slutpunkter. När en begäran tas emot av Traffic Manager undersöks käll-IP-adressen för begäran och slutpunkten som är kopplad till den returneras.

I det scenario som beskrivs i den här artikeln, med hjälp av under näts dirigering, beroende på IP-adressen för användarens fråga, dirigeras trafiken antingen till en intern webbplats eller en produktions webbplats.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
Om du vill se hur Traffic Manager fungerar i praktiken behöver du använda följande i den här självstudien:
- två grundläggande webbplatser som körs i olika Azure-regioner – **USA, östra** (fungerar som intern webbplats) och **Europa, västra** (fungerar som produktionswebbplats).
- två virtuella testdatorer för att testa Traffic Manager – en virtuell dator i **USA, östra** och den andra virtuella datorn i **Europa, västra**.

De virtuella testdatorerna används för att illustrera hur Traffic Manager dirigerar trafik till den interna webbplatsen eller produktionswebbplatsen baserat på undernätet som användarfrågan kommer från.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två tjänstslutpunkter för Traffic Manager-profilen i två Azure-regioner. Att skapa två webbplatser omfattar följande steg:
1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i **USA, östra** och den andra i **Europa, västra**.
2. Installera IIS-servern på de båda virtuella datorerna och uppdatera standardwebbsidan som beskriver namnet på den virtuella datorn som en användare är ansluten när webbplatsen besöks.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser
I det här avsnittet skapar du två virtuella datorer *myEndpointVMEastUS* och *myEndpointVMWEurope* i Azure-regionerna **USA, östra** och Västeuropa. 

1. I det övre vänstra hörnet av Azure Portal väljer du **skapa en resurs**  >  **Compute**  >  **Windows Server 2016 VM**.
2. Ange eller välj följande information för **Grundinställningar**, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myIISVMEastUS|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resursgrupp| Välj **ny** och skriv sedan *myResourceGroupTM1*.|
    |Location| Välj **USA, östra**.|
    |||

4. Välj en VM-storlek i **Välj en storlek**.
5. Välj följande värden för **Inställningar** och sedan **OK**:
    
    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk| Välj **Virtuellt nätverk** i **Skapa virtuellt nätverk**. För **Namn** anger du *myVNet1* och för undernätet anger du *mySubnet*.|
    |Nätverkssäkerhetsgrupp|Välj **Grundläggande** och i listrutan **Välj offentliga inkommande portar** väljer du **HTTP** och **RDP** |
    |Startdiagnostik|Välj **Inaktiverad**.|
    |||

6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

7. Utför steg 1–6 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Resursgrupp | Välj **Ny** och skriv sedan *myResourceGroupTM2*|
    |Location|Europa, västra|
    |Namn på virtuell dator | myIISVMWEurope|
    |Virtuellt nätverk | Välj **Virtuellt nätverk** i **Skapa virtuellt nätverk**. För **Namn** anger du *myVNet2* och för undernätet anger du *mySubnet*.|
    |||

8. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

   ![Skapa en virtuell dator](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installera IIS och anpassa standardwebbsidan

I det här avsnittet installerar du IIS-servern på de två VM- *myIISVMEastUS*   &  *myIISVMWEurope* och uppdaterar sedan sidan standard webbplats. Den anpassade webbsidan visar namnet på den virtuella datorn som du ansluter till när du besöker webbplatsen från en webbläsare.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan klickar du sedan på *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. På server Skriv bordet navigerar du till **Windows administrations verktyg** > **Serverhanteraren**.
7. Starta Windows PowerShell på *myIISVMEastUS* och Använd följande kommandon för att installera IIS-servern och uppdatera standard-htm-filen.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Stäng RDP-anslutningen med *myIISVMEastUS*.
9. Upprepa steg 1-6 med genom att skapa en RDP-anslutning med VM- *myIISVMWEurope* i resurs gruppen *myResourceGroupTM2* för att installera IIS och anpassa standard webb sidan.
10. Starta Windows PowerShell på *myIISVMWEurope* och Använd följande kommandon för att installera IIS-servern och uppdatera standard-htm-filen.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurera DNS-namnen för de virtuella datorer som kör IIS

Traffic Manager dirigerar användartrafik baserat på tjänstslutpunkternas DNS-namn. I det här avsnittet konfigurerar du DNS-namn för IIS-servrarna- *myIISVMEastUS* och *myIISVMWEurope*.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan väljer du sedan *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** under **DNS-namn** väljer du **Konfigurera**.
3. På sidan **Konfiguration**, under DNS-namnetiketten, lägger du till ett unikt namn och sedan väljer du **Spara**.
4. Upprepa steg 1–3, för den virtuella datorn med namnet *myIISVMWEurope* som finns i resursgruppen *myResourceGroupTM1*.

### <a name="create-test-vms"></a>Skapa virtuella testdatorer

I det här avsnittet skapar du en virtuell dator (*mVMEastUS* och *myVMWestEurope*) i varje Azure-region (USA **,****östra** och Västeuropa). Du använder dessa virtuella datorer för att testa hur Traffic Manager dirigerar trafik till den närmaste IIS-servern när du surfar till webbplatsen.

1. I det övre vänstra hörnet av Azure Portal väljer du **skapa en resurs**  >  **Compute**  >  **Windows Server 2016 VM**.
2. Ange eller välj följande information för **Grundinställningar**, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVMEastUS|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resursgrupp| Välj **Befintlig** och sedan *myResourceGroupTM1*.|
    |||

4. Välj en VM-storlek i **Välj en storlek**.
5. Välj följande värden för **Inställningar** och sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk| Välj **Virtuellt nätverk** i **Skapa virtuellt nätverk**. För **Namn** anger du *myVNet3* och för undernätet anger du *mySubnet3*.|
    |Nätverkssäkerhetsgrupp|Välj **Grundläggande** och i listrutan **Välj offentliga inkommande portar** väljer du **HTTP** och **RDP** |
    |Startdiagnostik|Välj **Inaktiverad**.|
    |||

6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

7. Utför steg 1–5 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Namn på virtuell dator | *myVMWEurope*|
    |Resursgrupp | Välj **Befintlig** och skriv sedan *myResourceGroupTM2*|
    |Virtuellt nätverk | Välj **Virtuellt nätverk** i **Skapa virtuellt nätverk**. För **Namn** anger du *myVNet4* och för undernätet anger du *mySubnet4*.|
    |||

8. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil
Skapa en Traffic Manager-profil som gör det möjligt att returnera specifika slutpunkter baserat på käll-IP-adressen för begäran.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Traffic Manager profil**  >  **skapa**.
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och generera DNS-namnet, trafficmanager.net, som används för att öppna din Traffic Manager-profil.                                   |
    | Routningsmetod          | Välj routningsmetoden för **undernät**.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj **Befintlig** och ange *myResourceGroupTM1*. |
    | |                              |
    |

    ![Skapa en Traffic Manager-profil](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till de två virtuella datorerna som kör IIS-servrarna *myIISVMEastUS*  &  *myIISVMWEurope* för att dirigera användar trafik baserat på under nätet för användarens fråga.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Azure-slutpunkt                                   |
    | Name           | myTestWebSiteEndpoint                                        |
    | Målresurstyp           | Offentlig IP-adress                          |
    | Målresurs          | **Välj en offentlig IP-adress** för att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resurs** väljer du den offentliga IP-adressen med namnet *myIISVMEastUS-ip*. Det här är den offentliga IP-adressen för virtuella datorer med IIS i USA, östra.|
    |  Inställningar för undernätsroutning    |   Lägg till IP-adressen för *myVMEastUS* test VM. Alla användar frågor som kommer från den här virtuella datorn dirigeras till *myTestWebSiteEndpoint*.    |

4. Upprepa steg 2 och 3 för att lägga till en annan slut punkt med namnet *myProductionEndpoint* för den offentliga IP-adressen *myIISVMWEurope-IP* som är ASSOCIERAD med den virtuella IIS-serverdatorn med namnet *myIISVMWEurope*. För **Inställningar för under näts routning** lägger du till IP-adressen för den virtuella test- *myVMWestEurope*. Alla användar frågor från den här virtuella test datorn kommer att dirigeras till slut punkten- *myProductionWebsiteEndpoint*.
5. När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

    ![Lägga till en Traffic Manager-slutpunkt](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil
I det här avsnittet testar du hur Traffic Manager dirigerar användartrafik från ett visst undernät till en specifik slutpunkt. Om du vill se hur Traffic Manager fungerar i praktiken gör du följande:
1. Fastställ DNS-namnet för din Traffic Manager-profil.
2. Se hur Traffic Manager fungerar i praktiken:
    - Från den virtuella test datorn (*myVMEastUS*) som finns i regionen **USA, östra** , i en webbläsare, bläddrar du till DNS-namnet för din Traffic Manager-profil.
    - Från den virtuella test datorn (*myVMEastUS*) som finns i regionen **Europa, västra** , i en webbläsare, bläddrar du till DNS-namnet för din Traffic Manager-profil.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Fastställ DNS-namnet på Traffic Manager-profilen
I den här självstudien använder du för enkelhetens skull DNS-namnet för Traffic Manager-profilen för att besöka webbplatserna.

Så här kan du fastställa DNS-namnet i Traffic Manager-profilen:

1. I portalens sökfält söker du efter det **Traffic Manager-profil** namn som du skapade i föregående avsnitt. I det resultat som visas klickar du på Traffic Manager-profilen.
1. Klicka på **Översikt**.
2. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. I Produktionsdistributioner konfigurerar du ett anpassat domännamn så att den pekar till Traffic Manager-domännamnet, med hjälp av en DNS CNAME-post.

   ![DNS-namn för Traffic Manager](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken
I det här avsnittet får du se Traffic Manager i arbete.

1. Välj **Alla resurser** på menyn till vänster och klicka i listan över resurser på *myVMEastUS* som vinns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
1. I en webbläsare på den virtuella datorn *myVMEastUS* anger du DNS-namnet i Traffic Manager-profilen för at visa din webbplats. Eftersom den virtuella datorns *myVMEastUS* -IP-adress är associerad med slut punkts *MyIISVMEastUS* startar webbläsaren test webbplats servern- *myIISVMEastUS*.

   ![Testa Traffic Manager-profil](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Anslut sedan till den VM- *myVMWestEurope* **som finns i västeuropa** med steg 1-5 och bläddra till den Traffic Manager profilens domän namn från den här virtuella datorn. Eftersom den virtuella datorns *myVMWestEurope* -IP-adress är associerad med slut punkts *MyIISVMEastUS* startar webbläsaren test webbplats servern- *myIISVMWEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Ta bort Traffic Manager-profilen
När resursgrupperna inte längre behövs kan du ta bort dem (**ResourceGroupTM1** och **ResourceGroupTM2**). Om du vill göra det markerar du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioritetsroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
