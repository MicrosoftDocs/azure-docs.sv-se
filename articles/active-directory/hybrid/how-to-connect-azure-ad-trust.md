---
title: Azure AD Connect-hantera AD FS förtroende med Azure AD med hjälp av Azure AD Connect | Microsoft Docs
description: Drift information om Azure AD Trust-hantering av Azure AD Connect.
keywords: AD FS, ADFS, AD FS hantering, AAD Connect, Connect, Azure AD, Trust, AAD, anspråk, anspråk, anspråks regler, utfärdande, transformering, regler, säkerhets kopiering, återställning
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13d56ec321cd257412c2b0abbe0be655c6cb4dbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85360103"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Hantera AD FS-förtroende med Azure AD med hjälp av Azure AD Connect

## <a name="overview"></a>Översikt

Azure AD Connect kan hantera federation mellan lokala Active Directory Federationstjänst (AD FS) och Azure AD. Den här artikeln innehåller en översikt över:

* De olika inställningarna som kon figurer ATS på förtroendet av Azure AD Connect
* Reglerna för omvandling av utfärdande (anspråks regler) angavs av Azure AD Connect
* Säkerhetskopiera och återställa anspråks reglerna mellan uppgraderingar och konfigurations uppdateringar. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Inställningar som styrs av Azure AD Connect

Azure AD Connect hanterar **endast** inställningar som rör Azure AD-förtroende. Azure AD Connect ändrar inte några inställningar på andra förlitande parters förtroenden i AD FS. Följande tabell visar inställningar som styrs av Azure AD Connect.

| Inställning | Beskrivning |
| :--- | :--- |
| Token signerings certifikat | Azure AD Connect kan användas för att återställa och återskapa förtroendet med Azure AD. Azure AD Connect sker en omedelbar förnyelse av token signerings certifikat för AD FS och uppdaterar Azure AD-domänens Federations inställningar.|
| Algoritm för tokensignering | Microsoft rekommenderar att du använder SHA-256 som algoritm för Token-signering. Azure AD Connect kan identifiera om algoritmen för tokensignering har angetts till ett värde som är mindre säkert än SHA-256. Inställningen uppdateras till SHA-256 i nästa möjliga konfigurations åtgärd. Andra förlitande parters förtroende måste uppdateras för att använda det nya token signerings certifikatet. |
| Tillförlitlighets identifierare för Azure AD | Azure AD Connect anger rätt Identifier-värde för Azure AD-förtroendet. AD FS unikt identifierar Azure AD-förtroendet med hjälp av Identifier-värdet. |
| Azure AD-slutpunkter | Azure AD Connect ser till att de slut punkter som har kon figurer ATS för Azure AD-förtroendet alltid är enligt de senaste rekommenderade värdena för återhämtning och prestanda. |
| Omvandlings regler för utfärdande | Det finns ett antal anspråks regler som behövs för optimala prestanda för funktionerna i Azure AD i en federerad inställning. Azure AD Connect ser till att Azure AD-förtroendet alltid är konfigurerat med rätt uppsättning rekommenderade anspråks regler. |
| Alternativt-ID | Om synkronisering har kon figurer ATS för att använda alternativ-ID, Azure AD Connect konfigurera AD FS för att utföra autentisering med hjälp av alternativt-ID. |
| Automatisk uppdatering av metadata | Förtroende med Azure AD har kon figurer ATS för automatisk uppdatering av metadata. AD FS regelbundet kontrollerar metadata för Azure AD-förtroendet och ser till att det är uppdaterat om det sker på Azure AD-sidan. |
| Integrerad Windows-autentisering (IWA) | Under hybrid Azure AD Join-åtgärden har IWA Aktiver ATS för enhets registrering för att under lätta hybrid Azure AD-anslutning för äldre enheter |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Körnings flöden och Federations inställningar som kon figurer ATS av Azure AD Connect

