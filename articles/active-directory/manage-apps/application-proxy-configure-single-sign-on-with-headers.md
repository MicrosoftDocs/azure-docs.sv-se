---
title: Rubrik-baserad enkel inloggning för lokala appar med Azure AD App proxy
description: Lär dig att tillhandahålla enkel inloggning för lokala program som skyddas med huvudbaserad autentisering.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c30d8a66cf9deffaa32e5ec0356a9067575b77e8
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660710"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy-preview"></a>Rubrik-baserad enkel inloggning för lokala appar med Azure AD App proxy (för hands version)

Azure Active Directory (Azure AD) Application Proxy har inbyggt stöd för enkel inloggning till program som använder rubriker för autentisering. Du kan konfigurera huvud värden som krävs av ditt program i Azure AD. Huvud värden skickas till programmet via programproxyn. Några fördelar med att använda inbyggt stöd för huvud-baserad autentisering med Application Proxy är:  

* **Förenkla att tillhandahålla fjärråtkomst till dina lokala appar – med** App proxy kan du förenkla din befintliga arkitektur för fjärråtkomst. Du kan ersätta VPN-åtkomsten till dessa appar. Du kan också ta bort beroenden för lokala identitets lösningar för autentisering. Användarna märker inte något annorlunda när de loggar in för att använda dina företags program. De kan fortfarande arbeta var som helst på valfri enhet.  

* **Ingen ytterligare program vara eller ändringar i dina appar** – du kan använda dina befintliga Application Proxy-kopplingar och det kräver inte att ytterligare program vara installeras.  

