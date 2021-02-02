---
title: 'Översätt länkar och URL: er Azure AD App proxy | Microsoft Docs'
description: Lär dig hur du omdirigerar hårdkodade länkar för appar som publicerats med Azure AD Application Proxy.
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
ms.date: 08/15/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44266f7a7485b44ba60a27aadd7422e8d1c3acf4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259414"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-ad-application-proxy"></a>Omdirigera hårdkodade länkar för appar som publicerats med Azure AD-programproxy

Azure AD-programproxy gör dina lokala appar tillgängliga för användare som är fjärranslutna eller på sina egna enheter. Vissa appar har dock utvecklats med lokala länkar som är inbäddade i HTML. Dessa länkar fungerar inte korrekt när appen används via fjärr anslutning. När du har flera lokala program som pekar på varandra, förväntar användarna att länkarna ska fortsätta att fungera när de inte är på kontoret. 

Det bästa sättet att se till att länkarna fungerar likadant i och utanför företagets nätverk är att konfigurera de externa URL: erna för dina appar att vara samma som deras interna URL: er. Använd [anpassade domäner](application-proxy-configure-custom-domain.md) för att konfigurera dina externa webb adresser så att de har företagets domän namn i stället för standard domänen för programproxyn.


Om du inte kan använda anpassade domäner i din klient organisation finns det flera andra alternativ för att tillhandahålla den här funktionen. Alla dessa är också kompatibla med anpassade domäner och varandra, så att du kan konfigurera anpassade domäner och andra lösningar om det behövs.

> [!NOTE]
> Länk översättning stöds inte för hårdkodade interna URL: er som genereras via Java Script.

**Alternativ 1: Använd Microsoft Edge** – den här lösningen kan bara användas om du planerar att rekommendera eller kräva att användarna får åtkomst till programmet via Microsoft Edge-webbläsaren. Den hanterar alla publicerade URL: er. 

**Alternativ 2: Använd tillägget för appar** – den här lösningen kräver att användare installerar ett webb läsar tillägg på klient sidan, men hanterar alla publicerade URL: er och fungerar med de flesta populära webbläsare. 

**Alternativ 3: Använd inställningen länk översättning** – det här är en inställning för administratörs sidan som är dold för användare. Det kommer dock att hantera URL: er endast i HTML och CSS.   

De här tre funktionerna gör att dina länkar fungerar oavsett var dina användare finns. När du har appar som pekar direkt till interna slut punkter eller portar kan du mappa dessa interna URL: er till URL: erna för publicerade externa programproxy. 

 
> [!NOTE]
> Det sista alternativet är endast för klienter som av någon anledning inte kan använda anpassade domäner för att ha samma interna och externa URL: er för sina appar. Innan du aktiverar den här funktionen kan du se om [anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md) kan arbeta åt dig. 
> 
> Eller, om programmet du behöver konfigurera med länk översättning är SharePoint, se [Konfigurera alternativa åtkomst mappningar för SharePoint 2013](/SharePoint/administration/configure-alternate-access-mappings) för en annan metod för att mappa länkar. 

 
### <a name="option-1-microsoft-edge-integration"></a>Alternativ 1: Microsoft Edge-integration 

Du kan använda Microsoft Edge för att ytterligare skydda ditt program och ditt innehåll. Om du vill använda den här lösningen måste du kräva/rekommendera användares åtkomst till programmet via Microsoft Edge. Alla interna URL: er som publiceras med programproxyn kommer att identifieras av Edge och omdirigeras till motsvarande externa URL. Detta säkerställer att alla hårdkodade interna URL: er fungerar, och om en användare går till webbläsaren och direkt skriver den interna URL: en, fungerar den även om användaren är fjärran sluten.  

Mer information, inklusive hur du konfigurerar det här alternativet finns i [Hantera webb åtkomst med hjälp av Edge för iOS och Android med Microsoft Intune](/mem/intune/apps/manage-microsoft-edge) -dokumentationen.  

### <a name="option-2-myapps-browser-extension"></a>Alternativ 2: webb läsar tillägget för appar 

Med webb läsar tillägget för appar, identifieras alla interna URL: er som publicerats med Application Proxy av tillägget och dirigeras till motsvarande externa URL. Detta säkerställer att alla hårdkodade interna URL: er fungerar, och om en användare går till webbläsarens Adress fält och direkt skriver den interna URL: en fungerar den även om användaren är fjärran sluten.  

Användaren måste ladda ned tillägget och vara inloggad för att kunna använda den här funktionen. Det finns ingen annan konfiguration som krävs för administratörer eller användare. 

Mer information, inklusive hur du konfigurerar det här alternativet finns i dokumentationen för [webb läsar tillägget för appar](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) .

### <a name="option-3-link-translation-setting"></a>Alternativ 3: inställning för länk Översättning 

När länk översättning är aktiverat söker Application Proxy-tjänsten igenom HTML och CSS efter publicerade interna länkar och översätter dem så att användarna får en oavbruten upplevelse. Om du använder webb läsar tillägget för appar föredrar du inställningen länk översättning eftersom det ger en mer välfungerande upplevelse för användarna.

> [!NOTE]
> Om du använder alternativ 2 eller 3 ska endast en av dessa vara aktive rad i taget.

## <a name="how-link-translation-works"></a>Så här fungerar länk Översättning

Efter autentiseringen, när proxyservern skickar program data till användaren, genomsöker Application Proxy programmet efter hårdkodade länkar och ersätter dem med deras respektive publicerade externa URL: er.

Programproxyn förutsätter att programmen kodas i UTF-8. Om så inte är fallet, anger du kodnings typen i ett HTTP-svarshuvuden, som `Content-Type:text/html;charset=utf-8` .

### <a name="which-links-are-affected"></a>Vilka länkar påverkas?

Funktionen länk översättning söker bara efter länkar som finns i kodmoduler i bröd texten i en app. Programproxyn har en separat funktion för översättning av cookies eller URL: er i huvuden. 

Det finns två vanliga typer av interna länkar i lokala program:

- **Relativa interna länkar** som pekar på en delad resurs i en lokal fil struktur som `/claims/claims.html` . Dessa länkar fungerar automatiskt i appar som publiceras via programproxy och fortsätter att arbeta med eller utan länk översättning. 
- **Hårdkodade interna länkar** till andra lokala appar som `http://expenses` eller publicerade filer som `http://expenses/logo.jpg` . Funktionen länk översättning fungerar på hårdkodade interna länkar och ändrar dem så att de pekar på de externa URL: er som fjärran vändarna måste gå igenom.

Den fullständiga listan med attribut i HTML-kodfragment som programproxy stöder länk översättning för inkluderar:
* a (href)
* ljud (SRC)
* Base (href)
* knapp (formaction)
* div (data – bakgrund, format, data-src)
* bädda in (SRC)
* formulär (åtgärd)
* RAM (SRC)
* huvud (profil)
* HTML (manifest)
* iframe (longdesc, src)
* IMG (longdesc, src)
* ininformation (formaction, src, Value)
* länk (href)
* MENUITEM (ikon)
* Meta (innehåll)
* objekt (Arkiv, data, Codebase)
* skript (SRC)
* källa (SRC)
* spåra (SRC)
* video (src, affisch)

Dessutom översätts URL-attributet även i CSS.

### <a name="how-do-apps-link-to-each-other"></a>Hur länkar appar till varandra?

Länk översättning är aktiverat för varje program, så att du har kontroll över användar upplevelsen på nivån per app. Aktivera länk översättning för en app när du vill att länkarna *från* appen ska översättas, inte länkar *till* den appen. 

Anta till exempel att du har tre program som publicerats via programproxyn som alla länkar till varandra: fördelar, utgifter och resor. Det finns en fjärde app, feedback, som inte har publicerats via programproxyn.

När du aktiverar länk översättning för Benefits-appen omdirigeras länkarna till utgifter och resor till de externa URL: erna för dessa appar, men länken till feedback omdirigeras inte eftersom det inte finns någon extern URL. Länkar från kostnader och tillbaka till förmåner fungerar inte, eftersom länk översättning inte har Aktiver ATS för dessa två appar.

![Länkar från förmåner till andra appar när länk översättning har Aktiver ATS](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Vilka länkar är inte översatta?

Vissa länkar är inte översatta för att förbättra prestanda och säkerhet:

- Länkar som inte ingår i kod taggar. 
- Länkar som inte är i HTML eller CSS. 
- Länkar i URL-kodat format.
- Interna länkar som öppnas från andra program. Länkar som skickas via e-post eller snabb meddelanden eller som ingår i andra dokument översätts inte. Användarna måste känna till att gå till den externa URL: en.

Om du behöver stöda ett av dessa två scenarier använder du samma interna och externa webb adresser i stället för länk översättning.  

## <a name="enable-link-translation"></a>Aktivera länk Översättning

Att komma igång med länk översättning är lika enkelt som att klicka på en knapp:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory**  >  **företags program**  >  **alla program** > Välj den app som du vill hantera > **Application Proxy**.
3. Omvandla **URL: er i program texten** till **Ja**.

   ![Välj Ja om du vill översätta URL: er i program texten](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Klicka på **Spara** för att tillämpa dina ändringar.

När dina användare till gång till det här programmet söker proxyen automatiskt efter interna URL: er som har publicerats via programproxyn på din klient organisation.

## <a name="send-feedback"></a>Skicka feedback

Vi vill att den här funktionen ska fungera för alla dina appar. Vi söker över 30 Taggar i HTML och CSS. Om du har ett exempel på genererade länkar som inte översätts skickar du ett kodfragment till [feedback för programproxy](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Nästa steg
[Använd anpassade domäner med Azure AD-programproxy](application-proxy-configure-custom-domain.md) att ha samma interna och externa URL

[Konfigurera alternativa åtkomst mappningar för SharePoint 2013](/SharePoint/administration/configure-alternate-access-mappings)