Azure AD Connect uppdaterar inte alla inställningar för Azure AD-förtroende under konfigurations flöden. De ändrade inställningarna beror på vilken aktivitet eller vilket körnings flöde som körs. I följande tabell visas de inställningar som påverkas i olika körnings flöden.

| Körnings flöde | Inställningar som påverkas |
| :--- | :--- |
| Första pass installation (Express) | Inget |
| Första pass installationen (ny AD FS server grupp) | En ny AD FS grupp skapas och ett förtroende med Azure AD skapas från grunden. |
| Första pass installation (befintlig AD FS server grupp, befintligt Azure AD-förtroende) | Azure AD-förtroende-ID, regler för utfärdande av utfärdande, Azure AD-slutpunkter, alternativ-ID (vid behov), automatisk uppdatering av metadata |
| Återställ Azure AD-förtroende | Token signerings certifikat, token Signeringsalgoritm, Azure AD Trust Identifier, regler för utfärdande av utfärdande, Azure AD-slutpunkter, alternativ-ID (vid behov), automatisk uppdatering av metadata |
| Lägg till Federations Server | Inget |
| Lägg till WAP-server | Inget |
| Enhetsalternativ | Omvandlings regler för utfärdande, IWA för enhets registrering |
| Lägg till federerad domän | Om domänen har lagts till för första gången, det vill säga, ändras konfigurationen från en enda domän federation till federationen för flera domäner – Azure AD Connect kommer att återskapa förtroendet från grunden. Om förtroendet med Azure AD redan har kon figurer ATS för flera domäner, ändras endast regler för utfärdande av utfärdande. |
| Uppdatera TLS | Inget |

Under alla åtgärder ändras, i vilken alla inställningar ändras, Azure AD Connect en säkerhets kopia av aktuella förtroende inställningar på **%programdata%\AADConnect\ADFS**

