---
title: Lägga till och hantera Azure AD-program – Azure Marketplace
description: Lär dig hur du lägger till och hanterar Azure AD-program för det kommersiella Marketplace-programmet i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108483"
---
# <a name="add-and-manage-azure-ad-applications"></a>Lägga till och hantera Azure AD-program

**Lämpliga roller**

- Ägare
- Ansvarig

Du kan tillåta att program eller tjänster som ingår i företagets Azure Active Directory (Azure AD) får åtkomst till ditt partner Center-konto.

## <a name="add-existing-azure-ad-applications"></a>Lägg till befintliga Azure AD-program

Så här lägger du till program som redan finns i företagets Azure Active Directory:

1. Från sidan **användare** (under **konto inställningar**) väljer du **Lägg till Azure AD-program**.
1. Välj ett eller flera Azure AD-program i listan som visas. Du kan använda sökrutan för att söka efter särskilda Azure AD-program. Om du väljer mer än ett Azure AD-program som ska läggas till i ditt partner Center-konto måste du tilldela samma roll eller uppsättning anpassade behörigheter. Om du vill lägga till flera Azure AD-program med olika roller/behörigheter upprepar du de här stegen för varje roll eller uppsättning anpassade behörigheter.
1. När du är färdig med att välja Azure AD-program väljer du **Lägg till markerade**.
1. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för de valda Azure AD-programmen.
1. Välj **Spara**.

## <a name="add-new-azure-ad-applications"></a>Lägg till nya Azure AD-program

Om du vill bevilja åtkomst till Partner Center till ett helt nytt Azure AD-programkonto kan du skapa ett i avsnittet **användare** . Då skapas ett nytt konto i ditt företags arbets konto (Azure AD-klient), inte bara i ditt partner Center-konto. Om du huvudsakligen använder Azure AD-programmet för partner Center-autentisering och inte behöver användare för att komma åt den direkt, kan du ange en giltig adress för **svars-URL:** en och **app-ID-URI: n**, förutsatt att dessa värden inte används av något annat Azure AD-program i katalogen.

1. Från sidan **användare** (under **konto inställningar**) väljer du **Lägg till Azure AD-program**.
1. På nästa sida väljer du **nytt Azure AD-program**.
1. Ange **svars-URL** för det nya Azure AD-programmet. Detta är URL: en där användarna kan logga in och använda ditt Azure AD-program (kallas ibland även för appens URL eller Sign-On-URL). *Svars-URL: en* får inte vara längre än 256 tecken och måste vara unik i din katalog.
1. Ange **app-ID-URI** för det nya Azure AD-programmet. Detta är en logisk identifierare för Azure AD-programmet som presenteras när en begäran om enkel inloggning skickas till Azure AD. *App-ID-URI: n* måste vara unik för varje Azure AD-program i din katalog. Detta ID får inte vara längre än 256 tecken. Mer information om app-ID-URI: n finns i [integrera program med Azure Active Directory](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts).
1. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för Azure AD-programmet.
1. Välj **Spara**.

När du har lagt till eller skapat ett Azure AD-program kan du gå tillbaka till avsnittet **användare** och välja program namnet för att granska inställningarna för programmet, inklusive klient-ID, klient-ID, svars-URL och app-ID-URI.

## <a name="remove-an-azure-ad-application"></a>Ta bort ett Azure AD-program

Om du vill ta bort ett program från ditt arbets konto (Azure AD-klient) går du till **användare** (under **konto inställningar**), väljer det program som du vill ta bort med hjälp av kryss rutan längst till höger och väljer sedan **ta bort** från de tillgängliga åtgärderna. Ett popup-fönster visas där du bekräftar att du vill ta bort de valda programmen.

## <a name="manage-keys-for-an-azure-ad-application"></a>Hantera nycklar för ett Azure AD-program

Om ditt Azure AD-program läser och skriver data i Microsoft Azure AD behöver den en nyckel. Du kan skapa nycklar för ett Azure AD-program genom att redigera dess information i Partner Center. Du kan också ta bort nycklar som inte längre behövs.

1. Från sidan **användare** (under **konto inställningar**) väljer du namnet på Azure AD-programmet. Du ser alla aktiva nycklar för Azure AD-programmet, inklusive det datum då nyckeln skapades och när den upphör att gälla 50.
1. Om du vill ta bort en nyckel som inte längre behövs väljer du **ta bort**.
1. Om du vill lägga till en ny nyckel väljer du **Lägg till ny nyckel**.
1. En skärm bild som visar **klient-ID** och **nyckel värden** visas. Se till att skriva ut eller kopiera den här informationen, eftersom du inte kan komma åt den igen när du lämnar den här sidan.
1. Om du vill skapa fler nycklar väljer du **Lägg till en annan nyckel**.
