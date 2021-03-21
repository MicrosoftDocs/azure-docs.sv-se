---
title: Referens för Azure Active Directory General Operations Guide
description: Den här åtgärds hand boken beskriver de kontroller och åtgärder som du bör vidta för att skydda allmänna åtgärder
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d5a8fe4192c3778e259ed18239a4198398d8807b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836843"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Referens för Azure Active Directory General Operations Guide

Det här avsnittet i [hand boken för Azure AD-åtgärder](active-directory-ops-guide-intro.md) beskriver de kontroller och åtgärder som du bör vidta för att optimera de allmänna åtgärderna i Azure Active Directory (Azure AD).

> [!NOTE]
> Dessa rekommendationer är aktuella vid publicerings datumet, men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sin operativa praxis när Microsofts produkter och tjänster utvecklas med tiden.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till viktiga uppgifter

Hantering av Azure Active Directory kräver kontinuerlig körning av viktiga operativa uppgifter och processer, som kanske inte ingår i ett lanserings projekt. Det är fortfarande viktigt att du konfigurerar dessa uppgifter för att optimera din miljö. De viktigaste uppgifterna och deras rekommenderade ägare är:

| Uppgift | Ägare |
| :- | :- |
| Förbättringar av identitet för säkra Poäng för identitet | Åtgärds team för informations säkerhet |
| Underhåll Azure AD Connect-servrar | IAM-åtgärds team |
| Köra och prioritering IdFix-rapporter regelbundet | IAM-åtgärds team |
| Prioritering Azure AD Connect Health-aviseringar för synkronisering och AD FS | IAM-åtgärds team |
| Om du inte använder Azure AD Connect Health, har kunden motsvarande process och verktyg för att övervaka den anpassade infrastrukturen | IAM-åtgärds team |
| Om du inte använder AD FS, har kunden motsvarande process och verktyg för att övervaka den anpassade infrastrukturen | IAM-åtgärds team |
| Övervaka hybrid loggar: Azure AD App proxy-anslutningar | IAM-åtgärds team |
| Övervaka hybrid loggar: genom strömnings agenter | IAM-åtgärds team |
| Övervaka hybrid loggar: tjänsten för tillbakaskrivning av lösen ord | IAM-åtgärds team |
| Övervaka hybrid loggar: lokal gateway för lösen ords skydd | IAM-åtgärds team |
| Övervaka hybrid loggar: Azure AD MFA NPS-tillägg (om tillämpligt) | IAM-åtgärds team |

När du granskar listan kanske du måste tilldela en ägare för aktiviteter som saknar ägare eller justera ägarskapet för aktiviteter med ägare som inte är justerade enligt rekommendationerna ovan.

#### <a name="owners-recommended-reading"></a>Ägare, Rekommenderad läsning

- [Tilldela administratörsroller i Azure Active Directory](../roles/permissions-reference.md)
- [Styrning i Azure](../../governance/index.yml)

## <a name="hybrid-management"></a>Hybrid hantering

### <a name="recent-versions-of-on-premises-components"></a>Nya versioner av lokala komponenter

Med de senaste versionerna av lokala komponenter får kunden alla de senaste säkerhets uppdateringarna, prestanda förbättringar och funktioner som kan bidra till att ytterligare förenkla miljön. De flesta komponenter har en inställning för automatisk uppgradering som automatiserar uppgraderings processen.

Dessa komponenter omfattar:

- Azure AD Connect
- Azure AD-programproxy-kopplingar
- Azure AD-vidarekoppling av agenter
- Azure AD Connect Health agenter

Om ingen har upprättats bör du definiera en process för att uppgradera dessa komponenter och förlita dig på den automatiska uppgraderings funktionen närhelst det är möjligt. Om du hittar komponenter som är sex eller fler månader bakom bör du uppgradera så snart som möjligt.

#### <a name="hybrid-management-recommended-reading"></a>Hybrid hantering, Rekommenderad läsning

