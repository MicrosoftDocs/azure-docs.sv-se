---
title: Azure AD Secure hybrid-åtkomst med F5 VPN | Microsoft Docs
description: Självstudie för Azure Active Directory enkel inloggning (SSO) med F5 BIG-IP för lösen ords mindre VPN
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730895"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Självstudie för Azure Active Directory integration med enkel inloggning med F5 BIG-IP för lösen ords mindre VPN

I den här självstudien får du lära dig att integrera F5's BIG-IP-baserade Secure Socket Layer Virtual Private Network (SSL-VPN)-lösning med Azure Active Directory (AD) för säker hybrid åtkomst (SHA).

Att integrera en BIG-IP SSL-VPN med Azure AD ger [många viktiga fördelar](f5-aad-integration.md), inklusive:

- Förbättrad kostnads styrning utan förtroende genom [förautentisering och auktorisering i Azure AD](../../app-service/overview-authentication-authorization.md)

- [Lösen ords mindre autentisering till VPN-tjänsten](https://www.microsoft.com/security/business/identity/passwordless)

- Hantera identiteter och åtkomst från ett enda kontroll plan – [Azure Portal](https://portal.azure.com/#home)

Trots att de här fantastiska värdena ökar, är det klassiska VPN-nätverket dock fortfarande predikat på begreppet nätverks gräns, där betrott är på insidan och inte är betrott utanför. Den här modellen är inte längre effektiv för att uppnå ett True position-förtroende, eftersom företags till gångar inte längre är begränsade till väggarna i ett företags data Center, utan i stället i miljöer med flera moln utan fasta gränser. Därför uppmuntrar vi våra kunder att överväga att flytta till en mer identitets driven metod vid hantering [av åtkomst per program](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md).

## <a name="scenario-description"></a>Scenariobeskrivning

I det här scenariot kommer BIG-IP APM-instansen av SSL-VPN-tjänsten att konfigureras som en SAML-tjänstprovider (SP) och Azure AD blir den betrodda SAML-IDP som tillhandahåller förautentisering. Enkel inloggning (SSO) från Azure AD tillhandahålls sedan via anspråksbaserad autentisering till BIG-IP APM, vilket ger en sömlös VPN-åtkomst.

![Bild som visar SSL-VPN-arkitektur](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Alla exempel strängar eller värden som refereras till i den här guiden bör ersättas med dem för din faktiska miljö.

## <a name="prerequisites"></a>Krav

Tidigare erfarenhet eller kunskaper om F5 BIG-IP är inte nödvändigt, men du behöver:

- En [kostnads fri](https://azure.microsoft.com/trial/get-started-active-directory/) Azure AD-prenumeration eller högre

- Användar identiteter bör [synkroniseras från sin lokala katalog](../hybrid/how-to-connect-sync-whatis.md) till Azure AD.

- Ett konto med administratörs [behörighet](../roles/permissions-reference.md#application-administrator) för Azure AD-program

- En befintlig stor IP-infrastruktur med Routning av klient trafik till och från BIG-IP eller [distribuera en stor virtuell IP-utgåva till Azure](f5-bigip-deployment-guide.md).

- En post för den BIG-IP-publicerade VPN-tjänsten måste finnas i offentlig DNS eller en test klients localhost-fil vid testning

- BIG-IP bör tillhandahållas med nödvändiga SSL-certifikat för publicering av tjänster via HTTPS.

Genom att bekanta dig med [F5 Big-IP-terminologi](https://www.f5.com/services/resources/glossary) kan du också förstå de olika komponenterna som refereras till i självstudien.

>[!NOTE]
>Azure utvecklas ständigt så att du inte blir förvånad om du hittar någon olika delarna mellan instruktionerna i den här hand boken och vad du ser i Azure Portal. Skärm bilderna kommer från BIG-IP-v15, men förblir relativt likvärdiga med v 13.1.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Lägg till F5 BIG-IP från Azure AD-galleriet

Genom att konfigurera ett SAML Federations förtroende mellan BIG-IP kan du få Azure AD BIG-IP att lämna förautentisering och [villkorlig åtkomst](../conditional-access/overview.md) till Azure AD innan du beviljar åtkomst till den publicerade VPN-tjänsten.

1. Logga in på Azure AD-portalen med ett konto med program administratörs behörighet

2. I det vänstra navigerings fönstret väljer du **tjänsten Azure Active Directory**

3. Gå till **företags program** och välj **nytt program** från det översta menyfliksområdet.

4. Sök efter F5 i galleriet och välj **F5 Big-IP APM Azure AD integration**.

5. Ange ett namn för programmet, följt av **Lägg till/skapa** om du vill att det ska läggas till i din klient organisation. Användaren kan se namnet som en ikon i program portalerna för Azure och Office 365. Namnet ska återspegla den aktuella tjänsten. Till exempel VPN.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

1. Med dina nya egenskaper för F5-program i vyn går du till **Hantera**  >  **enkel inloggning**

2. På sidan **Välj metod för enkel inloggning** väljer du **SAML**. Hoppa över prompten för att spara inställningarna för enkel inloggning genom att välja **Nej, jag sparar senare**.

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att ange följande information:

   - Ersätt den fördefinierade **identifierade URL:** en med URL: en för din Big-IP-publicerade tjänst. Till exempel `https://ssl-vpn.contoso.com`

   - Gör samma sak med text rutan **svars-URL** , inklusive sökvägen till SAML-slutpunkten. Till exempel `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - I den här konfigurationen kommer programmet att köras i ett IDP initierat läge, där Azure AD utfärdar användaren med en SAML-kontroll innan den omdirigeras till tjänsten BIG-IP SAML. För appar som inte stöder IDP initierat läge anger du **inloggnings-URL: en** för tjänsten Big-IP SAML. Ett exempel är `https://ssl-vpn.contoso.com`.

   - För utloggnings-URL: en anger du slut punkten för utloggnings punkten för BIG-IP APM (service nivå) för pended av värd rubriken för den tjänst som publiceras. Till exempel `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   Om du anger en URL för service nivå mål ser du till att en användarsession avslutas i båda ändar, BIG-IP och Azure AD efter att användaren har loggat ut. BIG-IP APM tillhandahåller också ett [alternativ](https://support.f5.com/csp/article/K12056) för att avsluta alla sessioner när du anropar en specifik program-URL.

![Bild som visar grundläggande SAML-konfiguration](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>Från TMOS v16 har slut punkten för SAML service nivå ändrats till/SAML/SP/Profile/Redirect/SLO

4. Välj **Spara** innan du avslutar konfigurations menyn för SAML och hoppa över SSO-testprompten.

Observera egenskaperna för avsnittet **användarattribut &-anspråk** , eftersom Azure AD kommer att utfärda dessa till användare för Big-IP APM-autentisering.

![Bild som visar anspråk för användar attribut](media/f5-sso-vpn/user-attributes-claims.png)

Du kan lägga till eventuella andra särskilda anspråk som din BIG-IP-publicerade tjänst förväntar sig, och Observera att alla anspråk som definierats utöver standard uppsättningen bara utfärdas om de finns i Azure AD, som ifyllda attribut. På samma sätt måste katalog [roller eller grupp](../hybrid/how-to-connect-fed-group-claims.md) medlemskap också definiera mot ett användar objekt i Azure AD innan de kan utfärdas som ett anspråk.

![Bild som visar nedladdnings länken för federationsmetadata](media/f5-sso-vpn/saml-signing-certificate.png)

SAML-signeringscertifikat som skapats av Azure AD har en livs längd på tre år, så kommer att behöva hantera med hjälp av Azure AD-publicerad vägledning.

### <a name="azure-ad-authorization"></a>Azure AD-auktorisering

Som standard utfärdar Azure AD bara tokens till användare som har beviljats åtkomst till en tjänst.

1. Välj **användare och grupper** fortfarande i programmets konfigurations vy

2. Välj **+ Lägg till användare** och i menyn Lägg till tilldelning väljer **du användare och grupper**

3. I dialog rutan **användare och grupper** lägger du till de användar grupper som har behörighet att komma åt VPN, följt av **Välj**  >  **tilldela**

![Bild som visar hur du lägger till användar länkar ](media/f5-sso-vpn/add-user-link.png)

4. Detta slutför Azure AD-delen av förtroendet för SAML Federation. BIG-IP APM kan nu konfigureras för att publicera SSL-VPN-tjänsten och konfigureras med en motsvarande uppsättning egenskaper för att slutföra förtroendet för SAML-förautentisering.

## <a name="big-ip-apm-configuration"></a>Konfiguration av BIG-IP APM

### <a name="saml-federation"></a>SAML-Federation

I följande avsnitt skapas tjänsten BIG-IP SAML-tjänst och motsvarande SAML IDP-objekt som krävs för att federera VPN-tjänsten med Azure AD.

1. Gå till **Access**  >  **Federation**  >  **SAML Service Provider**  >  **lokala SP-tjänster** och välj **skapa**

![Bild som visar konfiguration av stor IP-SAML](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Ange ett **namn** och samma **entitets-ID-** värde som du definierade i Azure AD tidigare och värd-FQDN som ska användas för att ansluta till programmet

![Bild som visar hur du skapar en ny SAML SP-tjänst](media/f5-sso-vpn/create-new-saml-sp.png)

   Inställningarna för SP- **namn** krävs endast om entitets-ID: t inte är en exakt matchning av hostname-delen av den publicerade URL: en eller om den inte är i reguljärt hostname-baserat URL-format. Ange det externa schemat och värd namnet för programmet som publiceras om entitets-ID: t är `urn:ssl-vpn:contosoonline` .

3. Rulla nedåt till Välj det nya **SAML SP-objektet** och välj **BIND/Unbind IDP-kopplingar**.

![Bild som visar hur du skapar Federation med lokal SP-tjänst](media/f5-sso-vpn/federation-local-sp-service.png)

4. Välj **Skapa ny IDP-koppling** och välj **från metadata** i den nedrullningsbara menyn

![Bild som visar skapa ny IDP-koppling](media/f5-sso-vpn/create-new-idp-connector.png)

5. Bläddra till XML-filen för federationsmetadata som du laddade ned tidigare och ange ett namn på en **identitets leverantör** för det APM-objekt som ska representera den externa SAML-IDP

6. Välj **Lägg till ny rad** för att välja den nya externa Azure AD IDP-anslutningen.

![Bild som visar extern IDP-koppling](media/f5-sso-vpn/external-idp-connector.png)

7. Välj **Uppdatera** för att binda SAML SP-objektet till SAML IDP-objektet och välj sedan **OK**.

![Bilden visar SAML-IDP med SP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Webtop-konfiguration

Följande steg gör det möjligt för SSL-VPN att erbjudas användare via BIG-IP: s patentskyddade webb Portal.

1. Gå till **åtkomst** till  >  **webbbäst**  >  **Webtop-listor** och välj **skapa**.

2. Ge portalen ett namn och ange en **fullständig** typ. Ett exempel är `Contoso_webtop`.

3. Justera återstående inställningar och välj sedan **avslutad**.

![Bild som visar Webtop-konfiguration](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN-konfiguration

VPN-funktionen består av flera element, var och en som styr en annan aspekt av den övergripande tjänsten.

1. Gå till **åtkomst**  >  **anslutning/VPN**-anslutningar  >  **(VPN)** för  >  **IPv4-lån** och välj **skapa**.

2. Ange ett namn för poolen med IP-adresser som allokeras till VPN-klienter. Till exempel Contoso_vpn_pool

3. Ange IP- **adressintervall** och ange en start-och slut-IP-adress, följt av **Lägg till** och **slutfört**.

![Bild som visar VPN-konfiguration](media/f5-sso-vpn/vpn-configuration.png)

En lista över nätverks åtkomst etablerar tjänsten med IP-och DNS-inställningar från VPN-poolen, användar Dirigerings behörigheter och kan också starta program vid behov.

1. Gå till **åtkomst**  >  **anslutning/VPN: nätverks åtkomst (VPN)**  >  **nätverks åtkomst listor** och välj **skapa**.

2. Ange ett namn för VPN-åtkomstens lista och rubrik, till exempel contoso-VPN följt av **avslutad**.

![Bild som visar VPN-konfiguration i listan över nätverks åtkomst](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. Välj **nätverks inställningar** i det översta menyfliksområdet och Lägg till inställningarna nedan:

   • **IP-version som stöds**: IPv4

   • **IPv4-hyr pool**: Välj den VPN-pool som skapades tidigare, till exempel Contoso_vpn_pool

![Bild visar contoso VPN-pool](media/f5-sso-vpn/contoso-vpn-pool.png)

   Alternativen för klient inställningar kan användas för att genomdriva begränsningar för hur klient trafiken dirigeras när en VPN-anslutning upprättas.

4. Välj **avslutad** och gå till fliken DNS/hosts och Lägg till inställningarna:

   • **IPv4 primär namnserver**: IP för din MILJÖS DNS-Server

   • **DNS-** standarddomänsuffix: domänsuffix för den här enskilda VPN-anslutningen. Till exempel contoso.com

![Bild visar standard domänsuffix](media/f5-sso-vpn/domain-suffix.png)

[F5-artikeln](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) innehåller information om hur du justerar de återstående inställningarna enligt dina önskemål.

Nu krävs en profil för BIG-IP-anslutning för att konfigurera inställningarna för var och en av de VPN-klienter som VPN-tjänsten behöver stöd för. Till exempel Windows, OSX och Android.

1. Gå till **åtkomst**  >  **anslutning/VPN-**  >  **anslutnings**  >  **profiler** och välj **Lägg till**.

2. Ange ett profil namn och ange den överordnade profilen till **/common/Connectivity**, till exempel Contoso_VPN_Profile.

![Bild som visar skapa ny anslutnings profil](media/f5-sso-vpn/create-connectivity-profile.png)

F5's- [dokumentationen](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) innehåller mer information om klient support.

## <a name="access-profile-configuration"></a>Åtkomst till profil konfiguration

Med de VPN-objekt som kon figurer ATS krävs en åtkomst princip för att aktivera tjänsten för SAML-autentisering.

1. Gå till **åtkomst**  >  **profiler/principer**  >  **åtkomst profiler (principer för varje session)** och välj **skapa**

2. Ange ett profil namn och för profil typen väljer du **alla**, till exempel Contoso_network_access

3. Rulla ned för att lägga till minst ett språk i listan över **godkända språk** och välj **avslutad**

![Bilden visar allmänna egenskaper](media/f5-sso-vpn/general-properties.png)

4. Välj **Redigera** i Per-Session princip fältet i den nya åtkomst profilen för att redigeraren för visuella principer i en separat flik i webbläsaren.

![Bild som visar princip per session](media/f5-sso-vpn/per-session-policy.png)

5. Välj **+** signeringen och i popup-fönstret Välj   >  **SAML auth**-autentisering  >  **Lägg till objekt**.

6. I SAML-autentisering SP-konfigurationen väljer du det virtuella SAML-objektet som du skapade tidigare, följt av **Spara**.

![Bilden visar SAML-autentisering](media/f5-sso-vpn/saml-authentication.png)

7. Välj **+** för lyckad gren av SAML auth.

8. På fliken tilldelning väljer du **Avancerad resurs tilldelning** följt av **Lägg till objekt**

![Bild visar avancerad resurs tilldelning](media/f5-sso-vpn/advance-resource-assign.png)

9. I popup-fönstret väljer du **ny post** och sedan **Lägg till/ta bort**.

10. I det underordnade fönstret väljer du **nätverks åtkomst** och väljer sedan den nätverks åtkomst profil som skapades tidigare

![Bild som visar hur du lägger till ny nätverks åtkomst post](media/f5-sso-vpn/add-new-entry.png)

11. Växla till fliken **Webtop** och Lägg till Webtop-objektet som skapades tidigare.

![Bild som visar hur du lägger till Webtop-objekt](media/f5-sso-vpn/add-webtop-object.png)

12. Välj **Uppdatera** följt av **Spara**.

13. Välj länken i rutan övre neka för att ändra den slutförda grenen så att den **tillåter** att du **sparar**.

![Bild som visar ny redigeraren för visuella principer](media/f5-sso-vpn/vizual-policy-editor.png)

14. Bekräfta dessa inställningar genom att välja **tillämpa åtkomst princip** och Stäng fliken redigeraren för visuella principer.

![Bild som visar ny åtkomst princip hanterare](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>Publicera VPN-tjänsten

Med alla inställningar på plats kräver APM nu en virtuell klient server för att lyssna efter klienter som ansluter till VPN-nätverket.

1. Välj lista över virtuella **lokala trafik**  >  **servrar**  >  **virtuell server** och välj **skapa**.

2. Ange ett **namn** för den virtuella VPN-servern, till exempel **VPN_Listener**.

3. Ange den virtuella servern med en oanvänd **IP-målserver** som har routning på plats för att ta emot klient trafik

4. Ställ in tjänst porten på **443 https** och se till att tillstånden visar **aktive rad**

![Bild visar ny virtuell server](media/f5-sso-vpn/new-virtual-server.png)

5. Ange http- **profilen** till http och Lägg till SSL-profilen (klienten) för det offentliga SSL-certifikatet som du etablerade som en del av kraven.

![Bild som visar SSL-profil](media/f5-sso-vpn/ssl-profile.png)

6. Under åtkomst princip anger du profil för **åtkomst profil** och **anslutnings profil** för att använda de VPN-objekt som skapats.

![Bild visar åtkomst princip](media/f5-sso-vpn/access-policy.png)

7. Välj **slutförd** när du är färdig.

8.  Din SSL-VPN-tjänst är nu publicerad och tillgänglig via SHA, antingen direkt via dess URL eller via Microsofts program portaler.

## <a name="additional-resources"></a>Ytterligare resurser

- [Slutet på lösen orden, växla lösen ord](https://www.microsoft.com/security/business/identity/passwordless)

- [Vad är villkorsstyrd åtkomst?](../conditional-access/overview.md)

- [Microsoft Zero Trust Framework för att aktivera fjärran slutet](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Fem steg till fullständig program integrering med Azure AD](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>Nästa steg

Öppna en webbläsare på en fjärran sluten Windows-klient och gå till URL: en för **tjänsten Big-IP VPN**. När du har autentiserat till Azure AD visas den stora IP-portalen och VPN-startprogrammet.

![Bild som visar VPN-start](media/f5-sso-vpn/vpn-launcher.png)

Om du väljer VPN-panelen installeras BIG-IP Edge-klienten och upprättar en VPN-anslutning som är konfigurerad för SHA.
F5 VPN-programmet bör också vara synligt som en mål resurs i villkorlig åtkomst för Azure AD. Se vår [vägledning](../conditional-access/concept-conditional-access-policies.md) för att skapa principer för villkorlig åtkomst och även aktivera användare för [lösen ords mindre autentisering](https://www.microsoft.com/security/business/identity/passwordless)med Azure AD.