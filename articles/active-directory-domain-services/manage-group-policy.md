---
title: Skapa och hantera grup princip i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du redigerar inbyggda grup princip objekt (GPO) och hur du skapar egna anpassade principer i en Azure Active Directory Domain Services hanterad domän.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f1f2499c49c4adf16b632bc75c246a28330ad27b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619393"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Administrera grupprincip i en Azure Active Directory Domain Services hanterad domän

Inställningar för användar-och dator objekt i Azure Active Directory Domain Services (Azure AD DS) hanteras ofta med hjälp av grupprincip objekt (GPO). Azure AD DS innehåller inbyggda grup princip objekt för behållare för *AADDC-användare* och AADDC- *datorer* . Du kan anpassa de här inbyggda grupprincipobjekten för att konfigurera grupprincip efter behov för din miljö. Medlemmar i gruppen *Azure AD DC-administratörer* har Grupprincip administratörs behörighet i Azure AD DS-domänen och kan också skapa anpassade grup princip objekt och organisationsenheter (OU). Mer information om vad grupprincip är och hur det fungerar finns i [Grupprincip översikt][group-policy-overview].

I en hybrid miljö synkroniseras inte grup principer som kon figurer ATS i en lokal AD DS-miljö med Azure AD DS. Om du vill definiera konfigurations inställningar för användare eller datorer i Azure AD DS redigerar du en av standard grup princip objekt eller skapar ett anpassat grup princip objekt.

Den här artikeln visar hur du installerar grupprincip hanterings verktyg och sedan redigerar de inbyggda grupprincipobjekten och skapar anpassade grup princip objekt.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* En virtuell Windows Server Management-dator som är ansluten till den hanterade Azure AD DS-domänen.
    * Om det behövs, slutför du själv studie kursen för att [skapa en virtuell Windows Server-dator och koppla den till en hanterad domän][create-join-windows-vm].
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.

> [!NOTE]
> Du kan använda grupprincip Administrativa mallar genom att kopiera de nya mallarna till hanterings arbets stationen. Kopiera *. admx* -filerna till `%SYSTEMROOT%\PolicyDefinitions` och kopiera språkspecifika *. adml* -filer till `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` , där `Language-CountryRegion` matchar språk och region för *. adml* -filerna.
>
> Kopiera till exempel den engelska USA versionen av *. adml* -filerna till `\en-us` mappen.
>
> Alternativt kan du centralt lagra din grupprincip administrativa mall på domän kontrol Lanterna som ingår i den hanterade domänen. Mer information finns i [så här skapar och hanterar du den centrala butiken för grupprincip administrativa mallar i Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>Installera grupprincip hanterings verktyg

Om du vill skapa och konfigurera grupprincip objekt (GPO) måste du installera grupprincip hanterings verktyg. Dessa verktyg kan installeras som en funktion i Windows Server. Mer information om hur du installerar administrations verktyg på en Windows-klient finns i installera [verktyg för fjärrserveradministration (RSAT)][install-rsat].

1. Logga in på den virtuella hanterings datorn. Anvisningar om hur du ansluter med hjälp av Azure Portal finns i [ansluta till en virtuell Windows Server-dator][connect-windows-server-vm].
1. **Serverhanteraren** bör öppnas som standard när du loggar in på den virtuella datorn. Annars väljer du **Serverhanteraren** på **Start** -menyn.
1. I fönstret *instrument panel* i fönstret **Serverhanteraren** väljer du **Lägg till roller och funktioner**.
1. På sidan **innan du börjar** i *guiden Lägg till roller och funktioner* väljer du **Nästa**.
1. För *installations typen* låter du alternativet för **rollbaserad eller funktions baserad installation** vara markerat och väljer **Nästa**.
1. På sidan **Server val** väljer du den aktuella virtuella datorn från serverpoolen, till exempel *myvm.aaddscontoso.com*, och väljer sedan **Nästa**.
1. På sidan **Server roller** klickar du på **Nästa**.
1. På sidan **funktioner** väljer du funktionen **Grupprincip hantering** .

    ![Installera grupprincip hantering från funktions Sidan](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. På sidan **bekräftelse** väljer du **Installera**. Det kan ta en minut eller två att installera grupprincip hanterings verktyg.
1. När funktions installationen är klar väljer du **Stäng** för att avsluta guiden **Lägg till roller och funktioner** .

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Öppna konsolen grupprinciphantering och redigera ett objekt

Det finns standard grup princip objekt (GPO) för användare och datorer i en hanterad domän. Med den grupprincip hanterings funktionen installerad från föregående avsnitt kan vi visa och redigera ett befintligt grup princip objekt. I nästa avsnitt skapar du ett anpassat grup princip objekt.

> [!NOTE]
> Om du vill administrera en grup princip i en hanterad domän måste du vara inloggad på ett användar konto som är medlem i *Administratörs gruppen för AAD-domänkontrollanten* .

1. Välj **administrations verktyg** på Start skärmen. En lista över tillgängliga hanterings verktyg visas, inklusive **Grupprincip hantering** som installerades i föregående avsnitt.
1. Öppna konsolen grupprinciphantering (GPMC) genom att välja **Grupprincip hantering**.

    ![konsolen grupprinciphantering öppnar redigera grup princip objekt](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Det finns två inbyggda grupprincip objekt (GPO: er) i en hanterad domän – ett för behållaren *AADDC-datorer* och en för behållaren *AADDC Users* . Du kan anpassa dessa grup princip objekt för att konfigurera grup principer efter behov i din hanterade domän.

1. I konsolen **Grupprincip hantering** expanderar du noden **skog: aaddscontoso.com** . Därefter expanderar du noderna **domäner** .

    Två inbyggda behållare finns för AADDC- *datorer* och *AADDC-användare*. Var och en av dessa behållare har ett standard grup princip objekt som tillämpas på dem.

    ![Inbyggda grup princip objekt tillämpas på standard behållarna "AADDC datorer" och "AADDC Users"](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Dessa inbyggda grup princip objekt kan anpassas för att konfigurera vissa grup principer på din hanterade domän. Högerklicka på en av grupprincipobjekten, till exempel *AADDC Computers GPO*, och välj sedan **Redigera.**...

    ![Välj alternativet för att redigera ett av de inbyggda grupprincipobjekten](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Verktyget Redigeraren Grupprinciphantering öppnas så att du kan anpassa GRUPPRINCIPOBJEKTet, till exempel *konto principer*:

    ![Skärm bild av Redigeraren Grupprinciphantering.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    När du är färdig väljer du **arkiv > Spara** för att spara principen. Datorer uppdaterar grupprincip som standard var 90: e minut och tillämpar de ändringar du gjort.

## <a name="create-a-custom-group-policy-object"></a>Skapa ett anpassat grupprincip-objekt

För att gruppera liknande princip inställningar skapar du ofta ytterligare grup princip objekt i stället för att tillämpa alla nödvändiga inställningar i GRUPPRINCIPOBJEKTet Single, default. Med Azure AD DS kan du skapa eller importera egna anpassade grup princip objekt och länka dem till en anpassad ORGANISATIONSENHET. Om du behöver skapa en anpassad ORGANISATIONSENHET första gången, se [skapa en anpassad organisationsenhet i en hanterad domän](create-ou.md).

1. I **Grupprincip hanterings** konsolen väljer du din anpassade ORGANISATIONSENHET (OU), till exempel *MyCustomOU*. Högerklicka på ORGANISATIONSENHETen och välj **skapa ett grup princip objekt i den här domänen och länka det här...**:

    ![Skapa ett anpassat grup princip objekt i grupprincip hanterings konsolen](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Ange ett namn för det nya GRUPPRINCIPOBJEKTet, till exempel *mitt anpassade grup princip objekt*, och välj sedan **OK**. Du kan också basera det här anpassade GRUPPRINCIPOBJEKTet på ett befintligt grup princip objekt och en uppsättning princip alternativ.

    ![Ange ett namn för det nya anpassade GRUPPRINCIPOBJEKTet](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Det anpassade GRUPPRINCIPOBJEKTet skapas och länkas till din anpassade ORGANISATIONSENHET. Du kan nu konfigurera princip inställningarna genom att högerklicka på det anpassade GRUPPRINCIPOBJEKTet och välja **Redigera...**:

    ![Välj alternativet för att redigera ditt anpassade grup princip objekt](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. **Redigeraren Grupprinciphantering** öppnas så att du kan anpassa grupprincipobjektet:

    ![Anpassa GPO för att konfigurera inställningar efter behov](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    När du är färdig väljer du **arkiv > Spara** för att spara principen. Datorer uppdaterar grupprincip som standard var 90: e minut och tillämpar de ändringar du gjort.

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga grupprincip inställningar som du kan konfigurera med hjälp av konsolen grupprinciphantering finns i [arbeta med Grupprincip inställnings objekt][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
