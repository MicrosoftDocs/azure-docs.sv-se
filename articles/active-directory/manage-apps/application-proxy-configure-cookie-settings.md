---
title: Cookie-inställningar för programproxy – Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) har åtkomst och sessionscookies för att komma åt lokala program via programproxyn. I den här artikeln får du veta hur du använder och konfigurerar inställningarna för cookies.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62afe97b44f45bc0b7aa12b33b6a65dd94ecf095
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252210"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie-inställningar för åtkomst till lokala program i Azure Active Directory

Azure Active Directory (Azure AD) har åtkomst och sessionscookies för att komma åt lokala program via programproxyn. Ta reda på hur du använder cookie-inställningar för programproxy. 

## <a name="what-are-the-cookie-settings"></a>Vilka är cookie-inställningarna?

[Application Proxy](application-proxy.md) använder följande cookie-inställningar för åtkomst och session.

| Cookie-inställning | Standardvärde | Beskrivning | Rekommendationer |
| -------------- | ------- | ----------- | --------------- |
| Använd endast HTTP-cookie | **Nej** | **Ja** tillåter att Application Proxy inkluderar HTTPOnly-flaggan i HTTP-svarshuvuden. Den här flaggan ger ytterligare säkerhets fördelar, till exempel förhindrar skript på klient sidan (CSS) från att kopiera eller ändra cookies.<br></br><br></br>Innan vi har stöd för den HTTP-Only inställningen krypterade programproxyn och skickade cookies via en säker TLS-kanal för att skydda mot ändring. | Använd **Ja** på grund av de ytterligare säkerhets fördelarna.<br></br><br></br>Använd **Nej** för klienter eller användar agenter som behöver åtkomst till sessions-cookien. Använd till exempel **Nej** för en RDP-eller MTSC-klient som ansluter till en server för fjärrskrivbordsgateway via programproxyn.|
| Använd säker cookie | **Nej** | **Ja** låter programproxyn ta med säker flagga i HTTP-svarshuvuden. Säkra cookies ökar säkerheten genom att skicka cookies via en säker TLS-kanal, till exempel HTTPS. Detta förhindrar att cookies observeras av obehöriga parter på grund av överföring av cookien i klartext. | Använd **Ja** på grund av de ytterligare säkerhets fördelarna.|
| Använd beständig cookie | **Nej** | **Ja** tillåter att programproxyn anger att åtkomst-cookies inte upphör att gälla när webbläsaren stängs. Persistence varar tills åtkomsttoken upphör att gälla, eller tills användaren manuellt tar bort de permanenta cookies. | Använd **Nej** på grund av säkerhets risken som är kopplad till användarens autentiserade.<br></br><br></br>Vi rekommenderar att du bara använder **Ja** för äldre program som inte kan dela cookies mellan processer. Det är bättre att uppdatera ditt program för att hantera delning av cookies mellan processer i stället för att använda beständiga cookies. Du kan till exempel behöva beständiga cookies för att tillåta att en användare öppnar Office-dokument i Utforskarvyn från en SharePoint-webbplats. Utan permanenta cookies kan den här åtgärden Miss Miss kan uppstå om åtkomst-cookies inte delas mellan webbläsaren, Explorer-processen och Office-processen. |

## <a name="samesite-cookies"></a>SameSite cookies
Från och med version Chrome 80 och slutligen i webbläsare som använder krom, kommer cookies som inte anger attributet [SameSite](https://web.dev/samesite-cookies-explained) att behandlas som om de var inställda på **SameSite = lax**. Attributet SameSite deklarerar hur cookies ska begränsas till en kontext på samma plats. När värdet är inställt på lax, skickas cookien endast till begäran om samma plats eller navigering på den översta nivån. Programproxyn kräver dock att dessa cookies bevaras i den tredje partens sammanhang för att användarna ska vara korrekt inloggade under sessionen. På grund av detta gör vi uppdateringar till programproxyns åtkomst och sessionscookies för att undvika negativ påverkan på den här ändringen. Uppdateringarna omfattar:

* Anger attributet **SameSite** till **none**. Detta gör att programproxy-åtkomst och sessioner av cookies kan skickas korrekt i den tredje partens sammanhang.
* Ställer in inställningen **Använd säker cookie** så att den använder **Ja** som standard. Chrome kräver också att cookies anger den säkra flaggan eller avvisas. Den här ändringen gäller för alla befintliga program som publicerats via programproxy. Observera att Access-cookies för programproxy alltid har angetts till säker och endast skickas via HTTPS. Den här ändringen gäller endast för sessionscookies.

De här ändringarna i Application Proxy-cookies kommer att distribueras under de kommande två veckorna innan versions datumet för Chrome 80.

Om ditt serverprogram har cookies som måste vara tillgängliga i en tredjeparts-kontext måste du dessutom uttryckligen välja att ändra ditt program till att använda SameSite = none för dessa cookies. Programproxyn översätter Set-Cookie rubriken till URL: erna och följer inställningarna för dessa cookies som anges av Server dels programmet.



## <a name="set-the-cookie-settings---azure-portal"></a>Ange cookie-inställningar – Azure Portal
Ange cookie-inställningar med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Gå till **Azure Active Directory**   >  **företags program**   >  **alla program**.
3. Välj det program som du vill aktivera en cookie-inställning för.
4. Klicka på **Application Proxy**.
5. Under **ytterligare inställningar** ställer du in cookie-inställningen på **Ja** eller **Nej**.
6. Klicka på **Spara** för att tillämpa dina ändringar. 

## <a name="view-current-cookie-settings---powershell"></a>Visa aktuella cookie-inställningar – PowerShell

Använd följande PowerShell-kommando för att se de aktuella cookie-inställningarna för programmet:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Ange cookie-inställningar – PowerShell

I följande PowerShell-kommandon ```<ObjectId>``` är ObjectID för programmet. 

**Http-Only cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Säker cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Beständiga cookies**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
