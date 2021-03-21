---
title: Skapa en organisationsenhet (OU) i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du skapar och hanterar en anpassad organisationsenhet (OU) i en Azure AD Domain Services hanterad domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fbdfcc23553a27aaa4d7bbd4bff9c4f33cb8fd19
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620026"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Skapa en organisationsenhet (OU) i en Azure Active Directory Domain Services hanterad domän

Organisationsenheter (OU) i en Active Directory Domain Services (AD DS)-hanterad domän gör det möjligt att logiskt gruppera objekt, till exempel användar konton, tjänst konton eller dator konton. Du kan sedan tilldela administratörer till specifika organisationsenheter och tillämpa grup principer för att genomdriva aktuella konfigurations inställningar.

Azure AD DS-hanterade domäner innehåller följande två inbyggda organisationsenheter:

* *AADDC-datorer* – innehåller dator objekt för alla datorer som är anslutna till den hanterade domänen.
* *AADDC-användare* – innehåller användare och grupper som är synkroniserade i från Azure AD-klienten.

När du skapar och kör arbets belastningar som använder Azure AD DS kan du behöva skapa tjänst konton för att program ska kunna autentisera sig själva. För att organisera dessa tjänst konton skapar du ofta en anpassad ORGANISATIONSENHET i den hanterade domänen och skapar sedan tjänst konton inom den ORGANISATIONSENHETen.

I en hybrid miljö synkroniseras inte organisationsenheter som skapats i en lokal AD DS-miljö till den hanterade domänen. Hanterade domäner använder en ORGANISATIONSENHETs struktur. Alla användar konton och grupper lagras i behållaren *AADDC Users* , trots att de synkroniseras från olika lokala domäner eller skogar, även om du har konfigurerat en hierarkisk ou-struktur där.

Den här artikeln visar hur du skapar en ORGANISATIONSENHET i din hanterade domän.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* En virtuell Windows Server Management-dator som är ansluten till den hanterade Azure AD DS-domänen.
    * Om det behövs kan du slutföra självstudien för att [skapa en virtuell hanterings dator][tutorial-create-management-vm].
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.

## <a name="custom-ou-considerations-and-limitations"></a>Överväganden och begränsningar för anpassade OU

När du skapar anpassade organisationsenheter i en hanterad domän får du ytterligare hanterings möjligheter för användar hantering och användning av grup principer. Jämfört med en lokal AD DS-miljö finns det några begränsningar och överväganden när du skapar och hanterar en anpassad OU-struktur i en hanterad domän:

* Om du vill skapa anpassade organisationsenheter måste användare vara medlem i gruppen *AAD DC-administratörer* .
* En användare som skapar en anpassad ORGANISATIONSENHET beviljas administratörs behörighet (fullständig behörighet) över ORGANISATIONSENHETen och är resurs ägare.
    * Som standard innehåller *Administratörs gruppen för AAD-domänkontrollanten* även fullständig kontroll över den anpassade organisationsenheten.
* En standard-OU för *AADDC-användare* skapas som innehåller alla synkroniserade användar konton från din Azure AD-klient.
    * Du kan inte flytta användare eller grupper från *AADDC-användarens* organisationsenhet till anpassade organisationsenheter som du skapar. Endast användar konton eller resurser som skapats i den hanterade domänen kan flyttas till anpassade organisationsenheter.
* Användar konton, grupper, tjänst konton och dator objekt som du skapar under anpassade organisationsenheter är inte tillgängliga i din Azure AD-klient.
    * De här objekten visas inte med Microsoft Graph API eller i användar gränssnittet för Azure AD; de är bara tillgängliga i din hanterade domän.

## <a name="create-a-custom-ou"></a>Skapa en anpassad ORGANISATIONSENHET

Om du vill skapa en anpassad ORGANISATIONSENHET använder du Active Directory administrations verktyg från en domänansluten virtuell dator. Med Active Directory Administrationscenter kan du Visa, redigera och skapa resurser i en hanterad domän, inklusive organisationsenheter.

> [!NOTE]
> Om du vill skapa en anpassad ORGANISATIONSENHET i en hanterad domän måste du vara inloggad på ett användar konto som är medlem i *Administratörs gruppen för AAD-domänkontrollanten* .

1. Logga in på den virtuella hanterings datorn. Anvisningar om hur du ansluter med hjälp av Azure Portal finns i [ansluta till en virtuell Windows Server-dator][connect-windows-server-vm].
1. Välj **administrations verktyg** på Start skärmen. En lista över tillgängliga hanterings verktyg visas som har installerats i självstudien för att [skapa en virtuell hanterings dator][tutorial-create-management-vm].
1. Om du vill skapa och hantera organisationsenheter väljer du **Active Directory Administrationscenter** i listan över administrations verktyg.
1. I det vänstra fönstret väljer du din hanterade domän, till exempel *aaddscontoso.com*. En lista över befintliga organisationsenheter och resurser visas:

    ![Välj din hanterade domän i Active Directory Administrationscenter](./media/create-ou/create-ou-adac-overview.png)

1. Fönstret **uppgifter** visas till höger om Active Directory Administrationscenter. Under domänen, till exempel *aaddscontoso.com*, väljer du **ny > organisationsenhet**.

    ![Välj alternativet för att skapa en ny ORGANISATIONSENHET i Active Directory Administrationscenter](./media/create-ou/create-ou-adac-new-ou.png)

1. I dialog rutan **skapa organisationsenhet** anger du ett **namn** för den nya organisationsenheten, till exempel *MyCustomOu*. Ange en kort beskrivning av ORGANISATIONSENHETen, till exempel *anpassad organisationsenhet för tjänst konton*. Om du vill kan du också ange fältet som **hanteras av** för organisationsenheten. Välj **OK** om du vill skapa en anpassad Organisationsenhet.

    ![Skapa en anpassad ORGANISATIONSENHET från Active Directory Administrationscenter](./media/create-ou/create-ou-dialog.png)

1. I Active Directory Administrationscenter visas den anpassade ORGANISATIONSENHETen nu och är tillgänglig för användning:

    ![Anpassad ORGANISATIONSENHET som är tillgänglig för användning i Active Directory Administrationscenter](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder administrations verktyg eller skapar och använder tjänst konton finns i följande artiklar:

* [Active Directory Administrationscenter: Komma igång](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [Stegvisa instruktioner för tjänstkonton](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm