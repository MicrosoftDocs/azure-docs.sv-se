---
title: Partner Center-överförings-API för att publicera Azure-appar på Microsofts kommersiella marknads plats
description: 'Läs om kraven för att använda Partner Center-sändnings-API: t för Azure-appar på en kommersiell Marketplace på Microsoft Partner Center.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9863ed24da9e427f885a4794bda7e103b0c1cc8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96455459"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>Partner Center-överförings-API till att publicera Azure-appar i Partner Center

Använd *partner Centers överförings-API* för att skicka frågor till program mässigt, skapa bidrag för och publicera Azure-erbjudanden.  Detta API är användbart om ditt konto hanterar många erbjudanden och du vill automatisera och optimera sändnings processen för dessa erbjudanden.

## <a name="api-prerequisites"></a>Krav för API

Det finns några programmerings resurser som du behöver för att kunna använda Partner Center API för Azure-produkter: 

- ett Azure Active Directory-program.
- en Azure Active Directory (Azure AD)-åtkomsttoken.

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Steg 1: Slutför nödvändiga komponenter för att använda Partner Center-API: et för överföring

Innan du börjar skriva kod som anropar API: t för partner Center-sändning kontrollerar du att du har slutfört följande krav.

- Du (eller din organisation) måste ha en Azure AD-katalog och du måste ha [Global administratörs](../../active-directory/roles/permissions-reference.md) behörighet för katalogen. Om du redan använder Microsoft 365 eller andra företags tjänster från Microsoft har du redan Azure AD-katalogen. Annars kan du [skapa en ny Azure AD i Partner Center](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) utan extra kostnad.

- Du måste [associera ett Azure AD-program med ditt partner Center-konto](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) och hämta klient-ID, klient-ID och nyckel. Du behöver dessa värden för att få en Azure AD-åtkomsttoken, som du kommer att använda i anrop till Microsoft Store sändnings-API: et.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Så här kopplar du ett Azure AD-program till ditt partner Center-konto

Om du vill använda API: et för Microsoft Store sändning måste du associera ett Azure AD-program med ditt partner Center-konto, Hämta klient-ID och klient-ID för programmet och generera en nyckel. Azure AD-programmet representerar den app eller tjänst som du vill anropa partner centret för överförings-API: t från. Du behöver klient-ID, klient-ID och nyckel för att få en Azure AD-åtkomsttoken som du skickar till API: et.

>[!Note]
>Du behöver bara utföra den här uppgiften en gång. När du har klient-ID, klient-ID och nyckel kan du återanvända dem varje gång du behöver skapa en ny Azure AD-åtkomsttoken.

1. [Koppla din organisations Partner Center-konto till din organisations Azure AD-katalog](/windows/uwp/publish/associate-azure-ad-with-partner-center)i Partner Center.
1. Gå sedan till sidan **användare** i avsnittet **konto inställningar** i Partner Center och [Lägg till Azure AD-programmet](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) som representerar den app eller tjänst som du kommer att använda för att få åtkomst till bidrag för ditt partner Center-konto. Se till att tilldela det här programmet rollen **chef** . Om programmet inte finns än i Azure AD-katalogen kan du [skapa ett nytt Azure AD-program i Partner Center](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Gå tillbaka till sidan **användare** , klicka på namnet på ditt Azure AD-program för att gå till program inställningarna och kopiera klient **-ID: t** och **klient-ID** -värdena.
1. Klicka på **Lägg till ny nyckel**. På följande skärm kopierar du värdet för **nyckel** . Du kommer inte att kunna komma åt den här informationen igen när du lämnar den här sidan. Mer information finns i [Hantera nycklar för ett Azure AD-program](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Steg 2: skaffa en Azure AD-åtkomsttoken

Innan du anropar någon av metoderna i överförings-API: et för partner Center måste du först skaffa en Azure AD-åtkomsttoken som du skickar till **Authorization** -huvudet för varje metod i API: et. När du har fått en åtkomsttoken har du 60 minuter att använda den innan den upphör att gälla. När token har gått ut kan du uppdatera token så att du kan fortsätta att använda den i framtida anrop till API: et.

Hämta åtkomsttoken genom att följa anvisningarna i [tjänst-till-tjänst-anrop med klientautentiseringsuppgifterna](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) för att skicka en `HTTP POST` till `https://login.microsoftonline.com/<tenant_id>/oauth2/token` slut punkten. Här är en exempel förfrågan:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

För *tenant_id* -värdet i `POST URI` parametrarna och *client_id* och *CLIENT_SECRET* , anger du klient-ID, klient-ID och nyckel för ditt program som du hämtade från Partner Center i föregående avsnitt. För *resurs* parametern måste du ange `https://api.partner.microsoft.com` .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Steg 3: Använd Microsoft Store sändnings-API: et

När du har en Azure AD-åtkomsttoken kan du anropa metoder i överförings-API: et för partner Center. Om du vill skapa eller uppdatera bidrag kan du vanligt vis anropa flera metoder i överförings-API: et för partner Center i en speciell ordning. Information om varje scenario och syntaxen för varje metod finns i inmatnings-API-Swagger.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Nästa steg

* Lär dig att skapa en [teknisk till gång för Azure VM](../create-azure-container-technical-assets.md)
* Lär dig hur du skapar ett [erbjudande för Azure Container](../create-azure-container-offer.md)