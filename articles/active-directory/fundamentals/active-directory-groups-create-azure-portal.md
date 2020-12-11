---
title: Skapa en basgrupp och lägga till medlemmar – Azure Active Directory | Microsoft Docs
description: Anvisningar för hur du skapar en basgrupp med Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/05/2020
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8041656a11689d0cca64c3edae1af2e70409028
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027721"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory
Du kan skapa en basgrupp med hjälp av Azure Active Directory (Azure AD)-portalen. I den här artikeln läggs en basgrupp till i en enskild resurs av resursägaren (administratören) och innehåller specifika medlemmar (anställda) som behöver åtkomst till den här resursen. Mer komplicerade scenarier, inklusive dynamiskt medlemskap och regelskapande, finns i [dokumentationen för Azure Active Directory-användarhantering](../enterprise-users/index.yml).

## <a name="group-and-membership-types"></a>Grupp-och medlemskaps typer
Det finns flera grupper och medlemskaps typer. Följande information förklarar varje grupp och medlemskaps typ och varför de används, för att hjälpa dig att avgöra vilka alternativ som ska användas när du skapar en grupp.

### <a name="group-types"></a>Grupp typer:
- **Säkerhet**. Används för att hantera medlems- och datoråtkomst till delade resurser för en grupp användare. Du kan till exempel skapa en säkerhetsgrupp för en specifik säkerhetsprincip. På det här sättet kan du ge en uppsättning behörigheter till alla medlemmar på samma gång utan att behöva lägga till behörigheterna för varje medlem individuellt. En säkerhets grupp kan ha användare, enheter, grupper och tjänstens huvud namn som medlemmar och användare och tjänstens huvud namn som ägare. Mer information om hur du hanterar åtkomst till resurser finns i [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md).
- **Microsoft 365**. Ger samarbetsmöjligheter genom att ge medlemmar tillgång till en delad postlåda, kalender, filer, SharePoint-webbplats och mer. Med det här alternativet kan du också ge personer utanför organisationen åtkomst till gruppen. En Microsoft 365 grupp kan bara ha användare som medlemmar. Både användare och tjänstens huvud namn kan vara ägare till en Microsoft 365 grupp. Mer information om Microsoft 365 grupper finns i [Läs mer om Microsoft 365 grupper](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

### <a name="membership-types"></a>Medlemskaps typer:
- **ATS.** Låter dig lägga till specifika användare som medlemmar i den här gruppen med unika behörigheter. I den här artikeln använder vi det här alternativet.
- **Dynamisk användare.** Gör att du kan använda dynamiska medlemskaps regler för att automatiskt lägga till och ta bort medlemmar. Om en medlems attribut ändras så tittar systemet på dina dynamiska gruppregler för katalogen för att se om medlemmen uppfyller regelkraven (läggs till) eller inte längre uppfyller regelkraven (tas bort).
- **Dynamisk enhet.** Låter dig använda dynamiska gruppregler för att automatiskt lägga till och ta bort enheter. Om en enhets attribut ändras så tittar systemet på dina dynamiska gruppregler för katalogen för att se om enheten uppfyller regelkraven (läggs till) eller inte längre uppfyller regelkraven (tas bort).

    > [!IMPORTANT]
    > Du kan skapa en dynamisk grupp för antingen enheter eller användare, men inte både och. Du kan inte heller skapa en enhetsgrupp baserat på enhetsägarens attribut. Medlemskapsregler för enheten kan bara referera till enhetens uppgifter. Mer information om hur du skapar en dynamisk grupp för användare och enheter finns i [skapa en dynamisk grupp och kontrol lera status](../enterprise-users/groups-create-rule.md)

## <a name="create-a-basic-group-and-add-members"></a>Skapa en basgrupp och lägg till medlemmar
Du kan skapa en basgrupp och lägga till dina medlemmar på samma gång. Använd följande procedur för att skapa en grundläggande grupp och lägga till medlemmar:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

1. Sök efter och välj **Azure Active Directory**.

1. På sidan **Active Directory** väljer du **grupper** och väljer sedan **ny grupp**.

    ![Sidan Azure AD med grupper som visar](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Fönstret **ny grupp** visas och du måste fylla i den information som krävs.

    ![Ny gruppsida ifylld med exempelinformation](media/active-directory-groups-create-azure-portal/new-group-blade.png)

1. Välj en fördefinierad **grupptyp**. Mer information om grupp typer finns i [grupp-och medlemskaps typer](#group-types).

1. Skapa och Lägg till ett **grupp namn.** Välj ett namn som du kommer ihåg och som passar för gruppen. En kontroll görs för att avgöra om namnet redan används av en annan grupp. Om namnet redan används, så uppmanas du att ändra namnet på din grupp för att undvika dubbla namn.

1. Lägg till en **grupps e-postadress** för gruppen eller Behåll e-postadressen som fylls i automatiskt.

1. **Grupp beskrivning.** Lägg till en valfri beskrivning till din grupp.

1. Välj en fördefinierad **medlemskaps typ (krävs).** Mer information om medlemskaps typer finns i [grupp-och medlemskaps typer](#membership-types).

1. Välj **Skapa**. Din grupp skapas och är redo att lägga till medlemmar.

1. Välj området **Medlemmar** från den **Grupp**-sidan och börja söka efter medlemmar att lägga till i din grupp från sidan **Välj medlemmar**.

    ![Välj medlemmar för din grupp när gruppen skapas](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. När du är klar med att lägga till medlemmar, väljer du **Välj**.

    Sidan **Gruppöversikt** uppdateras för att visa antalet medlemmar som nu lagts till i gruppen.

    ![Gruppöversiktssidan med antalet medlemmar markerat](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-group-welcome-email"></a>Aktivera eller inaktivera e-post för grupp Välkommen

När en ny Microsoft 365 grupp skapas, oavsett om det är ett dynamiskt eller statiskt medlemskap, skickas ett välkomst meddelande till alla användare som läggs till i gruppen. När alla attribut för en användare eller enhet ändras bearbetas alla dynamiska grupp regler i organisationen för potentiella medlemskaps ändringar. Användare som läggs till får även välkomst meddelandet. Du kan inaktivera det här beteendet i [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup). 

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomst till SaaS-appar med grupper](../enterprise-users/groups-saasapps.md)
- [Hantera grupper med PowerShell-kommandon](../enterprise-users/groups-settings-v2-cmdlets.md)
