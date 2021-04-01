---
title: Konfigurera direkt Federation med en AD FS för B2B – Azure AD
description: Lär dig hur du konfigurerar AD FS som identitets leverantör för direkt Federation så att gästerna kan logga in till dina Azure AD-appar
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3d7c47ff0a2c533bf12a67958a913b22915f75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87910196"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Exempel: direkt Federation med Active Directory Federation Services (AD FS) (AD FS) (för hands version)

> [!NOTE]
> Direkt Federation är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Den här artikeln beskriver hur du konfigurerar [direkt Federation](direct-federation.md) med Active Directory Federation Services (AD FS) (AD FS) som antingen en SAML 2,0-eller WS-Fed-identitetsprovider. För att stödja direkt Federation måste vissa attribut och anspråk konfigureras hos identitets leverantören. För att illustrera hur du konfigurerar en identitets leverantör för direkt Federation använder vi Active Directory Federation Services (AD FS) (AD FS) som exempel. Vi visar hur du konfigurerar AD FS både som en SAML-identitetsprovider och som en WS-Fed identitets leverantör.

> [!NOTE]
> I den här artikeln beskrivs hur du konfigurerar AD FS för både SAML och WS-Fed i illustrations syfte. För direkta Federations integrationer där identitetsprovider är AD FS rekommenderar vi att du använder WS-Fed som protokoll. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Konfigurera AD FS för SAML 2,0 direkt Federation
Azure AD B2B kan konfigureras för att federera med identitets leverantörer som använder SAML-protokollet med särskilda krav som anges nedan. I det här avsnittet visas hur du konfigurerar AD FS för SAML 2,0 för att illustrera stegen i SAML-konfigurationen. 

Om du vill konfigurera direkt Federation måste följande attribut tas emot i SAML 2,0-svaret från identitets leverantören. Dessa attribut kan konfigureras genom att länka till XML-filen för Online Security token eller genom att ange dem manuellt. Steg 12 i [skapa en test AD FS-instans](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beskriver hur du hittar AD FS slut punkter eller hur du skapar din metadata-URL, till exempel `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` . 

|Attribut  |Värde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdar-URI för partner IdP, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |

Följande anspråk måste konfigureras i SAML 2,0-token som utfärdas av identitets leverantören:


|Attribut  |Värde  |
|---------|---------|
|NameID-format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


I nästa avsnitt beskrivs hur du konfigurerar de attribut och anspråk som krävs med AD FS som ett exempel på en identitets leverantör för SAML 2,0.

### <a name="before-you-begin"></a>Innan du börjar

En AD FS server måste redan vara konfigurerad och fungerande innan du påbörjar den här proceduren. Information om hur du konfigurerar en AD FS-server finns i [skapa en test AD FS 3,0-instans på en virtuell Azure-dator](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Lägg till anspråks beskrivningen

1. På AD FS-servern väljer du **verktyg**  >  **AD FS hantering**.
2. I navigerings fönstret väljer du beskrivningar av **service**  >  **anspråks beskrivningar**.
3. Under **åtgärder** väljer du **Lägg till anspråks Beskrivning**.
4. I fönstret **Lägg till en anspråks Beskrivning** anger du följande värden:

   - **Visnings namn**: beständig identifierare
   - **Anspråks identifierare**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Markera kryss rutan för att **publicera den här anspråks beskrivningen i federationsmetadata som en anspråks typ som Federations tjänsten kan acceptera**.
   - Markera kryss rutan för att **publicera den här anspråks beskrivningen i federationsmetadata som en anspråks typ som Federations tjänsten kan skicka**.

5. Klicka på **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Lägg till förtroende för förlitande part och anspråks regler

1. På AD FS-servern går du till **verktyg**  >  **AD FS hantering**.
2. I navigerings fönstret väljer du **förtroende relationer**  >  **förlitande part förtroenden**.
3. Under **åtgärder** väljer du **Lägg till förtroende för förlitande part**. 
4. I guiden Lägg till förlitande part förtroende för **Välj data källa** använder du alternativet **Importera data om den förlitande parten som publicerats online eller i ett lokalt nätverk**. Ange URL för federationsmetadata – https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml . Lämna andra standard val. Välj **Stäng**.
5. Guiden **Redigera anspråks regler** öppnas.
6. I guiden **Redigera anspråks regler** väljer du **Lägg till regel**. I **Välj regeltyp** väljer du **Skicka LDAP-attribut som anspråk**. Välj **Nästa**.
7. I **Konfigurera anspråks regel** anger du följande värden: 

   - **Namn på anspråks regel**: regel för e-postanspråk 
   - **Attributarkiv**: Active Directory 
   - **LDAP-attribut**: e-post-adresser 
   - **Utgående anspråks typ**: e-postadress

8. Välj **Slutför**.
9. I fönstret **Redigera anspråks regler** visas den nya regeln. Klicka på **Applicera**. 
10. Klicka på **OK**.  

### <a name="create-an-email-transform-rule"></a>Skapa en regel för e-posttransformering
1. Gå till **Redigera anspråks regler** och klicka på **Lägg till regel**. I **Välj regeltyp** väljer du **transformera ett inkommande anspråk** och klickar på **Nästa**. 
2. I **Konfigurera anspråks regel** anger du följande värden: 

   - **Anspråks regel namn**: e-posttransformerings regel 
   - **Inkommande anspråks typ**: e-postadress 
   - **Utgående anspråks typ**: namn-ID 
   - **Format för utgående namn-ID**: beständig identifierare 
   - Välj **Släpp igenom alla anspråksvärden**.

3. Klicka på **Finish**. 
4. I fönstret **Redigera anspråks regler** visas de nya reglerna. Klicka på **Applicera**. 
5. Klicka på **OK**. AD FS servern har nu kon figurer ATS för direkt Federation med hjälp av SAML 2,0-protokollet.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Konfigurera AD FS för WS-Fed direkt Federation 
Azure AD B2B kan konfigureras för att federera med identitets leverantörer som använder WS-Fed-protokollet med de särskilda krav som anges nedan. För närvarande har de två WS-Fed-leverantörerna testats för kompatibilitet med Azure AD, inklusive AD FS och Shibboleth. Här använder vi Active Directory Federation Services (AD FS) (AD FS) som ett exempel på WS-Fed identitets leverantören. Om du vill ha mer information om hur du etablerar ett förlitande part förtroende mellan en WS-Fed-kompatibel Provider med Azure AD kan du hämta kompatibilitetsinställningarna för Azure AD Identity Provider.

Om du vill konfigurera direkt Federation måste följande attribut tas emot i WS-Fed-meddelandet från identitets leverantören. Dessa attribut kan konfigureras genom att länka till XML-filen för Online Security token eller genom att ange dem manuellt. Steg 12 i [skapa en test AD FS-instans](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beskriver hur du hittar AD FS slut punkter eller hur du skapar din metadata-URL, till exempel `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` .
 
|Attribut  |Värde  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdar-URI för partner IdP, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |

Obligatoriska anspråk för den WS-Fed token som utfärdats av IdP:

|Attribut  |Värde  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

I nästa avsnitt beskrivs hur du konfigurerar nödvändiga attribut och anspråk med AD FS som ett exempel på en WS-Fed identitets leverantör.

### <a name="before-you-begin"></a>Innan du börjar
En AD FS server måste redan vara konfigurerad och fungerande innan du påbörjar den här proceduren. Information om hur du konfigurerar en AD FS-server finns i [skapa en test AD FS 3,0-instans på en virtuell Azure-dator](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Lägg till förtroende för förlitande part och anspråks regler 
1. På AD FS-servern går du till **verktyg**  >  **AD FS hantering**. 
1. I navigerings fönstret väljer du **förtroende relationer**  >  **förlitande part förtroenden**. 
1. Under **åtgärder** väljer du **Lägg till förtroende för förlitande part**.  
1. I guiden Lägg till förlitande part förtroende, för **Välj data källa**, använder du alternativet **Importera data om den förlitande parten som publicerats online eller i ett lokalt nätverk**. Ange den här URL: en för federationsmetadata: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml` .  Lämna andra standard val. Välj **Stäng**.
1. Guiden **Redigera anspråks regler** öppnas. 
1. I guiden **Redigera anspråks regler** väljer du **Lägg till regel**. I **Välj regeltyp** väljer du **skicka anspråk med en anpassad regel**. Välj *Nästa*. 
1. I **Konfigurera anspråks regel** anger du följande värden:

   - **Namn på anspråks regel**: utfärda oföränderligt ID  
   - **Anpassad regel**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Välj **Slutför**. 
1. I fönstret **Redigera anspråks regler** visas den nya regeln. Klicka på **Applicera**.  
1. I guiden **Redigera anspråks regler** väljer du **Lägg till regel**. I **typ av inslang-regel** väljer du **Skicka LDAP-attribut som anspråk**. Välj **Nästa**.
1. I **Konfigurera anspråks regel** anger du följande värden: 

   - **Namn på anspråks regel**: regel för e-postanspråk  
   - **Attributarkiv**: Active Directory  
   - **LDAP-attribut**: e-post-adresser  
   - **Utgående anspråks typ**: e-postadress 

1.  Välj **Slutför**. 
1.  I fönstret **Redigera anspråks regler** visas den nya regeln. Klicka på **Applicera**.  
1.  Klicka på **OK**. Den AD FS servern har nu kon figurer ATS för direkt Federation med WS-utfodras.

## <a name="next-steps"></a>Nästa steg
Sedan [konfigurerar du direkt Federation i Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) antingen i Azure AD-portalen eller med hjälp av PowerShell. 
