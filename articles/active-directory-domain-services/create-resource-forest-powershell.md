---
title: Skapa en Azure AD Domain Services resurs skog med Azure PowerShell | Microsoft Docs
description: I den här artikeln får du lära dig hur du skapar och konfigurerar en Azure Active Directory Domain Services resurs skog och en utgående skog till en lokal Active Directory Domain Services miljö med hjälp av Azure PowerShell.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: iainfou
ms.openlocfilehash: 50a8e4f6d966a63a8e727dbacefbc7bb21f5f98b
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506336"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Skapa en Azure Active Directory Domain Services resurs skog och utgående skogs förtroende till en lokal domän med hjälp av Azure PowerShell

I miljöer där du inte kan synkronisera lösen ord, eller om du har användare som enbart loggar in med smartkort så att de inte känner till sitt lösen ord, kan du använda en resurs skog i Azure Active Directory Domain Services (Azure AD DS). En resurs skog använder ett enkelriktat utgående förtroende från Azure AD DS till en eller flera lokala AD DS-miljöer. Den här förtroende relationen låter användare, program och datorer autentisera mot en lokal domän från den hanterade domänen i Azure AD DS. I en resurs skog synkroniseras aldrig lokala lösenords-hashvärden.

![Diagram över skogs förtroende från Azure AD DS till lokal AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en Azure AD DS-resurs skog med Azure PowerShell
> * Skapa ett enkelriktat utgående skogs förtroende i den hanterade domänen med hjälp av Azure PowerShell
> * Konfigurera DNS i en lokal AD DS-miljö för att stödja hanterad domän anslutning
> * Skapa ett enkelriktat inkommande skogs förtroende i en lokal AD DS-miljö
> * Testa och verifiera förtroende relationen för autentiserings-och resurs åtkomst

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> Hanterade domän resurs skogar har för närvarande inte stöd för Azure HDInsight eller Azure Files. Standard hanterade domän användar skogar stöder båda dessa ytterligare tjänster.

## <a name="prerequisites"></a>Krav

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].

* Installera och konfigurera Azure PowerShell.
    * Om det behövs följer du anvisningarna för att [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](/powershell/azure/install-az-ps).
    * Kontrol lera att du loggar in på Azure-prenumerationen med hjälp av cmdleten [Connect-AzAccount][Connect-AzAccount] .
* Installera och konfigurera Azure AD PowerShell.
    * Om det behövs följer du anvisningarna för att [Installera Azure AD PowerShell-modulen och ansluta till Azure AD](/powershell/azure/active-directory/install-adv2).
    * Kontrol lera att du loggar in på Azure AD-klienten med hjälp av cmdleten [Connect-AzureAD][Connect-AzureAD] .
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att aktivera Azure AD DS.
* Du behöver *deltagar* behörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här artikeln skapar och konfigurerar du utgående skogs förtroende från en hanterad domän med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="deployment-process"></a>Distributionsprocess

Det är en process med flera delar där du kan skapa en resurs skog för en hanterad domän och förtroende relationen till en lokal AD DS. Följande avancerade steg bygger på din betrodda hybrid miljö:

1. Skapa ett huvud namn för en hanterad domän tjänst.
1. Skapa en resurs skog för hanterad domän.
1. Skapa hybrid nätverks anslutning med plats-till-plats-VPN eller Express Route.
1. Skapa den hanterade domän sidan av förtroende relationen.
1. Skapa den lokala AD DS-sidan av förtroende relationen.

