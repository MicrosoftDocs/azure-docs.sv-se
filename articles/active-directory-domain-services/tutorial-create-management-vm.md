---
title: Självstudie – Skapa en virtuell hanterings dator för Azure Active Directory Domain Services | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar och konfigurerar en virtuell Windows-dator som du använder för att administrera Azure Active Directory Domain Services hanterad domän.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d017fc3fc53c9f89978893670df10ecd731da904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618186"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Självstudie: skapa en virtuell hanterings dator för att konfigurera och administrera en Azure Active Directory Domain Services hanterad domän

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP och Kerberos/NTLM-autentisering som är helt kompatibelt med Windows Server Active Directory. Du administrerar den här hanterade domänen med samma verktyg för fjärrserveradministration (RSAT) som med en lokal Active Directory Domain Services domän. Eftersom Azure AD DS är en hanterad tjänst finns det några administrativa uppgifter som du inte kan utföra, till exempel att använda Remote Desktop Protocol (RDP) för att ansluta till domän kontrol Lanterna.

Den här självstudien visar hur du konfigurerar en virtuell Windows Server-dator i Azure och installerar de verktyg som krävs för att administrera en hanterad Azure AD DS-domän.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förstå de tillgängliga administrativa uppgifterna i en hanterad domän
> * Installera Active Directory administrations verktyg på en virtuell Windows Server-dator
> * Använd Active Directory Administrationscenter för att utföra vanliga uppgifter

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du läsa den första självstudien för att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* En virtuell Windows Server-dator som är ansluten till den hanterade domänen.
    * Om det behövs, se föregående självstudie för att [skapa en virtuell Windows Server-dator och koppla den till en hanterad domän][create-join-windows-vm].
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.
* En Azure skydds-värd som har distribuerats i Azure AD DS Virtual Network.
    * Om det behövs [skapar du en Azure skydds-värd][azure-bastion].

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar du och konfigurerar en virtuell hanterings dator med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Tillgängliga administrativa uppgifter i Azure AD DS

Azure AD DS tillhandahåller en hanterad domän för användare, program och tjänster som ska konsumeras. Den här metoden ändrar några av de tillgängliga hanterings uppgifterna som du kan göra och vilka behörigheter du har i den hanterade domänen. Dessa uppgifter och behörigheter kan skilja sig från vad du upplever med en vanlig lokal Active Directory Domain Services miljö. Du kan inte heller ansluta till domänkontrollanter på den hanterade domänen med hjälp av fjärr skrivbord.

### <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Administrativa uppgifter som du kan utföra på en hanterad domän

Medlemmar i gruppen *AAD DC-administratörer* beviljas behörigheter på den hanterade domänen som gör det möjligt för dem att utföra uppgifter som:

* Konfigurera det inbyggda grup princip objekt (GPO) för *AADDC-datorer* och AADDC- *användares* behållare i den hanterade domänen.
* Administrera DNS i den hanterade domänen.
* Skapa och administrera anpassade organisationsenheter (OU) på den hanterade domänen.
* Få administrativ åtkomst till datorer som är anslutna till den hanterade domänen.

### <a name="administrative-privileges-you-dont-have-on-a-managed-domain"></a>Administratörs behörighet för en hanterad domän

Den hanterade domänen är låst och du har inte behörighet att utföra vissa administrativa uppgifter på domänen. Några av följande exempel är uppgifter som du inte kan göra:

* Utöka schemat för den hanterade domänen.
* Anslut till domänkontrollanter för den hanterade domänen med hjälp av fjärr skrivbord.
* Lägg till domänkontrollanter i den hanterade domänen.
* Du har inte *domän administratörs* behörighet eller *företags administratörs* behörighet för den hanterade domänen.

## <a name="sign-in-to-the-windows-server-vm"></a>Logga in på den virtuella Windows Server-datorn

I den föregående själv studie kursen har en virtuell Windows Server-dator skapats och anslutits till den hanterade domänen. Använd den virtuella datorn för att installera hanterings verktygen. Om det behövs [följer du stegen i självstudien för att skapa och ansluta en virtuell Windows Server-dator till en hanterad domän][create-join-windows-vm].

