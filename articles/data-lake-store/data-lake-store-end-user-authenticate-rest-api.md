---
title: Autentisering av slutanvändare – REST med Data Lake Storage Gen1 – Azure
description: Lär dig hur du uppnår autentisering för slutanvändare med Azure Data Lake Storage Gen1 att använda Azure Active Directory med REST API
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 462cd06c9da3b1f0a57c293d52c59181372b709b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103755"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autentisering med slutanvändare med Azure Data Lake Storage Gen1 med hjälp av REST API
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

I den här artikeln får du lära dig hur du använder REST API för att utföra autentisering med slutanvändare med Azure Data Lake Storage Gen1. För tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av REST API, se [tjänst-till-tjänst-autentisering med data Lake Storage gen1 som använder REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "internt"-program**. Du måste ha slutfört stegen i [slut användar autentisering med data Lake Storage gen1 med hjälp av Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[sväng](https://curl.haxx.se/)**. I den här artikeln används en sväng för att demonstrera hur du gör REST API anrop mot ett Data Lake Storage Gen1-konto.

## <a name="end-user-authentication"></a>Slutanvändarautentisering
Autentisering med slutanvändare är den rekommenderade metoden om du vill att en användare ska logga in på ditt program med hjälp av Azure AD. Ditt program kan komma åt Azure-resurser med samma åtkomst nivå som den inloggade användaren. Användaren måste ange sina autentiseringsuppgifter med jämna mellanrum för att ditt program ska ha åtkomst.

Resultatet av att slutanvändaren har loggat in är att ditt program får en åtkomsttoken och en uppdateringstoken. Åtkomsttoken bifogas till varje begäran som görs till Data Lake Storage Gen1 eller Data Lake Analytics, och den är giltig i en timme som standard. Uppdateringstoken kan användas för att hämta en ny åtkomsttoken och den är giltig i upp till två veckor som standard, om den används regelbundet. Du kan använda två olika metoder för slut användar inloggning.

I det här scenariot uppmanar programmet användaren att logga in och alla åtgärder utförs i kontexten för användaren. Utför följande steg:

1. Omdirigera användaren via ditt program till följande URL:

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> måste kodas för användning i en URL. För https://localhost , Använd `https%3A%2F%2Flocalhost` )

    För självstudierna kan du ersätta platshållarvärdena i URL-adressen ovan och klistra in den i webbläsarens adressfält. Du omdirigeras för att autentisera med Azure-autentiseringsuppgifter. När du har loggat in visas svaret i webbläsarens adressfält. Svaret ska ha följande format:

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. Avbilda auktoriseringskoden från svaret. I den här självstudien kan du kopiera auktoriseringskod från adress fältet i webbläsaren och skicka den i POST-begäran till token-slutpunkten, som du ser i följande kodfragment:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > I det här fallet \<REDIRECT-URI> behöver de inte vara kodade.
   > 
   > 

3. Svaret är ett JSON-objekt som innehåller en åtkomsttoken (till exempel `"access_token": "<ACCESS_TOKEN>"` ) och en uppdateringstoken (till exempel `"refresh_token": "<REFRESH_TOKEN>"` ). Programmet använder åtkomsttoken vid åtkomst till Azure Data Lake Storage Gen1 och uppdateringstoken för att få en annan åtkomsttoken när en åtkomsttoken upphör att gälla.

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. När åtkomsttoken upphör att gälla kan du begära en ny åtkomsttoken med hjälp av uppdateringstoken, som du ser i följande kodfragment:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

Mer information om interaktiv användarautentisering finns i [Flöde beviljat med auktoriseringskod](/previous-versions/azure/dn645542(v=azure.100)).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder tjänst-till-tjänst-autentisering för att autentisera med Azure Data Lake Storage Gen1 med hjälp av REST API. Nu kan du titta på följande artiklar som talar om hur du använder REST API för att arbeta med Azure Data Lake Storage Gen1.

* [Konto hanterings åtgärder på Data Lake Storage Gen1 med REST API](data-lake-store-get-started-rest-api.md)
* [Data åtgärder på Data Lake Storage Gen1 med REST API](data-lake-store-data-operations-rest-api.md)