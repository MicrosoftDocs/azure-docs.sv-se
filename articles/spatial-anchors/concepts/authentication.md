---
title: Autentisering och auktorisering
description: Lär dig mer om de olika sätt som en app eller tjänst kan autentisera till Azure spatiala ankare och de kontroll nivåer som du måste använda för att få åtkomst till spatiala ankare.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 01065f9ac26599d26d6e2a6979eae1e559a82854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97722971"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autentisering och auktorisering till Azure spatiala ankare

I den här artikeln får du lära dig de olika sätt som du kan använda för att autentisera till Azure spatiala ankare från din app eller webb tjänst. Du får också lära dig hur du kan använda Azures rollbaserade åtkomst kontroll (Azure RBAC) i Azure Active Directory (Azure AD) för att styra åtkomsten till dina konton för spatialdata.

## <a name="overview"></a>Översikt

![Diagram som visar en översikt över autentisering till Azure spatiala ankare.](./media/spatial-anchors-authentication-overview.png)

För att få åtkomst till ett specifikt konto för kontot för Azure-spatialdata måste klienterna först skaffa en åtkomsttoken från Azure Mixed Reality Security Token Service (STS). Token som erhålls från STS har en livs längd på 24 timmar. De innehåller information som spatiala ankare använder för att fatta auktoriseringsbeslut på kontot och se till att endast behöriga huvud konton har åtkomst till kontot.

Åtkomsttoken kan erhållas i Exchange för antingen konto nycklar eller token som utfärdats av Azure AD.

Med konto nycklar kan du snabbt komma igång med att använda tjänsten Azure spatial ankare. Men innan du distribuerar ditt program till produktion, rekommenderar vi att du uppdaterar din app för att använda Azure AD-autentisering.

Du kan hämta Azure AD-autentiseringstoken på två sätt:

- Om du skapar ett företags program och ditt företag använder Azure AD som identitets system, kan du använda användar-baserad Azure AD-autentisering i din app. Du beviljar sedan åtkomst till kontona för spatiala ankare med hjälp av dina befintliga Azure AD-säkerhetsgrupper. Du kan också ge åtkomst direkt till användare i din organisation.
- Annars rekommenderar vi att du skaffar Azure AD-tokens från en webb tjänst som stöder din app. Vi rekommenderar den här metoden för produktions program eftersom det gör att du kan undvika att bädda in autentiseringsuppgifterna för åtkomst till Azure spatiala ankare i klient programmet.

## <a name="account-keys"></a>Konto nycklar

Det enklaste sättet att komma igång är att använda konto nycklar för åtkomst till ditt Azure-konto för spatiala ankare. Du kan hämta dina konto nycklar på Azure Portal. Gå till ditt konto och välj fliken **nycklar** :

