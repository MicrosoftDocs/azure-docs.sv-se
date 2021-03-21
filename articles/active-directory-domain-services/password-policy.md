---
title: Skapa och Använd lösen ords principer i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur och varför du kan använda detaljerade lösen ords principer för att skydda och kontrol lera konto lösen ord i en hanterad Azure AD DS-domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: df132af1675b3f373fe1eab5685c5d2f07813445
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619240"
---
# <a name="password-and-account-lockout-policies-on-azure-active-directory-domain-services-managed-domains"></a>Principer för lösen ords-och konto utelåsning på Azure Active Directory Domain Services hanterade domäner

Om du vill hantera användar säkerhet i Azure Active Directory Domain Services (Azure AD DS) kan du definiera detaljerade lösen ords principer som styr inställningarna för konto utelåsning eller minsta längd och komplexitet för lösen ord. En standard detaljerad lösen ords princip skapas och tillämpas på alla användare i en Azure AD DS-hanterad domän. För att ge detaljerad kontroll och uppfylla specifika krav för affärs-eller efterlevnad kan ytterligare principer skapas och tillämpas på specifika användar grupper.

Den här artikeln visar hur du skapar och konfigurerar en detaljerad lösen ords princip i Azure AD DS med hjälp av Active Directory Administrationscenter.

> [!NOTE]
> Lösen ords principer är bara tillgängliga för hanterade domäner som skapats med distributions modellen Resource Manager. För äldre hanterade domäner som skapats med klassisk [migrering från den klassiska virtuella nätverks modellen till Resource Manager][migrate-from-classic].

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
  * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
  * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
  * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
  * Den hanterade domänen måste ha skapats med hjälp av distributions modellen för Resource Manager. Om det behövs [migrerar du från den klassiska virtuella nätverks modellen till Resource Manager][migrate-from-classic].
* En virtuell Windows Server Management-dator som är ansluten till den hanterade domänen.
  * Om det behövs kan du slutföra självstudien för att [skapa en virtuell hanterings dator][tutorial-create-management-vm].
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.

## <a name="default-password-policy-settings"></a>Standard princip inställningar för lösen ord

Med detaljerade lösen ords principer (FGPP) kan du använda vissa begränsningar för lösen ord och konto utelåsnings principer för olika användare i en domän. För att skydda privilegierade konton kan du till exempel använda striktare konto utelåsnings inställningar än vanliga icke-privilegierade konton. Du kan skapa flera FGPP i en hanterad domän och ange prioritetsordningen för användarna.

Mer information om lösen ords principer och hur du använder Active Directory administrations Center finns i följande artiklar:

* [Lär dig mer om detaljerade lösen ords principer](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurera detaljerade lösen ords principer med hjälp av administrations Center för AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

Principer distribueras via grupp associationen i en hanterad domän, och alla ändringar du gör tillämpas vid nästa användar inloggning. Att ändra principen låser inte upp ett användar konto som redan är låst.

Lösen ords principer beter sig lite annorlunda beroende på hur det användar konto som de tillämpas på har skapats. Det finns två sätt att skapa ett användar konto i Azure AD DS:

* Användar kontot kan synkroniseras i från Azure AD. Detta inkluderar endast molnbaserade användar konton som skapas direkt i Azure och hybrid användar konton som synkroniseras från en lokal AD DS-miljö med hjälp av Azure AD Connect.
    * Majoriteten av användar kontona i Azure AD DS skapas genom synkroniseringsprocessen från Azure AD.
* Användar kontot kan skapas manuellt i en hanterad domän och finns inte i Azure AD.

Alla användare, oavsett hur de har skapats, har följande principer för konto utelåsning som används av standard lösen ords principen i Azure AD DS:

* **Varaktighet för konto utelåsning:** 30
* **Antal misslyckade inloggnings försök som tillåts:** 5
* **Återställ antal misslyckade inloggnings försök efter:** 30 minuter
* **Högsta ålder för lösen ord (livs längd):** 90 dagar

Med dessa standardinställningar är användar konton utelåsta i 30 minuter om fem ogiltiga lösen ord används inom två minuter. Konton låses automatiskt upp efter 30 minuter.

Konto utelåsning sker bara i den hanterade domänen. Användar konton är bara utelåsta i Azure AD DS och endast på grund av misslyckade inloggnings försök mot den hanterade domänen. Användar konton som synkroniserats i från Azure AD eller lokalt är inte utelåsta i sina käll kataloger, bara i Azure AD DS.

Om du har en lösen ords princip för Azure AD som anger en högsta ålder för lösen ord som är större än 90 dagar tillämpas lösen ordets ålder på standard principen i Azure AD DS. Du kan konfigurera en anpassad lösen ords princip för att definiera en annan högsta ålder för lösen ord i Azure AD DS. Var försiktig om du har en kortare högsta ålder för lösen ord som kon figurer ATS i en Azure AD DS-lösenord än i Azure AD eller i en lokal AD DS-miljö. I det scenariot kan en användares lösen ord gå ut i Azure AD DS innan de uppmanas att ändra i Azure AD eller en lokal AD DS-miljö.

För användar konton som skapats manuellt i en hanterad domän, tillämpas även följande ytterligare lösen ords inställningar från standard principen. Dessa inställningar gäller inte för användar konton som synkroniserats i från Azure AD, eftersom en användare inte kan uppdatera sitt lösen ord direkt i Azure AD DS.

* **Minsta längd på lösen ord (tecken):** 7
* **Lösen ord måste uppfylla komplexitets kraven**

Du kan inte ändra konto utelåsnings-eller lösen ords inställningarna i standard lösen ords principen. I stället kan medlemmar i *Administratörs* gruppen för AAD-domänkontrollanter skapa anpassade lösen ords principer och konfigurera den att åsidosätta (prioriteras framför) den inbyggda standard principen, som visas i nästa avsnitt.

## <a name="create-a-custom-password-policy"></a>Skapa en anpassad lösen ords princip

När du skapar och kör program i Azure kanske du vill konfigurera en anpassad lösen ords princip. Du kan till exempel skapa en princip för att ange olika princip inställningar för konto utelåsning.

Anpassade lösen ords principer tillämpas på grupper i en hanterad domän. Den här konfigurationen åsidosätter standard principen.

Om du vill skapa en anpassad lösen ords princip använder du Active Directory administrations verktyg från en domänansluten virtuell dator. Med Active Directory Administrationscenter kan du Visa, redigera och skapa resurser i en hanterad domän, inklusive organisationsenheter.

> [!NOTE]
> Om du vill skapa en anpassad lösen ords princip i en hanterad domän måste du vara inloggad på ett användar konto som är medlem i *Administratörs gruppen för AAD-domänkontrollanten* .

1. Välj **administrations verktyg** på Start skärmen. En lista över tillgängliga hanterings verktyg visas som har installerats i självstudien för att [skapa en virtuell hanterings dator][tutorial-create-management-vm].
1. Om du vill skapa och hantera organisationsenheter väljer du **Active Directory Administrationscenter** i listan över administrations verktyg.
1. I det vänstra fönstret väljer du din hanterade domän, till exempel *aaddscontoso.com*.
1. Öppna **system** behållaren, sedan **Password Settings Container**.

    En inbyggd lösen ords princip för den hanterade domänen visas. Du kan inte ändra den här inbyggda principen. Skapa i stället en anpassad lösen ords princip som åsidosätter standard principen.

    ![Skapa en lösen ords princip i Active Directory Administrationscenter](./media/password-policy/create-password-policy-adac.png)

1. I **aktivitets** panelen till höger väljer du **nytt > lösen ords inställningar**.
1. I dialog rutan **skapa lösen ords inställningar** anger du ett namn för principen, till exempel *MyCustomFGPP*.
1. När det finns flera lösen ords principer tillämpas principen med högst prioritet, eller prioritet, för en användare. Ju lägre siffra, desto högre prioritet. Standard lösen ords principen har prioritet *200*.

    Ange prioriteten för din anpassade lösen ords princip för att åsidosätta standardvärdet, till exempel *1*.

1. Redigera andra inställningar för lösen ords principer som du vill. Kom ihåg följande viktiga punkter:

    * Inställningar som lösen ords komplexitet, ålder eller förfallo tid endast för användare som skapats manuellt i en hanterad domän.
    * Inställningarna för konto utelåsning gäller för alla användare, men börjar bara gälla i den hanterade domänen och inte i själva Azure AD.

    ![Skapa en egen detaljerad lösen ords princip](./media/password-policy/custom-fgpp.png)

1. Avmarkera **skydda från oavsiktlig borttagning**. Om det här alternativet är markerat kan du inte spara FGPP.
1. I avsnittet **tillämpas direkt på** väljer du knappen **Lägg till** . Välj knappen **platser** i dialog rutan **Välj användare eller grupper** .

    ![Välj de användare och grupper som lösen ords principen ska tillämpas på](./media/password-policy/fgpp-applies-to.png)

1. Lösen ords principer kan bara tillämpas på grupper. I dialog rutan **platser** expanderar du domän namnet, till exempel *aaddscontoso.com*, och väljer sedan en organisationsenhet, till exempel **AADDC-användare**. Om du har en anpassad ORGANISATIONSENHET som innehåller en grupp med användare som du vill tillämpa väljer du den ORGANISATIONSENHETen.

    ![Välj den ORGANISATIONSENHET som gruppen tillhör](./media/password-policy/fgpp-container.png)

1. Skriv namnet på den grupp som du vill tillämpa principen på och välj sedan **kontrol lera namn** för att kontrol lera att gruppen finns.

    ![Sök efter och välj gruppen som ska användas för FGPP](./media/password-policy/fgpp-apply-group.png)

1. Med namnet på den grupp som du valde nu, som visas **direkt** i avsnittet, väljer du **OK** för att spara din anpassade lösen ords princip.

## <a name="next-steps"></a>Nästa steg

Mer information om lösen ords principer och hur du använder Active Directory administrations Center finns i följande artiklar:

* [Lär dig mer om detaljerade lösen ords principer](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurera detaljerade lösen ords principer med hjälp av administrations Center för AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
