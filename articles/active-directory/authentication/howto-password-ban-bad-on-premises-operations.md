---
title: Aktivera lokalt Azure AD-lösenord för lösen ords skydd
description: Lär dig hur du aktiverar Azure AD Password Protection för en lokal Active Directory Domain Services miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741787"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Aktivera lokalt Azure Active Directory lösen ords skydd

Användare skapar ofta lösen ord som använder vanliga lokala ord som skolan, idrotts lag eller berömda person. Dessa lösen ord är lätta att gissa sig till och är svaga mot ordbaserade attacker. För att kunna använda starka lösen ord i din organisation har Azure Active Directory (Azure AD) lösen ords skydd en lista med globala och anpassade lösen ord. En begäran om lösen ords ändring Miss lyckas om det finns en matchning i dessa förbjudna lösen ords listor.

Om du vill skydda din lokala Active Directory Domain Services (AD DS)-miljö kan du installera och konfigurera Azure AD Password Protection så att den fungerar med din lokal DC. Den här artikeln visar hur du aktiverar lösen ords skydd i Azure AD för din lokala miljö.

Mer information om hur du arbetar med Azure AD Password Protection i en lokal miljö finns i [så här tillämpar du Azure AD Password Protection för Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln visar hur du aktiverar lösen ords skydd i Azure AD för din lokala miljö. Innan du slutför den här artikeln [installerar och registrerar du Azure AD-proxyn för lösen ords skydd och DC-agenter](howto-password-ban-bad-on-premises-deploy.md) i din lokala AD DS-miljö.

## <a name="enable-on-premises-password-protection"></a>Aktivera lokalt lösen ords skydd

1. Logga in på [Azure Portal](https://portal.azure.com) och bläddra till **Azure Active Directory**  >    >  **säkerhetsautentiseringsmetoder metod**  >  **lösen ords skydd**.
1. Ange alternativet för att **Aktivera lösen ords skydd på Windows Server Active Directory** till *Ja*.

    När den här inställningen är inställd på *Nej*, går alla distribuerade Azure AD-quiescent till ett läge där alla lösen ord godkänns. Inga validerings aktiviteter utförs och gransknings händelser genereras inte.

1. Vi rekommenderar att du ursprungligen anger **läget** för *granskning*. När du är van att använda funktionen och påverkan på användare i din organisation, kan du växla **läge** till *tvingande*. Mer information finns i följande avsnitt om [drifts lägen](#modes-of-operation).
1. När du är klar väljer du **Spara**.

    [![Aktivera lokalt lösen ords skydd under autentiseringsmetoder i Azure Portal](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Lägen för åtgärden

När du aktiverar ett lokalt Azure AD-lösenord kan du antingen använda *gransknings* läge eller *Framtvinga* läge. Vi rekommenderar att inledande distribution och testning alltid startar i gransknings läge. Poster i händelse loggen bör sedan övervakas för att förutse om eventuella befintliga operativa processer skulle störas när *tvingande* läge är aktiverat.

### <a name="audit-mode"></a>Gransknings läge

*Gransknings* läget är avsett som ett sätt att köra program varan i "Vad händer om"-läge. Varje Azure AD Password Protection DC-agenttjänsten utvärderar ett inkommande lösen ord enligt den aktuella aktiva principen.

Om den aktuella principen har kon figurer ATS som gransknings läge resulterar "dåliga" lösen ord i händelse logg meddelanden men bearbetas och uppdateras. Det här beteendet är den enda skillnaden mellan gransknings-och framtvingande läge. Alla andra åtgärder kör samma.

### <a name="enforced-mode"></a>Tvingat läge

*Tvingande* läge är avsett som den slutliga konfigurationen. Precis som i gransknings läget utvärderar varje Azure AD Password Protection DC-agenttjänsten inkommande lösen ord enligt den aktuella aktiva principen. När tvingat läge aktive ras, avvisas ett lösen ord som betraktas som osäkert enligt principen.

När ett lösen ord avvisas i tvingat läge av Azure AD Password Protection DC-agenten ser en slutanvändare ett liknande fel som att se om deras lösen ord avvisades av traditionell lokal lösen ords komplexitet. En användare kan till exempel se följande vanliga fel meddelande på skärmen Windows-inloggning eller ändra lösen ord:

*"Det gick inte att uppdatera lösen ordet. Det angivna värdet för det nya lösen ordet uppfyller inte domänens krav på längd, komplexitet eller historik. "*

Det här meddelandet är bara ett exempel på flera möjliga resultat. Det specifika fel meddelandet kan variera beroende på den faktiska program varan eller det scenario som försöker ange ett osäkert lösen ord.

Berörda slutanvändare kan behöva arbeta med sin IT-personal för att förstå de nya kraven och välja säkra lösen ord.

> [!NOTE]
> Lösen ords skydd i Azure AD har ingen kontroll över det särskilda fel meddelande som visas av klient datorn när ett svagt lösen ord avvisas.

## <a name="next-steps"></a>Nästa steg

Information om hur du anpassar listan över förbjudna lösen ord för din organisation finns i [Konfigurera den anpassade lösen ords listan i Azure AD Password Protection](tutorial-configure-custom-password-protection.md).

Information om hur du övervakar lokal-händelser finns i [övervakning av lösen ords skydd på lokal Azure AD](howto-password-ban-bad-on-premises-monitor.md).
