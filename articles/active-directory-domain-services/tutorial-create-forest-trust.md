---
title: Självstudie – Skapa ett skogs förtroende i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du skapar en enkelriktad utgående skog till en lokal AD DS-domän i Azure Portal för Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2021
ms.author: justinha
ms.openlocfilehash: e381c80dddc4484d541f5f81de6b5df712cff69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673475"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services"></a>Självstudie: skapa en utgående skogs förtroende till en lokal domän i Azure Active Directory Domain Services

I miljöer där du inte kan synkronisera lösen ord, eller om användarna enbart loggar in med smartkort och inte känner till sitt lösen ord, kan du använda en resurs skog i Azure Active Directory Domain Services (Azure AD DS). En resurs skog använder ett enkelriktat utgående förtroende från Azure AD DS till en eller flera lokala AD DS-miljöer. Den här förtroende relationen låter användare, program och datorer autentisera mot en lokal domän från den hanterade domänen i Azure AD DS. I en resurs skog synkroniseras aldrig lokala lösenords-hashvärden.

![Diagram över skogs förtroende från Azure AD DS till lokal AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera DNS i en lokal AD DS-miljö för att stödja Azure AD DS-anslutning
> * Skapa ett enkelriktat inkommande skogs förtroende i en lokal AD DS-miljö
> * Skapa ett enkelriktat utgående skogs förtroende i Azure AD DS
> * Testa och verifiera förtroende relationen för autentiserings-och resurs åtkomst

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän som skapats med hjälp av en resurs skog och som kon figurer ATS i din Azure AD-klient.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Se till att du skapar en hanterad domän med hjälp av en *resurs* skog. Standard alternativet skapar en *användar* skog. Endast resurs skogar kan skapa förtroenden till lokal AD DS-miljöer.
    >
    > Du måste också använda minst *företags* -SKU: er för din hanterade domän. Om det behövs [ändrar du SKU: n för en hanterad domän][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar du och konfigurerar det utgående skogs förtroendet från Azure AD DS med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="networking-considerations"></a>Nätverksöverväganden

Det virtuella nätverk som är värd för Azure AD DS-resurs skogen behöver nätverks anslutning till din lokala Active Directory. Program och tjänster behöver också nätverks anslutning till det virtuella nätverket som är värd för Azure AD DS-resurs skogen. Nätverks anslutningen till Azure AD DS-resurs skogen måste vara alltid aktive ras och stabilt, annars kan användare Miss lyckas med att autentisera eller komma åt resurser.

Innan du konfigurerar ett skogs förtroende i Azure AD DS ser du till att nätverket mellan Azure och den lokala miljön uppfyller följande krav:

* Använd privata IP-adresser. Förlita dig inte på DHCP med dynamisk IP-adresstilldelning.
* Undvik överlappande IP-adressutrymme för att tillåta att peering och routning i virtuella nätverk kan kommunicera mellan Azure och lokalt.
* Ett virtuellt Azure-nätverk behöver ett Gateway-undernät för att konfigurera en [Azure plats-till-plats (S2S) VPN-][vpn-gateway] eller [ExpressRoute][expressroute] -anslutning.
* Skapa undernät med tillräckligt med IP-adresser för att ge stöd för ditt scenario.
* Kontrol lera att Azure AD DS har sitt eget undernät, dela inte det virtuella nätverkets undernät med virtuella program datorer och-tjänster.
* Peer-kopplat virtuella nätverk är inte transitiva.
    * Peer-kopplingar i Azure virtuella nätverk måste skapas mellan alla virtuella nätverk som du vill använda Azure AD DS-resursens skogs förtroende för den lokala AD DS-miljön.
* Ge kontinuerlig nätverks anslutning till din lokala Active Directory skog. Använd inte anslutningar på begäran.
* Se till att det finns en kontinuerlig namn matchning (DNS) mellan Azure AD DS-resursens skogs namn och namnet på den lokala Active Directory skogen.

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurera DNS i den lokala domänen

För att korrekt lösa den hanterade domänen från den lokala miljön kan du behöva lägga till vidarebefordrare till de befintliga DNS-servrarna. Om du inte har konfigurerat den lokala miljön för att kommunicera med den hanterade domänen utför du följande steg från en hanterings arbets station för den lokala AD DS-domänen:

1. Välj **Starta**  >  **administrations verktyg**  >  **DNS**.
1. Högerklicka på DNS-server, till exempel *myAD01*, och välj sedan **Egenskaper**.
1. Välj **vidarebefordrare** och sedan **Redigera** för att lägga till ytterligare vidarebefordrare.
1. Lägg till IP-adresserna för den hanterade domänen, till exempel *10.0.2.4* och *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Skapa inkommande skogs förtroende i den lokala domänen

Den lokala AD DS-domänen måste ha ett inkommande skogs förtroende för den hanterade domänen. Detta förtroende måste skapas manuellt i den lokala AD DS-domänen, det kan inte skapas från Azure Portal.

Om du vill konfigurera inkommande förtroende för den lokala AD DS-domänen utför du följande steg från en hanterings arbets station för den lokala AD DS-domänen:

1. Välj **Starta**  >  **administrations verktyg**  >  **Active Directory domäner och förtroenden**.
1. Högerklicka på domänen, till exempel *OnPrem.contoso.com*, och välj sedan **Egenskaper**.
1. Välj fliken **förtroenden** och sedan **Nytt förtroende**.
1. Ange namnet på Azure AD DS-domännamnet, till exempel *aaddscontoso.com*, och välj sedan **Nästa**.
1. Välj alternativet för att skapa ett **skogs förtroende** och skapa ett enkelriktat **sätt: inkommande** förtroende.
1. Välj att endast skapa förtroende för **den här domänen**. I nästa steg skapar du förtroendet i Azure Portal för den hanterade domänen.
1. Välj att använda **autentisering för hela skogen** och ange och bekräfta ett lösen ord för förtroende. Samma lösen ord anges också i Azure Portal i nästa avsnitt.
1. Gå igenom de kommande Fönstren med standard alternativ och välj alternativet för **Nej, bekräfta inte det utgående förtroendet**.
1. Välj **Slutför**.

Om skogs förtroendet inte längre behövs för en miljö utför du följande steg för att ta bort den från den lokala domänen:

1. Välj **Starta**  >  **administrations verktyg**  >  **Active Directory domäner och förtroenden**.
1. Högerklicka på domänen, till exempel *OnPrem.contoso.com*, och välj sedan **Egenskaper**.
1. Välj fliken **förtroenden** , sedan **domäner som litar på den här domänen (inkommande förtroenden)**, klicka på det förtroende som ska tas bort och klicka sedan på **ta bort**.
1. På fliken förtroenden under **domäner som är betrodda av den här domänen (utgående förtroenden)** klickar du på det förtroende som ska tas bort och klickar sedan på ta bort.
1. Klicka på **Nej, ta bort förtroendet från den lokala domänen**.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Skapa utgående skogs förtroende i Azure AD DS

När den lokala AD DS-domänen har kon figurer ATS för att matcha den hanterade domänen och ett inkommande skogs förtroende har skapats, skapar nu det utgående skogs förtroendet. Detta utgående skogs förtroende Slutför förtroende relationen mellan den lokala AD DS-domänen och den hanterade domänen.

Utför följande steg för att skapa utgående förtroende för den hanterade domänen i Azure Portal:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services** och väljer sedan din hanterade domän, till exempel *aaddscontoso.com*.
1. I menyn till vänster i den hanterade domänen väljer du **förtroenden** och väljer sedan till **+ Lägg till** ett förtroende.

   > [!NOTE]
   > Om du inte ser meny alternativet **förtroende** kontrollerar du under **Egenskaper** för *skogs typen*. Endast *resurs* skogar kan skapa förtroenden. Om skogs typen är *användare* kan du inte skapa förtroenden. Det finns för närvarande inget sätt att ändra skogs typen för en hanterad domän. Du måste ta bort och återskapa den hanterade domänen som en resurs skog.

1. Ange ett visnings namn som identifierar ditt förtroende, sedan DNS-namnet för den lokala betrodda skogen, till exempel *OnPrem.contoso.com*.
1. Ange samma förtroende lösen ord som användes för att konfigurera det inkommande skogs förtroendet för den lokala AD DS-domänen i föregående avsnitt.
1. Ange minst två DNS-servrar för den lokala AD DS-domänen, till exempel *10.1.1.4* och *10.1.1.5*.
1. När du är klar **sparar** du det utgående skogs förtroendet.

    ![Skapa utgående skogs förtroende i Azure Portal](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

Om skogs förtroendet inte längre behövs för en miljö utför du följande steg för att ta bort den från Azure AD DS:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services** och väljer sedan din hanterade domän, till exempel *aaddscontoso.com*.
1. Välj **förtroenden** på menyn till vänster i den hanterade domänen, Välj förtroende och klicka på **ta bort**.
1. Ange samma förtroende lösen ord som användes för att konfigurera skogs förtroendet och klicka på **OK**.

## <a name="validate-resource-authentication"></a>Verifiera autentisering av resurs

I följande vanliga scenarier kan du kontrol lera att skogs förtroendet autentiserar användare och åtkomst till resurser på rätt sätt:

* [Lokal användarautentisering från Azure AD DS-resurs skogen](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Få åtkomst till resurser i Azure AD DS-resurs skogen med lokal användare](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Aktivera fil-och skrivar delning](#enable-file-and-printer-sharing)
    * [Skapa en säkerhets grupp och Lägg till medlemmar](#create-a-security-group-and-add-members)
    * [Skapa en fil resurs för åtkomst över flera skogar](#create-a-file-share-for-cross-forest-access)
    * [Verifiera autentisering mellan skogar till en resurs](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Lokal användarautentisering från Azure AD DS-resurs skogen

Du bör ha en virtuell Windows Server-dator som är ansluten till den hanterade domänen. Använd den här virtuella datorn för att testa din lokala användare kan autentiseras på en virtuell dator. Om det behövs [skapar du en virtuell Windows-dator och ansluter den till den hanterade domänen][join-windows-vm].

1. Anslut till den virtuella Windows Server-datorn som är ansluten till resurs skogen för Azure AD DS med hjälp av [Azure skydds](../bastion/bastion-overview.md) och dina autentiseringsuppgifter för Azure AD DS-administratören.
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

Med hjälp av den virtuella Windows Server-datorn som är ansluten till Azure AD DS-resurs skogen kan du testa scenariot där användare kan komma åt resurser som finns i resurs skogen när de autentiseras från datorer i den lokala domänen med användare från den lokala domänen. I följande exempel visas hur du skapar och testar olika vanliga scenarier.

#### <a name="enable-file-and-printer-sharing"></a>Aktivera fil-och skrivar delning

1. Anslut till den virtuella Windows Server-datorn som är ansluten till resurs skogen för Azure AD DS med hjälp av [Azure skydds](../bastion/bastion-overview.md) och dina autentiseringsuppgifter för Azure AD DS-administratören.

1. Öppna **Windows-inställningar** och Sök sedan efter och välj **nätverks-och delnings Center**.
1. Välj alternativet för att **Ändra avancerade delnings** inställningar.
1. Under **domän profil** väljer du **aktivera fil-och skrivar delning** och **sparar ändringarna**.
1. Stäng **nätverks-och delnings Center**.

#### <a name="create-a-security-group-and-add-members"></a>Skapa en säkerhets grupp och Lägg till medlemmar

1. Öppna **Active Directory-användare och datorer**.
1. Högerklicka på domän namnet, Välj **nytt** och välj sedan **organisationsenhet**.
1. I rutan Namn skriver du *LocalObjects* och väljer sedan **OK**.
1. Markera och högerklicka på **LocalObjects** i navigerings fönstret. Välj **ny** och sedan **grupp**.
1. Skriv *FileServerAccess* i rutan **grupp namn** . För **grupp omfånget** väljer du **domän lokalt** och väljer sedan **OK**.
1. I innehålls rutan dubbelklickar du på **FileServerAccess**. Välj **medlemmar**, Välj att **lägga till** och välj sedan **platser**.
1. Välj din lokala Active Directory från vyn **plats** och välj sedan **OK**.
1. Skriv *domän användare* i rutan **Ange de objekt namn som ska väljas** . Välj **kontrol lera namn**, ange autentiseringsuppgifter för den lokala Active Directory och välj sedan **OK**.

    > [!NOTE]
    > Du måste ange autentiseringsuppgifter eftersom förtroende relationen bara är ett sätt. Det innebär att användare från den hanterade domänen i Azure AD DS inte kan komma åt resurser eller söka efter användare eller grupper i den betrodda domänen (lokalt).

1. Gruppen **domän användare** från din lokala Active Directory bör vara medlem i gruppen **FileServerAccess** . Välj **OK** för att spara gruppen och Stäng fönstret.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Skapa en fil resurs för åtkomst över flera skogar

1. På den virtuella Windows Server-datorn som är ansluten till resurs skogen för Azure AD DS skapar du en mapp och anger namn som *CrossForestShare*.
1. Högerklicka på mappen och välj **Egenskaper**.
1. Välj fliken **säkerhet** och välj sedan **Redigera**.
1. I dialog rutan *behörigheter för CrossForestShare* väljer du **Lägg till**.
1. Skriv *FileServerAccess* i **Ange de objekt namn som ska väljas** och välj sedan **OK**.
1. Välj *FileServerAccess* i listan **grupper eller användar namn** . I listan **behörigheter för FileServerAccess** väljer du *Tillåt* för behörigheterna **ändra** och **Skriv** och väljer sedan **OK**.
1. Välj fliken **delning** och välj sedan **Avancerad delning...**.
1. Välj **dela den här mappen** och ange ett minnes minnes namn för fil resursen i **resurs namn** , till exempel *CrossForestShare*.
1. Välj **Behörigheter**. I listan **behörigheter för alla** väljer du **Tillåt** för behörigheten **ändra** .
1. Välj **OK** två gånger och **Stäng** sedan.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Verifiera autentisering mellan skogar till en resurs

1. Logga in på en Windows-dator som är ansluten till din lokala Active Directory med ett användar konto från din lokala Active Directory.
1. Med **Utforskaren i Windows** ansluter du till den resurs som du skapade med det fullständigt kvalificerade värd namnet och resursen, till exempel `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Om du vill verifiera Skriv behörigheten högerklickar du i mappen, väljer **nytt** och väljer sedan **text dokument**. Använd standard namnet **nytt text dokument**.

    Om Skriv behörigheterna har angetts korrekt skapas ett nytt text dokument. Följande steg kommer sedan att öppna, redigera och ta bort filen efter behov.
1. Om du vill verifiera Läs behörighet öppnar du **nytt text dokument**.
1. Du kan kontrol lera behörigheten Ändra genom att lägga till text i filen och stänga **anteckningar**. När du uppmanas att spara ändringarna väljer du **Spara**.
1. Om du vill verifiera borttagnings behörigheten högerklickar du på **nytt text dokument** och väljer **ta bort**. Bekräfta borttagning av filer genom att välja **Ja** .

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera DNS i en lokal AD DS-miljö för att stödja Azure AD DS-anslutning
> * Skapa ett enkelriktat inkommande skogs förtroende i en lokal AD DS-miljö
> * Skapa ett enkelriktat utgående skogs förtroende i Azure AD DS
> * Testa och verifiera förtroende relationen för autentiserings-och resurs åtkomst

Mer konceptuell information om skogs typer i Azure AD DS finns i [Vad är resurs skogar?][concepts-forest] och [Hur fungerar skogs förtroenden i Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md