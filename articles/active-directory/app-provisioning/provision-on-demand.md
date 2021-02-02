---
title: Etablera en användare på begäran genom att använda Azure Active Directory
description: Framtvinga synkronisering
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: be03a149f34c16621905081a2f9bb663d85bc53c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255669"
---
# <a name="on-demand-provisioning"></a>Etablering på begäran
Använd etablering på begäran för att etablera en användare i ett program på några sekunder. Bland annat kan du använda den här funktionen för att:

* Felsöka konfigurations problem snabbt.
* Validera uttryck som du har definierat.
* Test omfångs filter.

## <a name="how-to-use-on-demand-provisioning"></a>Så här använder du etablering på begäran

1. Logga in på **Azure-portalen**.
1. Gå till **alla tjänster**  >  **företags program**.
1. Välj ditt program och gå sedan till etablerings konfigurations sidan.
1. Konfigurera etablering genom att ange autentiseringsuppgifter för din administratör.
1. Välj **etablera på begäran**.
1. Sök efter en användare efter förnamn, efter namn, visnings namn, User Principal Name eller e-postadress.
   > [!NOTE]
   > För Cloud HR Provisioning-appen (Workday/SuccessFactors till AD/Azure AD) är indatavärdet annorlunda. För arbets dag scenariot anger du "WID" för användaren i arbets dagen. För SuccessFactors-scenario anger du "personIdExternal" för användaren i SuccessFactors. 
 
1. Välj **etablera** längst ned på sidan.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Skärm bild som visar Azure Portal användar gränssnitt för att tillhandahålla en användare på begäran.":::

## <a name="understand-the-provisioning-steps"></a>Förstå etablerings stegen

Etablerings processen på begäran försöker visa de steg som etablerings tjänsten vidtar vid etablering av en användare. Det finns vanligt vis fem steg för att etablera en användare. Ett eller flera av dessa steg, som beskrivs i följande avsnitt, visas under etablerings upplevelsen på begäran.

### <a name="step-1-test-connection"></a>Steg 1: Testa anslutning

Etablerings tjänsten försöker auktorisera åtkomst till mål programmet genom att göra en begäran om en "test användare". Etablerings tjänsten förväntar sig ett svar som anger att tjänsten som har behörighet att fortsätta med etablerings stegen. Det här steget visas bara när det Miss lyckas. Den visas inte under etablering på begäran när steget har slutförts.

#### <a name="troubleshooting-tips"></a>Felsökningstips

