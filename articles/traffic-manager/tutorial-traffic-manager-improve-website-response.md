---
title: Självstudie – förbättra webbplatsens svar med Azure Traffic Manager
description: Den här artikeln beskriver hur du skapar en Traffic Manager-profil för att skapa en webbplats med hög tillgänglighet.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: d8262a80fac42f103d571523c75c5064d5d43949
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003828"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Självstudie: förbättra webbplatsens svar med hjälp av Traffic Manager

I den här självstudien beskrivs hur du använder Traffic Manager för att skapa en webbplats med hög tillgänglighet genom att dirigera användar trafik till webbplatsen med den lägsta svars tiden. Normalt är data centret med den lägsta svars tiden det som är närmast det geografiska avståndet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer som kör en grundläggande webbplats i IIS
> * Skapa två virtuella testdatorer för att visa hur Traffic Manager fungerar i praktiken
> * Konfigurera DNS-namn för de virtuella datorer som kör IIS
> * Skapa en Traffic Manager-profil för förbättrade webbplats prestanda
> * Lägg till VM-slutpunkter i Traffic Manager-profilen
> * Se hur Traffic Manager fungerar i praktiken

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill se hur Traffic Manager fungerar i praktiken behöver du använda följande i den här självstudien:

- Två instanser av grundläggande webbplatser som körs i olika Azure-regioner – **östra USA** **och Västeuropa**.
- Två test- **VM: er** för att testa Traffic Manager-en VM i **östra USA** och den andra virtuella datorn i Västeuropa. De virtuella test datorerna används för att illustrera hur Traffic Manager dirigerar användar trafik till webbplatsen som körs i samma region som den ger den lägsta svars tiden.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två tjänstslutpunkter för Traffic Manager-profilen i två Azure-regioner. Att skapa två webbplatser omfattar följande steg:

1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i **USA, östra** och den andra i **Europa, västra**.
2. Installera IIS-servern på de båda virtuella datorerna och uppdatera standardwebbsidan som beskriver namnet på den virtuella datorn som en användare är ansluten när webbplatsen besöks.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser

I det här avsnittet skapar du två virtuella datorer *myIISVMEastUS* och *myIISVMWestEurope* i Azure-regionerna **USA, östra** och Västeuropa. 

1. I det övre vänstra hörnet av Azure Portal väljer du **skapa en resurs**  >  **Compute**  >  **Windows Server 2019 Data Center**.
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Prenumeration**  >  **Resurs grupp**: Välj **Skapa ny** och skriv sedan **myResourceGroupTM1**.
   - **Instans information**  >  **Namn på virtuell dator**: Skriv *myIISVMEastUS*.
   - **Instans information**  >  **Region**: Välj **USA, östra**.
   - **Administratörs konto**  >  **Användar** namn: Ange ett användar namn som du väljer.
   - **Administratörs konto**  >  **Lösen ord**: Ange ett lösen ord som du väljer. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - Regler för inkommande **portar**  >  **Offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - Regler för inkommande **portar**  >  **Välj inkommande portar**: Välj **RDP** och **http** i rutan Hämta.

