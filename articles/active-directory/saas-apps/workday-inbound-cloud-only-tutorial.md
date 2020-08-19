---
title: 'Självstudie: Konfigurera inkommande etablering för Workday i Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar inkommande etablering från arbets dagar till Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 69c3246c910a83d889151d6ad749e1be86340e8c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540974"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Självstudie: Konfigurera arbets dag till användar etablering i Azure AD
Syftet med den här självstudien är att visa de steg som du måste utföra för att etablera arbetarnas data från arbets dagar till Azure Active Directory. 

>[!NOTE]
>Använd den här självstudien om de användare som du vill etablera från Workday är molnbaserade användare som inte behöver ett lokalt AD-konto. Om användarna bara behöver ett lokalt AD-konto eller både AD-och Azure AD-konto, kan du läsa själv studie kursen om hur [du konfigurerar arbets dagar för att Active Directory](workday-inbound-tutorial.md) användar etablering. 

## <a name="overview"></a>Översikt

[Azure Active Directory användar etablerings tjänsten](../app-provisioning/user-provisioning.md) integreras med [personalavdelningen-API: t för arbets dagar](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) för att etablera användar konton. Arbets flöden för användar etablering av arbets dagar som stöds av Azure AD-tjänsten för användar etablering möjliggör automatisering av följande personal-och identitets cykel hanterings scenarier:

* **Anställning av nya anställda** – när en ny medarbetare läggs till i arbets dagen skapas ett användar konto automatiskt i Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md), med Skriv-tillbaka till Workday-e-postadressen.

* **Uppdateringar av anställda och profiler** – när en medarbetar post uppdateras i Workday (till exempel namn, titel eller chef), uppdateras användar kontot automatiskt Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

* **Anställdas uppsägningar** – när en medarbetare avslutas i Workday inaktive ras användar kontot automatiskt i Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

* **Anställdas återställningar** – när en medarbetare återställs i Workday kan deras gamla konto automatiskt återaktiveras eller etableras på nytt (beroende på din preferens) för att Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här användar etablerings lösningen som passar bäst för?

Den här arbets dagen för att Azure Active Directory användar etablerings lösningen passar utmärkt för:

* Organisationer som vill ha en förbyggd, molnbaserad lösning för användar etablering i Workday

* Organisationer som kräver direkt användar etablering från Workday till Azure Active Directory

* Organisationer som kräver att användare tillhandahålls med data som hämtas från Workday

* Organisationer som använder Office 365 för e-post

## <a name="solution-architecture"></a>Lösningsarkitektur

I det här avsnittet beskrivs slut punkt till slut punkt för användar etablerings lösnings arkitekturen för endast molnbaserade användare. Det finns två relaterade flöden:

