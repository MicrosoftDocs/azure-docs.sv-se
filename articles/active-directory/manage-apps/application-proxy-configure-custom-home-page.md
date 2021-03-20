---
title: Anpassad start sida för publicerade appar – Azure AD-programproxy
description: Beskriver grunderna för Azure AD-programproxy-kopplingar
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebef93455414c114fb60df9af8ebc629769a33e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254436"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ange en anpassad start sida för publicerade appar med hjälp av Azure AD-programproxy

Den här artikeln beskriver hur du konfigurerar en app för att dirigera en användare till en anpassad start sida. När du publicerar en app med Application Proxy anger du en intern URL, men ibland är inte sidan som en användare bör se först. Ange en anpassad start sida så att en användare får rätt sida när de ansluter till appen. En användare ser den anpassade start sidan som du anger, oavsett om de kommer åt appen från Azure Active Directory Mina appar eller Microsoft 365 App-starta.

När en användare startar appen dirigeras de som standard till rot domänens URL för den publicerade appen. Landnings sidan anges vanligt vis som start sidans URL. Använd Azure AD PowerShell-modulen för att definiera en anpassad URL för start sidan när du vill att en app-användare ska landa på en viss sida i appen.

Här är ett scenario som förklarar varför företaget skulle ange en anpassad start sida:

- I företags nätverket går en användare att `https://ExpenseApp/login/login.aspx` Logga in och komma åt din app.
- Eftersom du har andra till gångar (till exempel bilder) som programproxyn behöver åtkomst till på den översta nivån i mappstrukturen, publicerar du appen med `https://ExpenseApp` som den interna URL: en.
- Den externa standard-URL: en är `https://ExpenseApp-contoso.msappproxy.net` , som inte tar en extern användare till inloggnings sidan.
- Du vill ange `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` som start sidans URL i stället, så att en extern användare ser inloggnings sidan först.

> [!NOTE]
> När du ger användare åtkomst till publicerade appar visas apparna i [Mina appar](../user-help/my-apps-portal-end-user-access.md) och [Office 365-program start](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Innan du börjar

Innan du anger URL: en för start sidan bör du tänka på följande:

- Den sökväg du anger måste vara en under domäns sökväg till rot domänens URL.

  Om rot domänens URL till exempel är `https://apps.contoso.com/app1/` , måste den URL för start sidan som du konfigurerar starta med `https://apps.contoso.com/app1/` .

- Om du gör en ändring i den publicerade appen kan ändringen återställa värdet för start sidans URL. När du uppdaterar appen i framtiden bör du kontrol lera igen och, om det behövs, uppdatera URL: en för start sidan.

Du kan ställa in URL: en för start sidan antingen via Azure Portal eller genom att använda PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Ändra start sidan i Azure Portal

Följ dessa steg om du vill ändra start sidans URL för din app via Azure AD-portalen:

1. Logga in på [Azure Portal](https://portal.azure.com/) som administratör.
1. Välj **Azure Active Directory** och **Appregistreringar**. Listan över registrerade appar visas.
1. Välj din app i listan. En sida som visar information om den registrerade appen visas.
1. Under **Hantera** väljer du **märkes anpassning**.
1. Uppdatera **Start sidans URL**  med din nya sökväg.

   ![Anpassnings sida för en registrerad app som visar start sidans URL-fält](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Välj **Spara**.

## <a name="change-the-home-page-with-powershell"></a>Ändra start sidan med PowerShell

Om du vill konfigurera start sidan för en app med hjälp av PowerShell måste du:

1. Installera Azure AD PowerShell-modulen.
1. Hitta appens ObjectId-värde.
1. Uppdatera appens start sida URL med PowerShell-kommandon.

### <a name="install-the-azure-ad-powershell-module"></a>Installera Azure AD PowerShell-modulen

Innan du definierar en anpassad start sidas webb adress med PowerShell installerar du Azure AD PowerShell-modulen. Du kan hämta paketet från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)som använder Graph API-slutpunkten.

Följ dessa steg för att installera paketet:

1. Öppna ett standard PowerShell-fönster och kör sedan följande kommando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Använd alternativet om du kör kommandot som icke-administratör `-scope currentuser` .

1. Under installationen väljer du **Y** för att installera två paket från NuGet.org. Båda paketen krävs.

### <a name="find-the-objectid-of-the-app"></a>Hitta appens ObjectId

Du får appens ObjectId genom att söka efter appen med dess visnings namn eller hem sida.

1. Importera Azure AD-modulen i samma PowerShell-fönster.

   ```powershell
   Import-Module AzureAD
   ```

1. Logga in på Azure AD-modulen som klient organisations administratör.

   ```powershell
   Connect-AzureAD
   ```

1. Hitta appen. I det här exemplet används PowerShell för att hitta ObjectId genom att söka efter appen med visnings namnet `SharePoint` .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Du bör få ett resultat som liknar det som visas här. Kopiera ObjectId GUID som ska användas i nästa avsnitt.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternativt kan du bara hämta listan över alla appar, söka i listan efter appen med ett särskilt visnings namn eller en start sida och kopiera appens ObjectId när appen har hittats.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Uppdatera start sidans URL

Skapa URL: en för start sidan och uppdatera din app med det värdet. Fortsätt att använda samma PowerShell-fönster, eller om du använder ett nytt PowerShell-fönster loggar du in på Azure AD-modulen igen med `Connect-AzureAD` . Följ sedan de här stegen:

1. Skapa en variabel som ska innehålla det ObjectId-värde som du kopierade i föregående avsnitt. (Ersätt det ObjectId-värde som används för i det här SharePoint-exemplet med appens ObjectId-värde.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Kontrol lera att du har rätt app genom att köra följande kommando. Utdata bör vara identiska med de utdata som du såg i föregående avsnitt ([hitta appens ObjectID](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Skapa ett tomt program objekt för att lagra de ändringar som du vill göra.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Ange start sidans URL till det värde som du vill använda. Värdet måste vara en under domän Sök väg till den publicerade appen. Om du till exempel ändrar Start sidans URL från `https://sharepoint-iddemo.msappproxy.net/` till `https://sharepoint-iddemo.msappproxy.net/hybrid/` , går App-användare direkt till den anpassade start sidan.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Gör uppdateringen av start sidan.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Bekräfta att ändringen har genomförts genom att köra följande kommando från steg 2 igen.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   I vårt exempel bör utdata nu se ut så här:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Starta om appen för att bekräfta att start sidan visas som den första skärmen som förväntat.

> [!NOTE]
> Eventuella ändringar som du gör i appen kan återställa start sidans URL. Om URL: en för start sidan återställs upprepar du stegen i det här avsnittet för att ställa in den igen.

## <a name="next-steps"></a>Nästa steg

- [Aktivera fjärråtkomst till SharePoint med Azure AD-programproxy](application-proxy-integrate-with-sharepoint-server.md)
- [Självstudie: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](application-proxy-add-on-premises-application.md)
