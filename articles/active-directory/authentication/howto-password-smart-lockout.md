---
title: Förhindra attacker med Smart utelåsning – Azure Active Directory
description: Läs mer om hur Azure Active Directory Smart utelåsning hjälper till att skydda din organisation från brute-force-attacker som försöker gissa användar lösen ord.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f501c19da3c2ddc06ad89fe5649789477af7ec
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255381"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Skydda användar konton mot attacker med Azure Active Directory Smart utelåsning

Smart utelåsning hjälper till att låsa Felaktiga aktörer som försöker gissa dina användares lösen ord eller använda råa metoder för att komma igång. Smart utelåsning kan identifiera inloggningar som kommer från giltiga användare och behandla dem på olika sätt än de som är angripare och andra okända källor. Angripare låser sig ut, medan användarna fortsätter att komma åt sina konton och vara produktiva.

## <a name="how-smart-lockout-works"></a>Så här fungerar Smart utelåsning

Som standard låser Smart utelåsning kontot från inloggnings försök i en minut efter 10 misslyckade försök. Kontot låser sig igen efter varje efterföljande misslyckad inloggnings försök, i en minut vid första och längre vid efterföljande försök. För att minimera de sätt som en angripare kan kringgå problemet, avslöjar vi inte den hastighet som blockeringstiden ökar vid ytterligare misslyckade inloggnings försök.

Smart utelåsning spårar de tre senaste Felaktiga hasharna för lösen ord för att undvika ökning av utelåsnings räknaren för samma lösen ord. Om någon anger samma Felaktiga lösen ord flera gånger kan det här beteendet inte medföra att kontot låser sig.

> [!NOTE]
> Funktionen för hash-spårning är inte tillgänglig för kunder med direkt autentisering aktive rad eftersom autentisering sker lokalt, inte i molnet.