* Se till att du har angett giltiga autentiseringsuppgifter, till exempel den hemliga token och klient-URL: en, till mål programmet. De autentiseringsuppgifter som krävs varierar beroende på program. Detaljerade konfigurations självstudier finns i [själv studie listan](../saas-apps/tutorial-list.md). 
* Kontrol lera att mål programmet stöder filtrering för de matchande attribut som definieras i fönstret **mappningar för attribut** . Du kan behöva kontrol lera API-dokumentationen från programutvecklaren för att förstå de filter som stöds.
* För system för SCIM-program (Cross-Domain Identity Management) kan du använda ett verktyg som Postman. Sådana verktyg hjälper dig att se till att programmet svarar på autentiseringsbegäranden på det sätt som Azure Active Directory (Azure AD) etablerings tjänsten förväntar sig. Ta en titt på en [exempel förfrågan](./use-scim-to-provision-users-and-groups.md#request-3).

### <a name="step-2-import-user"></a>Steg 2: importera användare

Därefter hämtar etablerings tjänsten användaren från käll systemet. Användarattribut som tjänsten hämtar används senare för att:

* Utvärdera om användaren är omfånget för etablering.
* Kontrol lera mål systemet för en befintlig användare.
* Bestäm vilka användarattribut som ska exporteras till mål systemet.

#### <a name="view-details"></a>Visa detaljer


I avsnittet **Visa information** visas egenskaperna för den användare som har importer ATS från käll systemet (till exempel Azure AD).

#### <a name="troubleshooting-tips"></a>Felsökningstips

* Det går inte att importera användaren när det matchande attributet saknas i användarobjektet i käll systemet. Försök med någon av följande metoder för att lösa det här problemet:

  * Uppdatera objektet användare med ett värde för det matchande attributet.
  * Ändra matchande attribut i etablerings konfigurationen.

* Om ett attribut som du förväntar dig saknas i den importerade listan, kontrollerar du att attributet har ett värde för användarobjektet i käll systemet. Etablerings tjänsten stöder för närvarande inte etablering av null-attribut.
* Kontrol lera att sidan **Mappning av attribut** i etablerings konfigurationen innehåller attributet som du förväntar dig.

### <a name="step-3-determine-if-user-is-in-scope"></a>Steg 3: kontrol lera om användaren är inom omfånget

Sedan fastställer etablerings tjänsten om användaren är i [omfånget](./how-provisioning-works.md#scoping) för etablering. Tjänsten tar hänsyn till aspekter som:

* Om användaren är tilldelad till programmet.
* Huruvida omfattning är inställd på **Synkronisera tilldelad** eller **synkronisera alla**.
* De omfångs filter som definierats i din etablerings konfiguration.  

#### <a name="view-details"></a>Visa detaljer

I avsnittet **Visa information** visas de omfångs villkor som utvärderades. Du kan se en eller flera av följande egenskaper:

* **Aktiv i käll systemet** anger att egenskapen har `IsActive` angetts till **Sant** i Azure AD.
* **Tilldelat till program** anger att användaren är tilldelad till programmet i Azure AD.
* **Omfångs synkronisering alla** anger att inställningen omfång tillåter alla användare och grupper i klienten.
* **Användaren har en obligatorisk roll** indikerar att användaren har de roller som krävs för att kunna tillhandahållas i programmet. 
* **Omfångs filter** visas också om du har definierat omfångs filter för ditt program. Filtret visas med följande format: {scope filter title} {scope filter-attribut} {scope filter operator} {omfångs filter värde}.

#### <a name="troubleshooting-tips"></a>Felsökningstips

* Kontrol lera att du har definierat en giltig omfattnings roll. Undvik till exempel att använda [operatorn Greater_Than](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) med ett värde som inte är heltal.
* Om användaren inte har den nödvändiga rollen granskar du [tipsen för att konfigurera användare som har tilldelats rollen som standard åtkomst roll](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role).

### <a name="step-4-match-user-between-source-and-target"></a>Steg 4: matcha användare mellan källa och mål

I det här steget försöker tjänsten matcha användaren som hämtades i import steget med en användare i mål systemet.

#### <a name="view-details"></a>Visa detaljer

Sidan **Visa information** visar egenskaperna för de användare som matchades i mål systemet. De egenskaper som visas i kontext fönstret varierar på följande sätt:

* Om inga användare matchas i mål systemet ser du inga egenskaper.
* Om en användare matchas i mål systemet visas egenskaperna för den matchade användaren från mål systemet.
* Om flera användare matchas visas egenskaperna för båda matchade användare.
* Om flera matchande attribut är en del av dina mappningar för attribut utvärderas varje matchande attribut sekventiellt och de matchade användarna för attributet visas.

#### <a name="troubleshooting-tips"></a>Felsökningstips

* Etablerings tjänsten kanske inte kan matcha en användare i käll systemet med en unik användare i målet. Lös problemet genom att se till att det matchande attributet är unikt.
* Kontrol lera att mål programmet stöder filtrering av attributet som definieras som matchande attribut.  

### <a name="step-5-perform-action"></a>Steg 5: utför åtgärd

Slutligen vidtar etablerings tjänsten en åtgärd, till exempel att skapa, uppdatera, ta bort eller hoppa över användaren.

Här är ett exempel på vad du kan se när du har slutfört etableringen av en användare på begäran:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Skärm bild som visar att en användare har slutfört etableringen på begäran.":::

#### <a name="view-details"></a>Visa detaljer

I avsnittet **Visa information** visas de attribut som har ändrats i mål programmet. Den här vyn representerar slut resultatet av etablerings tjänstens aktivitet och de attribut som exporterades. Om det här steget Miss lyckas representerar de attribut som visas de attribut som etablerings tjänsten försökte ändra.

#### <a name="troubleshooting-tips"></a>Felsökningstips

* Felen för att exportera ändringar kan variera mycket. Se [dokumentationen för etablering av loggar](../reports-monitoring/concept-provisioning-logs.md#error-codes) för vanliga problem.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Behöver du inaktivera etablering för att använda etablering på begäran?** Inga ytterligare åtgärder krävs för program som använder sig av en token med lång livs längd eller användar namn och lösen ord för auktorisering. För program som använder OAuth för auktorisering krävs det att etablerings jobbet stoppas innan du använder etablering på begäran. Program som G Suite, Box, arbets yta efter Facebook och slack omfattas av den här kategorin. Arbetet pågår för att stödja etablering på begäran för alla program utan att behöva stoppa etablerings jobben.

* **Hur lång tid tar etableringen på begäran?** Etablering på begäran tar vanligt vis mindre än 30 sekunder.

## <a name="known-limitations"></a>Kända begränsningar

Det finns för närvarande några kända begränsningar för etablering på begäran. Publicera dina [förslag och din feedback](https://aka.ms/appprovisioningfeaturerequest) så att vi bättre kan avgöra vilka förbättringar som ska utföras härnäst.

> [!NOTE]
> Följande begränsningar gäller för etablerings funktionen på begäran. Om du vill ha mer information om hur ett program stöder etablering av grupper, borttagningar eller andra funktioner, se själv studie kursen för programmet.

* Amazon Web Services-programmet (AWS) stöder inte etablering på begäran. 
* Etablering på begäran av grupper och roller stöds inte.
* Etablering på begäran stöder inaktive ring av användare som har kopplats från programmet. Den har dock inte stöd för att inaktivera eller ta bort användare som har inaktiverats eller tagits bort från Azure AD. Dessa användare visas inte när du söker efter en användare.

## <a name="next-steps"></a>Nästa steg

* [Felsöka etablering](./application-provisioning-config-problem.md)