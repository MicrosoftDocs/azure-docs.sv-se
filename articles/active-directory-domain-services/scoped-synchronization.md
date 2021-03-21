---
title: Omfångs synkronisering för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att konfigurera omfångst synkronisering från Azure AD till en Azure Active Directory Domain Services hanterad domän
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34692f5e563e4931a27ea59db84d9c88f27817da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660906"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Konfigurera omfångst synkronisering från Azure AD till Azure Active Directory Domain Services med hjälp av Azure Portal

Azure Active Directory Domain Services (Azure AD DS) för att tillhandahålla Authentication Services synkroniserar användare och grupper från Azure AD. I en hybrid miljö kan användare och grupper från en lokal Active Directory Domain Services-miljö (AD DS) först synkroniseras till Azure AD med hjälp av Azure AD Connect och sedan synkroniseras till en hanterad Azure AD DS-domän.

Som standard synkroniseras alla användare och grupper från en Azure AD-katalog till en hanterad domän. Om du har speciella behov kan du istället välja att bara synkronisera en definierad uppsättning användare.

Den här artikeln visar hur du konfigurerar omfångst synkronisering och sedan ändrar eller inaktiverar uppsättningen med begränsade användare med hjälp av Azure Portal. Du kan också [utföra dessa steg med hjälp av PowerShell][scoped-sync-powershell].

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][tutorial-create-instance].
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att ändra synkroniserings omfånget för Azure AD DS.

## <a name="scoped-synchronization-overview"></a>Översikt över omfånget synkronisering

Som standard synkroniseras alla användare och grupper från en Azure AD-katalog till en hanterad domän. Om bara några få användare behöver åtkomst till den hanterade domänen kan du bara synkronisera dessa användar konton. Den här omfånget är gruppbaserad. När du konfigurerar gruppbaserad omsynkronisering synkroniseras bara de användar konton som tillhör de grupper som du anger till den hanterade domänen. Kapslade grupper är inte synkroniserade, bara de enskilda grupper som du väljer.

Du kan ändra omfånget för synkronisering före eller efter att du har skapat den hanterade domänen. Omfånget för synkronisering definieras av ett huvud namn för tjänsten med program identifieraren 2565bd9d-DA50-47d4-8b85-4c97f669dc36. Du kan förhindra omfattnings förlust genom att inte ta bort eller ändra tjänstens huvud namn. Om det tas bort av misstag går det inte att återställa omfånget för synkronisering. 

Tänk på följande varningar om du ändrar omfånget för synkronisering:

- En fullständig synkronisering sker.
- Objekt som inte längre behövs i den hanterade domänen tas bort. Nya objekt skapas i den hanterade domänen.

Läs mer om synkroniseringsprocessen i [förstå synkronisering i Azure AD Domain Services][concepts-sync].

## <a name="enable-scoped-synchronization"></a>Aktivera omfångs synkronisering

Utför följande steg för att aktivera omfångs synkronisering i Azure Portal:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services**. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. Välj **synkronisering** på menyn till vänster.
1. För *synkroniseringens typ* väljer du **omfattning**.
1. Välj **Välj grupper** och Sök sedan efter och välj de grupper som ska läggas till.
1. När alla ändringar görs väljer du **Spara omfånget för synkronisering**.

Om du ändrar omfånget för synkroniseringen synkroniserar den hanterade domänen om alla data. Objekt som inte längre behövs i den hanterade domänen tas bort och omsynkroniseringen kan ta lite tid att slutföra.

## <a name="modify-scoped-synchronization"></a>Ändra omfångs synkronisering

Utför följande steg om du vill ändra listan över grupper vars användare ska synkroniseras till den hanterade domänen:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services**. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. Välj **synkronisering** på menyn till vänster.
1. Om du vill lägga till en grupp väljer du **+ Välj grupper** högst upp och väljer sedan de grupper som ska läggas till.
1. Om du vill ta bort en grupp från omfånget synkronisering väljer du den i listan över synkroniserade grupper och väljer **ta bort grupper**.
1. När alla ändringar görs väljer du **Spara omfånget för synkronisering**.

Om du ändrar omfånget för synkroniseringen synkroniserar den hanterade domänen om alla data. Objekt som inte längre behövs i den hanterade domänen tas bort och omsynkroniseringen kan ta lite tid att slutföra.

## <a name="disable-scoped-synchronization"></a>Inaktivera omfångs synkronisering

Utför följande steg för att inaktivera gruppbaserad omsynkronisering för en hanterad domän:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services**. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. Välj **synkronisering** på menyn till vänster.
1. Ändra *synkroniseringens typ* från **omfång** till **alla** och välj sedan **Spara omfånget för synkronisering**.

Om du ändrar omfånget för synkroniseringen synkroniserar den hanterade domänen om alla data. Objekt som inte längre behövs i den hanterade domänen tas bort och omsynkroniseringen kan ta lite tid att slutföra.

## <a name="next-steps"></a>Nästa steg

Läs mer om synkroniseringsprocessen i [förstå synkronisering i Azure AD Domain Services][concepts-sync].

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
