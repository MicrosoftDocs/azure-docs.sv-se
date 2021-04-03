---
title: Aktivera SharePoint-tjänsten för användar profiler med Azure AD DS | Microsoft Docs
description: Lär dig hur du konfigurerar en Azure Active Directory Domain Services hanterad domän så att den stöder profilsynkronisering för SharePoint Server
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8fbde10ccf5a7f083f5fbddaadd6668d880a1fac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619835"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Konfigurera Azure Active Directory Domain Services som stöder synkronisering av användar profiler för SharePoint Server

SharePoint Server innehåller en tjänst för att synkronisera användar profiler. Med den här funktionen kan användar profiler lagras på en central plats och nås på flera SharePoint-webbplatser och-grupper. Om du vill konfigurera tjänsten användar profil för SharePoint Server måste rätt behörigheter beviljas i en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän. Mer information finns i [synkronisering av användar profiler i SharePoint Server](/SharePoint/administration/user-profile-service-administration).

Den här artikeln visar hur du konfigurerar Azure AD DS så att synkroniseringstjänsten för användar profiler för SharePoint Server tillåts.

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
* Ett SharePoint-tjänstkonto för tjänsten för synkronisering av användar profiler.
    * Om det behövs, se [Planera för administrations-och tjänst konton i SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Översikt över tjänst konton

I en hanterad domän finns en säkerhets grupp med namnet *AAD DC-tjänstekonton* som en del av *användarnas* organisationsenhet (OU). Medlemmar i den här säkerhets gruppen har delegerats följande privilegier:

- **Replikera katalog ändringar** privilegium på rot-DSE.
- **Replikera katalog ändringar** privilegium i *konfigurationens* namngivnings kontext ( `cn=configuration` behållare).

Säkerhets gruppen *AAD DC service-konton* är också medlem i den inbyggda gruppen *för Windows 2000-kompatibel åtkomst*.

När det läggs till i den här säkerhets gruppen, beviljas tjänst kontot för SharePoint Server-synkroniseringstjänsten för användar profiler den behörighet som krävs för att fungera korrekt.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Aktivera stöd för synkronisering av SharePoint Server-användarprofil

Tjänst kontot för SharePoint Server måste ha tillräcklig behörighet för att replikera ändringar till katalogen och låta synkroniseringen av användar profiler för SharePoint Server fungera korrekt. Om du vill ange de här behörigheterna lägger du till tjänst kontot som används för synkronisering av SharePoint-användarprofil till *AAD DC Service accounts* Group.

Utför följande steg från din Azure AD DS Management VM:

> [!NOTE]
> Om du vill redigera grupp medlemskap i en hanterad domän måste du vara inloggad på ett användar konto som är medlem i *Administratörs gruppen för AAD-domänkontrollanten* .

1. Välj **administrations verktyg** på Start skärmen. En lista över tillgängliga hanterings verktyg visas som har installerats i självstudien för att [skapa en virtuell hanterings dator][tutorial-create-management-vm].
1. Om du vill hantera grupp medlemskap väljer du **Active Directory Administrationscenter** i listan över administrations verktyg.
1. I det vänstra fönstret väljer du din hanterade domän, till exempel *aaddscontoso.com*. En lista över befintliga organisationsenheter och resurser visas.
1. Välj ou för **användare** och välj sedan säkerhets gruppen *AAD DC Service accounts* .
1. Välj **medlemmar** och välj sedan **Lägg till.**...
1. Ange namnet på SharePoint-tjänstkontot och välj sedan **OK**. I följande exempel heter SharePoint-tjänstkontot *SPAdmin*:

    ![Lägg till SharePoint-tjänstkontot i säkerhets gruppen för AAD DC service-konton](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [hantera synkronisering av användar profiler i SharePoint Server](/SharePoint/administration/manage-profile-synchronization).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts