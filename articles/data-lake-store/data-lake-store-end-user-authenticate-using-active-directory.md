---
title: Autentisering med slutanvändare – Data Lake Storage Gen1 med Azure AD
description: Lär dig hur du uppnår autentisering för slutanvändare med Azure Data Lake Storage Gen1 med hjälp av Azure Active Directory
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: a5272f7d580a3f8a68afda9150a1e95c1807eba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103789"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autentisering med slutanvändare med Azure Data Lake Storage Gen1 med hjälp av Azure Active Directory
> [!div class="op_single_selector"]
> * [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

Azure Data Lake Storage Gen1 använder Azure Active Directory för autentisering. Innan du redigerar ett program som fungerar med Data Lake Storage Gen1 eller Azure Data Lake Analytics måste du bestämma hur du vill autentisera ditt program med Azure Active Directory (Azure AD). De två tillgängliga huvud alternativen är:

* Autentisering med slutanvändare (den här artikeln)
* Tjänst-till-tjänst-autentisering (Välj det här alternativet i list rutan ovan)

Båda dessa alternativ resulterar i att ditt program tillhandahålls med en OAuth 2,0-token som kopplas till varje begäran som görs till Data Lake Storage Gen1 eller Azure Data Lake Analytics.

Den här artikeln handlar om hur du skapar ett **internt Azure AD-program för** slutanvändare-autentisering. Instruktioner för Azure AD-programkonfiguration för tjänst-till-tjänst-autentisering finns i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 som använder Azure Active Directory](./data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Ditt prenumerations-ID. Du kan hämta den från Azure Portal. Till exempel är det tillgängligt från bladet Data Lake Storage Gen1 konto.

    ![Hämta prenumerations-ID](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Ditt Azure AD-domännamn. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. I skärm bilden nedan är domän namnet **contoso.onmicrosoft.com** och GUID inom hakparenteser är klient-ID: t.

    ![Hämta AAD-domän](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Ditt Azure-klient-ID. Instruktioner för hur du hämtar klient-ID finns i [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Den här autentiseringsmetoden är den rekommenderade metoden om du vill att en användare ska logga in på ditt program via Azure AD. Ditt program kan sedan komma åt Azure-resurser med samma åtkomst nivå som slutanvändaren som loggade in. Slutanvändaren måste ange sina autentiseringsuppgifter regelbundet för att ditt program ska ha åtkomst.

Resultatet av att slut användar inloggningen är att ditt program får en åtkomsttoken och en uppdateringstoken. Åtkomsttoken bifogas till varje begäran som görs till Data Lake Storage Gen1 eller Data Lake Analytics, och den är giltig i en timme som standard. Uppdateringstoken kan användas för att hämta en ny åtkomsttoken och den är giltig i upp till två veckor som standard. Du kan använda två olika metoder för slut användar inloggning.

### <a name="using-the-oauth-20-pop-up"></a>Använda OAuth 2,0-popup-fönster
Ditt program kan utlösa ett popup-fönster för OAuth 2,0-auktorisering där slutanvändaren kan ange sina autentiseringsuppgifter. Det här popup-fönstret fungerar också med 2FA-processen (Azure AD tvåfaktorautentisering) om det behövs.

> [!NOTE]
> Den här metoden stöds ännu inte i Azure AD Authentication Library (ADAL) för python eller Java.
>
>

### <a name="directly-passing-in-user-credentials"></a>Skicka autentiseringsuppgifter direkt i användare
Ditt program kan ange användarautentiseringsuppgifter direkt för Azure AD. Den här metoden fungerar bara med organisations-ID-användarkonton. den är inte kompatibel med användar kontona personliga/"Live ID", inklusive de konton som slutar i @outlook.com eller @live.com . Den här metoden är dessutom inte kompatibel med användar konton som kräver Azure AD tvåfaktorautentisering (2FA).

### <a name="what-do-i-need-for-this-approach"></a>Vad behöver jag för den här metoden?
* Namn på Azure AD-domän. Det här kravet är redan listat i förutsättningen för den här artikeln.
* Klient-ID för Azure AD. Det här kravet är redan listat i förutsättningen för den här artikeln.
* Internt Azure AD- **program**
* Program-ID för det inbyggda Azure AD-programmet
* Omdirigerings-URI för det interna Azure AD-programmet
* Ange delegerade behörigheter


## <a name="step-1-create-an-active-directory-native-application"></a>Steg 1: skapa ett internt Active Directory-program

Skapa och konfigurera ett internt Azure AD-program för slutanvändare-autentisering med Data Lake Storage Gen1 med hjälp av Azure Active Directory. Instruktioner finns i [skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md).

När du följer anvisningarna i länken, se till att du väljer **inbyggd** för program typ, som visas på följande skärm bild:

![Skapa webbapp](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Skapa inbyggd app")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Steg 2: Hämta program-ID och omdirigerings-URI

Läs [Hämta program-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) : t för att hämta program-ID: t.

Utför följande steg för att hämta omdirigerings-URI: n.

1. Välj **Azure Active Directory** från Azure Portal, klicka på **Appregistreringar** och leta sedan reda på och klicka på det inbyggda Azure AD-programmet som du skapade.

2. Från bladet **Inställningar** för programmet klickar du på **omdirigering av URI: er**.

    ![Hämta omdirigerings-URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Kopiera värdet som visas.


## <a name="step-3-set-permissions"></a>Steg 3: ange behörigheter

1. Välj **Azure Active Directory** från Azure Portal, klicka på **Appregistreringar** och leta sedan reda på och klicka på det inbyggda Azure AD-programmet som du skapade.

2. Klicka på **nödvändiga behörigheter** på bladet **Inställningar** för programmet och klicka sedan på **Lägg till**.

    ![Skärm bild av bladet inställningar med alternativet omdirigering U R I rutan ut och bladet omdirigering U R i bladet med det faktiska U R jag anropade.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Klicka på **Välj ett API** i bladet **Lägg till API-åtkomst** , klicka på **Azure Data Lake** och klicka sedan på **Välj**.

    ![Skärm bild av bladet Lägg till API-åtkomst med alternativet Välj ett API som kallas för och välj ett API-blad med alternativet Azure Data Lake och alternativet Välj som kallas.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  I bladet **Lägg till API-åtkomst** klickar du på **Välj behörigheter**, markerar kryss rutan för att ge **fullständig åtkomst till data Lake Store** och klickar sedan på **Välj**.

    ![Skärm bild av bladet Lägg till API-åtkomst med alternativet Välj behörigheter som anropas och bladet aktivera åtkomst med alternativet ge fullständig åtkomst till Azure Data Lake tjänsten och alternativet Välj som kallas för.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klicka på **Klar**.

5. Upprepa de senaste två stegen för att bevilja behörigheter för **Windows Azure-Service Management-API** också.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat ett internt Azure AD-program och samlat in den information du behöver i dina klient program som du skapar med .NET SDK, Java SDK, REST API osv. Nu kan du fortsätta till följande artiklar som talar om hur du använder Azure AD-webbprogrammet för att först autentisera med Data Lake Storage Gen1 och sedan utföra andra åtgärder i butiken.

* [Slutanvändarens autentisering med Data Lake Storage Gen1 med Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Autentisering med slutanvändare med Data Lake Storage Gen1 med .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Autentisering med slutanvändare med Data Lake Storage Gen1 med python](data-lake-store-end-user-authenticate-python.md)
* [Autentisering med slutanvändare med Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-end-user-authenticate-rest-api.md)