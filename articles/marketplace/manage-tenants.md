---
title: Hantera klienter på den kommersiella Marketplace – Azure Marketplace
description: Lär dig hur du hanterar klienter för programmet för kommersiella marknads platser i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 446792b26527126a4db99da14a2585c17cf8610c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108456"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>Hantera klienter på den kommersiella marknaden

**Lämpliga roller**

- Ansvarig

En Azure Active Directory-klient (AD), som även kallas ditt *arbets konto* i den här dokumentationen, är en representation av organisationen som är konfigurerad i Azure Portal och hjälper dig att hantera en särskild instans av Microsofts moln tjänster för dina interna och externa användare. Om din organisation prenumererar på en moln tjänst från Microsoft, till exempel Azure, Microsoft Intune eller Microsoft 365, upprättades en Azure AD-klient åt dig.

Du kan konfigurera flera klienter som ska användas med partner Center. Du kanske vill göra detta om ditt företag har flera klienter (till exempel contoso.com, contoso.uk och så vidare) kan du länka ytterligare klienter här. Med den här associationen kan du lägga till och hantera användare från ytterligare klienter på ditt kommersiella marknads plats konto.

Alla användare med rollen chef i Partner Center-kontot kan välja att lägga till och ta bort Azure AD-klienter från kontot.

## <a name="add-an-existing-tenant"></a>Lägg till en befintlig klient

Koppla en annan Azure AD-klient till ditt partner Center-konto:

1. I det övre högra hörnet av Partner Center väljer du **Inställningar**  >  **konto inställningar**.
1. Under **organisations profil** väljer du **klienter**. Aktuella klient associationer visas.
1. På fliken **utvecklare** väljer du **associera**.
1. Ange dina autentiseringsuppgifter för Azure AD för den klient som du vill koppla.
1. Granska organisations-och domän namnet för din Azure AD-klient. Slutför kopplingen genom att välja **Bekräfta**.

Om kopplingen lyckas är du redo att lägga till och hantera konto användare i avsnittet användare i Partner Center. Mer information om hur du lägger till användare finns i [Hantera användare](add-manage-users.md).

## <a name="create-a-new-tenant"></a>Skapa en ny klientorganisation

För att skapa en helt ny Azure AD-klient med ditt partner Center-konto:

1. I det övre högra hörnet av Partner Center väljer du **Inställningar**  >  **konto inställningar**.
1. Under **organisations profil** väljer du **klienter**. Aktuella klient associationer visas.
1. På fliken Utvecklare väljer du **skapa**.
1. Ange katalog informationen för din nya Azure AD:
    - **Domän namn**: det unika namn som vi använder för din Azure AD-domän, tillsammans med ". onmicrosoft.com". Om du till exempel har angett "example" är din Azure AD-domän "example.onmicrosoft.com".
    - **Kontakta e-** postadressen: en e-postadress där vi kan kontakta dig om ditt konto om det behövs.
    - **Användar konto information för global administratör**: förnamn, efter namn, användar namn och lösen ord som du vill använda för det nya globala administratörs kontot.
1. Välj Skapa för att bekräfta den nya domänen och konto informationen.
1. Logga in med ditt nya Azure AD global-administratörs användar namn och lösen ord för att börja [lägga till och hantera användare](add-manage-users.md).

Mer information om hur du skapar nya klienter i din Azure Portal, i stället för partner Center-portalen, finns i artikeln [skapa en ny klient i Azure Active Directory](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

## <a name="remove-a-tenant"></a>Ta bort en klient

Om du vill ta bort en klient från ditt partner Center-konto letar du reda på namnet på sidan **innehavare** (i **konto inställningar**) och väljer sedan **ta bort**. Du uppmanas att bekräfta att du vill ta bort klient organisationen. När du har gjort det kommer inga användare i den klienten att kunna logga in på Partner Center-kontot och alla behörigheter som du har konfigurerat för dessa användare kommer att tas bort.

> [!TIP]
> Du kan inte ta bort en klient om du är inloggad på Partner Center med ett konto i samma klient organisation. Om du vill ta bort en klient måste du logga in på Partner Center som **hanterare** för en annan klient som är kopplad till kontot. Om det bara finns en klient som är associerad med kontot, kan klienten bara tas bort när du har loggat in med Microsoft-konto som öppnade kontot.