Innan du börjar ska du se till att du förstår [nätverks överväganden, skogens namn och DNS-krav](tutorial-create-forest-trust.md#networking-considerations). Du kan inte ändra namnet på den hanterade domän skogen när det har distribuerats.

## <a name="create-the-azure-ad-service-principal"></a>Skapa Azure AD-tjänstens huvud namn

Azure AD DS kräver tjänstens huvud namn för att synkronisera data från Azure AD. Det här huvud kontot måste skapas i din Azure AD-klient innan du skapar den hanterade domän resurs skogen.

Skapa ett Azure AD-tjänstens huvud namn för Azure AD DS för att kommunicera och autentisera sig själv. Ett angivet program-ID används med namnet *domänkontrollant tjänster* med ID *2565bd9d-DA50-47d4-8b85-4c97f669dc36*. Ändra inte det här program-ID: t.

Skapa ett Azure AD-tjänstens huvud namn med cmdleten [New-AzureADServicePrincipal][New-AzureADServicePrincipal] :

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="create-a-managed-domain-resource-forest"></a>Skapa en resurs skog för hanterad domän

Om du vill skapa en resurs skog för en hanterad domän använder du `New-AzureAaddsForest` skriptet. Det här skriptet är en del av en bredare uppsättning kommandon som har stöd för att skapa och hantera hanterade domän resurs skogar, inklusive att skapa en enkelriktad bindnings skog i ett av följande avsnitt. Dessa skript är tillgängliga från [PowerShell-galleriet](https://www.powershellgallery.com/) och signeras digitalt av Azure AD Engineering-teamet.

1. Börja med att skapa en resurs grupp med cmdleten [New-AzResourceGroup][New-AzResourceGroup] . I följande exempel heter resurs gruppen *myResourceGroup* och skapas i regionen *väst* . Använd ditt eget namn och önskad region:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. Installera `New-AaddsResourceForestTrust` skriptet från [PowerShell-galleriet][powershell-gallery] med hjälp av cmdleten [install-script][Install-Script] :

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Granska följande parametrar som behövs för `New-AzureAaddsForest` skriptet. Se till att du även har de nödvändiga **Azure PowerShell** -och **Azure AD PowerShell** -modulerna. Se till att du har planerat de virtuella nätverks kraven för att tillhandahålla program och lokal anslutning.

    | Namn                         | Skript parameter          | Beskrivning |
    |:-----------------------------|---------------------------|:------------|
    | Prenumeration                 | *– azureSubscriptionId*    | Prenumerations-ID som används för Azure AD DS-fakturering. Du kan hämta listan över prenumerationer med hjälp av cmdleten [Get-AzureRMSubscription][Get-AzureRMSubscription] . |
    | Resursgrupp               | *-aaddsResourceGroupName* | Namnet på resurs gruppen för den hanterade domänen och de associerade resurserna. |
    | Plats                     | *-aaddsLocation*          | Azure-regionen som är värd för din hanterade domän. För tillgängliga regioner, se [regioner som stöds för Azure AD DS.](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) |
    | Azure AD DS-administratör    | *-aaddsAdminUser*         | User Principal Name av den första hanterade domän administratören. Det här kontot måste vara ett befintligt moln användar konto i din Azure Active Directory. Användaren, och användaren som kör skriptet, läggs till i *Administratörs gruppen för AAD-domänkontrollanten* . |
    | Namn på Azure AD DS-domän      | *-aaddsDomainName*        | Det fullständiga domän namnet för den hanterade domänen, baserat på den tidigare vägledningen om hur du väljer ett skogs namn. |

    `New-AzureAaddsForest`Skriptet kan skapa det virtuella Azure-nätverket och Azure AD DS-undernätet om dessa resurser inte redan finns. Skriptet kan också skapa arbets belastnings under näten när det anges:

    | Namn                              | Skript parameter                  | Beskrivning |
    |:----------------------------------|:----------------------------------|:------------|
    | Virtuellt nätverksnamn              | *-aaddsVnetName*                  | Namnet på det virtuella nätverket för den hanterade domänen.|
    | Adressutrymme                     | *-aaddsVnetCIDRAddressSpace*      | Det virtuella nätverkets adress intervall i CIDR-format (om du skapar det virtuella nätverket).|
    | Namn på Azure AD DS-undernät           | *-aaddsSubnetName*                | Namnet på under nätet för det *aaddsVnetName* virtuella nätverk som är värd för den hanterade domänen. Distribuera inte dina egna virtuella datorer och arbets belastningar i det här under nätet. |
    | Azure AD DS-adressintervall         | *-aaddsSubnetCIDRAddressRange*    | Under näts adress intervall i CIDR-notering för AAD DS-instansen, till exempel *192.168.1.0/24*. Adress intervallet måste finnas i adress intervallet för det virtuella nätverket och skiljer sig från andra undernät. |
    | Under näts namn för arbets belastning (valfritt)   | *-workloadSubnetName*             | Valfritt namn på ett undernät i det virtuella *aaddsVnetName* -nätverket som ska skapas för dina egna program arbets belastningar. Virtuella datorer och program och även vara anslutna till ett peer-kopplat Azure-nätverk i stället. |
    | Adress intervall för arbets belastning (valfritt) | *-workloadSubnetCIDRAddressRange* | Valfritt under näts adress intervall i CIDR-notation för program arbets belastning, till exempel *192.168.2.0/24*. Adress intervallet måste finnas i adress intervallet för det virtuella nätverket och skiljer sig från andra undernät.|

1. Skapa nu en resurs skog med hanterad domän med hjälp av `New-AzureAaaddsForest` skriptet. I följande exempel skapas en skog med namnet *addscontoso.com* och ett arbets belastnings undernät skapas. Ange egna parameter namn och IP-adressintervall eller befintliga virtuella nätverk.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    Det tar ganska lite tid att skapa den hanterade domän resurs skogen och stöd resurser. Tillåt att skriptet slutförs. Fortsätt till nästa avsnitt för att konfigurera din lokala nätverks anslutning medan Azure AD-resursens skogs regler i bakgrunden.

## <a name="configure-and-validate-network-settings"></a>Konfigurera och validera nätverks inställningar

När den hanterade domänen fortsätter att distribuera, konfigurera och validera hybrid nätverks anslutningen till det lokala data centret. Du behöver också en virtuell hanterings dator för att använda med den hanterade domänen för regelbundet underhåll. Det kanske redan finns en hybrid anslutning i din miljö, eller så kanske du måste arbeta med andra i din grupp för att konfigurera anslutningarna.

Innan du börjar ska du kontrol lera att du förstår [nätverks övervägandena och rekommendationerna](tutorial-create-forest-trust.md#networking-considerations).

1. Skapa hybrid anslutningen till ditt lokala nätverk till Azure med hjälp av en Azure VPN-eller Azure ExpressRoute-anslutning. Hybrid nätverks konfigurationen ligger utanför den här dokumentationens omfattning och finns kanske redan i din miljö. Mer information om vissa scenarier finns i följande artiklar:

    * [Azure plats-till-plats-VPN](/azure/vpn-gateway/vpn-gateway-about-vpngateways).
    * [Översikt över Azure ExpressRoute](/azure/expressroute/expressroute-introduction).

    > [!IMPORTANT]
    > Om du skapar anslutningen direkt till din hanterade domäns virtuella nätverk, använder du ett separat Gateway-undernät. Skapa inte gatewayen i under nätet för den hanterade domänen.

1. Om du vill administrera en hanterad domän skapar du en virtuell hanterings dator, ansluter den till den hanterade domänen och installerar de nödvändiga hanterings verktygen för AD DS.

    Medan resurs skogen för hanterad domän distribueras, [skapar du en virtuell Windows Server-dator](https://docs.microsoft.com/azure/active-directory-domain-services/join-windows-vm) och installerar sedan de viktiga hanterings verktygen för [AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-management-vm) . Vänta med att ansluta den virtuella hanterings datorn till den hanterade domänen innan du gör något av följande när domänen har distribuerats.

1. Verifiera nätverks anslutningen mellan det lokala nätverket och det virtuella Azure-nätverket.

    * Bekräfta att den lokala domänkontrollanten kan ansluta till den hanterade virtuella datorn med hjälp av `ping` eller fjärr skrivbord, till exempel.
    * Kontrol lera att den virtuella hanterings datorn kan ansluta till dina lokala domänkontrollanter och försök igen med ett verktyg som `ping` .

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services**. Välj din hanterade domän, till exempel *aaddscontoso.com* och vänta tills status har rapporteras som **körs**.

    När du kör [uppdaterar du DNS-inställningarna för det virtuella Azure-nätverket](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) och [aktiverar sedan användar konton för Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) för att slutföra konfigurationerna för den hanterade domän resurs skogen.

1. Anteckna DNS-adresserna som visas på sidan Översikt. Du behöver dessa adresser när du konfigurerar den lokala Active Directory sidan av förtroende relationen i ett av följande avsnitt.
1. Starta om den virtuella hanterings datorn för att ta emot de nya DNS-inställningarna och [Anslut sedan den virtuella datorn till den hanterade domänen](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. När den virtuella hanterings datorn har anslutits till den hanterade domänen ansluter du igen med hjälp av fjärr skrivbord.

    Från en kommando tolk använder du `nslookup` och namnet på den hanterade domän resurs skogen för att verifiera namn matchningen för resurs skogen.

    ```console
    nslookup aaddscontoso.com
    ```

    Kommandot ska returnera två IP-adresser för resurs skogen.

## <a name="create-the-forest-trust"></a>Skapa skogs förtroendet

Skogs förtroendet har två delar: ett enkelriktat utgående skogs förtroende i den hanterade domän resurs skogen och det enkelriktade inkommande skogs förtroendet i den lokala AD DS-skogen. Du skapar båda sidorna i den här förtroende relationen manuellt. När båda sidorna skapas kan användare och resurser autentiseras med skogs förtroendet. En hanterad domän resurs skog har stöd för upp till 5 1 utgående skogs förtroenden till lokala skogar.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>Skapa den hanterade domän sidan av förtroende relationen

Använd `Add-AaddsResourceForestTrust` skriptet för att skapa den hanterade domän sidan av förtroende relationen. Installera först `Add-AaddsResourceForestTrust` skriptet från [PowerShell-galleriet][powershell-gallery] med hjälp av cmdleten [install-script][Install-Script] :

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

Ange nu skriptet med följande information:

| Namn                               | Skript parameter     | Beskrivning |
|:-----------------------------------|:---------------------|:------------|
| Namn på Azure AD DS-domän            | *-ManagedDomainFqdn* | Fullständigt domän namn för den hanterade domänen, till exempel *aaddscontoso.com* |
| Namn på lokal AD DS-domän      | *-TrustFqdn*         | FQDN för den betrodda skogen, till exempel *OnPrem.contoso.com* |
| Eget namn på förtroende                | *-TrustFriendlyName* | Eget namn på förtroende relationen. |
| Lokala AD DS DNS IP-adresser | *-TrustDnsIPs*       | En kommaavgränsad lista över DNS-serverns IPv4-adresser för den betrodda domänen i listan. |
| Förtroende lösen ord                     | *-TrustPassword*     | Ett komplext lösen ord för förtroende relationen. Det här lösen ordet anges också när du skapar ett enkelriktat inkommande förtroende i den lokala AD DS. |
| Autentiseringsuppgifter                        | *-Autentiseringsuppgifter*       | De autentiseringsuppgifter som används för att autentisera till Azure. Användaren måste vara i *gruppen AAD DC-administratörer*. Om detta inte anges uppmanas skriptet för autentisering. |

I följande exempel skapas en förtroende relation med namnet *myAzureADDSTrust* till *OnPrem.contoso.com*. Använd dina egna parameter namn och lösen ord:.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Kom ihåg lösen ordet för förtroende. Du måste använda samma lösen ord när du skapar den lokala sidan av förtroendet.

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurera DNS i den lokala domänen

För att korrekt lösa den hanterade domänen från den lokala miljön kan du behöva lägga till vidarebefordrare till de befintliga DNS-servrarna. Om du inte har konfigurerat den lokala miljön för att kommunicera med den hanterade domänen utför du följande steg från en hanterings arbets station för den lokala AD DS-domänen:

1. Välj **Start | Administrations verktyg | DNS**
1. Högerklicka på DNS-server, till exempel *myAD01*, Välj **Egenskaper**
1. Välj **vidarebefordrare**och sedan **Redigera** för att lägga till ytterligare vidarebefordrare.
1. Lägg till IP-adresserna för den hanterade domänen, till exempel *10.0.1.4* och *10.0.1.5*.
1. Från en lokal kommando tolk, verifiera namn matchning med hjälp av **nslookup** i domän namnet för den hanterade domänens resurs skog. Exempelvis `Nslookup aaddscontoso.com` ska returnera de två IP-adresserna för den hanterade domän resurs skogen.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Skapa inkommande skogs förtroende i den lokala domänen

Den lokala AD DS-domänen måste ha ett inkommande skogs förtroende för den hanterade domänen. Detta förtroende måste skapas manuellt i den lokala AD DS-domänen, det kan inte skapas från Azure Portal.

Om du vill konfigurera inkommande förtroende för den lokala AD DS-domänen utför du följande steg från en hanterings arbets station för den lokala AD DS-domänen:

1. Välj **Start | Administrations verktyg | Active Directory domäner och förtroenden**
1. Högerklicka på domän, till exempel *OnPrem.contoso.com*, Välj **Egenskaper**
1. Välj fliken **förtroenden** och sedan **Nytt förtroende**
1. Ange namnet på den hanterade domänen, till exempel *aaddscontoso.com*, och välj sedan **Nästa**
1. Välj alternativet för att skapa ett **skogs förtroende**och skapa ett enkelriktat **sätt: inkommande** förtroende.
1. Välj att endast skapa förtroende för **den här domänen**. I nästa steg skapar du förtroendet i Azure Portal för den hanterade domänen.
1. Välj att använda **autentisering för hela skogen**och ange och bekräfta ett lösen ord för förtroende. Samma lösen ord anges också i Azure Portal i nästa avsnitt.
1. Gå igenom de kommande Fönstren med standard alternativ och välj alternativet för **Nej, bekräfta inte det utgående förtroendet**. Du kan inte validera förtroende relationen eftersom det delegerade administratörs kontot till resurs skogen för hanterad domän inte har de behörigheter som krävs. Det här beteendet är avsiktligt.
1. Välj **Slutför**

## <a name="validate-resource-authentication"></a>Verifiera autentisering av resurs

I följande vanliga scenarier kan du kontrol lera att skogs förtroendet autentiserar användare och åtkomst till resurser på rätt sätt:

* [Lokal användarautentisering från Azure AD DS-resurs skogen](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Få åtkomst till resurser i Azure AD DS-resurs skogen med lokal användare](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Aktivera fil-och skrivar delning](#enable-file-and-printer-sharing)
    * [Skapa en säkerhets grupp och Lägg till medlemmar](#create-a-security-group-and-add-members)
    * [Skapa en fil resurs för åtkomst över flera skogar](#create-a-file-share-for-cross-forest-access)
    * [Verifiera autentisering mellan skogar till en resurs](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Lokal användarautentisering från Azure AD DS-resurs skogen

Du bör ha en virtuell Windows Server-dator som är ansluten till resurs domänen för den hanterade domänen. Använd den här virtuella datorn för att testa din lokala användare kan autentiseras på en virtuell dator.

1. Anslut till den virtuella Windows Server-datorn som är ansluten till resurs skogen för hanterad domän med hjälp av fjärr skrivbord och dina autentiseringsuppgifter för hanterad domän administratör. Om du får ett autentisering på nätverksnivå fel (NLA) kontrollerar du att det användar konto du använde inte är ett domän användar konto.

    > [!TIP]
    > För att på ett säkert sätt ansluta till dina virtuella datorer som är anslutna till Azure AD Domain Services kan du använda [Azure skydds Host-tjänsten](https://docs.microsoft.com/azure/bastion/bastion-overview) i Azure-regioner som stöds.

1. Öppna en kommando tolk och Använd `whoami` kommandot för att visa det unika namnet för den för tillfället autentiserade användaren:

    ```console
    whoami /fqdn
    ```

1. Använd `runas` kommandot för att autentisera som en användare från den lokala domänen. I följande kommando ersätter `userUpn@trusteddomain.com` du med UPN för en användare från den betrodda lokala domänen. I kommandot uppmanas du att ange användarens lösen ord:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Om autentiseringen lyckas öppnas en ny kommando tolk. Rubriken på den nya kommando tolken innehåller `running as userUpn@trusteddomain.com` .
1. Använd `whoami /fqdn` i den nya kommando tolken för att visa det unika namnet för den autentiserade användaren från den lokala Active Directory.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Få åtkomst till resurser i Azure AD DS-resurs skogen med lokal användare

Med den virtuella Windows Server-datorn ansluten till resurs skogen för hanterad domän kan du testa scenariot där användare kan komma åt resurser som finns i resurs skogen när de autentiseras från datorer i den lokala domänen med användare från den lokala domänen. I följande exempel visas hur du skapar och testar olika vanliga scenarier.

#### <a name="enable-file-and-printer-sharing"></a>Aktivera fil-och skrivar delning

1. Anslut till den virtuella Windows Server-datorn som är ansluten till resurs skogen för hanterad domän med hjälp av fjärr skrivbord och dina autentiseringsuppgifter för hanterad domän administratör. Om du får ett autentisering på nätverksnivå fel (NLA) kontrollerar du att det användar konto du använde inte är ett domän användar konto.

    > [!TIP]
    > För att på ett säkert sätt ansluta till dina virtuella datorer som är anslutna till Azure AD Domain Services kan du använda [Azure skydds Host-tjänsten](https://docs.microsoft.com/azure/bastion/bastion-overview) i Azure-regioner som stöds.

1. Öppna **Windows-inställningar**och Sök sedan efter och välj **nätverks-och delnings Center**.
1. Välj alternativet för att **Ändra avancerade delnings** inställningar.
1. Under **domän profil**väljer du **aktivera fil-och skrivar delning** och **sparar ändringarna**.
1. Stäng **nätverks-och delnings Center**.

#### <a name="create-a-security-group-and-add-members"></a>Skapa en säkerhets grupp och Lägg till medlemmar

1. Öppna **Active Directory-användare och datorer**.
1. Högerklicka på domän namnet, Välj **nytt**och välj sedan **organisationsenhet**.
1. I rutan Namn skriver du *LocalObjects*och väljer sedan **OK**.
1. Markera och högerklicka på **LocalObjects** i navigerings fönstret. Välj **ny** och sedan **grupp**.
1. Skriv *FileServerAccess* i rutan **grupp namn** . För **grupp omfånget**väljer du **domän lokalt**och väljer sedan **OK**.
1. I innehålls rutan dubbelklickar du på **FileServerAccess**. Välj **medlemmar**, Välj att **lägga till**och välj sedan **platser**.
1. Välj din lokala Active Directory från vyn **plats** och välj sedan **OK**.
1. Skriv *domän användare* i rutan **Ange de objekt namn som ska väljas** . Välj **kontrol lera namn**, ange autentiseringsuppgifter för den lokala Active Directory och välj sedan **OK**.

    > [!NOTE]
    > Du måste ange autentiseringsuppgifter eftersom förtroende relationen bara är ett sätt. Det innebär att användare från den hanterade domänen inte kan komma åt resurser eller söka efter användare eller grupper i den betrodda (lokala) domänen.

1. Gruppen **domän användare** från din lokala Active Directory bör vara medlem i gruppen **FileServerAccess** . Välj **OK** för att spara gruppen och Stäng fönstret.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Skapa en fil resurs för åtkomst över flera skogar

1. På den virtuella Windows Server-datorn som är ansluten till resurs skogen för hanterad domän skapar du en mapp och anger namn som *CrossForestShare*.
1. Högerklicka på mappen och välj **Egenskaper**.
1. Välj fliken **säkerhet** och välj sedan **Redigera**.
1. I dialog rutan *behörigheter för CrossForestShare* väljer du **Lägg till**.
1. Skriv *FileServerAccess* i **Ange de objekt namn som ska väljas**och välj sedan **OK**.
1. Välj *FileServerAccess* i listan **grupper eller användar namn** . I listan **behörigheter för FileServerAccess** väljer du *Tillåt* för behörigheterna **ändra** och **Skriv** och väljer sedan **OK**.
1. Välj fliken **delning** och välj sedan **Avancerad delning...**
1. Välj **dela den här mappen**och ange ett minnes minnes namn för fil resursen i **resurs namn** , till exempel *CrossForestShare*.
1. Välj **behörigheter**. I listan **behörigheter för alla** väljer du **Tillåt** för behörigheten **ändra** .
1. Välj **OK** två gånger och **Stäng**sedan.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Verifiera autentisering mellan skogar till en resurs

1. Logga in på en Windows-dator som är ansluten till din lokala Active Directory med ett användar konto från din lokala Active Directory.
1. Med **Utforskaren i Windows**ansluter du till den resurs som du skapade med det fullständigt kvalificerade värd namnet och resursen, till exempel `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Om du vill verifiera Skriv behörigheten högerklickar du i mappen, väljer **nytt**och väljer sedan **text dokument**. Använd standard namnet **nytt text dokument**.

    Om Skriv behörigheterna har angetts korrekt skapas ett nytt text dokument. Följande steg kommer sedan att öppna, redigera och ta bort filen efter behov.
1. Om du vill verifiera Läs behörighet öppnar du **nytt text dokument**.
1. Du kan kontrol lera behörigheten Ändra genom att lägga till text i filen och stänga **anteckningar**. När du uppmanas att spara ändringarna väljer du **Spara**.
1. Om du vill verifiera borttagnings behörigheten högerklickar du på **nytt text dokument** och väljer **ta bort**. Bekräfta borttagning av filer genom att välja **Ja** .

## <a name="update-or-remove-outbound-forest-trust"></a>Uppdatera eller ta bort utgående skogs förtroende

Om du behöver uppdatera en befintlig enkelriktad utgående skog från den hanterade domänen kan du använda-och- `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` skripten. Dessa skript hjälper i scenarier där du vill uppdatera det egna namnet för skogs förtroendet eller förtroende lösen ordet. Om du vill ta bort ett enkelriktat utgående förtroende från den hanterade domänen kan du använda `Remove-AaddsResourceForestTrust` skriptet. Du måste manuellt ta bort det enkelriktade inkommande skogs förtroendet i den associerade lokala AD DS-skogen.

### <a name="update-a-forest-trust"></a>Uppdatera ett skogs förtroende

I normal drift förhandlar den hanterade domänen och den lokala skogen en vanlig process för lösen ords uppdatering sinsemellan. Detta är en del av säkerhets processen för AD DS-förtroende för förtroende. Du behöver inte rotera förtroende lösen ordet manuellt om inte förtroende relationen har drabbats av ett problem och du vill återställa till ett känt lösen ord manuellt. Mer information finns i avsnittet om [lösen ords ändringar för betrodda domän objekt](concepts-forest-trust.md#tdo-password-changes).

Följande exempel visar hur du uppdaterar en befintlig förtroende relation om du behöver återställa lösen ordet för utgående förtroende manuellt:

1. Installera `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` skripten och från [PowerShell-galleriet][powershell-gallery] med hjälp av cmdleten [install-script][Install-Script] :

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. Innan du kan uppdatera ett befintligt förtroende måste du först hämta förtroende resursen med hjälp av `Get-AaddsResourceForestTrusts` skriptet. I följande exempel tilldelas det befintliga förtroendet ett objekt med namnet *existingTrust*. Ange ett eget namn på den hanterade domän skogen och namnet på den lokala skogen som ska uppdateras:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. Om du vill uppdatera det befintliga förtroende lösen ordet använder du `Set-AaddsResourceForestTrust` skriptet. Ange det befintliga förtroendeobjektet från föregående steg och sedan ett nytt lösen ord för förtroende relationen. Ingen lösen ords komplexitet tillämpas av PowerShell, så se till att du genererar och använder ett säkert lösen ord för din miljö.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Ta bort ett skogs förtroende

Om du inte längre behöver det enkelriktade utgående skogs förtroendet från den hanterade domänen till en lokal AD DS-skog kan du ta bort den. Se till att inga program eller tjänster behöver autentisera mot den lokala AD DS-skogen innan du tar bort förtroendet. Du måste också manuellt ta bort det enkelriktade inkommande förtroendet i den lokala AD DS-skogen.

1. Installera `Remove-AaddsResourceForestTrust` skriptet från [PowerShell-galleriet][powershell-gallery] med hjälp av cmdleten [install-script][Install-Script] :

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. Ta nu bort skogs förtroendet med hjälp av `Remove-AaddsResourceForestTrust` skriptet. I följande exempel tas förtroendet med namnet *myAzureADDSTrust* mellan den hanterade domän skogen med namnet *aaddscontoso.com* och den lokala skogs *OnPrem.contoso.com* bort. Ange ett eget namn på den hanterade domän skogen och det lokala skogs namnet som ska tas bort:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Om du vill ta bort det enkelriktade inkommande förtroendet från den lokala AD DS-skogen ansluter du till en hanterings dator med åtkomst till den lokala AD DS-skogen och utför följande steg:

1. Välj **Start | Administrations verktyg | Active Directory domäner och förtroenden**
1. Högerklicka på domän, till exempel *OnPrem.contoso.com*, Välj **Egenskaper**
1. Välj fliken **förtroenden** och välj sedan det befintliga inkommande förtroendet från den hanterade domän skogen.
1. Välj **ta bort**och bekräfta sedan att du vill ta bort det inkommande förtroendet.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa en resurs skog för hanterad domän med hjälp av Azure PowerShell
> * Skapa ett enkelriktat utgående skogs förtroende i den hanterade domänen med hjälp av Azure PowerShell
> * Konfigurera DNS i en lokal AD DS-miljö för att stödja hanterad domän anslutning
> * Skapa ett enkelriktat inkommande skogs förtroende i en lokal AD DS-miljö
> * Testa och verifiera förtroende relationen för autentiserings-och resurs åtkomst

Mer konceptuell information om skogs typer i Azure AD DS finns i [Vad är resurs skogar?][concepts-forest] och [Hur fungerar skogs förtroenden i Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/