Federerade distributioner som använder AD FS 2016 och AF FS 2019 kan möjliggöra liknande förmåner med [AD FS extra näts utelåsning och extra näts Smart utelåsning](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Smart utelåsning är alltid aktive ras, för alla Azure AD-kunder, med dessa standardinställningar som ger rätt blandning av säkerhet och användbarhet. Anpassning av inställningarna för smart utelåsning, med värden som är speciella för din organisation, kräver Azure AD Premium P1 eller högre licenser för dina användare.

Att använda Smart utelåsning garanterar inte att en äkta användare aldrig är utelåst. När Smart utelåsning låser ett användar konto, provar vi vår bästa att inte låsa ut den äkta användaren. Utelåsnings tjänsten försöker se till att felaktiga aktörer inte får åtkomst till ett äkta användar konto. Följande gäller:

* Varje Azure AD-datacenter spårar utelåsning oberoende av varandra. En användare har (*threshold_limit * datacenter_count*) antal försök om användaren träffar varje data Center.
* Smart utelåsning använder bekant plats kontra osäker plats för att skilja mellan en felaktig aktör och den äkta användaren. Okända och välbekanta platser har båda separata utelåsnings räknare.

Smart utelåsning kan integreras med hybrid distributioner som använder hash-synkronisering av lösen ord eller direktautentisering för att skydda lokala Active Directory Domain Services-konton (AD DS) från att låsas av angripare. Genom att ange principer för smart utelåsning i Azure AD kan angrepp filtreras ut innan de når lokala AD DS.

När du använder [direktautentisering](../hybrid/how-to-connect-pta.md)använder du följande överväganden:

* Tröskelvärdet för Azure AD-utelåsning är **mindre** än tröskelvärdet för AD DS-konto utelåsning. Ange värdena så att tröskelvärdet för AD DS-konto utelåsning är minst två eller tre gånger längre än tröskelvärdet för Azure AD-utelåsning.
* Azure AD-utelåsningsens varaktighet måste vara längre än AD DS-räknaren för återställning av konto utelåsning efter varaktighet. Azure AD-varaktigheten anges i sekunder, medan AD-varaktigheten anges i minuter.

Om du till exempel vill att din Azure AD-räknare ska vara högre än AD DS blir Azure AD 120 sekunder (2 minuter) medan din lokala AD har angetts till 1 minut (60 sekunder).

> [!IMPORTANT]
> För närvarande kan en administratör inte låsa upp användarnas moln konton om de har låsts av funktionen Smart utelåsning. Administratören måste vänta tills utelåsnings tiden upphör att gälla. Användaren kan dock låsa upp med hjälp av självbetjäning för återställning av lösen ord (SSPR) från en betrodd enhet eller plats.

## <a name="verify-on-premises-account-lockout-policy"></a>Verifiera den lokala konto utelåsnings principen

Verifiera din lokala AD DS-utelåsnings princip för AD DS genom att utföra följande steg från ett domänanslutna system med administratörs behörighet:

1. Öppna verktyget grupprincip hantering.
2. Redigera grup principen som innehåller din organisations princip för konto utelåsning, till exempel **standard domän principen**.
3. Bläddra till **dator konfiguration**  >  **principer**  >  **Windows-inställningar**  >  **säkerhets inställningar**  >  **konto principer**  >  **konto utelåsnings princip**.
4. Verifiera ditt **konto utelåsnings tröskelvärde** och **Återställ räknaren för konto utelåsning efter** värden.

![Ändra principen för den lokala Active Directory konto utelåsning](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Hantera Azure AD Smart utelåsnings värden

Utifrån organisationens krav kan du anpassa Azure AD Smart utelåsnings värden. Anpassning av inställningarna för smart utelåsning, med värden som är speciella för din organisation, kräver Azure AD Premium P1 eller högre licenser för dina användare.

Utför följande steg för att kontrol lera eller ändra de smarta utelåsnings värdena för din organisation:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj *Azure Active Directory* **och välj sedan**  >  **säkerhetsautentisering metod**  >  **lösen ords skydd**.
1. Ange **tröskelvärdet för utelåsning**, baserat på hur många misslyckade inloggningar som tillåts på ett konto före det första utelåsning.

    Standardvärdet är 10.

1. Ange **utelåsnings tiden i sekunder** till längden i sekunder för varje utelåsning.

    Standardvärdet är 60 sekunder (en minut).

> [!NOTE]
> Om den första inloggningen efter en utelåsning också Miss lyckas, låser kontot ut igen. Om ett konto låser sig upprepade gånger ökar utelåsnings varaktigheten.

![Anpassa Azure AD Smart utelåsnings-principen i Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Så här avgör du om funktionen för smart utelåsning fungerar eller inte

När tröskelvärdet för smart utelåsning utlöses, får du följande meddelande när kontot är låst:

*Ditt konto är tillfälligt låst för att förhindra obehörig användning. Försök igen senare och kontakta administratören om du fortfarande har problem.*

När du testar Smart utelåsning kan dina inloggnings begär Anden hanteras av olika data Center på grund av den geo-distribuerade och belastningsutjämnad typen av Azure AD-autentiseringstjänst. I det scenariot, eftersom varje Azure AD-datacenter spårar oberoende av varandra, kan det ta mer än ditt definierade utelåsnings tröskel antal försök att orsaka en utelåsning. En användare har (*threshold_limit * datacenter_count*) antal felaktiga försök om användaren träffar varje data Center innan en utelåsning sker.

## <a name="next-steps"></a>Nästa steg

Om du vill anpassa upplevelsen ytterligare kan du [Konfigurera anpassade förbjudna lösen ord för lösen ords skydd i Azure AD](tutorial-configure-custom-password-protection.md).

För att hjälpa användare att återställa eller ändra sitt lösen ord från en webbläsare kan du [Konfigurera återställning av lösen ord för Azure AD-självbetjäning](tutorial-enable-sspr.md).
