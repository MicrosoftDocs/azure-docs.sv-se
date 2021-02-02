---
title: Publicera appar i separata nätverk via anslutnings grupper – Azure AD
description: Beskriver hur du skapar och hanterar grupper med kopplingar i Azure AD-programproxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca42137192fefbc669fca775b0deff2f5226c5c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253840"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicera program i separata nätverk och platser med anslutnings grupper

Kunder använder Azure ADs tillämpningsproxy för fler och fler scenarier och program. Vi har gjort App proxy ännu mer flexibel genom att aktivera fler topologier. Du kan skapa kopplings grupper för programproxy så att du kan tilldela specifika anslutningar för att betjäna specifika program. Den här funktionen ger dig större kontroll och sätt att optimera distributionen av programproxyn.

Varje Application Proxy-koppling är tilldelad till en kopplings grupp. Alla kopplingar som tillhör samma kopplings grupp fungerar som en separat enhet för hög tillgänglighet och belastnings utjämning. Alla kopplingar tillhör en kopplings grupp. Om du inte skapar grupper finns alla dina kopplingar i en standard grupp. Din administratör kan skapa nya grupper och tilldela kopplingar till dem i Azure Portal.

Alla program har tilldelats en kopplings grupp. Om du inte skapar grupper tilldelas alla dina program till en standard grupp. Men om du ordnar dina kopplingar i grupper kan du ange att varje program ska fungera med en specifik kopplings grupp. I det här fallet är det bara kopplingarna i gruppen som betjänar programmet på begäran. Den här funktionen är användbar om dina program finns på olika platser. Du kan skapa anslutnings grupper baserat på plats, så att programmen alltid betjänas av anslutningar som är fysiskt nära dem.

> [!TIP]
> Om du har en stor Application Proxy-distribution ska du inte tilldela några program till standard anslutnings gruppen. På så sätt tar nya anslutningar inte emot någon Live-trafik förrän du tilldelar dem till en aktiv anslutnings grupp. Den här konfigurationen gör det också möjligt att placera anslutningar i ett inaktivt läge genom att flytta tillbaka dem till standard gruppen så att du kan utföra underhåll utan att påverka användarna.

## <a name="prerequisites"></a>Förutsättningar

Om du vill gruppera dina anslutningar måste du kontrol lera att du har [installerat flera kopplingar](application-proxy-add-on-premises-application.md). När du installerar en ny anslutning kopplas den automatiskt till **standard** anslutnings gruppen.

## <a name="create-connector-groups"></a>Skapa anslutnings grupper

Använd de här stegen för att skapa så många kopplings grupper du vill.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory**  >    >  **programproxy** för företags program.
1. Välj **ny anslutnings grupp**. Bladet ny kopplings grupp visas.

   ![Visar skärmen för att välja en ny anslutnings grupp](./media/application-proxy-connector-groups/new-group.png)

1. Ge din nya anslutnings grupp ett namn och Använd sedan List menyn för att välja vilka kopplingar som tillhör den här gruppen.
1. Välj **Spara**.

## <a name="assign-applications-to-your-connector-groups"></a>Tilldela program till dina anslutnings grupper

Använd de här stegen för varje program som du har publicerat med Application Proxy. Du kan tilldela ett program till en kopplings grupp första gången du publicerar den, eller så kan du använda de här stegen för att ändra tilldelningen när du vill.

1. Från hanterings instrument panelen för din katalog väljer du **företags program**  >  **alla program** > det program som du vill tilldela till en anslutnings grupp > **programproxy**.
1. Använd List Rute menyn för **kopplings grupper** för att välja den grupp som du vill att programmet ska använda.
1. Välj **Spara** för att tillämpa ändringen.

## <a name="use-cases-for-connector-groups"></a>Användnings fall för anslutnings grupper

Kopplings grupper är användbara för olika scenarier, inklusive:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Webbplatser med flera sammankopplade Data Center

Många organisationer har ett antal sammankopplade Data Center. I det här fallet vill du ha lika mycket trafik i data centret som möjligt eftersom länkar mellan data Center är dyra och långsamma. Du kan distribuera anslutningar i varje data Center för att endast betjäna de program som finns i data centret. Den här metoden minimerar länkar mellan data Center och ger en helt transparent upplevelse för dina användare.

### <a name="applications-installed-on-isolated-networks"></a>Program som är installerade på isolerade nätverk

Program kan finnas i nätverk som inte ingår i huvud företags nätverket. Du kan använda anslutnings grupper för att installera dedikerade anslutningar i isolerade nätverk för att också isolera program till nätverket. Detta inträffar vanligt vis när en leverantör av tredje part underhåller ett enskilt program för din organisation.

Med anslutnings grupper kan du installera dedikerade anslutningar för de nätverk som endast publicerar specifika program, vilket gör det enklare och säkrare att hantera program hantering till tredjepartsleverantörer.

### <a name="applications-installed-on-iaas"></a>Program som är installerade på IaaS

För program som är installerade på IaaS för moln åtkomst tillhandahåller anslutnings grupper en gemensam tjänst för att skydda åtkomsten till alla appar. Kopplings grupper skapar inte ytterligare beroende på företagets nätverk eller fragmenterar appens upplevelse. Anslutningar kan installeras på alla moln Data Center och endast betjäna program som finns i nätverket. Du kan installera flera kopplingar för att uppnå hög tillgänglighet.

Ta ett exempel på en organisation som har flera virtuella datorer som är anslutna till sitt eget IaaS-värdbaserade virtuella nätverk. För att anställda ska kunna använda dessa program är dessa privata nätverk anslutna till företags nätverket med plats-till-plats-VPN. Detta ger en bättre upplevelse för anställda som finns lokalt. Men det kanske inte är idealiskt för fjär anställda eftersom det krävs ytterligare lokal infrastruktur för att dirigera åtkomst, som du kan se i diagrammet nedan:

![Diagram som illustrerar Azure AD IaaS-nätverket](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Med Azure AD-programproxy kopplings grupper kan du aktivera en gemensam tjänst för att skydda åtkomsten till alla program utan att skapa ytterligare beroende på företagets nätverk:

![Azure AD IaaS flera moln leverantörer](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Flera skogar – olika anslutnings grupper för varje skog

De flesta kunder som har distribuerat Application Proxy använder sina funktioner för enkel inloggning (SSO) genom att utföra Kerberos-begränsad delegering (KCD). För att uppnå detta måste anslutningens datorer vara anslutna till en domän som kan delegera användarna mot programmet. KCD stöder funktioner för flera skogar. Men för företag som har skilda miljöer med flera skogar utan förtroende mellan dem, kan en enda koppling inte användas för alla skogar. 

I det här fallet kan specifika anslutningar distribueras per skog och konfigureras för att hantera program som har publicerats för att endast betjäna användare av den specifika skogen. Varje anslutnings grupp representerar en annan skog. Även om klienten och de flesta av erfarenheten är enhetlig för alla skogar, kan användarna tilldelas sina skogs program med hjälp av Azure AD-grupper.

### <a name="disaster-recovery-sites"></a>Katastrof återställnings webbplatser

Det finns två olika metoder som du kan utföra med en katastrof återställning (DR), beroende på hur dina platser implementeras:

* Om din DR-webbplats är inbyggd i aktivt läge där det är precis som huvud platsen och har samma inställningar för nätverk och AD, kan du skapa kopplingarna på DR-platsen i samma kopplings grupp som huvud platsen. Detta gör att Azure AD kan identifiera redundans åt dig.
* Om din DR-webbplats är separat från huvud platsen kan du skapa en annan kopplings grupp på DR-platsen och antingen 1) ha säkerhets kopierings program eller 2) manuellt överföra det befintliga programmet till DR Connector-gruppen efter behov.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Hantera flera företag från en enda klient organisation

Det finns många olika sätt att implementera en modell där en enskild tjänst leverantör distribuerar och underhåller Azure AD-relaterade tjänster för flera företag. Kopplings grupper hjälper administratören att åtskilja kopplingarna och programmen i olika grupper. Ett sätt, som lämpar sig för små företag, är att ha en enda Azure AD-klient medan de olika företagen har sitt eget domän namn och sina egna nätverk. Detta gäller även för M&scenarier och situationer där en enda IT-avdelning har flera företag för myndighets-eller affärs skäl.

## <a name="sample-configurations"></a>Exempel på konfigurationer

Några exempel som du kan implementera inkluderar följande anslutnings grupper.

### <a name="default-configuration--no-use-for-connector-groups"></a>Standard konfiguration – ingen användning för anslutnings grupper

Om du inte använder anslutnings grupper skulle konfigurationen se ut så här:

![Exempel på Azure AD inga anslutnings grupper](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Den här konfigurationen räcker för små distributioner och test. Det fungerar också bra om din organisation har en plan för en enkel nätverks sto pol Ogin.

### <a name="default-configuration-and-an-isolated-network"></a>Standard konfiguration och ett isolerat nätverk

Den här konfigurationen är en utveckling av standardvärdet, där det finns en speciell app som körs i ett isolerat nätverk, till exempel IaaS virtuellt nätverk:

![Exempel på Azure AD, inga anslutnings grupper och ett isolerat nätverk](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Rekommenderad konfiguration – flera olika grupper och en standard grupp för inaktivitet

Den rekommenderade konfigurationen för stora och komplexa organisationer är att ha standard anslutnings gruppen som en grupp som inte har några program och som används för inaktiva eller nyligen installerade anslutningar. Alla program hanteras med hjälp av anpassade anslutnings grupper. Detta möjliggör all komplexitet i scenarier som beskrivs ovan.

I exemplet nedan har företaget två Data Center, A och B, med två kopplingar som hanterar varje plats. Varje plats har olika program som körs på den.

![Exempel på företag med 2 data Center och 2 anslutningar](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
* [Aktivera enkel inloggning](what-is-single-sign-on.md)