- [Azure AD Connect: Automatisk uppgradering](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Förstå Azure AD-programproxy-kopplingar | Automatiska uppdateringar](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health aviserings bas linje

Organisationer bör distribuera [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) för övervakning och rapportering av Azure AD Connect och AD FS. Azure AD Connect och AD FS är kritiska komponenter som kan bryta livs cykel hantering och autentisering och därför leda till avbrott. Azure AD Connect Health hjälper till att övervaka och få insikter om din lokala identitets infrastruktur och därigenom se till att din miljö är tillförlitlig.

![Azure AD Connect hälso-arkitektur](./media/active-directory-ops-guide/active-directory-ops-img16.png)

När du övervakar hälso tillståndet för din miljö måste du omedelbart åtgärda aviseringar med hög allvarlighets grad, följt av lägre allvarlighets grader.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health Rekommenderad läsning

- [Installation av Azure AD Connect Health Agent](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Loggar för lokala agenter

Vissa identitets-och åtkomst hanterings tjänster kräver lokala agenter för att aktivera hybrid scenarier. Exempel på detta är lösen ords återställning, direktautentisering (PTA), Azure AD-programproxy och Azure AD MFA NPS-tillägget. Det är viktigt att drifts bas linjen och övervakar hälsan för dessa komponenter genom att arkivera och analysera komponent agent loggar med hjälp av lösningar som System Center Operations Manager eller SIEM. Det är lika viktigt för driften av informations lag eller supportavdelningen att förstå hur fel fel uppstår.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Lokala agenter loggar Rekommenderad läsning

- [Felsöka programproxyn](../manage-apps/application-proxy-troubleshoot.md)
- [Fel sökning av självbetjäning för lösen ords återställning – Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [Förstå Azure AD-programproxy-kopplingar](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: Felsök direktautentisering](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Felsöka fel koder för Azure AD MFA NPS-tillägget](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Hantering av lokala agenter

Att införa bästa praxis kan hjälpa till att optimera lokala agenter. Överväg följande metod tips:

- Flera Azure AD Application Proxy-kopplingar per kopplings grupp rekommenderas för att tillhandahålla sömlös belastnings utjämning och hög tillgänglighet genom att undvika enskilda felpunkter när du ansluter till proxy-programmen. Om du för närvarande bara har en koppling i en anslutnings grupp som hanterar program i produktion bör du distribuera minst två anslutningar för redundans.
- Att skapa och använda en app proxy Connector-grupp för fel söknings syften kan vara användbart för fel söknings scenarier och när du registrerar nya lokala program. Vi rekommenderar också att du installerar nätverks verktyg som Message Analyzer och Fiddler på anslutnings datorerna.
- Flera direktautentisering för direktautentisering rekommenderas för att tillhandahålla sömlös belastnings utjämning och hög tillgänglighet genom att undvika en enskild felpunkt vid ett flöde av autentisering. Se till att du distribuerar minst två direktautentisering för redundans.

#### <a name="on-premises-agents-management-recommended-reading"></a>Hantering av lokala agenter som rekommenderar läsning

- [Förstå Azure AD-programproxy-kopplingar](../manage-apps/application-proxy-connectors.md)
- [Direkt autentisering i Azure AD – snabb start](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Hantering i skala

### <a name="identity-secure-score"></a>Identitetssäker poäng

Med de [säkraste poängen för identiteter](./identity-secure-score.md) får du ett kvantifierbart mått på säkerhets position i din organisation. Det är viktigt att du ständigt granskar och åtgärdar resultat som rapporter ATS och strävar efter att ha det högsta möjliga resultatet. Resultatet hjälper dig att:

- Objektivt mäta din identitetssäkerhetsstatus
- Planera förbättringar i identitetssäkerheten
- Granska framgången för dina förbättringar

![Säkerhetspoäng](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Om din organisation för närvarande inte har något program på plats för att övervaka ändringar i identitetens säkra poäng, rekommenderar vi att du implementerar en plan och tilldelar ägare för att övervaka och förbättra förbättrings åtgärder. Organisationer bör åtgärda förbättringar med en poäng effekt som är högre än 30 så snart som möjligt.

### <a name="notifications"></a>Meddelanden

Microsoft skickar e-postkommunikation till administratörer för att meddela olika ändringar i tjänsten, konfigurations uppdateringar som behövs och fel som kräver administratörs åtgärder. Det är viktigt att kunderna anger e-postadresserna för aviseringar så att meddelanden skickas till rätt team medlemmar som kan bekräfta och agera på alla meddelanden. Vi rekommenderar att du lägger till flera mottagare i [meddelande centret](/office365/admin/manage/message-center) och begär att meddelanden (inklusive Azure AD Connect Health meddelanden) skickas till en distributions lista eller delad post låda. Om du bara har ett globalt administratörs konto med en e-postadress måste du konfigurera minst två e-postkompatibla konton.

Det finns två "från"-adresser som används av Azure AD: <o365mc@email2.microsoft.com> , som skickar meddelande Center-meddelanden och <azure-noreply@microsoft.com> , som skickar meddelanden som rör:

- [Åtkomst granskningar för Azure AD](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [Företags program som förfaller certifikat meddelanden](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Meddelanden om företags-app-etablering

Se följande tabell för att lära dig vilken typ av meddelanden som skickas och var de ska kontrol leras:

| Meddelande källa | Vad som skickas | Var ska du kontrol lera |
|:-|:-|:-|
| Teknisk kontakt | Synkroniseringsfel | Bladet Azure Portal-egenskaper |
| Meddelandecenter | Meddelanden om incidenter och försämring av identitets tjänster och Microsoft 365 backend-tjänster | Office-portalen |
| Veckovis sammandrag av identitets skydd | Sammandrag av identitets skydd | Azure AD Identity Protection bladet |
| Azure AD Connect Health | Aviserings meddelanden | Bladet Azure Portal-Azure AD Connect Health |
| Meddelanden om företags program | Meddelanden när certifikat upphör att gälla och etablerings fel | Bladet Azure Portal-företags program (varje app har sin egen e-postadress inställning) |

#### <a name="notifications-recommended-reading"></a>Meddelanden som rekommenderas vid läsning

- [Ändra din organisations adress, teknisk kontakt med mera](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Drift ytans yta

### <a name="ad-fs-lockdown"></a>AD FS låsning

Organisationer, som konfigurerar program att autentisera direkt till Azure AD-förmånen från [Azure AD Smart utelåsning](../authentication/concept-sspr-howitworks.md). Om du använder AD FS i Windows Server 2012 R2 implementerar du AD FS [extra näts utelåsning-skydd](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Om du använder AD FS på Windows Server 2016 eller senare implementerar du [Smart utelåsning för extra nät](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Vi rekommenderar minst att du aktiverar extra näts utelåsning för att kunna innehålla risken för angrepp mot lokala Active Directory. Men om du har AD FS i Windows 2016 eller högre bör du även aktivera Smart utelåsning för extra nät som hjälper till att minimera angrepp vid [lösen ords spridning](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) .

Om AD FS bara används för Azure AD Federation finns det några slut punkter som kan stängas av för att minimera attack ytan. Om AD FS till exempel bara används för Azure AD bör du inaktivera WS-Trust andra slut punkter än de slut punkter som är aktiverade för **usernamemixed** och **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Åtkomst till datorer med lokala identitets komponenter

Organisationer bör låsa åtkomsten till datorerna med lokala hybrid komponenter på samma sätt som din lokala domän. En säkerhets kopierings ansvarig eller Hyper-V-administratör ska till exempel inte kunna logga in på Azure AD Connect servern för att ändra regler.

Active Directory administrativ nivå modell har utformats för att skydda identitets system med en uppsättning buffertzoner mellan fullständig kontroll av miljön (nivå 0) och arbets stationerna med hög risk som angripare ofta kompromettera. ![Diagram över de tre nivåerna i nivåmodellen](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[Nivå modellen](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) består av tre nivåer och inkluderar bara administrativa konton, inte standard användar konton.

- **Nivå 0** – Direkt kontroll av företagsidentiteter i miljön. Nivå 0 innehåller konton, grupper och andra resurser som har direkt eller indirekt administrativ kontroll över Active Directory-skogen, domäner eller domänkontrollanter och alla tillgångar i den. Säkerhetskänsligheten för alla tillgångar i nivå 0 är motsvarande eftersom de alla har effektiv kontroll av varandra.
- **Nivå 1** – Kontroll av företagsservrar och program. Nivå 1-material inkluderar serveroperativsystem, molntjänster och företagsprogram. Nivå 1-administratörskonton har administrativ kontroll över en betydande mängd affärsvärden som dessa tillgångar är värdar för. En vanlig exempelroll är serveradministratörer som hanterar de här operativsystemen med möjlighet att påverka alla företagstjänster.
- **Nivå 2** – Kontroll över användararbetsstationer och enheter. Nivå 2-administratörskonton har administrativ kontroll över en betydande mängd affärsvärden som dessa arbetsstationer och enheter är värdar för. Exempel innefattar administratörer för supportavdelningen och datorsupport eftersom de kan påverka integriteten för nästan alla användardata.

Lås åtkomst till lokala identitets komponenter som Azure AD Connect, AD FS och SQL-tjänster på samma sätt som du gör för domänkontrollanter.

## <a name="summary"></a>Sammanfattning

Det finns sju aspekter av en säker identitets infrastruktur. I den här listan får du hjälp att hitta de åtgärder som du bör vidta för att optimera åtgärder för Azure Active Directory (Azure AD).

- Tilldela ägare till viktiga uppgifter.
- Automatisera uppgraderings processen för lokala hybrid komponenter.
- Distribuera Azure AD Connect Health för övervakning och rapportering av Azure AD Connect och AD FS.
- Övervaka hälsan för lokala hybrid komponenter genom att arkivera och analysera komponenternas agent loggar med System Center Operations Manager eller en SIEM-lösning.
- Implementera säkerhets förbättringar genom att mäta din säkerhets position med identitets säkra poäng.
- Lås AD FS.
- Lås åtkomst till datorer med lokala identitets komponenter.

## <a name="next-steps"></a>Nästa steg

Se [distributions planerna för Azure AD](active-directory-deployment-plans.md) för implementerings information om funktioner som du inte har distribuerat.