![Skärm bild som visar fliken nycklar med kopierings knappen för den primära nyckeln markerad.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Två nycklar är tillgängliga. Båda är samtidigt giltiga för åtkomst till kontot för spatiala ankare. Vi rekommenderar att du regelbundet uppdaterar den nyckel som du använder för att få åtkomst till kontot. Om du har två separata giltiga nycklar aktive ras dessa uppdateringar utan drift avbrott. Du behöver bara uppdatera primär nyckeln och den sekundära nyckeln.

SDK: n har inbyggt stöd för autentisering via konto nycklar. Du behöver bara ange `AccountKey` egenskapen för ditt `cloudSession` objekt:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

När du har angett egenskapen hanterar SDK utbyte av konto nyckeln för en åtkomsttoken och nödvändig cachelagring av token för din app.

> [!WARNING]
> Vi rekommenderar att du använder konto nycklar för snabb registrering, men bara vid utveckling/prototypering. Vi rekommenderar inte att du levererar ditt program till produktion med en inbäddad konto nyckel i det. Använd i stället de användarbaserade eller tjänstebaserade Azure AD-autentiseringarna som beskrivs nedan.

## <a name="azure-ad-user-authentication"></a>Azure AD-användarautentisering

För program som riktar Azure Active Directory användare rekommenderar vi att du använder en Azure AD-token för användaren. Du kan hämta denna token med hjälp av [MSAL](../../active-directory/develop/msal-overview.md). Följ stegen i snabb starten [när du registrerar en app](../../active-directory/develop/quickstart-register-app.md), bland annat:

**I Azure Portal**
1.    Registrera ditt program i Azure AD som ett inbyggt program. Som en del av registreringen måste du bestämma om ditt program ska vara flera innehavare. Du måste också ange de omdirigerings-URL: er som tillåts för ditt program.
1.  Gå till fliken **API-behörigheter** .
2.  Välj **Lägg till en behörighet**.
    1.  Välj **resurs leverantör för Mixad verklighet** på fliken **API: er som min organisation använder** .
    2.  Välj **delegerade behörigheter**.
    3.  Välj **mixedreality. signin** under **mixedreality**.
    4.  Välj **Lägg till behörigheter**.
3.  Välj **bevilja administratörs medgivande**.

2. Ge ditt program eller dina användare åtkomst till din resurs:
   1.    Gå till resurser för spatial ankare i Azure Portal.
   2.    Gå till fliken **åtkomst kontroll (IAM)** .
   3.    Välj **Lägg till rolltilldelning**.
   1.    [Välj en roll](#azure-role-based-access-control).
   2.    I rutan **Välj** anger du namnen på de användare, grupper och/eller program som du vill tilldela åtkomst till.
   3.    Välj **Spara**.

**I din kod**
1.    Se till att använda program-ID och omdirigerings-URI för ditt eget Azure AD **-program för klient-ID** och **RedirectUri** -parametrar i MSAL.
2.    Ange klient information:
        1.    Om ditt program **endast stöder min organisation** ersätter du värdet med **klient-ID** eller **klient namn**. Till exempel contoso.microsoft.com.
        2.    Om ditt program har stöd **för konton i en organisations katalog** ersätter du värdet med **organisationer**.
        3.    Om programmet har stöd för **alla Microsoft-konto användare ersätter du** det här värdet med **vanligt**.
3.    På din Tokenbegäran anger du **omfånget** till **" `https://sts.<account-domain>//.default` "**, där `<account-domain>` ersätts med **konto domänen** för ditt Azure-konto för spatialdata. Ett exempel på ett Azure-konto för spatial ankare i kontot för USA, östra 2 konto domänen är **" `https://sts.mixedreality.azure.com//.default` "**. Det här omfånget indikerar till Azure AD att ditt program begär en token för tjänsten Mixed Reality säkerhetstokentjänst (STS).

När du har slutfört de här stegen bör programmet kunna hämta från MSAL till en Azure AD-token. Du kan ange att Azure AD-token som `authenticationToken` i konfigurations objekt för moln sessionen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD-tjänsteautentisering

För att distribuera appar som använder Azures spatialdata till produktion, rekommenderar vi att du använder en backend-tjänst som hanterar begär Anden från Broker-autentisering. Här är en översikt över processen:

![Diagram som ger en översikt över autentisering till Azure spatiala ankare.](./media/spatial-anchors-aad-authentication.png)

Här förutsätter vi att appen använder sin egen mekanism för att autentisera till backend-tjänsten. (Till exempel ett Microsoft-konto, PlayFab, Facebook, ett Google ID eller ett eget användar namn och lösen ord.)  När dina användare har autentiserats till backend-tjänsten kan tjänsten hämta en Azure AD-token, byta ut den mot en åtkomsttoken för Azure spatiala ankare och tillbaka tillbaka till klient programmet.

Azure AD-åtkomsttoken hämtas via [MSAL](../../active-directory/develop/msal-overview.md). Följ stegen i snabb starten för att [Registrera en app](../../active-directory/develop/quickstart-register-app.md)som innehåller:

**I Azure Portal**
1.    Registrera ditt program i Azure AD:
        1.    I Azure Portal väljer du **Azure Active Directory** och väljer sedan **Appregistreringar**.
        2.    Välj **ny registrering**.
        3.    Ange namnet på programmet, Välj **webbapp/API** som program typ och ange autentiserings-URL: en för din tjänst. Välj **Skapa**.
2.    Välj **Inställningar** på programmet och välj sedan fliken **certifikat och hemligheter** . Skapa en ny klient hemlighet, Välj en varaktighet och välj sedan **Lägg till**. Se till att spara det hemliga värdet. Du måste ta med den i din webb tjänst kod.
3.    Ge ditt program och/eller användare åtkomst till din resurs:
        1.    Gå till resurser för spatial ankare i Azure Portal.
        2.    Gå till fliken **åtkomst kontroll (IAM)** .
        3.    Välj **Lägg till rolltilldelning**.
        4.    [Välj en roll](#azure-role-based-access-control).
        5.    I rutan **Välj** anger du namn eller namn för de program som du vill tilldela åtkomst till. Om du vill att appens användare ska ha olika roller mot kontot för spatiala ankare, registrerar du flera program i Azure AD och tilldelar en separat roll till var och en. Implementera sedan din auktoriserings logik för att använda rätt roll för dina användare.

              > [!NOTE]
              > I fönstret **Lägg till roll tilldelning** , i **tilldela åtkomst till** väljer du **Azure AD-användare, grupp eller tjänstens huvud namn**.

        6.    Välj **Spara**.

**I din kod**

>[!NOTE]
> Du kan använda [tjänst exemplet](https://github.com/Azure/azure-spatial-anchors-samples/tree/master/Sharing/SharingServiceSample) som är tillgängligt som en del av de [spatiala ankare som exempel appar](https://github.com/Azure/azure-spatial-anchors-samples).

1.    Se till att använda program-ID, program hemlighet och omdirigerings-URI för ditt eget Azure AD-program som **klient-ID**, **hemligheter** och **RedirectUri** -parametrar i MSAL.
2.    Ange klient-ID: t till ditt egen Azure AD-klient-ID i **auktoritets** parametern i MSAL.
3.    På din Tokenbegäran anger du **omfånget** till **" `https://sts.<account-domain>//.default` "**, där `<account-domain>` ersätts med **konto domänen** för ditt Azure-konto för spatialdata. Ett exempel på ett Azure-konto för spatial ankare i kontot för USA, östra 2 konto domänen är **" `https://sts.mixedreality.azure.com//.default` "**.

När du har slutfört de här stegen kan Server dels tjänsten hämta en Azure AD-token. Den kan sedan utväxla den för en MR-token som den kommer tillbaka till klienten. Att använda en Azure AD-token för att hämta en MR-token görs via ett REST-anrop. Här är ett exempel på ett anrop:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Authorization-huvudet är formaterat på följande sätt: `Bearer <Azure_AD_token>`

Svaret innehåller MR-token i oformaterad text.

Den MR-token returneras sedan till klienten. Klient programmet kan sedan ange den som åtkomsttoken i Cloud session-konfigurationen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

För att hjälpa dig att kontrol lera åtkomst nivån för program, tjänster eller Azure AD-användare av tjänsten kan du tilldela de här befintliga rollerna efter behov mot dina konton för ditt konto för spatialdata i Azure:

- **Konto ägare för spatiala ankare**. Program eller användare som har den här rollen kan skapa spatiala ankare, fråga efter dem och ta bort dem. När du autentiserar dig på ditt konto med hjälp av konto nycklar tilldelas kontot ägare till det autentiserade huvudobjektet.
- **Konto deltagare för spatiala ankare**. Program eller användare som har den här rollen kan skapa spatiala ankare och fråga efter dem, men de kan inte ta bort dem.
- **Konto läsare för spatiala ankare**. Program eller användare som har den här rollen kan bara fråga efter avstånds ankare. De kan inte skapa nya, ta bort befintliga eller uppdatera metadata. Den här rollen används vanligt vis för program där vissa användare kan granska miljön, men andra kan bara återkalla ankare som tidigare placerats i miljön.

## <a name="next-steps"></a>Nästa steg

Skapa din första app med avstånds ankare för Azure:

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
