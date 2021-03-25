---
title: Auktorisera utvecklares konton genom att använda Azure Active Directory
titleSuffix: Azure API Management
description: Lär dig hur du auktoriserar användare genom att använda Azure Active Directory i API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2021
ms.author: apimpm
ms.openlocfilehash: 743a7e7d34457405aa4be42b196dc994506c6587
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035818"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Auktorisera utvecklarkonton med Azure Active Directory i Azure API Management

Den här artikeln visar hur du aktiverar åtkomst till Developer-portalen för användare från Azure Active Directory (Azure AD). Den här guiden visar också hur du hanterar grupper med Azure AD-användare genom att lägga till externa grupper som innehåller användarna.

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
- Importera och publicera en Azure API Management-instans. Mer information finns i [import och publicering](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Auktorisera Developer-konton med hjälp av Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Välj ![Pil-ikon.](./media/api-management-howto-aad/arrow.png).
3. Skriv **API** i sökrutan.
4. Välj **API Management tjänster**.
5. Välj API Management-tjänstinstansen.
6. Välj **identiteter** under **Developer Portal**.
7. Välj **+ Lägg till** överst.

    Fönstret **Lägg till identitetsprovider** visas till höger.
8. Under **providertyp** väljer du **Azure Active Directory**.

    Kontroller som gör att du kan ange annan information som krävs visas i fönstret. Kontrollerna omfattar **klient-ID** och **klient hemlighet**. (Du får information om de här kontrollerna senare i artikeln.)
9. Anteckna innehållet i **omdirigerings-URL:** en.
    

    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Lägg till identitets leverantör i Azure Portal":::
    > [!NOTE]
    > Det finns två omdirigerings-URL: er:<br/>
    > **Omdirigerings-URL** – pekar på den senaste Developer-portalen för API Management.<br/>
    > **Omdirigerings-URL (inaktuell Portal)** – pekar på den föråldrade Developer-portalen för API Management.
    >
    > Vi rekommenderar att du använder den senaste omdirigerings portalen för utvecklare.
   
10. Öppna en annan flik i webbläsaren. 
11. Navigera till [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) för att registrera en App i Active Directory.
12. Välj **Appregistreringar** under **Hantera**.
13. Välj **ny registrering**. På sidan **Registrera ett program** ställer du in värdena enligt följande:
    
    * Ange ett beskrivande namn som **namn** . t. ex. *Developer – Portal*
    * Ange **konto typer som stöds** till **konton endast i den här organisations katalogen**. 
    * Ange **omdirigerings-URI** till värdet som du fick från steg 9. 
    * Välj **Registrera**. 

14.  När programmet har registrerats kopierar du **program-ID: t (klient)-ID: t** från **översikts** sidan. 
15. Gå tillbaka till API Management-instansen. I fönstret **Lägg till identitetsprovider** klistrar du in **programmets (klient) ID-** värde i rutan **klient-ID** .
16. Växla tillbaka till Azure AD-konfigurationen, Välj **certifikat & hemligheter** under **Hantera**. Klicka på knappen **Ny klienthemlighet**. Ange ett värde i **Beskrivning**, välj ett alternativ för **Förfaller** och välj **Lägg till**. Kopiera klientens hemliga värde innan du lämnar sidan. Du behöver den i nästa steg. 
17. Under **Hantera** väljer du **autentisering** och väljer sedan **ID-token** under **implicit beviljande**
18. Gå tillbaka till API Management instansen och klistra in hemligheten i rutan **klient hemlighet** .

    > [!IMPORTANT]
    > Var noga med att uppdatera **klient hemligheten** innan nyckeln upphör att gälla. 
    >  
    >

19. Fönstret **Lägg till identitetsprovider** innehåller också text rutan **tillåtna innehavare** . Där anger du domänerna för de Azure AD-instanser som du vill bevilja åtkomst till API: erna för API Management tjänst instansen. Du kan separera flera domäner med newlines, blank steg eller kommatecken.

    > [!NOTE]
    > Du kan ange flera domäner i avsnittet **tillåtna innehavare** . Innan en användare kan logga in från en annan domän än den ursprungliga domän där programmet registrerades måste en global administratör för den andra domänen ge behörighet för programmet för att få åtkomst till katalog data. För att bevilja behörighet bör den globala administratören: a. Gå till `https://<URL of your developer portal>/aadadminconsent` (till exempel https://contoso.portal.azure-api.net/aadadminconsent) .
    > b. Skriv in domän namnet för den Azure AD-klient som de vill ge åtkomst till.
    > c. Välj **Skicka**. 

20.  När du har angett önskad konfiguration väljer du **Lägg till**.

När ändringarna har sparats kan användare i den angivna Azure AD-instansen logga in på Developer-portalen genom att följa stegen i [Logga in på Developer-portalen med ett Azure AD-konto](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Lägg till en extern Azure AD-grupp

När du har aktiverat åtkomst för användare i en Azure AD-klient kan du lägga till Azure AD-grupper i API Management. Det innebär att du kan kontrol lera produkt synlighet med hjälp av Azure AD-grupper.

Om du vill lägga till en extern Azure AD-grupp i APIM måste du först slutföra det föregående avsnittet. Dessutom måste det program som du har registrerat beviljas åtkomst till Microsoft Graph-API: et med `Directory.Read.All` behörighet genom att följa dessa steg: 

1. Gå tillbaka till din app-registrering som skapades i föregående avsnitt.
2. Välj **API-behörigheter** och klicka sedan på **+ Lägg till en behörighet**. 
3. I rutan **begär API-behörigheter** väljer du fliken **Microsoft API: er** , bläddrar nedåt och väljer sedan panelen **Azure Active Directory diagram** . Välj **program behörigheter**, Sök efter **katalog** och välj sedan **katalogen. Read. all** behörighet. 
4. Klicka på **Lägg till behörigheter** längst ned i fönstret och klicka sedan på **bevilja administrativt medgivande för {tenantname}** så att du beviljar åtkomst för alla användare i den här katalogen. 

Nu kan du lägga till externa Azure AD-grupper från fliken **grupper** i API Management-instansen.

1. Välj fliken **Grupper**.
2. Välj knappen **Lägg till AAD-grupp** .
    ![Knappen Lägg till AAD-grupp](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Välj den grupp som du vill lägga till.
4. Tryck på knappen **Välj** .

När du har lagt till en extern Azure AD-grupp kan du granska och konfigurera dess egenskaper. Välj namnet på gruppen från fliken **grupper** . Härifrån kan du redigera **namn** och **beskrivnings** information för gruppen.
 
Användare från den konfigurerade Azure AD-instansen kan nu logga in på Developer-portalen. De kan visa och prenumerera på grupper som de har synlighet för.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> Utvecklings Portal – Lägg till Azure AD-konto-autentisering

I Developer-portalen är det möjligt att logga in med AAD med **inloggnings knappen: OAuth-** widgeten. Widgeten är redan inkluderad på inloggnings sidan för standard innehållet i Developer-portalen.

Även om ett nytt konto skapas automatiskt när en ny användare loggar in med AAD, kan du överväga att lägga till samma widget på registrerings sidan.

**Registrerings formuläret: OAuth** widget representerar ett formulär som används för att registrera sig med OAuth.

> [!IMPORTANT]
> Du måste [publicera portalen](api-management-howto-developer-portal-customize.md#publish) igen för att ändringarna i AAD ska börja gälla.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Äldre utvecklares Portal – så här loggar du in med Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Logga in på Developer-portalen genom att använda ett Azure AD-konto som du konfigurerade i föregående avsnitt:

1. Öppna ett nytt webbläsarfönster genom att använda inloggnings-URL: en från Active Directory program konfigurationen och välj **Azure Active Directory**.

   ![Inloggnings sida][api-management-dev-portal-signin]

1. Ange autentiseringsuppgifterna för en av användarna i Azure AD och välj **Logga** in.

   ![Logga in med användar namn och lösen ord][api-management-aad-signin]

1. Du kan uppmanas att ange ett registrerings formulär om det krävs ytterligare information. Fyll i registrerings formuläret och välj **Registrera dig**.

   ![Knappen "Registrera dig" i registrerings formuläret][api-management-complete-registration]

Användaren är nu inloggad på Developer-portalen för din API Management tjänst instans.

![Developer-portalen när registreringen är klar][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