> [!NOTE]
> I den här självstudien använder du en virtuell Windows Server-dator i Azure som är ansluten till den hanterade domänen. Du kan också använda en Windows-klient, till exempel Windows 10, som är ansluten till den hanterade domänen.
>
> Mer information om hur du installerar administrations verktyg på en Windows-klient finns i [installera verktyg för fjärrserveradministration (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Kom igång genom att ansluta till den virtuella Windows Server-datorn enligt följande:

1. I Azure Portal väljer du **resurs grupper** på den vänstra sidan. Välj den resurs grupp där den virtuella datorn skapades, till exempel *myResourceGroup*, och välj sedan den virtuella datorn, till exempel *myVM*.
1. I **översikts** fönstret för den virtuella datorn väljer du **Anslut** och sedan **skydds**.

    ![Ansluta till en virtuell Windows-dator med skydds i Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Ange autentiseringsuppgifterna för den virtuella datorn och välj sedan **Anslut**.

   ![Anslut genom skydds-värden i Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

Om det behövs kan du tillåta att webbläsaren öppnar popup-fönster för att skydds-anslutningen ska visas. Det tar några sekunder att upprätta anslutningen till den virtuella datorn.

## <a name="install-active-directory-administrative-tools"></a>Installera Active Directory administrations verktyg

Du använder samma administrativa verktyg i en hanterad domän som lokala AD DS-miljöer, till exempel Active Directory Administrationscenter (ADAC) eller AD PowerShell. Dessa verktyg kan installeras som en del av funktionen verktyg för fjärrserveradministration (RSAT) på Windows Server-och klient datorer. Medlemmar i gruppen *AAD DC-administratörer* kan sedan administrera hanterade domäner via fjärr anslutning med hjälp av dessa AD administrations verktyg från en dator som är ansluten till den hanterade domänen.

Slutför följande steg för att installera Active Directory administrations verktyg på en domänansluten virtuell dator:

1. Om **Serverhanteraren** inte öppnas som standard när du loggar in på den virtuella datorn väljer du **Start** -menyn och väljer **Serverhanteraren**.
1. I fönstret *instrument panel* i fönstret **Serverhanteraren** väljer du **Lägg till roller och funktioner**.
1. På sidan **innan du börjar** i *guiden Lägg till roller och funktioner* väljer du **Nästa**.
1. För *installations typen* låter du alternativet för **rollbaserad eller funktions baserad installation** vara markerat och väljer **Nästa**.
1. På sidan **Server val** väljer du den aktuella virtuella datorn från serverpoolen, till exempel *myvm.aaddscontoso.com*, och väljer sedan **Nästa**.
1. På sidan **Server roller** klickar du på **Nästa**.
1. På sidan **funktioner** expanderar du noden **verktyg för fjärrserveradministration** och expandera sedan noden **roll administrations verktyg** .

    Välj **AD DS och AD LDS verktygs** funktion i listan över roll administrations verktyg och välj sedan **Nästa**.

    ![Installera AD DS och AD LDS verktyg på sidan funktioner](./media/tutorial-create-management-vm/install-features.png)

1. På sidan **bekräftelse** väljer du **Installera**. Det kan ta en minut eller två att installera administrations verktyg.
1. När funktions installationen är klar väljer du **Stäng** för att avsluta guiden **Lägg till roller och funktioner** .

## <a name="use-active-directory-administrative-tools"></a>Använda Active Directory administrations verktyg

När de administrativa verktygen är installerade ska vi se hur de används för att administrera den hanterade domänen. Kontrol lera att du är inloggad på den virtuella datorn med ett användar konto som är medlem i *Administratörs gruppen för AAD-domänkontrollanten* .

1. Välj **Windows administrations verktyg** på **Start** -menyn. De administrations verktyg för AD som installeras i föregående steg visas.

    ![Lista över administrations verktyg som är installerade på servern](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Välj **Active Directory Administrationscenter**.
1. Om du vill utforska den hanterade domänen väljer du domän namnet i den vänstra rutan, till exempel *aaddscontoso*. Två behållare med namnet *AADDC datorer* och *AADDC-användare* visas överst i listan.

    ![Visa en lista över tillgängliga behållare i den hanterade domänen](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Om du vill se de användare och grupper som hör till den hanterade domänen väljer du behållaren **AADDC Users** . Användar kontona och grupperna från din Azure AD-klient listas i den här behållaren.

    I följande exempel utdata visas ett användar konto med namnet *contoso admin* och en grupp för *AAD DC-administratörer* i den här behållaren.

    ![Visa listan över Azure AD DS-domän användare i Active Directory Administrationscenter](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Om du vill se de datorer som är anslutna till den hanterade domänen väljer du behållaren **AADDC Computers** . En post för den aktuella virtuella datorn, till exempel *myVM*, visas. Dator konton för alla enheter som är anslutna till den hanterade domänen lagras i behållaren för *AADDC-datorer* .

Vanliga Active Directory Administrationscenter åtgärder som att återställa användar kontots lösen ord eller hantera grupp medlemskap är tillgängliga. Dessa åtgärder fungerar bara för användare och grupper som skapats direkt i den hanterade domänen. Identitets information synkroniseras bara *från* Azure AD till Azure AD DS. Det går inte att skriva tillbaka från Azure AD DS till Azure AD. Du kan inte ändra lösen ord eller hanterad grupp medlemskap för användare som synkroniseras från Azure AD och som synkroniseras igen.

Du kan också använda *Active Directory-modulen för Windows PowerShell* som installeras som en del av administrations verktygen för att hantera vanliga åtgärder i din hanterade domän.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Förstå de tillgängliga administrativa uppgifterna i en hanterad domän
> * Installera Active Directory administrations verktyg på en virtuell Windows Server-dator
> * Använd Active Directory Administrationscenter för att utföra vanliga uppgifter

För att på ett säkert sätt interagera med din hanterade domän från andra program, aktivera säkert Lightweight Directory Access Protocol (LDAPs).

> [!div class="nextstepaction"]
> [Konfigurera säker LDAP för din hanterade domän](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md