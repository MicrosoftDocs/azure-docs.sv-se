---
title: Problem med att konfigurera användar etablering i en Azure AD-Galleri-app
description: Så här felsöker du vanliga problem med att konfigurera användar etablering till ett program som redan finns med i Azure AD-programgalleriet
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 651bc199a43881add7faa00a9d1cf78000de6944
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256280"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problem med att konfigurera användar etablering i ett Azure AD Gallery-program

Konfigurera [Automatisk användar etablering](user-provisioning.md) för en app (där stöds) kräver att specifika instruktioner följs för att förbereda programmet för automatisk etablering. Sedan kan du använda Azure Portal för att konfigurera etablerings tjänsten för att synkronisera användar konton till programmet.

Du bör alltid starta genom att söka efter installations självstudien som är specifik för att konfigurera etablering för ditt program. Följ sedan dessa steg för att konfigurera både appen och Azure AD för att skapa en etablerings anslutning. En lista med själv studie kurser finns i [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Så här ser du om etableringen fungerar 

När tjänsten har kon figurer ATS kan de flesta insikter om driften av tjänsten hämtas från två platser:

-   **Etablerings loggar (för hands version)** – [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) registrerar alla åtgärder som utförs av etablerings tjänsten, inklusive frågor om Azure AD för tilldelade användare som omfattas av etableringen. Fråga appen mål för att söka efter dessa användare, och jämför användar objekt mellan systemet. Lägg sedan till, uppdatera eller inaktivera användar kontot i mål systemet baserat på jämförelsen. Du kan komma åt etablerings loggarna i Azure Portal genom att välja **Azure Active Directory** &gt; etablerings loggar för **företags appar** &gt; **(för hands version)** i avsnittet **aktivitet** .

-   **Aktuell status –** En sammanfattning av den senaste etablerings körningen för en specifik app visas i avsnittet **Azure Active Directory &gt; företags Apps &gt; \[ program namns \] &gt; etablering** , längst ned på skärmen under tjänst inställningarna. Avsnittet aktuell status visar om en etablerings cykel har börjat att tillhandahålla användar konton. Du kan se förloppet för cykeln, se hur många användare och grupper som har etablerats och se hur många roller som skapas. Om det finns några fel kan du hitta information i [etablerings loggarna (.. /Reports-Monitoring/Concept-Provisioning-logs.MD? context = Azure/Active-Directory/hantera – appar/kontext/hantera-Apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Allmänna problemområden med etablering att överväga

Nedan visas en lista över allmänna problemområden som du kan detaljgranska i om du har en uppfattning om var du ska börja.

* [Etablerings tjänsten verkar inte starta](#provisioning-service-does-not-appear-to-start)
* Det går inte att spara konfigurationen på grund av att autentiseringsuppgifter för appen inte fungerar
* [Etablerings loggar säger att användarna är "överhoppade" och inte etablerade, även om de är tilldelade](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Etablerings tjänsten verkar inte starta

Om du ställer in **etablerings statusen** **på** i avsnittet **Azure Active Directory &gt; företags Apps &gt; \[ program namns \] &gt; etablering** i Azure Portal. Ingen annan statusinformation visas på den sidan efter efterföljande omläsningar. Det är troligt att tjänsten körs men inte har slutfört en första cykel ännu. Kontrol lera **etablerings loggarna** som beskrivs ovan för att avgöra vilka åtgärder tjänsten utför, och om det finns några fel.

>[!NOTE]
>En första cykel kan ta var som helst från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. Efterföljande synkroniseringar efter den första cykeln går snabbare, eftersom etablerings tjänsten lagrar vattenstämplar som representerar båda systemens tillstånd efter den första cykeln, vilket förbättrar prestandan för efterföljande synkroniseringar.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Det går inte att spara konfigurationen på grund av att autentiseringsuppgifter för appen inte fungerar

För att etableringen ska fungera kräver Azure AD giltiga autentiseringsuppgifter som gör det möjligt att ansluta till ett API för användar hantering som tillhandahålls av appen. Om autentiseringsuppgifterna inte fungerar, eller om du inte vet vad de är, kan du läsa självstudien för att konfigurera den här appen, som beskrivs ovan.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Etablerings loggar innebär att användare hoppas över och inte tillhandahålls trots att de har tilldelats

När en användare visas som "överhoppad" i etablerings loggarna är det mycket viktigt att läsa den utökade informationen i logg meddelandet för att fastställa orsaken. Nedan visas vanliga orsaker och lösningar:

- **Ett omfångs filter har kon figurer ATS** **som filtrerar användaren utifrån ett attributvärde**. Mer information finns i [attribut-baserad program etablering med omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- **Användaren är "inte korrekt berättigad".** Om du ser det här fel meddelandet beror det på att det finns ett problem med användar tilldelnings posten som är lagrad i Azure AD. Du kan åtgärda det här problemet genom att ta bort användaren (eller gruppen) från appen och sedan tilldela den igen. Mer information finns i [tilldela en användare eller grupp till en Enterprise-App](../manage-apps/assign-user-or-group-access-portal.md).

- **Ett nödvändigt attribut saknas eller har inte fyllts i för en användare.** Ett viktigt saker att tänka på när du konfigurerar etableringen är att granska och konfigurera de mappningar och arbets flöden som definierar vilka användar egenskaper (eller grupp) som flödar från Azure AD till programmet. Detta innefattar att ange "matchande egenskap" som används för att unikt identifiera och matcha användare/grupper mellan de två systemen. Mer information om den här viktiga processen finns i [Anpassa användar etablering attribut-mappningar](../app-provisioning/customize-application-attributes.md).

  * **Attribut mappningar för grupper:** Etablering av grupp namn och grupp information, förutom medlemmarna, om de stöds för vissa program. Du kan aktivera eller inaktivera den här funktionen genom att aktivera eller inaktivera **mappningen** för grupp objekt som visas på fliken **etablering** . Om etablerings grupper har Aktiver ATS, se till att granska mappningar för attribut för att säkerställa att ett lämpligt fält används för "matchande ID". Detta kan vara visnings namnet eller e-postaliaset, eftersom gruppen och dess medlemmar inte tillhandahålls om matchande egenskap är tom eller inte har fyllts i för en grupp i Azure AD.

## <a name="next-steps"></a>Nästa steg
[Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)