3. Välj fliken **hantering** eller Välj **Nästa: diskar**, klicka sedan på **Nästa: nätverk**, och sedan på **Nästa: hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Granska inställningarna och klicka sedan på **skapa**.  
6. Följ stegen för att skapa en andra virtuell dator med namnet *myIISVMWestEurope*, med **resurs grupp** namnet *myResourceGroupTM2*, en **plats** för *Västeuropa och alla* andra inställningar på samma sätt som *myIISVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

   ![Skapa en virtuell dator](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installera IIS och anpassa standardwebbsidan

I det här avsnittet installerar du IIS-servern på de två VM- *myIISVMEastUS* och *myIISVMWestEurope* och uppdaterar sedan sidan standard webbplats. Den anpassade webbsidan visar namnet på den virtuella datorn som du ansluter till när du besöker webbplatsen från en webbläsare.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan klickar du sedan på *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. På server Skriv bordet navigerar du till **Windows administrations verktyg** > **Serverhanteraren**.
7. Starta Windows PowerShell på VM1 och använd följande kommandon för att installera IIS-servern och uppdatera standard-HTML-filen.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Installera IIS och anpassa webbsida](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Stäng RDP-anslutningen med *myIISVMEastUS*.
9. Upprepa steg 1-8 med genom att skapa en RDP-anslutning med VM- *myIISVMWestEurope* i resurs gruppen *myResourceGroupTM2* för att installera IIS och anpassa standard webb sidan.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurera DNS-namnen för de virtuella datorer som kör IIS

Traffic Manager dirigerar användartrafik baserat på tjänstslutpunkternas DNS-namn. I det här avsnittet konfigurerar du DNS-namn för IIS-servrarna- *myIISVMEastUS* och *myIISVMWestEurope*.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan väljer du sedan *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** under **DNS-namn** väljer du **Konfigurera**.
3. På sidan **Konfiguration**, under DNS-namnetiketten, lägger du till ett unikt namn och sedan väljer du **Spara**.
4. Upprepa steg 1-3 för den virtuella datorn med namnet *myIISVMWestEurope* som finns i resurs gruppen *myResourceGroupTM2* .

### <a name="create-test-vms"></a>Skapa virtuella testdatorer

I det här avsnittet skapar du en virtuell dator (*myVMEastUS* och *myVMWestEurope*) i varje Azure-region (USA **,****östra** och Västeuropa). Du använder dessa virtuella datorer för att testa hur Traffic Manager dirigerar trafik till den närmaste IIS-servern när du surfar till webbplatsen.

1. I det övre vänstra hörnet av Azure Portal väljer du **skapa en resurs**  >  **Compute**  >  **Windows Server 2019 Data Center**.
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Prenumeration**  >  **Resurs grupp**: Välj **myResourceGroupTM1**.
   - **Instans information**  >  **Namn på virtuell dator**: Skriv *myVMEastUS*.
   - **Instans information**  >  **Region**: Välj **USA, östra**.
   - **Administratörs konto**  >  **Användar** namn: Ange ett användar namn som du väljer.
   - **Administratörs konto**  >  **Lösen ord**: Ange ett lösen ord som du väljer. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - Regler för inkommande **portar**  >  **Offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - Regler för inkommande **portar**  >  **Välj inkommande portar**: Välj **RDP** i list rutan.

3. Välj fliken **hantering** eller Välj **Nästa: diskar**, klicka sedan på **Nästa: nätverk**, och sedan på **Nästa: hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Granska inställningarna och klicka sedan på **skapa**.  
6. Följ stegen för att skapa en andra virtuell dator med namnet *myVMWestEurope*, med **resurs grupp** namnet *myResourceGroupTM2*, en **plats** för *Västeuropa och alla* andra inställningar på samma sätt som *myVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager profil som dirigerar användar trafiken genom att skicka dem till slut punkten med lägsta latens.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Traffic Manager profil**  >  **skapa**.
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och generera DNS-namnet, trafficmanager.net, som används för att öppna din Traffic Manager-profil.                                   |
    | Routningsmetod          | Välj routningsmetod för **prestanda** .                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj resurs gruppen *myResourceGroupTM1*. |
    | Location                | Välj **USA, östra**. Inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.                              |
    |

    ![Skapa en Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till de två virtuella datorerna som kör IIS-servrarna *myIISVMEastUS*  &  *myIISVMWestEurope* för att dirigera användar trafik till den närmaste slut punkten för användaren.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Azure-slutpunkt                                   |
    | Name           | myEastUSEndpoint                                        |
    | Målresurstyp           | Offentlig IP-adress                          |
    | Målresurs          | **Välj en offentlig IP-adress** för att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resurs** väljer du den offentliga IP-adressen med namnet *myIISVMEastUS-ip*. Det här är den offentliga IP-adressen för virtuella datorer med IIS i USA, östra.|
    |        |           |

4. Upprepa steg 2 och 3 för att lägga till en annan slut punkt med namnet *myWestEuropeEndpoint* för den offentliga IP-adressen *myIISVMWestEurope-IP* som är ASSOCIERAD med den virtuella IIS-serverdatorn med namnet *myIISVMWestEurope*.
5. När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

    ![Lägga till en Traffic Manager-slutpunkt](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet ska du testa hur Traffic Manager dirigerar användar trafik till närmaste virtuella datorer som kör webbplatsen för att få en lägsta latens. Om du vill se hur Traffic Manager fungerar i praktiken gör du följande:

1. Fastställ DNS-namnet för din Traffic Manager-profil.
2. Se hur Traffic Manager fungerar i praktiken:
    - Från den virtuella test datorn (*myVMEastUS*) som finns i regionen **USA, östra** , i en webbläsare, bläddrar du till DNS-namnet för din Traffic Manager-profil.
    - Från den virtuella test datorn (*myVMWestEurope*) som finns i regionen **Europa, västra** , i en webbläsare, bläddrar du till DNS-namnet för din Traffic Manager-profil.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Fastställ DNS-namnet på Traffic Manager-profilen

I den här självstudien använder du för enkelhetens skull DNS-namnet för Traffic Manager-profilen för att besöka webbplatserna.

Så här kan du fastställa DNS-namnet i Traffic Manager-profilen:

1. I portalens sökfält söker du efter det **Traffic Manager-profil** namn som du skapade i föregående avsnitt. I det resultat som visas klickar du på Traffic Manager-profilen.
2. Klicka på **Översikt**.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. I Produktionsdistributioner konfigurerar du ett anpassat domännamn så att den pekar till Traffic Manager-domännamnet, med hjälp av en DNS CNAME-post.

   ![DNS-namn för Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

I det här avsnittet får du se Traffic Manager i arbete.

1. Välj **Alla resurser** på menyn till vänster och klicka i listan över resurser på *myVMEastUS* som vinns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
1. I en webbläsare på den virtuella datorn *myVMEastUS* anger du DNS-namnet i Traffic Manager-profilen för at visa din webbplats. Eftersom den virtuella datorn finns i **östra USA** dirigeras du till den närmaste webbplats som finns på den närmaste IIS- *myIISVMEastUS* som finns i **USA, östra**.

   ![Skärm bild som visar "Traffic Manager"-profilen i en webbläsare.](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Anslut sedan till den VM- *myVMWestEurope* **som finns i västeuropa** med steg 1-5 och bläddra till den Traffic Manager profilens domän namn från den här virtuella datorn. Eftersom den virtuella datorn finns i **Västeuropa** dirigeras du nu till webbplatsen som finns på närmaste IIS- *myIISVMWestEurope* som finns i **Västeuropa**.

   ![Testa Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="clean-up-resources"></a>Rensa resurser

När resursgrupperna inte längre behövs kan du ta bort dem (**ResourceGroupTM1** och **ResourceGroupTM2**). Om du vill göra det markerar du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera trafik till en uppsättning slutpunkter](traffic-manager-configure-weighted-routing-method.md)
