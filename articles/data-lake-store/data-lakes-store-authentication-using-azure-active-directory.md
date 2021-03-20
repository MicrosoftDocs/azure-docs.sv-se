---
title: Autentisering – Data Lake Storage Gen1 med Azure AD
description: Lär dig hur du autentiserar med Azure Data Lake Storage Gen1 med hjälp av Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6fc09f9145b7a1652b621ed38a8bf9af7c4c82a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106577"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autentisering med Azure Data Lake Storage Gen1 som använder Azure Active Directory

Azure Data Lake Storage Gen1 använder Azure Active Directory för autentisering. Innan du redigerar ett program som fungerar med Data Lake Storage Gen1 måste du bestämma hur du vill autentisera ditt program med Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Autentiseringsalternativ

* **Autentisering** med slutanvändare – en slutanvändares Azure-autentiseringsuppgifter används för att autentisera med data Lake Storage gen1. Det program som du skapar för att arbeta med Data Lake Storage Gen1 prompter för dessa användarautentiseringsuppgifter. Detta innebär att denna autentiseringsmekanism är *interaktiv* och att programmet körs i den inloggade användarens kontext. Mer information och instruktioner finns i [slut användar autentisering för data Lake Storage gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Tjänst-till-tjänst-autentisering** – Använd det här alternativet om du vill att ett program ska autentisera sig själv med data Lake Storage gen1. I sådana fall skapar du ett Azure Active Directory (AD)-program och använder nyckeln från Azure AD-programmet för att autentisera med Data Lake Storage Gen1. Detta innebär att denna autentiseringsmekanism *inte är interaktiv*. Mer information och instruktioner finns i [tjänst-till-tjänst-autentisering för data Lake Storage gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

I följande tabell visas hur autentisering med slutanvändare och tjänst-till-tjänst stöds för Data Lake Storage Gen1. Så här läser du tabellen.

* Symbolen ✔ * anger att autentiseringsalternativet stöds och länkar till en artikel som visar hur du använder alternativet autentisering. 
* ✔ Symbolen anger att autentiseringsalternativet stöds. 
* De tomma cellerna anger att alternativet autentisering inte stöds.


|Använd det här autentiseringsalternativet med...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Slutanvändare (utan MFA * *)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(inaktuell)     |    **[✔ *](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Slutanvändare (med MFA)                           |    **[✔ *](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔ *](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔ *](data-lake-store-get-started-cli-2.0.md)**      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Tjänst-till-tjänst (med klient nyckel)         |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔ *](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔ *](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔ *](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Tjänst-till-tjänst (med klient certifikat) |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klicka på <b>✔ \* </b> symbolen. Det är en länk.</i><br>
<i>* * MFA står för Multi-Factor Authentication</i>

Mer information om hur du använder Azure Active Directory för autentisering finns i  [scenarier för autentisering för Azure Active Directory](../active-directory/develop/authentication-vs-authorization.md) .

## <a name="next-steps"></a>Nästa steg

* [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)