![Azure AD Connect sida som visar meddelande om befintlig säkerhets kopiering av Azure AD-förtroende](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Före version 1.1.873.0 är säkerhets kopian endast av reglerna för utfärdande av utfärdande och de säkerhetskopierades i guidens spårnings logg fil.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Regler för utfärdande av utfärdande som angetts av Azure AD Connect

Azure AD Connect ser till att Azure AD-förtroendet alltid är konfigurerat med rätt uppsättning rekommenderade anspråks regler. Microsoft rekommenderar att du använder Azure AD Connect för att hantera ditt Azure AD-förtroende. I det här avsnittet visas reglerna för omvandling av utfärdande och deras beskrivning.

| Regelnamn | Beskrivning |
| --- | --- |
| Utfärda UPN | Den här regeln frågar värdet för UserPrincipalName som från attributet som kon figurer ATS i synkroniseringsinställningar för userPrincipalName.|
| Fråga objectGUID och msdsconsistencyguid för anpassat ImmutableId-anspråk | Den här regeln lägger till ett tillfälligt värde i pipelinen för objectGUID och msdsconsistencyguid-värdet om det finns |
| Sök efter förekomsten av msdsconsistencyguid | Baserat på om värdet för msdsconsistencyguid finns eller inte, anger vi en tillfällig flagga för att dirigera vad som ska användas som ImmutableId |
| Utfärda msdsconsistencyguid som oföränderligt ID om det finns | Utfärda msdsconsistencyguid som ImmutableId om värdet finns |
| Utfärda objectGuidRule om msdsConsistencyGuid-regeln inte finns | Om värdet för msdsconsistencyguid inte finns kommer värdet objectGUID att utfärdas som ImmutableId |
| Utfärda NameIdentifier | Den här regeln utfärdar värdet för NameIdentifier-anspråket.|
| Utfärda start@ för domänanslutna datorer | Om den entitet som autentiseras är en domänansluten enhet, utfärdar den här regeln konto typen som DJ som betecknar en domänansluten enhet |
| Utfärda en kontokod med värdet användare när det inte är ett dator konto | Om den entitet som autentiseras är en användare utfärdar den här regeln konto typen som användare |
| Utfärda issuerid när det inte är ett dator konto | Den här regeln utfärdar värdet issuerId när den autentiserande entiteten inte är en enhet. Värdet skapas via ett regex, som konfigureras av Azure AD Connect. Regex skapas efter att du beaktat alla domäner som är federerade med Azure AD Connect. |
| Utfärda issuerid för DJ-datorns auth | Den här regeln utfärdar värdet issuerId när den autentiserande enheten är en enhet |
| Utfärda onpremobjectguid för domänanslutna datorer | Om den entitet som autentiseras är en domänansluten enhet, utfärdar den här regeln den lokala objectGUID för enheten |
| Släpp via primärt SID | Den här regeln utfärdar primärt SID för den autentiserande entiteten |
| Släpp igenom anspråk – insideCorporateNetwork | Den här regeln utfärdar ett anspråk som hjälper Azure AD att veta om autentiseringen kommer från företags nätverket eller externt |
| Släpp igenom anspråk – PSSO |   |
| Utfärda anspråk på förfallo datum för lösen ord | Den här regeln utfärdar tre anspråk för förfallo tid för lösen ord, antalet dagar tills lösen ordet upphör att gälla den entitet som autentiseras och URL: en för att ändra lösen ordet.|
| Släpp igenom anspråk – authnmethodsreferences | Värdet i det anspråk som utfärdas enligt den här regeln anger vilken typ av autentisering som har utförts för entiteten |
| Släpp igenom anspråk – multifactorauthenticationinstant | Värdet för det här anspråket anger tiden, i UTC, när användaren senast utförde flera Factor Authentication. |
| Släpp igenom anspråk – AlternateLoginID | Den här regeln utfärdar AlternateLoginID-anspråk om autentiseringen utfördes med hjälp av alternativt inloggnings-ID. |

> [!NOTE]
> Anspråks reglerna för problem-UPN och ImmutableId skiljer sig om du använder ett alternativ som inte är standard under Azure AD Connect konfiguration

## <a name="restore-issuance-transform-rules"></a>Återställ regler för utfärdande av utfärdande

Azure AD Connect version 1.1.873.0 eller senare gör en säkerhets kopia av inställningarna för Azure AD-förtroende när en uppdatering görs i förtroende inställningarna för Azure AD. Inställningarna för Azure AD-förtroende säkerhets kopie ras på **%programdata%\AADConnect\ADFS**. Fil namnet har följande format AadTrust- &lt; date- &gt; - &lt; Time &gt; . txt, till exempel-AadTrust-20180710-150216.txt

![En skärm bild av exempel på säkerhets kopiering av Azure AD-förtroende](./media/how-to-connect-azure-ad-trust/backup.png)

Du kan återställa reglerna för omvandling av utfärdande med hjälp av de föreslagna stegen nedan

1. Öppna användar gränssnittet för AD FS hantering i Serverhanteraren
2. Öppna egenskaperna för Azure AD-förtroendet genom att gå **AD FS &gt; förlitande part förtroenden &gt; Microsoft Office 365 identitets plattform &gt; Redigera anspråk utgivnings princip**
3. Klicka på **Lägg till regel**
4. I mallen för anspråks regel väljer du skicka anspråk med en anpassad regel och klickar på **Nästa**
5. Kopiera namnet på anspråks regeln från säkerhets kopian och klistra in den i fält **anspråks regelns namn**
6. Kopiera anspråks regeln från säkerhets kopian till text fältet för den **anpassade regeln** och klicka på **Slutför**

> [!NOTE]
> Se till att dina ytterligare regler inte står i konflikt med reglerna som kon figurer ATS av Azure AD Connect.

## <a name="next-steps"></a>Nästa steg
* [Hantera och anpassa Active Directory Federation Services (AD FS) med Azure AD Connect](how-to-connect-fed-management.md)