* **Bred lista över attribut och transformeringar är tillgängliga** – alla huvud värden som är tillgängliga baseras på standard anspråk som utfärdas av Azure AD. Alla attribut och omvandlingar som är tillgängliga för att [Konfigurera anspråk för SAML-eller OIDC-program](../develop/active-directory-saml-claims-customization.md#attributes) är också tillgängliga att användas som rubrik värden. 

## <a name="pre-requisites"></a>Förutsättningar
Innan du börjar med enkel inloggning för huvudbaserade autentiseringscertifikat kontrollerar du att din miljö är klar med följande inställningar och konfigurationer:
- Du måste aktivera programproxyn och installera en anslutning som har en webbplats för dina program. Se självstudien [Lägg till ett lokalt program för fjärråtkomst via Application Proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) för att lära dig hur du förbereder din lokala miljö, installerar och registrerar en anslutning och testar anslutningen. 

## <a name="supported-capabilities"></a>Funktioner som stöds

I följande tabell visas vanliga funktioner som krävs för huvudbaserade autentiseringscertifikat som stöds med Application Proxy. 

|Krav   |Beskrivning|
|----------|-----------|
|Federerad enkel inloggning |I förautentiserat läge skyddas alla program med Azure AD-autentisering och gör det möjligt för användare att ha enkel inloggning. |
|Fjärråtkomst |Application Proxy möjliggör fjärråtkomst till appen. Användare kan komma åt programmet från Internet i valfri webbläsare med hjälp av den externa URL: en. Application Proxy är inte avsett för användning i företags åtkomst. |
|Rubrik baserad integration |Programproxyn gör SSO-integreringen med Azure AD och skickar sedan identiteter eller andra program data som HTTP-huvuden till programmet. |
|Program auktorisering |Vanliga principer kan anges baserat på vilket program som används, användarens grupp medlemskap och andra principer. I Azure AD implementeras principer med [villkorlig åtkomst](../conditional-access/overview.md). Principer för programauktorisering gäller endast för den inledande autentiseringsbegäran. |
|Stegvis autentisering |Principer kan definieras för att tvinga ytterligare autentisering, till exempel för att få åtkomst till känsliga resurser. |
|Detaljerad auktorisering |Ger åtkomst kontroll på URL-nivå. Tillagda principer kan tillämpas baserat på URL: en som nås. Den interna URL: en som kon figurer ATS för appen definierar omfånget för appen som principen tillämpas på. Principen som kon figurer ATS för den mest detaljerade sökvägen tillämpas.  |

> [!NOTE] 
> Den här artikeln innehåller information om hur du ansluter huvudbaserade autentiserings program till Azure AD med hjälp av programproxy och är det rekommenderade mönstret. Som ett alternativ finns det också ett integrations mönster som använder PingAccess med Azure AD för att aktivera huvud-baserad autentisering. Mer information finns i [rubrik-baserad autentisering för enkel inloggning med Application Proxy och PingAccess](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Så här fungerar det

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="Hur rubrik-baserad enkel inloggning fungerar med programproxy." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. Administratören anpassar de mappningar för attribut som krävs av programmet i Azure AD-portalen. 
2. När en användare ansluter till appen, ser programproxyn till att användaren autentiseras av Azure AD 
3. Cloud-tjänsten för programproxy är medveten om de attribut som krävs. Tjänsten hämtar därför motsvarande anspråk från ID-token som togs emot under autentiseringen. Tjänsten översätter sedan värdena till de HTTP-huvuden som krävs som en del av begäran till anslutnings tjänsten. 
4. Begäran skickas sedan vidare till anslutningen, som sedan skickas till Server dels programmet. 
5. Programmet tar emot huvudena och kan använda dessa meddelandehuvuden efter behov. 

## <a name="publish-the-application-with-application-proxy"></a>Publicera programmet med Application Proxy

1. Publicera programmet enligt instruktionerna som beskrivs i [Publicera program med programproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).  
    - Det interna URL-värdet avgör programmets omfång. Om du konfigurerar ett internt URL-värde på rot Sök vägen för programmet, får alla under Sök vägar under roten samma huvud konfiguration och annan program konfiguration. 
    - Skapa ett nytt program för att ange en annan huvud konfiguration eller användar tilldelning för en mer detaljerad sökväg än det program som du har konfigurerat. I det nya programmet konfigurerar du den interna URL: en med den specifika sökväg du behöver och konfigurerar sedan de specifika huvuden som krävs för den här URL: en. Programproxyn kommer alltid att matcha dina konfigurations inställningar till den mest detaljerade sökvägen för ett program. 

2. Välj **Azure Active Directory**   som **metod för för autentisering**. 
3. Tilldela en test användare genom att navigera till **användare och grupper** och tilldela lämpliga användare och grupper. 
4. Öppna en webbläsare och navigera till den **externa URL:** en   från inställningarna för programproxyn. 
5. Kontrol lera att du kan ansluta till programmet. Även om du kan ansluta kan du inte komma åt appen ännu eftersom huvudena inte har kon figurer ATS. 

## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning 
Innan du börjar med enkel inloggning för huvudbaserade program bör du redan ha installerat en Application Proxy-anslutning och anslutnings programmet kan komma åt mål programmen. Annars följer du stegen i [Självstudier: Azure AD-programproxy](application-proxy-add-on-premises-application.md)   kommer sedan tillbaka hit. 

1. När ditt program visas i listan över företags program markerar du det och väljer **enkel inloggning**. 
2. Ställ in läget för enkel inloggning till **rubrik-baserad**. 
3. I grundläggande konfiguration kommer **Azure Active Directory** att väljas som standard. 
4. Välj Redigera penna i sidhuvuden för att konfigurera huvuden som ska skickas till programmet. 
5. Välj **Lägg till nytt sidhuvud**. Ange ett **namn** för sidhuvudet och välj antingen **attribut** eller **omvandling** och välj i list rutan vilket sidhuvud ditt program behöver.  
    - Mer information om listan med tillgängliga attribut finns i [anspråk anpassningar-attribut](../develop/active-directory-saml-claims-customization.md#attributes). 
    - Mer information om listan över tillgängliga omvandling finns i [anspråk anpassningar-anspråks omvandlingar](../develop/active-directory-saml-claims-customization.md#claim-transformations). 
    - Du kan också lägga till ett **grupp huvud** för att skicka alla grupper som en användare är en del av eller de grupper som har tilldelats programmet som en rubrik. Läs mer om att konfigurera grupper som ett värde i: [Konfigurera grupp anspråk för program](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration). 
6. Välj Spara 

## <a name="test-your-app"></a>Testa appen 

När du har slutfört alla de här stegen ska appen köras och vara tillgänglig. Så här testar du appen: 
1. Öppna en ny webbläsare eller ett privat webbläsarfönster för att se till att tidigare cachelagrade rubriker är rensade. Gå sedan till den **externa URL:** en   från inställningarna för programproxyn.
2. Logga in med det test konto som du har tilldelat till appen. Om du kan läsa in och logga in i programmet med hjälp av SSO är du nöjd! 

## <a name="considerations"></a>Överväganden

- Application Proxy används för att ge fjärråtkomst till appar lokalt eller i ett privat moln. Application Proxy rekommenderas inte för att hantera trafik som härstammar internt från företags nätverket.
- Åtkomst till huvudbaserade autentiserings program bör begränsas till enbart trafik från anslutningen eller någon annan tillåten autentiseringsnyckel. Detta görs vanligt vis genom att begränsa nätverks åtkomsten till programmet med hjälp av en brand vägg eller en IP-begränsning på program servern.

## <a name="next-steps"></a>Nästa steg

- [Vad är enkel inloggning?](what-is-single-sign-on.md)
- [Vad är Application Proxy?](what-is-application-proxy.md)
- [Snabb starts serie för program hantering](view-applications-portal.md)
