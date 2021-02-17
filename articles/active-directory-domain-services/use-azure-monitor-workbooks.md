---
title: Använd Azure Monitor arbets böcker med Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du använder Azure Monitor-arbetsböcker för att granska säkerhets granskningar och förstå problem i en Azure Active Directory Domain Services hanterad domän.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 9a126fe4676f340fb45677382a3aeeed79291f0b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574207"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Granska säkerhets gransknings händelser i Azure Active Directory Domain Services att använda Azure Monitor arbets böcker

För att hjälpa dig att förstå statusen för din Azure Active Directory Domain Services (Azure AD DS)-hanterade domän, kan du aktivera säkerhets gransknings händelser. Dessa säkerhets gransknings händelser kan sedan granskas med Azure Monitor arbets böcker som kombinerar text, analys frågor och parametrar till interaktiva interaktiva rapporter. Azure AD DS innehåller mallar för säkerhet – översikt och konto aktivitet som gör att du kan använda gransknings händelser och hantera din miljö.

Den här artikeln visar hur du använder Azure Monitor-arbetsböcker för att granska säkerhets gransknings händelser i Azure AD DS.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* Säkerhets gransknings händelser som är aktiverade för din hanterade domän och strömma data till en Log Analytics-arbetsyta.
    * Om det behövs [aktiverar du säkerhets granskningar för Azure AD DS][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Översikt över Azure Monitor arbets böcker

När säkerhets gransknings händelser aktive ras i Azure AD DS kan det vara svårt att analysera och identifiera problem i den hanterade domänen. Med Azure Monitor kan du samla dessa säkerhets gransknings händelser och fråga data. Med Azure Monitor arbets böcker kan du visualisera dessa data så att det går snabbare och enklare att identifiera problem.

Mallar för arbets böcker är granskade rapporter som har utformats för flexibel åter användning av flera användare och team. När du öppnar en arbetsboksmall-mall läses data från din Azure Monitors miljö in. Du kan använda mallar utan påverkan på andra användare i din organisation och du kan spara dina egna arbets böcker baserat på mallen.

Azure AD DS innehåller följande två mallar för arbets böcker:

* Säkerhets översikts rapport
* Rapporten konto aktivitet

Mer information om hur du redigerar och hanterar arbets böcker finns i [Översikt över Azure Monitor-arbetsböcker](../azure-monitor/visualize/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Använda arbets boken säkerhets översikts rapport

För att hjälpa dig att bättre förstå användningen och identifiera potentiella säkerhetshot, sammanfattar rapporten säkerhets översikt inloggnings data och identifierar de konton som du kanske vill kontrol lera. Du kan visa händelser inom ett visst datum intervall och öka detalj nivån för specifika inloggnings händelser, till exempel dåliga lösen ords försök eller var kontot inaktiverades.

Slutför följande steg för att få åtkomst till arbets boks mal len för säkerhets översikts rapporten:

1. Sök efter och välj **Azure Active Directory Domain Services** i Azure Portal.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*
1. På menyn till vänster väljer du **övervakning > arbets böcker**

    ![Skärm bild som hightlights där du kan välja rapporten säkerhets översikt och rapporten konto aktivitet.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Välj **rapporten säkerhets översikt**.
1. Välj din Azure-prenumeration på den nedrullningsbara menyn längst upp i arbets boken och sedan en Azure Monitor arbets yta.

    Välj ett **tidsintervall**, till exempel de *senaste 7 dagarna*, som du ser i följande exempel skärm bild:

    ![Välj meny alternativet arbets böcker i Azure Portal](./media/use-azure-monitor-workbooks/select-query-filters.png)

    Du kan också **ändra visnings alternativ och** **diagram visnings** alternativ för att analysera och visualisera data som du vill.

1. Om du vill öka detalj nivån till en speciell händelse typ väljer du en av **inloggnings resultat** korten, till exempel *kontot låst*, som du ser i följande exempel:

    ![Exempel på säkerhets översikts rapport data som visualiseras i Azure Monitor arbets böcker](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. Den nedre delen av rapporten säkerhets översikt nedanför diagrammet delar sedan upp aktivitets typen som valts. Du kan filtrera efter användar namn som ingår i den högra sidan, som du ser i följande exempel rapport:

    [![Information om konto utelåsning i Azure Monitor arbets böcker.](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Använd arbets boken konto aktivitets rapport

För att hjälpa dig att felsöka problem för ett angivet användar konto delar rapporten konto aktivitet ned detaljerad information om gransknings händelse loggen. Du kan granska när ett felaktigt användar namn eller lösen ord angavs under inloggningen och källan till inloggnings försöket.

Utför följande steg för att få åtkomst till arbets boks mal len för konto aktivitets rapporten:

1. Sök efter och välj **Azure Active Directory Domain Services** i Azure Portal.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*
1. På menyn till vänster väljer du **övervakning > arbets böcker**
1. Välj **rapporten konto aktivitet**.
1. Välj din Azure-prenumeration på den nedrullningsbara menyn längst upp i arbets boken och sedan en Azure Monitor arbets yta.

    Välj ett **tidsintervall**, till exempel de *senaste 30 dagarna*, och sedan hur du vill att **visnings** området ska representera data.

    Du kan filtrera efter **konto användar namn**, till exempel *Felix*, som du ser i följande exempel rapport:

    [![Rapporten konto aktivitet i Azure Monitor arbets böcker.](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    I ytan nedanför diagrammet visas enskilda inloggnings händelser tillsammans med information som aktivitets resultat och käll arbets Station. Den här informationen kan hjälpa till att avgöra upprepade källor till inloggnings händelser som kan orsaka konto utelåsning eller Visa en potentiell attack.

Som i rapporten säkerhets översikt kan du öka detalj nivån i de olika panelerna överst i rapporten för att visualisera och analysera data efter behov.

## <a name="save-and-edit-workbooks"></a>Spara och redigera arbets böcker

De två mallarna för arbets böcker som tillhandahålls av Azure AD DS är en bra plats för att börja med din egen data analys. Om du behöver få mer detaljerad information i data frågorna och utredningarna kan du spara dina egna arbets böcker och redigera frågorna.

1. Om du vill spara en kopia av en arbetsboksmall väljer du **redigera > Spara som > delade rapporter** och anger sedan ett namn och sparar den.
1. Från din egen kopia av mallen väljer du **Redigera** för att ange redigerings läget. Du kan välja den blå **redigerings** knappen bredvid vilken del som helst av rapporten och ändra den.

Alla diagram och tabeller i Azure Monitor arbets böcker genereras med hjälp av Kusto-frågor. Mer information om hur du skapar egna frågor finns i självstudier för [Azure Monitor logg frågor][azure-monitor-queries] och [Kusto frågor][kusto-queries].

## <a name="next-steps"></a>Nästa steg

Om du behöver justera lösen ord och utelåsnings principer, se [principer för lösen ord och konto utelåsning på hanterade domäner][password-policy].

Om du har problem med användare kan du läsa om hur du felsöker [problem med inloggnings problem][troubleshoot-sign-in] eller [problem med konto utelåsning][troubleshoot-account-lockout].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: /azure/data-explorer/kusto/query/
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer