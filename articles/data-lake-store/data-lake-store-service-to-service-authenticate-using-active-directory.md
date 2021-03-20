---
title: Autentisering från tjänst till tjänst – Data Lake Storage Gen1 – Azure
description: Lär dig hur du uppnår tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med hjälp av Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 9dc195f98310e63cbde06885effe86ea3c239249
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576106"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 som använder Azure Active Directory
> [!div class="op_single_selector"]
> * [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 använder Azure Active Directory för autentisering. Innan du redigerar ett program som fungerar med Data Lake Storage Gen1 måste du bestämma hur du vill autentisera ditt program med Azure Active Directory (Azure AD). De två tillgängliga huvud alternativen är:

* Slutanvändarautentisering 
* Tjänst-till-tjänst-autentisering (den här artikeln) 

Båda dessa alternativ resulterar i att ditt program tillhandahålls med en OAuth 2,0-token som kopplas till varje begäran som görs till Data Lake Storage Gen1.

Den här artikeln handlar om hur du skapar ett **Azure AD-webbprogram för tjänst-till-tjänst-autentisering**. Instruktioner för autentisering av Azure AD-program för slutanvändare finns i [autentisering med slutanvändare med data Lake Storage gen1 med hjälp av Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Steg 1: skapa ett Active Directory-webbprogram

Skapa och konfigurera ett Azure AD-webbprogram för tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med hjälp av Azure Active Directory. Instruktioner finns i [skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md).

När du följer anvisningarna i föregående länk, se till att du väljer **Web App/API** för program typ, som visas på följande skärm bild:

![Skapa webbapp](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Skapa webbapp")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Steg 2: Hämta program-ID, autentiseringsnyckel och klient-ID
När program mässigt logga in måste du ha ID: t för ditt program. Om programmet körs under sina egna autentiseringsuppgifter behöver du också en autentiseringsnyckel.

* Instruktioner för hur du hämtar program-ID och autentiseringsnyckel (kallas även klient hemlighet) för ditt program finns i [Hämta program-ID och autentiseringsnyckel](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

* Instruktioner för hur du hämtar klient-ID finns i [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Steg 3: tilldela Azure AD-programmet till Azure Data Lake Storage Gen1 kontots fil eller mapp


1. Logga in på [Azure Portal](https://portal.azure.com). Öppna det Data Lake Storage Gen1-konto som du vill associera med det Azure Active Directory-program som du skapade tidigare.
2. I bladet Data Lake Storage Gen1 konto klickar du på **datautforskaren**.
   
    ![Skapa kataloger i Data Lake Storage Gen1 konto](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Skapa kataloger i Data Lake konto")
3. Klicka på den fil eller mapp i bladet **datautforskaren** som du vill ge åtkomst till Azure AD-programmet och klicka sedan på **åtkomst**. Om du vill konfigurera åtkomst till en fil måste du klicka på **åtkomst** från bladet för **fil granskning** .
   
    ![Ange ACL: er på Data Lake fil system](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Ange ACL: er på Data Lake fil system")
4. **Access** -bladet visar standard åtkomst och anpassad åtkomst som redan har tilldelats till roten. Klicka på ikonen **Lägg** till för att lägga till ACL: er för anpassad nivå.
   
    ![Lista standard och anpassad åtkomst](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Lista standard och anpassad åtkomst")
5. Klicka på ikonen **Lägg** till för att öppna bladet **Lägg till anpassad åtkomst** . I det här bladet klickar du på **Välj användare eller grupp** och letar sedan efter det Azure Active Directory program som du skapade tidigare i **Välj användare eller grupp** blad. Om du har många grupper att söka från använder du text rutan överst för att filtrera efter grupp namnet. Klicka på den grupp som du vill lägga till och klicka sedan på **Välj**.
   
    ![Lägg till en grupp](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Lägg till en grupp")
6. Klicka på **Välj behörigheter**, Välj behörigheter och ange om du vill tilldela behörighet som standard-ACL, åtkomst-ACL eller båda. Klicka på **OK**.
   
    ![Skärm bild av bladet Lägg till anpassad åtkomst med alternativet Välj behörigheter som anropas och bladet Select Permissions med alternativet OK som kallas.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Tilldela behörigheter till grupp")
   
    Mer information om behörigheter i Data Lake Storage Gen1 och standard-/åtkomst-ACL: er finns i [Access Control i data Lake Storage gen1](data-lake-store-access-control.md).
7. I bladet **Lägg till anpassad åtkomst** klickar du på **OK**. De nyligen tillagda grupperna, med tillhör ande behörigheter, visas i **Access** -bladet.
   
    ![Skärm bild av Access-bladet med den nyligen tillagda gruppen som anropas i avsnittet anpassad åtkomst.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Tilldela behörigheter till grupp")

> [!NOTE]
> Om du planerar att begränsa ditt Azure Active Directory program till en specifik mapp, måste du också ge samma Azure Active Directory-program behörighet att **köra** behörighet till roten för att aktivera åtkomst till filer via .NET SDK.

> [!NOTE]
> Om du vill använda SDK: er för att skapa ett Data Lake Storage Gen1 konto måste du tilldela Azure AD-webbappen som en roll till resurs gruppen där du skapar Data Lake Storage Gen1-kontot.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Steg 4: Hämta OAuth 2,0-token-slutpunkt (endast för Java-baserade program)

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på Active Directory i det vänstra fönstret.

2. Klicka på **Appregistreringar** i det vänstra fönstret.

3. Klicka på **slut punkter** överst på Appregistreringar bladet.

    ![Skärm bild av Active Directory med alternativet Appregistreringar och alternativet slut punkter som kallas för.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth token-slutpunkt")

4. I listan över slut punkter kopierar du OAuth 2,0-token-slutpunkt.

    ![Skärm bild av bladet slut punkter med ikonen O för o-TOKEn o-TOKEn för slut punkts kopiering.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth token-slutpunkt")   

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat ett Azure AD-webbprogram och samlat in den information du behöver i dina klient program som du skapar med .NET SDK, Java, python, REST API osv. Nu kan du fortsätta till följande artiklar som talar om hur du använder det interna Azure AD-programmet för att först autentisera med Data Lake Storage Gen1 och sedan utföra andra åtgärder i butiken.

* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Java](data-lake-store-service-to-service-authenticate-java.md)
* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med python](data-lake-store-service-to-service-authenticate-python.md)
* [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 som använder REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


