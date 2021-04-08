---
title: Ansluta en virtuell Windows Server-dator till en Azure AD Domain Services hanterad domän | Microsoft Docs
description: I den här självstudien får du lära dig hur du ansluter en virtuell Windows Server-dator till en Azure Active Directory Domain Services hanterad domän.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 869c827485d9b7a6baf68d2619af98d4c2ee82b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619580"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Självstudie: ansluta en virtuell Windows Server-dator till en Azure Active Directory Domain Services hanterad domän

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibelt med Windows Server Active Directory. Med en Azure AD DS-hanterad domän kan du tillhandahålla funktioner för domän anslutning och hantering av virtuella datorer i Azure. Den här självstudien visar hur du skapar en virtuell Windows Server-dator och ansluter den till en hanterad domän.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows Server-dator
> * Ansluta den virtuella Windows Server-datorn till ett virtuellt Azure-nätverk
> * Anslut den virtuella datorn till den hanterade domänen

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* Ett användar konto som är en del av den hanterade domänen.
    * Kontrol lera att Azure AD Connect hash-synkronisering av lösen ord eller lösen ords återställning via självbetjäning har utförts så att kontot kan logga in på den hanterade domänen.
* En Azure skydds-värd som har distribuerats i Azure AD DS Virtual Network.
    * Om det behövs [skapar du en Azure skydds-värd][azure-bastion].

Om du redan har en virtuell dator som du vill ansluta till, kan du gå vidare till avsnittet för att [ansluta den virtuella datorn till den hanterade domänen](#join-the-vm-to-the-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar du en virtuell Windows Server-dator för att ansluta till din hanterade domän med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="create-a-windows-server-virtual-machine"></a>Skapa en virtuell Windows Server-dator

För att se hur du ansluter en dator till en hanterad domän, ska vi skapa en virtuell Windows Server-dator. Den här virtuella datorn är ansluten till ett virtuellt Azure-nätverk som tillhandahåller anslutning till den hanterade domänen. Processen för att ansluta till en hanterad domän är detsamma som att ansluta till en vanlig lokal Active Directory Domain Services domän.

Om du redan har en virtuell dator som du vill ansluta till, kan du gå vidare till avsnittet för att [ansluta den virtuella datorn till den hanterade domänen](#join-the-vm-to-the-managed-domain).

1. Från Azure Portal-menyn eller från **Start** sidan väljer du **skapa en resurs**.

1. Från **Kom igång** väljer du **Windows Server 2016 Data Center**.

    ![Välj att skapa en virtuell Windows Server 2016 datacenter-dator i Azure Portal](./media/join-windows-vm/select-vm-image.png)

1. I fönstret **grundläggande** inställningar konfigurerar du kärn inställningarna för den virtuella datorn. Lämna standardinställningarna för *tillgänglighets alternativ*, *bild* och *storlek*.

    | Parameter            | Föreslaget värde   |
    |----------------------|-------------------|
    | Resursgrupp       | Välj eller skapa en resurs grupp, till exempel *myResourceGroup* |
    | Namn på virtuell dator | Ange ett namn för den virtuella datorn, till exempel *myVM* |
    | Region               | Välj region för att skapa din virtuella dator i, t. ex. *USA, östra* |
    | Användarnamn             | Ange ett användar namn för det lokala administratörs kontot som ska skapas på den virtuella datorn, till exempel *azureuser* |
    | Lösenord             | Ange och bekräfta sedan ett säkert lösen ord för den lokala administratören som ska skapas på den virtuella datorn. Ange inte autentiseringsuppgifter för ett domän användar konto. |

1. Som standard är virtuella datorer som skapats i Azure tillgängliga från Internet via RDP. När RDP är aktiverat, kommer automatiska inloggnings attacker att uppstå, vilket kan inaktivera konton med vanliga namn som *administratör* eller *administratör* på grund av flera misslyckade inloggnings försök.

    RDP bör endast aktive ras vid behov, och begränsas till en uppsättning auktoriserade IP-intervall. Den här konfigurationen hjälper till att förbättra säkerheten på den virtuella datorn och minskar risken för potentiella angrepp. Du kan också skapa och använda en Azure skydds-värd som endast tillåter åtkomst via Azure Portal via TLS. I nästa steg i den här självstudien använder du en Azure skydds-värd för att ansluta till den virtuella datorn på ett säkert sätt.

    Under **offentliga inkommande portar** väljer du *ingen*.

1. När du är färdig väljer du **Nästa: diskar**.
1. Välj *standard SSD* på den nedrullningsbara menyn för **typ av operativ system disk** och välj sedan **Nästa: nätverk**.
1. Din virtuella dator måste ansluta till ett virtuellt nätverk i Azure som kan kommunicera med det undernät som din hanterade domän har distribuerats till. Vi rekommenderar att en hanterad domän distribueras till ett eget dedikerat undernät. Distribuera inte den virtuella datorn i samma undernät som den hanterade domänen.

    Det finns två huvudsakliga sätt att distribuera den virtuella datorn och ansluta till ett lämpligt virtuellt nätverks under nät:
    
    * Skapa ett, eller Välj ett befintligt undernät i samma virtuella nätverk som din hanterade domän är distribuerad.
    * Välj ett undernät i ett virtuellt Azure-nätverk som är anslutet till det med [Azure Virtual Network-peering][vnet-peering].
    
    Om du väljer ett undernät för virtuellt nätverk som inte är anslutet till under nätet för din hanterade domän, kan du inte ansluta den virtuella datorn till den hanterade domänen. I den här självstudien ska vi skapa ett nytt undernät i det virtuella Azure-nätverket.

    I fönstret **nätverk** väljer du det virtuella nätverk som din hanterade domän är distribuerad till, till exempel *aaads-VNet*
1. I det här exemplet visas det befintliga *aaads-under nätet* som den hanterade domänen är ansluten till. Anslut inte den virtuella datorn till det här under nätet. Om du vill skapa ett undernät för den virtuella datorn väljer du **Hantera under näts konfiguration**.

    ![Välj att hantera under näts konfigurationen i Azure Portal](./media/join-windows-vm/manage-subnet.png)

1. I den vänstra menyn i fönstret virtuellt nätverk väljer du **adress utrymme**. Det virtuella nätverket skapas med ett enda adress utrymme på *10.0.2.0/24*, som används av standard under nätet. Andra undernät, till exempel för *arbets belastningar* eller Azure-skydds, kan också redan finnas.

    Lägg till ytterligare ett IP-adressintervall i det virtuella nätverket. Storleken på det här adress intervallet och det faktiska IP-adressintervall som ska användas beror på andra nätverks resurser som redan har distribuerats. IP-adressintervallet får inte överlappa några befintliga adress intervall i din Azure eller lokala miljö. Se till att du ändrar storlek på IP-adressintervallet tillräckligt stort för antalet virtuella datorer som du förväntar dig att distribuera till under nätet.

    I följande exempel läggs ytterligare ett IP-adressintervall för *10.0.5.0/24* till. När du är klar väljer du **Spara**.

    ![Lägg till ytterligare ett IP-adressintervall för virtuellt nätverk i Azure Portal](./media/join-windows-vm/add-vnet-address-range.png)

1. Sedan väljer du **undernät** på menyn till vänster i fönstret virtuellt nätverk och väljer sedan **+ undernät** för att lägga till ett undernät.

1. Välj **+ undernät** och ange sedan ett namn för under nätet, till exempel *hantering*. Ange ett **adress intervall (CIDR-block)**, till exempel *10.0.5.0/24*. Kontrol lera att detta IP-adressintervall inte överlappar andra befintliga Azure-eller lokala adress intervall. Lämna de andra alternativen som standardvärden och välj sedan **OK**.

    ![Skapa en under näts konfiguration i Azure Portal](./media/join-windows-vm/create-subnet.png)

1. Det tar några sekunder att skapa under nätet. När den har skapats väljer du *X* för att stänga under näts fönstret.
1. Gå tillbaka till fönstret **nätverk** för att skapa en virtuell dator och välj det undernät som du skapade i den nedrullningsbara menyn, till exempel *hantering*. Se till att du väljer rätt undernät och distribuera inte den virtuella datorn i samma undernät som den hanterade domänen.
1. För **offentlig IP-adress** väljer du *ingen* på den nedrullningsbara menyn. När du använder Azure-skydds i den här självstudien för att ansluta till-hanteringen behöver du inte någon offentlig IP-adress som tilldelats den virtuella datorn.
1. Lämna de andra alternativen som standardvärden och välj sedan **hantering**.
1. Ange att **startdiagnostik** ska *stängas av*. Lämna de andra alternativen som standardvärden och välj sedan **Granska + skapa**.
1. Granska inställningarna för den virtuella datorn och välj sedan **skapa**.

Det tar några minuter att skapa den virtuella datorn. I Azure Portal visas distributionens status. När den virtuella datorn är klar väljer **du gå till resurs**.

![Gå till den virtuella dator resursen i Azure Portal när den har skapats](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Anslut till den virtuella Windows Server-datorn

Använd en Azure skydds-värd för att på ett säkert sätt ansluta till dina virtuella datorer. Med Azure skydds distribueras en hanterad värd till det virtuella nätverket och tillhandahåller webbaserade RDP-eller SSH-anslutningar till virtuella datorer. Inga offentliga IP-adresser krävs för de virtuella datorerna, och du behöver inte öppna regler för nätverks säkerhets grupper för extern fjärrtrafik. Du ansluter till virtuella datorer med hjälp av Azure Portal från webbläsaren. Om det behövs [skapar du en Azure skydds-värd][azure-bastion].

Utför följande steg för att använda en skydds-värd för att ansluta till din virtuella dator:

1. I **översikts** fönstret för den virtuella datorn väljer du **Anslut** och sedan **skydds**.

    ![Ansluta till en virtuell Windows-dator med skydds i Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Ange autentiseringsuppgifterna för den virtuella dator som du angav i föregående avsnitt och välj sedan **Anslut**.

   ![Anslut genom skydds-värden i Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

Om det behövs kan du tillåta att webbläsaren öppnar popup-fönster för att skydds-anslutningen ska visas. Det tar några sekunder att upprätta anslutningen till den virtuella datorn.

## <a name="join-the-vm-to-the-managed-domain"></a>Anslut den virtuella datorn till den hanterade domänen

När den virtuella datorn har skapats och en webbaserad RDP-anslutning har etablerats med hjälp av Azure skydds kan du nu ansluta den virtuella Windows Server-datorn till den hanterade domänen. Den här processen är samma som en dator som ansluter till en vanlig lokal Active Directory Domain Services domän.

1. Om **Serverhanteraren** inte öppnas som standard när du loggar in på den virtuella datorn väljer du **Start** -menyn och väljer **Serverhanteraren**.
1. I den vänstra rutan i fönstret **Serverhanteraren** väljer du **lokal server**. Välj **arbets grupp** under **Egenskaper** i den högra rutan.

    ![Öppna Serverhanteraren på den virtuella datorn och redigera arbets grupps egenskapen](./media/join-windows-vm/server-manager.png)

1. I fönstret **system egenskaper** väljer du **ändra** för att ansluta till den hanterade domänen.

    ![Välj att ändra egenskaper för arbets grupp eller domän](./media/join-windows-vm/change-domain.png)

1. I rutan **domän** anger du namnet på din hanterade domän, till exempel *aaddscontoso.com*, och väljer sedan **OK**.

    ![Ange den hanterade domänen som ska anslutas](./media/join-windows-vm/join-domain.png)

1. Ange domänautentiseringsuppgifter för att ansluta till domänen. Ange autentiseringsuppgifter för en användare som är en del av den hanterade domänen. Kontot måste vara en del av den hanterade domänen eller Azure AD-klient-konton från externa kataloger som är associerade med din Azure AD-klient kan inte autentiseras korrekt under processen för domän anslutning.

    Kontoautentiseringsuppgifter kan anges på något av följande sätt:

    * **UPN-format** (rekommenderas) – ange suffixet User Principal Name (UPN) för användar kontot, enligt konfigurationen i Azure AD. UPN-suffixet för användaren *contosoadmin* skulle till exempel vara `contosoadmin@aaddscontoso.onmicrosoft.com` . Det finns ett par vanliga användnings fall där UPN-formatet kan användas på ett tillförlitligt sätt för att logga in på domänen snarare än *sAMAccountName* -formatet:
        * Om en användares UPN-prefix är långt, till exempel *deehasareallylongname*, kan *sAMAccountName* skapas automatiskt.
        * Om flera användare har samma UPN-prefix i din Azure AD-klient, till exempel *Dee*, kan deras *sAMAccountName* -format skapas automatiskt.
    * **SAMAccountName-format** – ange konto namnet i *sAMAccountName* -formatet. Till exempel skulle *sAMAccountName* för User *contosoadmin* vara `AADDSCONTOSO\contosoadmin` .

1. Det tar några sekunder att ansluta till den hanterade domänen. När du är klar, välkomnar följande meddelande till domänen:

    ![Välkommen till domänen](./media/join-windows-vm/join-domain-successful.png)

    Klicka på **OK** för att fortsätta.

1. Om du vill slutföra processen för att ansluta till den hanterade domänen startar du om den virtuella datorn.

> [!TIP]
> Du kan domän ansluta till en virtuell dator med hjälp av PowerShell med cmdleten [Add-Computer][add-computer] . Följande exempel ansluter till *AADDSCONTOSO* -domänen och startar sedan om den virtuella datorn. När du uppmanas till det anger du autentiseringsuppgifterna för en användare som är en del av den hanterade domänen:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Om du vill ansluta till en virtuell dator utan att ansluta till den och manuellt konfigurera anslutningen kan du använda cmdleten [set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell.

När den virtuella Windows Server-datorn har startats om flyttas alla principer som tillämpas i den hanterade domänen till den virtuella datorn. Nu kan du logga in på den virtuella Windows Server-datorn med rätt domänautentiseringsuppgifter.

## <a name="clean-up-resources"></a>Rensa resurser

I nästa självstudie använder du den här virtuella Windows Server-datorn för att installera hanterings verktygen som du kan använda för att administrera den hanterade domänen. Om du inte vill fortsätta i den här själv studie serien läser du följande rensnings steg för att [ta bort den virtuella datorn](#delete-the-vm). Annars [fortsätter du till nästa självstudie](#next-steps).

### <a name="unjoin-the-vm-from-the-managed-domain"></a>Ta en anslutning till den virtuella datorn från den hanterade domänen

Ta bort den virtuella datorn från den hanterade domänen genom att följa stegen igen för att [ansluta den virtuella datorn till en domän](#join-the-vm-to-the-managed-domain). I stället för att ansluta till den hanterade domänen väljer du att ansluta till en arbets grupp, till exempel standard *arbets gruppen*. När den virtuella datorn har startats om tas datorobjektet bort från den hanterade domänen.

Om du [tar bort den virtuella](#delete-the-vm) datorn utan att koppla från domänen, lämnas ett överblivna dator objekt i Azure AD DS.

### <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Om du inte använder den här virtuella Windows Server-datorn tar du bort den virtuella datorn med följande steg:

1. Välj **resurs grupper** på den vänstra menyn.
1. Välj din resurs grupp, till exempel *myResourceGroup*.
1. Välj den virtuella datorn, till exempel *myVM*, och välj sedan **ta bort**. Bekräfta borttagningen av resurs genom att välja **Ja** . Det tar några minuter att ta bort den virtuella datorn.
1. När den virtuella datorn tas bort väljer du OS-disken, nätverkskortet och andra resurser med *myVM-* prefixet och tar bort dem.

## <a name="troubleshoot-domain-join-issues"></a>Felsöka problem med domän anslutning

Den virtuella Windows Server-datorn ska kunna ansluta till den hanterade domänen, samma sätt som en vanlig lokal dator skulle ansluta till en Active Directory Domain Services domän. Om den virtuella Windows Server-datorn inte kan ansluta till den hanterade domänen, indikerar det att det finns ett anslutnings-eller autentiseringsuppgifter relaterat problem. Läs följande fel söknings avsnitt för att lyckas ansluta till den hanterade domänen.

### <a name="connectivity-issues"></a>Anslutningsproblem

Om du inte får en uppmaning om att fråga om autentiseringsuppgifter för att ansluta till domänen, finns det ett anslutnings problem. Den virtuella datorn kan inte komma åt den hanterade domänen på det virtuella nätverket.

Försök att ansluta den virtuella Windows Server-datorn till den hanterade domänen igen efter att ha försökt med de här fel söknings stegen.

* Kontrol lera att den virtuella datorn är ansluten till samma virtuella nätverk som Azure AD DS är aktiverat i eller har en peer-ansluten nätverks anslutning.
* Försök att pinga DNS-domännamnet för den hanterade domänen, till exempel `ping aaddscontoso.com` .
    * Om ping-begäran Miss lyckas kan du försöka pinga IP-adresserna för den hanterade domänen, till exempel `ping 10.0.0.4` . IP-adressen för din miljö visas på sidan *Egenskaper* när du väljer den hanterade domänen i listan över Azure-resurser.
    * Om du kan pinga IP-adressen, men inte domänen, kan DNS vara felaktigt konfigurerat. Bekräfta att IP-adresserna för den hanterade domänen är konfigurerade som DNS-servrar för det virtuella nätverket.
* Försök att tömma DNS-matcharens cacheminne på den virtuella datorn med `ipconfig /flushdns` kommandot.

### <a name="credentials-related-issues"></a>Problem som rör autentiseringsuppgifter

Om du får en fråga om autentiseringsuppgifter för att ansluta till domänen, men ett fel efter att du angett dessa autentiseringsuppgifter, kan den virtuella datorn ansluta till den hanterade domänen. De angivna autentiseringsuppgifterna låter inte den virtuella datorn ansluta till den hanterade domänen.

Försök att ansluta den virtuella Windows Server-datorn till den hanterade domänen igen efter att ha försökt med de här fel söknings stegen.

* Kontrol lera att det användar konto som du anger tillhör den hanterade domänen.
* Bekräfta att kontot ingår i den hanterade domänen eller Azure AD-klienten. Konton från externa kataloger som är associerade med din Azure AD-klient kan inte autentiseras korrekt under processen för domän anslutning.
* Försök att använda UPN-formatet för att ange autentiseringsuppgifter, till exempel `contosoadmin@aaddscontoso.onmicrosoft.com` . Om det finns många användare med samma UPN-prefix i din klient organisation eller om ditt UPN-prefix är för långt, kan *sAMAccountName* för ditt konto skapas automatiskt. I dessa fall kan *sAMAccountName* -formatet för ditt konto skilja sig från vad du förväntar dig eller använder i din lokala domän.
* Kontrol lera att du har [aktiverat][password-sync] Lösenordssynkronisering till din hanterade domän. Utan det här konfigurations steget finns inte nödvändiga lösen ords-hashar i den hanterade domänen för att autentisera ditt inloggnings försök på rätt sätt.
* Vänta tills Lösenordssynkronisering har slutförts. När ett användar kontos lösen ord ändras uppdaterar en automatisk Bakgrundssynkronisering från Azure AD lösen ordet i Azure AD DS. Det tar lite tid för lösen ordet att vara tillgängligt för domän kopplings användning.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows Server-dator
> * Ansluta till den virtuella Windows Server-datorn till ett virtuellt Azure-nätverk
> * Anslut den virtuella datorn till den hanterade domänen

Om du vill administrera en hanterad domän konfigurerar du en virtuell hanterings dator med hjälp av Active Directory Administrationscenter (ADAC).

> [!div class="nextstepaction"]
> [Installera administrations verktyg på en virtuell hanterings dator](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension