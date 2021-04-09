---
title: Arbets bok för villkorlig åtkomst och rapportering – Azure Active Directory
description: Använda Azure ADs arbets bok för villkorlig åtkomst och rapportering för att felsöka principer
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae802038626a1fbf8d533800a0b8eb43c4565e8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574173"
---
# <a name="conditional-access-insights-and-reporting"></a>Villkorlig åtkomst insikter och rapportering

Med arbets boken för villkorlig åtkomst i insikter och rapporter kan du förstå hur principer för villkorlig åtkomst fungerar i organisationen över tid. Under inloggningen kan en eller flera principer för villkorlig åtkomst tillämpas, bevilja åtkomst om vissa beviljande kontroller är uppfyllda eller nekar åtkomst i övrigt. Eftersom flera principer för villkorlig åtkomst kan utvärderas under varje inloggning kan du använda arbets boken insikter och rapportering för att undersöka effekten av en enskild princip eller en delmängd av alla principer.  

## <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera arbets boken insikter och rapportering måste klienten ha en Log Analytics arbets yta för att behålla inloggnings loggar data. Användare måste ha Azure AD Premium P1-eller P2-licenser för att kunna använda villkorlig åtkomst.

Följande roller kan komma åt insikter och rapportering:  

- Administratör för villkorlig åtkomst 
- Säkerhetsläsare 
- Säkerhetsadministratör 
- Global läsare 
- Global administratör 

Användare behöver också en av följande Log Analytics arbets ytans roller:  

- Deltagare  
- Ägare 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Strömma inloggnings loggar från Azure AD till Azure Monitor loggar 

Om du inte har integrerat Azure AD-loggar med Azure Monitor loggar måste du utföra följande steg innan arbets boken kommer att läsas in:  

