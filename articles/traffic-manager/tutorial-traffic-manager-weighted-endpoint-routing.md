---
title: 'Självstudie: dirigera trafik till viktade slut punkter – Azure Traffic Manager'
description: Den här artikeln beskriver hur du dirigerar trafik till viktade slutpunkter med Traffic Manager.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: 55c316a370b9e44e906e48b4716201384567c9c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003794"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Självstudie: Styr trafikroutning med viktade slutpunkter med hjälp av Traffic Manager

Den här självstudien beskriver hur du använder Azure Traffic Manager till att styra routningen av användartrafik mellan slutpunkter med hjälp av routningsmetoden Viktat. I den här routningsmetoden tilldelar du vikter till varje slutpunkt i Traffic Manager-profilkonfigurationen. Sedan dirigeras användartrafiken baserat på vikt som tilldelats varje slutpunkt. Vikten är ett heltal mellan 1 och 1000. Ju högre viktningsvärde som tilldelas till en slutpunkt desto högre prioritet har det.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer som kör en grundläggande webbplats i IIS.
> * Skapa två virtuella testdatorer för att visa hur Traffic Manager fungerar i praktiken.
> * Konfigurera ett DNS-namn för de virtuella datorer som kör IIS.
> * Skapa en Traffic Manager-profil.
> * Lägg till VM-slutpunkter i Traffic Manager-profilen.
> * Visa Traffic Manager i aktion.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill se hur Traffic Manager fungerar distribuerar du följande för den här självstudien:

- Två instanser av grundläggande webbplatser i olika Azure-regioner – USA, östra och Europa, västra.
- Två virtuella testdatorer för att testa Traffic Manager: en i USA, östra och den andra vi Europa, västra. De virtuella testdatorerna används till att illustrera hur Traffic Manager dirigerar användartrafik som har högre vikt tilldelad till sin slutpunkt.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två tjänstslutpunkter för Traffic Manager-profilen i två Azure-regioner. Skapa de två webbplatserna genom att göra följande:

1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i USA, östra och den andra i Europa, västra.
2. Installera en IIS-server på varje virtuell dator. Uppdatera standardwebbsidan som beskriver namnet på den virtuella datorn som en användare är ansluten när webbplatsen besöks.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser

I det här avsnittet skapar du två virtuella datorer (*myIISVMEastUS* och *MyIISVMWestEurope*) i Azure-regionerna USA, östra och Västeuropa.

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

#### <a name="install-iis-and-customize-the-default-webpage"></a>Installera IIS och anpassa standardwebbsidan

I det här avsnittet installerar du IIS-servern på de två virtuella datorerna myIISVMEastUS och myIISVMWestEurope och uppdaterar sedan standard webb sidan. Den anpassade webbsidan visar namnet på den virtuella datorn som du ansluter till när du besöker webbplatsen från en webbläsare.

1. Välj **Alla resurser** på menyn till vänster. I resurslistan väljer du **myIISVMEastUS** i resursgruppen **myResourceGroupTM1**.
2. På **översiktssidan** väljer du **Anslut**. I **Anslut till virtuell dator** väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange det namn och lösenord du angav när du skapade den virtuella datorn. Du kan behöva välja **fler alternativ**  >  för att ange de autentiseringsuppgifter som du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du får varningen väljer du **Ja** eller **fortsätter** att fortsätta med anslutningen.
6. På server Skriv bordet bläddrar du till **administrations verktyg för Windows**  >  **Serverhanteraren**.
7. Öppna Windows PowerShell på VM1. Använd följande kommandon för att installera IIS-servern och uppdatera standard-HTML-filen.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![Installera IIS och anpassa webbsidan](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Stäng RDP-anslutningen med **myIISVMEastUS**.
9. Upprepa steg 1–8. Skapa en RDP-anslutning med VM- **myIISVMWestEurope** i resurs gruppen **myResourceGroupTM2** för att installera IIS och anpassa dess standard webb sida.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurera DNS-namnen för de virtuella datorer som kör IIS

Traffic Manager dirigerar användartrafik baserat på tjänstslutpunkternas DNS-namn. I det här avsnittet konfigurerar du DNS-namn för IIS-servrarna myIISVMEastUS och myIISVMWestEurope.

1. Välj **Alla resurser** på menyn till vänster. I resurslistan väljer du **myIISVMEastUS** i resursgruppen **myResourceGroupTM1**.
2. På sidan **Översikt** under **DNS-namn** väljer du **Konfigurera**.
3. På sidan **Konfiguration**, under DNS-namnetiketten, lägger du till ett unikt namn. Välj sedan **Spara**.
4. Upprepa steg 1-3 för den virtuella datorn med namnet **myIISVMWestEurope** i resurs gruppen **myResourceGroupTM2** .

### <a name="create-a-test-vm"></a>Skapa en virtuell testdator

I det här avsnittet skapar du en virtuell dator (*myVMEastUS* och *myVMWestEurope*) i varje Azure-region (USA **,****östra** och Västeuropa). Du kommer att använda de här virtuella datorerna för att testa hur Traffic Manager dirigerar trafik till webbplats slut punkten som har det högre viktning svärdet.

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

Skapa en Traffic Manager-profil baserat på routningsmetoden **Viktat**.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Traffic Manager profil**  >  **skapa**.
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information. Acceptera standardinställningarna för de andra inställningarna och välj sedan **skapa**.

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Ange ett unikt namn inom trafficmanager.net-zonen. Det resulterar i DNS-namnet trafficmanager.net, som används för att få åtkomst till Traffic Manager-profilen.                                   |
    | Routningsmetod          | Välj routningsmetoden **Viktat**.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj **Använd befintlig** och sedan **myResourceGroupTM1**. |
    |        |   |

    ![Skapa en Traffic Manager-profil](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till de två virtuella datorerna som kör IIS-servrarna myIISVMEastUS och myIISVMWestEurope för att dirigera användar trafik till dem.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt. Välj profilen i resultaten som visas.
2. Klicka på **Slutpunkter** på bladet **Traffic Manager-profil** i avsnittet **Inställningar** och välj  > **Lägg till**.
3. Ange eller välj följande information. Acceptera standardinställningarna för de andra inställningarna och välj sedan **OK**.

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Ange Azure-slutpunkten.                                   |
    | Name           | Ange **myEastUSEndpoint**.                                        |
    | Målresurstyp           | Välj **Offentlig IP-adress**.                          |
    | Målresurs          | Välj en offentlig IP-adress för att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resurs** väljer du den offentliga IP-adressen med namnet **myIISVMEastUS-ip**. Det här är den offentliga IP-adressen för virtuella datorer med IIS i USA, östra.|
    |  Vikt      | Ange **100**.        |
    |        |           |

4. Upprepa steg 2 och 3 för att lägga till en annan slut punkt med namnet **myWestEuropeEndpoint** för den offentliga IP-adressen **myIISVMWestEurope-IP**. Den här adressen är kopplad till IIS-serverns virtuella dator med namnet myIISVMWestEurope. Som **vikt** anger du **25**.
5. När båda slutpunkterna har lagts till visas de i Traffic Manager-profilen tillsammans med sin övervakningsstatus, som är **Online**.

## <a name="test-the-traffic-manager-profile"></a>Testa Traffic Manager-profilen

Om du vill se hur Traffic Manager fungerar i praktiken gör du följande:

1. Fastställ DNS-namnet för din Traffic Manager-profil.
2. Visa Traffic Manager i aktion.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Fastställ DNS-namnet på Traffic Manager-profilen

I den här självstudien använder du för enkelhetens skull DNS-namnet för Traffic Manager-profilen för att besöka webbplatserna.

Så här kan du fastställa DNS-namnet i Traffic Manager-profilen:

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt. I det resultat som visas väljer du Traffic Manager-profilen.
2. Välj **Översikt**.
3. Traffic Manager-profilen visar DNS-namnet. I Produktionsdistributioner konfigurerar du ett anpassat domännamn så att den pekar till Traffic Manager-domännamnet, med hjälp av en DNS CNAME-post.

   ![DNS-namn för Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

I det här avsnittet får du se Traffic Manager i arbete.

1. Välj **Alla resurser** på menyn till vänster. I resurslistan väljer du **myVMEastUS** i resursgruppen **myResourceGroupTM1**.
2. På **översiktssidan** väljer du **Anslut**. I **Anslut till virtuell dator** väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **fler alternativ**  >  för att ange de autentiseringsuppgifter som du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du får varningen väljer du **Ja** eller **fortsätter** att fortsätta med anslutningen.
6. I en webbläsare på den virtuella datorn myVMEastUS anger du DNS-namnet i Traffic Manager-profilen för at visa din webbplats. Du dirigeras till en webbplats på IIS-servern myIISVMEastUS eftersom den har tilldelats en högre vikt på **100**. IIS-myIISVMWestEurope tilldelas ett lägre värde för slut punkts vikt på **25**.

   ![Testa Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Upprepa steg 1-6 på VM-myVMWestEurope för att se svaret på den viktade webbplatsen.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resursgrupper som skapades i den här självstudien kan du ta bort dem. Om du vill göra det markerar du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information om routningsmetoder finns i:

> [!div class="nextstepaction"]
> [Traffic routing-metod](traffic-manager-routing-methods.md)