* **Auktoritativt Tim data flöde – från arbets dag till Azure Active Directory:** I de här flödes händelserna (t. ex. nya anställningar, överföringar, uppsägningar) sker först i Workday och händelse data flödar sedan till Azure Active Directory. Beroende på händelsen kan det leda till att du skapar/uppdaterar/aktiverar/inaktiverar åtgärder i Azure AD.
* **Tillbakaskrivning flöde – från lokala Active Directory till arbets dag:** När kontot har skapats i Active Directory synkroniseras det med Azure AD via Azure AD Connect och information som e-post, användar namn och telefonnummer kan skrivas tillbaka till arbets dagen.

  ![Översikt](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Slut punkt till slut punkt för användar data flöde

1. HR-teamet utför arbets transaktioner (anslutningar/flytter/Lämnare eller nya anställningar/överföringar/uppsägningar) i arbets dagen personal Central
2. Azure AD Provisioning-tjänsten kör schemalagda synkroniseringar av identiteter från Workday EG och identifierar ändringar som behöver bearbetas för synkronisering med lokala Active Directory.
3. Azure AD Provisioning-tjänsten fastställer ändringen och anropar åtgärden Skapa/uppdatera/aktivera/inaktivera för användaren i Azure AD.
4. Om tillbakaskrivning-appen för [Workday](workday-writeback-tutorial.md) har kon figurer ATS hämtar den attribut som e-post, användar namn och telefonnummer från Azure AD. 
5. Azure AD Provisioning-tjänsten anger e-post, användar namn och telefonnummer i arbets dagen.

## <a name="planning-your-deployment"></a>Planera distributionen

Konfigurering av moln HR-drivna användar etablering från arbets dagar till Azure AD kräver avsevärd planering som omfattar olika aspekter, till exempel:

* Identifiera matchnings-ID 
* Attributmappning
* Attribute-transformering 
* Omfångsfilter

Mer information om de här ämnena finns i [Cloud HR Deployment plan](../app-provisioning/plan-cloud-hr-provision.md) . 

## <a name="configure-integration-system-user-in-workday"></a>Konfigurera användare av integrations systemet på arbets dagen

Se avsnittet [Konfigurera integrations system användare](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) för att skapa ett användar konto för en Workday-integrerings system med behörighet att hämta arbets data. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Konfigurera användar etablering från Workday till Azure AD

I följande avsnitt beskrivs hur du konfigurerar användar etablering från Workday till Azure AD för distributioner i molnet.

* [Lägga till Azure AD Provisioning Connector-appen och skapa anslutningen till arbets dagen](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurera mappar för Workday och Azure AD-attribut](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Aktivera och starta användar etablering](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 1: lägga till Azure AD Provisioning Connector-appen och skapa anslutningen till arbets dagen

**Konfigurera arbets dagar för att Azure Active Directory etablering för endast moln användare:**

1. Gå till <https://portal.azure.com>.

2. I Azure Portal söker du efter och väljer **Azure Active Directory**.

3. Välj **företags program**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj sedan kategorin **alla** .

5. Sök efter **arbets dag till Azure AD-användar etablering**och Lägg till den appen från galleriet.

6. När appen har lagts till och skärmen information om appen visas väljer du **etablering**.

7. Ändra **etablerings** **läget** till **automatiskt**.

8. Slutför avsnittet **admin credentials** enligt följande:

   * **Workday-användarnamn** – Ange användar namnet för kontot för arbets dag integrerings systemet, med namnet på klient organisationen som lagts till. Bör se ut ungefär så här: username@contoso4

   * **Lösen ord för arbets dag –** Ange lösen ordet för system kontot för Workday-integrering

   * **URL för Workday webb tjänster-API –** Ange URL: en till slut punkten för webb tjänster för arbets dag för din klient. URL: en avgör vilken version av webb tjänstens API för Workday som används av anslutningen. 
   
     | URL-format | WWS-API-version som används | XPATH-ändringar krävs |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v-21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v-21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Yes |

      > [!NOTE]
     > Om ingen versions information anges i URL: en använder appen Workday-WWS (Web Services) och inga ändringar krävs för standard-XPATH API-uttryck som levereras med appen. Om du vill använda en viss WWS API-version anger du versions nummer i URL: en <br>
     > Exempel: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Om du använder en WWS API v-30.0 +, innan du aktiverar etablerings jobbet, uppdaterar du **XPath API-uttryck** under **attribut mappning-> avancerade alternativ-> redigera attributlistan för arbets dagar** som refererar till avsnittet [Hantera din konfiguration och ditt](workday-inbound-tutorial.md#managing-your-configuration) Workday- [attribut referens](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **E-postavisering –** Ange din e-postadress och markera kryss rutan "skicka e-post om fel inträffar".

   * Klicka på knappen **Testa anslutning** .

   * Om anslutnings testet lyckas, klickar du på knappen **Spara** längst upp. Om det Miss lyckas, kontrol lera att URL: en för arbets dagar och autentiseringsuppgifter är giltiga i arbets dagen.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Del 2: Konfigurera mappningar för Workday och Azure AD-attribut

I det här avsnittet ska du konfigurera hur användar data flödar från arbets dagar till Azure Active Directory endast för molnbaserade användare.

1. På fliken etablering under **mappningar**klickar du på **Synkronisera arbetare till Azure AD**.

2. I fältet **käll objekt omfånget** kan du välja vilka uppsättningar av användare i arbets dagar som ska ingå i omfånget för etablering till Azure AD, genom att definiera en uppsättning attributbaserade filter. Standard omfånget är "alla användare i Workday". Exempel filter:

   * Exempel: omfång till användare med Worker-ID: n mellan 1000000 och 2000000

      * Attribut: WorkerID

      * Operator: REGEX-matchning

      * Värde: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Exempel: endast eventualtillgångar och inte vanliga anställda

      * Attribut: ContingentID

      * Operator: är inte NULL

3. I fältet **mål objekts åtgärder** kan du globalt filtrera vilka åtgärder som utförs i Azure AD. **Skapa**  och **Uppdatera** är de vanligaste.

4. I avsnittet **mappningar för attribut** kan du definiera hur enskilda Workday-attribut mappar ska Active Directory attribut.

5. Klicka på en befintlig attributmappning för att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En mappning för enskilda attribut stöder följande egenskaper:

   * **Mappnings typ**

      * **Direct** – skriver värdet för attributet Workday till attributet AD, utan ändringar

      * **Konstant** – Skriv ett statiskt, konstant sträng värde till attributet AD

      * **Uttryck** – gör att du kan skriva ett anpassat värde till attributet AD, baserat på ett eller flera Workday-attribut. [Mer information finns i den här artikeln om uttryck](../app-provisioning/functions-for-customizing-application-data.md).

   * **Källattribut** – attributet användare från Workday. Om det attribut som du letar efter inte finns kan du läsa mer i [Anpassa listan med användar](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)-och Workday-användarattribut.

   * **Standardvärde** – valfritt. Om källattributet har ett tomt värde, skrivs värdet i stället av mappningen.
            Den vanligaste konfigurationen är att lämna detta tomt.

   * **Target** -attribut – användarattribut i Azure AD.

   * **Matcha objekt med det här attributet** – om det här attributet ska användas för att unikt identifiera användare mellan Workday och Azure AD. Det här värdet anges vanligt vis i fältet Worker-ID för arbets dag, som vanligt vis mappas till ID-attributet för personal (ny) eller ett tillägg i Azure AD.

   * **Matchnings prioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som definieras av det här fältet. Så fort en matchning hittas utvärderas inga ytterligare matchande attribut.

   * **Använd den här mappningen**

     * **Alltid** – Använd den här mappningen för både skapande av användare och uppdaterings åtgärder

     * **Endast vid skapande** – Använd endast den här mappningen för åtgärder för att skapa användare

6. Spara dina mappningar genom att klicka på **Spara** överst i avsnittet attribut-mappning.


## <a name="enable-and-launch-user-provisioning"></a>Aktivera och starta användar etablering

När du har slutfört konfigurationen av appar för arbets dag etablering kan du aktivera etablerings tjänsten i Azure Portal.

> [!TIP]
> Som standard när du aktiverar etablerings tjänsten kommer den att initiera etablerings åtgärder för alla användare i omfånget. Om det uppstår fel i mappnings-eller data frågor för data lagret kan etablerings jobbet Miss Miss kan och gå in i karantäns läget. För att undvika detta rekommenderar vi att du konfigurerar **käll objekt omfångs** filter och testar dina mappningar av attribut med några test användare innan du startar den fullständiga synkroniseringen för alla användare. När du har kontrollerat att mappningarna fungerar och ger dig önskade resultat kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

1. På fliken **etablering** ställer du in **etablerings status** på **på**.

2. Klicka på **Spara**.

3. Den här åtgärden startar den inledande synkroniseringen, vilket kan ta ett variabelt antal timmar beroende på hur många användare som finns i arbets belastnings klienten. Du kan kontrol lera förlopps indikatorn för att följa synkroniseringens förlopp. 

4. Gå till fliken **gransknings loggar** i Azure Portal för att se vilka åtgärder som etablerings tjänsten har utfört. I gransknings loggarna visas alla enskilda synkroniseringsfel som utförs av etablerings tjänsten, t. ex. vilka användare som ska läsas ut från Workday och därefter läggs till eller uppdateras till Azure Active Directory. 

5. När den inledande synkroniseringen har slutförts skrivs en gransknings sammanfattnings rapport på fliken **etablering** , som visas nedan.

   > [!div class="mx-imgBorder"]
   > ![Förlopps indikator för etablering](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Nästa steg

* [Läs mer om de Workday-attribut som stöds för inkommande etablering](../app-provisioning/workday-attribute-reference.md)
* [Lär dig hur du konfigurerar tillbakaskrivning av arbets dagar](workday-writeback-tutorial.md)
* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan arbets dagar och Azure Active Directory](workday-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du exporterar och importerar dina etablerings konfigurationer](../app-provisioning/export-import-provisioning-configuration.md)