1. [Skapa en Log Analytics arbets yta i Azure Monitor](../../azure-monitor/logs/quick-create-workspace.md).
1. [Integrera Azure AD-loggar med Azure Monitor loggar](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Så här fungerar det 

För att komma åt arbets boken insikter och rapportering:  

1. Logga in på **Azure-portalen**.
1. Bläddra till **Azure Active Directory**  >  **säkerhet** för  >  **villkorlig åtkomst**  >  **och rapportering**.

### <a name="get-started-select-parameters"></a>Kom igång: Välj parametrar 

På instrument panelen insikter och rapporter kan du se effekten av en eller flera principer för villkorlig åtkomst under en angiven period. Börja med att ställa in var och en av parametrarna överst i arbets boken. 

![Instrument panelen för villkorlig åtkomst insikter och rapportering i Azure Portal](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Princip för villkorlig åtkomst**: Välj en eller flera principer för villkorlig åtkomst för att visa deras kombinerade påverkan. Principer är indelade i två grupper: aktiverade och endast för rapport-principer. Som standard är alla aktiverade principer markerade. De här aktiverade principerna är de principer som för närvarande används i din klient organisation.  

**Tidsintervall**: Välj ett tidsintervall från 4 timmar till så långt tillbaka som 90 dagar. Om du väljer ett tidsintervall ytterligare tillbaka än när du integrerar Azure AD-loggarna med Azure Monitor, visas bara inloggningar efter tidpunkten för integreringen.  

**Användare**: instrument panelen visar som standard effekten av de valda principerna för alla användare. Om du vill filtrera efter en enskild användare skriver du namnet på användaren i textfältet. Om du vill filtrera efter alla användare skriver du "alla användare" i textfältet eller låter parametern vara tom. 

**App**: som standard visar instrument panelen effekten av de valda principerna för alla appar. Om du vill filtrera efter en enskild app skriver du namnet på appen i textfältet. Om du vill filtrera efter alla appar skriver du "alla appar" i textfältet eller låter parametern vara tom. 

**Datavy**: Välj om du vill att instrument panelen ska visa resultat avseende antalet användare eller antal inloggningar. En enskild användare kan ha hundratals inloggnings program till många appar med många olika resultat under ett visst tidsintervall. Om du väljer datavyn som användare, kan en användare inkluderas i både lyckade och misslyckade räkningar (till exempel om det finns 10 användare kan 8 av dem ha haft ett resultat av framgång under de senaste 30 dagarna och 9 av dem kunde ha haft ett resultat av ett haveri under de senaste 30 dagarna).

## <a name="impact-summary"></a>Effekt Sammanfattning 

När du har angett parametrarna läses effekt sammanfattningen in. Sammanfattningen visar hur många användare eller inloggningar under tidsintervallet som resulterade i "lyckad", "Misslyckad", "användar åtgärd krävs" eller "används inte" när de valda principerna utvärderades.  

![Effekt Sammanfattning i arbets boken för villkorlig åtkomst](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Totalt**: antalet användare eller inloggningar under den tids period då minst en av de valda principerna utvärderades.

**Lyckades**: antalet användare eller inloggningar under den tids period då det kombinerade resultatet av de valda principerna var "lyckades" eller "endast rapporter: lyckades".

**Misslyckades**: antalet användare eller inloggningar under den tids period då resultatet av minst en av de valda principerna var "misslyckades" eller "endast rapporter: misslyckades".

**Användar åtgärd krävs**: antalet användare eller inloggningar under den tids period då det kombinerade resultatet av de valda principerna var "endast rapporter: användar åtgärd krävs". Användar åtgärd krävs när en interaktiv beviljande kontroll, till exempel Multi-Factor Authentication, krävs av en princip för villkorlig åtkomst för endast rapporter. Eftersom interaktiva beviljade kontroller inte tillämpas av endast rapport principer, går det inte att fastställa lyckade eller misslyckade åtgärder.  

**Används inte**: antalet användare eller inloggningar under den tids period då ingen av de valda principerna tillämpades.

### <a name="understanding-the-impact"></a>Förstå effekten 

![Arbets boks uppdelning per villkor och status](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Visa nedbrytningen av användare eller inloggningar för varje villkor. Du kan filtrera inloggnings tilläggen för ett visst resultat (till exempel lyckat eller misslyckat) genom att välja på sammanfattnings panelerna överst i arbets boken. Du kan se fördelningen av inloggningar för var och en av de villkorliga åtkomst villkoren: enhets tillstånd, enhets plattform, klient program, plats, program och inloggnings risk.  

## <a name="sign-in-details"></a>Inloggningsinformation 

![Inloggnings information för arbets bok](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

Du kan också undersöka inloggnings tilläggen för en speciell användare genom att söka efter inloggningar längst ned i instrument panelen. Frågan till vänster visar de vanligaste användarna. Om du väljer en användare filtreras frågan till höger.  

> [!NOTE]
> När du laddar ned inloggnings loggar väljer du JSON-format för att inkludera endast resultat data för rapporten villkorlig åtkomst.

## <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Konfigurera en princip för villkorlig åtkomst i endast rapport läge

Så här konfigurerar du en princip för villkorlig åtkomst i endast rapport läge:

1. Logga in på **Azure Portal** som administratör för villkorlig åtkomst, säkerhets administratör eller global administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj en befintlig princip eller skapa en ny princip.
1. Under **Aktivera princip** ställer du in växla till **endast rapport** läge.
1. Välj **Spara**

> [!TIP]
> Om du redigerar **aktiverings princip** läget för en befintlig princip från **på** till **endast rapporter** inaktive ras befintlig princip tillämpning. 

## <a name="troubleshooting"></a>Felsökning

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>Varför fungerar inte frågor på grund av ett behörighets fel?

För att du ska kunna komma åt arbets boken behöver du rätt Azure AD-behörigheter samt Log Analytics arbets ytans behörigheter. Så här testar du om du har rätt behörighet för arbets ytan genom att köra en exempel logg analys fråga:

1. Logga in på **Azure-portalen**.
1. Bläddra till **Azure Active Directory**  >  **loggar**.
1. Skriv `SigninLogs` i rutan fråga och välj **Kör**.
1. Om frågan inte returnerar några resultat kanske arbets ytan inte har kon figurer ATS korrekt. 

![Felsöka misslyckade frågor](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Mer information om hur du strömmar inloggnings loggar i Azure AD till en Log Analytics arbets yta finns i artikeln [integrera Azure AD-loggar med Azure Monitor loggar](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Varför fungerar inte frågorna i arbets boken?

Kunder har lagt märke till att frågor ibland inte fungerar om fel eller flera arbets ytor är associerade med arbets boken. Åtgärda problemet genom att klicka på **Redigera** överst i arbets boken och sedan på inställnings växeln. Välj och ta sedan bort arbets ytor som inte är associerade med arbets boken. Det får bara finnas en arbets yta som är kopplad till varje arbets bok.

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>Varför är parametern för villkorlig åtkomst tom?

Listan med principer genereras genom att granska principerna som utvärderas för den senaste inloggnings händelsen. Om det inte finns några nya inloggningar i din klient organisation kan du behöva vänta några minuter på att arbets boken ska läsa in listan över principer för villkorlig åtkomst. Detta kan inträffa direkt efter att du har konfigurerat Log Analytics eller kan ta längre tid om en klient organisation inte har senaste inloggnings aktivitet.

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Varför tar det lång tid att läsa in arbets boken?  

Beroende på vilket tidsintervall som valts och storleken på din klient, kan arbets boken utvärdera ett ovanligt stort antal inloggnings händelser. För stora klienter kan det hända att volymen av inloggningar överskrider Log Analyticss kapacitet. Försök att förkorta tidsintervallet till 4 timmar för att se om arbets boken läses in.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>När du har läst in några minuter, varför returnerar arbets boken noll resultat? 

Om volymerna för inloggningar överskrider fråge kapaciteten för Log Analytics kommer arbets boken returnera noll resultat. Försök att förkorta tidsintervallet till 4 timmar för att se om arbets boken läses in.  

### <a name="can-i-save-my-parameter-selections"></a>Kan jag spara mina parameter val?  

Du kan spara dina parameter val längst upp i arbets boken genom att gå till **Azure Active Directory**  >  **arbets böcker**  >  **villkorlig åtkomst insikter och rapportering**. Här hittar du arbets boks mal len där du kan redigera arbets boken och spara en kopia på din arbets yta, inklusive parameter valen, i **Mina rapporter** eller **delade rapporter**. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Kan jag redigera och anpassa arbets boken med fler frågor? 

Du kan redigera och anpassa arbets boken genom att gå till **Azure Active Directory**  >  **arbets böcker**  >  **villkorlig åtkomst insikter och rapportering**. Här hittar du arbets boks mal len där du kan redigera arbets boken och spara en kopia på din arbets yta, inklusive parameter valen, i **Mina rapporter** eller **delade rapporter**. Om du vill börja redigera frågorna klickar du på **Redigera** överst i arbets boken.  
 
## <a name="next-steps"></a>Nästa steg

- [Endast rapport läge för villkorlig åtkomst](concept-conditional-access-report-only.md)

- Mer information om Azure AD-arbetsböcker finns i artikeln [så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
