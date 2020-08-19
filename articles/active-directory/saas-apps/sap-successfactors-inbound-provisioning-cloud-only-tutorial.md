---
title: 'Självstudie: Konfigurera SuccessFactors inkommande etablering i Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar inkommande etablering från SuccessFactors till Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: 9c94d2ab2e0c49fe38142e42cf934c440746da75
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546686"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>Självstudie: konfigurera SAP-SuccessFactors till användar etablering i Azure AD
Syftet med den här självstudien är att visa de steg som du behöver utföra för att etablera arbetarnas data från SuccessFactors personal Central till Azure Active Directory, med valfri Skriv åtgärd för e-postadressen till SuccessFactors. 

>[!NOTE]
>Använd den här självstudien om de användare som du vill etablera från SuccessFactors är endast molnbaserade användare som inte behöver ett lokalt AD-konto. Om användarna bara behöver ett lokalt AD-konto eller både AD-och Azure AD-konto kan du läsa själv studie kursen om hur [du konfigurerar SAP-SuccessFactors för att Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview) användar etablering. 

## <a name="overview"></a>Översikt

[Azure Active Directory användar etablerings tjänsten](../app-provisioning/user-provisioning.md) integreras med [SuccessFactors personal Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) för att hantera användarens identitets livs cykel. 

Användar etablerings arbets flöden för SuccessFactors som stöds av Azure AD-tjänsten för användar etablering möjliggör automatisering av följande personal-och identitets cykel hanterings scenarier:

* **Anställning av nya anställda** – när en ny medarbetare läggs till i SuccessFactors skapas ett användar konto automatiskt i Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md), med Skriv-tillbaka till SuccessFactors-adressen.

* **Uppdateringar av anställda och profiler** – när en medarbetar post uppdateras i SuccessFactors (t. ex. namn, titel eller chef) uppdateras användar kontot automatiskt Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

* **Anställdas uppsägningar** – när en medarbetare avslutas i SuccessFactors inaktive ras användar kontot automatiskt i Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

* **Anställdas återställningar** – när en medarbetare återställs i SuccessFactors kan deras gamla konto automatiskt återaktiveras eller etableras på nytt (beroende på din preferens) för att Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här användar etablerings lösningen som passar bäst för?

Den här SuccessFactors för att Azure Active Directory användar etablerings lösningen passar utmärkt för:

* Organisationer som vill ha en förbyggd, molnbaserad lösning för SuccessFactors användar etablering

* Organisationer som kräver direkt användar etablering från SuccessFactors till Azure Active Directory

* Organisationer som kräver att användare tillhandahålls med hjälp av data från [SuccessFactors Employee Central (EG)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisationer som använder Office 365 för e-post

## <a name="solution-architecture"></a>Lösnings arkitektur

I det här avsnittet beskrivs slut punkt till slut punkt för användar etablerings lösnings arkitekturen för endast molnbaserade användare. Det finns två relaterade flöden:

* **Auktoritativt Tim data flöde – från SuccessFactors till Azure Active Directory:** I dessa flödes händelser (till exempel nya anställningar, överföringar, uppsägningar) sker först i Cloud SuccessFactors personal Central och därefter flödar händelse data till Azure Active Directory. Beroende på händelsen kan det leda till att du skapar/uppdaterar/aktiverar/inaktiverar åtgärder i Azure AD.
* **Flöde för tillbakaskrivning av e-post – från lokala Active Directory till SuccessFactors:** När kontot har skapats i Azure Active Directory kan värdet för e-postattributet eller UPN som genereras i Azure AD skrivas tillbaka till SuccessFactors.

  ![Översikt](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Slut punkt till slut punkt för användar data flöde

1. HR-teamet utför arbets transaktioner (anslutare/flytter/Lämnare eller nya anställningar/överföringar/uppsägningar) i SuccessFactors personal Central
2. Azure AD Provisioning-tjänsten kör schemalagda synkroniseringar av identiteter från SuccessFactors EC och identifierar ändringar som behöver bearbetas för synkronisering med lokala Active Directory.
3. Azure AD Provisioning-tjänsten fastställer ändringen och anropar åtgärden Skapa/uppdatera/aktivera/inaktivera för användaren i Azure AD.
4. Om [SuccessFactors tillbakaskrivning-appen](sap-successfactors-writeback-tutorial.md) har kon figurer ATS hämtas användarens e-postadress från Azure AD. 
5. Azure AD Provisioning-tjänsten skriver tillbaka e-postattribut till SuccessFactors, baserat på det matchande attributet som används.

## <a name="planning-your-deployment"></a>Planera distributionen

Konfigurering av moln HR-driven användar etablering från SuccessFactors till Azure AD kräver avsevärd planering som omfattar olika aspekter, till exempel:

* Identifiera matchnings-ID 
* Attributmappning
* Attribute-transformering 
* Omfångsfilter

Mer information om de här ämnena finns i [Cloud HR Deployment plan](../app-provisioning/plan-cloud-hr-provision.md) . Se [SAP SuccessFactors integration Reference](../app-provisioning/sap-successfactors-integration-reference.md) för att lära dig mer om de entiteter som stöds, bearbetnings information och hur du anpassar integreringen för olika HR-scenarier. 

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurera SuccessFactors för integrering

Ett gemensamt krav för alla SuccessFactors etablerings anslutningar är att de kräver autentiseringsuppgifter för ett SuccessFactors-konto med rätt behörighet för att anropa SuccessFactors OData-API: erna. I det här avsnittet beskrivs steg för att skapa tjänst kontot i SuccessFactors och bevilja lämpliga behörigheter. 

* [Skapa/identifiera API-användarkonto i SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Skapa en roll för API-behörigheter](#create-an-api-permissions-role)
* [Skapa en behörighets grupp för API-användaren](#create-a-permission-group-for-the-api-user)
* [Bevilja behörighets rollen till behörighets gruppen](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Skapa/identifiera API-användarkonto i SuccessFactors
Arbeta med ditt SuccessFactors-administratörs team eller implementerings partner för att skapa eller identifiera ett användar konto i SuccessFactors som ska användas för att anropa OData-API: erna. Autentiseringsuppgifterna för användar namn och lösen ord för det här kontot kommer att krävas när du konfigurerar etablerings appar i Azure AD. 

### <a name="create-an-api-permissions-role"></a>Skapa en roll för API-behörigheter

* Logga in på SAP SuccessFactors med ett användar konto som har åtkomst till administrations centret.
* Sök efter *Hantera behörighets roller*och välj **Hantera behörighets roller** från Sök resultaten.
  ![Hantera behörighets roller](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* I listan behörighets roll klickar du på **Skapa ny**.
  > [!div class="mx-imgBorder"]
  > ![Skapa ny behörighets roll](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Lägg till ett **roll namn** och en **Beskrivning** för den nya behörighets rollen. Namnet och beskrivningen ska ange att rollen är för API-användnings behörigheter.
  > [!div class="mx-imgBorder"]
  > ![Information om behörighets roll](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Under behörighets inställningar klickar du på **behörighet...** och bläddrar sedan ned behörighets listan och klickar på **Hantera integrerings verktyg**. Markera kryss rutan om **du vill tillåta administratörs åtkomst till OData-API via grundläggande autentisering**.
  > [!div class="mx-imgBorder"]
  > ![Hantera integrerings verktyg](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Rulla nedåt i samma ruta och välj **medarbetares centrala API**. Lägg till behörigheter som visas nedan för att läsa med ODATA API och redigera med ODATA API. Välj alternativet Redigera om du planerar att använda samma konto för SuccessFactors-scenariot för tillbakaskrivning. 
  > [!div class="mx-imgBorder"]
  > ![Läs Skriv behörighet](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Klicka på **färdig**. Klicka på **Spara ändringar**.

### <a name="create-a-permission-group-for-the-api-user"></a>Skapa en behörighets grupp för API-användaren

* I SuccessFactors administrations Center söker du efter *Hantera behörighets grupper*och väljer **Hantera behörighets grupper** från Sök resultaten.
  > [!div class="mx-imgBorder"]
  > ![Hantera behörighets grupper](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* I fönstret Hantera behörighets grupper klickar du på **Skapa nytt**.
  > [!div class="mx-imgBorder"]
  > ![Lägg till ny grupp](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Lägg till ett grupp namn för den nya gruppen. Grupp namnet ska indikera att gruppen är för API-användare.
  > [!div class="mx-imgBorder"]
  > ![Namn på behörighets grupp](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Lägg till medlemmar i gruppen. Du kan till exempel välja **användar namn** i list rutan personer i poolen och sedan ange användar namnet för det API-konto som ska användas för integreringen. 
  > [!div class="mx-imgBorder"]
  > ![Lägga till gruppmedlemmar](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Slutför skapandet av behörighets gruppen genom att klicka på **klar** .

### <a name="grant-permission-role-to-the-permission-group"></a>Bevilja behörighets rollen till behörighets gruppen

* I SuccessFactors administrations Center kan du söka efter *Hantera behörighets roller*och sedan välja **Hantera behörighets roller** från Sök resultaten.
* I **listan behörighets roll**väljer du den roll som du har skapat för behörigheter för API-användning.
* Under **tilldela den här rollen till... klickar du**på **Lägg till...** -knapp.
* Välj **behörighets grupp...** från den nedrullningsbara menyn och klicka sedan på **Välj...** för att öppna fönstret grupper för att söka efter och välja den grupp som skapades ovan. 
  > [!div class="mx-imgBorder"]
  > ![Lägg till behörighets grupp](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Granska behörighets rollen bevilja behörighets gruppen. 
  > [!div class="mx-imgBorder"]
  > ![Behörighets roll och grupp information](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klicka på **Spara ändringar**.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Konfigurera användar etablering från SuccessFactors till Azure AD

Det här avsnittet innehåller steg för användar konto etablering från SuccessFactors till Azure AD.

* [Lägg till etablerings anslutnings programmet och konfigurera anslutningen till SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurera mappningar för attribut](#part-2-configure-attribute-mappings)
* [Aktivera och starta användar etablering](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Del 1: Lägg till etablerings anslutnings programmet och konfigurera anslutningen till SuccessFactors

**Så här konfigurerar du SuccessFactors till Azure AD-etablering:**

1. Gå till <https://portal.azure.com>

2. I det vänstra navigerings fältet väljer du **Azure Active Directory**

3. Välj **företags program**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj kategorin **alla** .

5. Sök efter **SuccessFactors för att Azure Active Directory användar etablering**och Lägg till den appen från galleriet.

6. När appen har lagts till och skärmen information om appen visas väljer du **etablering**

7. Ändra **etablerings** **läget** till **automatiskt**

8. Slutför avsnittet **admin credentials** enligt följande:

   * **Administratörens användar namn** – Ange användar namnet för SuccessFactors-API: t med det företags-ID som lagts till. Formatet: **användar namn \@ companyID**

   * **Administratörs lösen ord –** Ange lösen ordet för SuccessFactors-API-användarkontot. 

   * **Klient-URL –** Ange namnet på SuccessFactors OData API Services-slutpunkten. Ange bara värd namnet för servern utan http eller https. Det här värdet bör se ut så här: **API-Server-Name.SuccessFactors.com**.

   * **E-postavisering –** Ange din e-postadress och markera kryss rutan "skicka e-post om fel inträffar".
    > [!NOTE]
    > Azure AD Provisioning-tjänsten skickar e-postavisering om etablerings jobbet hamnar i [karantän](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) .

   * Klicka på knappen **Testa anslutning** . Om anslutnings testet lyckas, klickar du på knappen **Spara** längst upp. Om det Miss lyckas kontrollerar du att autentiseringsuppgifterna och URL: en för SuccessFactors är giltiga.
    >[!div class="mx-imgBorder"]
    >![Azure-portalen](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * När autentiseringsuppgifterna har sparats visas standard mappningen synkronisera SuccessFactors användare i avsnittet **mappningar** **som används för att Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera mappningar för attribut

I det här avsnittet ska du konfigurera hur användar data flödar från SuccessFactors till Active Directory.

1. På fliken etablering under **mappningar**klickar du på **Synkronisera SuccessFactors-användare för att Azure Active Directory**.

1. I fältet **käll objekt omfånget** kan du välja vilka uppsättningar av användare i SuccessFactors som ska ligga inom omfånget för etablering till Azure AD genom att definiera en uppsättning attributbaserade filter. Standard omfånget är "alla användare i SuccessFactors". Exempel filter:

   * Exempel: scope till användare med personIdExternal mellan 1000000 och 2000000 (exklusive 2000000)

      * Attribut: personIdExternal

      * Operator: REGEX-matchning

      * Värde: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Exempel: endast anställda och inte eventualtillgångar

      * Attribut: Anställningsnr

      * Operator: är inte NULL

   > [!TIP]
   > När du konfigurerar etablerings appen för första gången måste du testa och verifiera dina mappningar och uttryck för att kontrol lera att det ger önskat resultat. Microsoft rekommenderar att du använder omfångs filter under **käll objekt omfånget** för att testa dina mappningar med några test användare från SuccessFactors. När du har kontrollerat att mappningarna fungerar kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

   > [!CAUTION] 
   > Standard beteendet för etablerings motorn är att inaktivera/ta bort användare som omfattas av omfånget. Det kanske inte är önskvärt i din SuccessFactors till Azure AD-integrering. Om du vill åsidosätta det här standard beteendet läser du artikeln [hoppa över borttagning av användar konton som omfattas av omfånget](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. I fältet **mål objekts åtgärder** kan du globalt filtrera vilka åtgärder som utförs på Active Directory. **Skapa** och **Uppdatera** är de vanligaste.

1. I avsnittet **mappningar för attribut** kan du definiera hur enskilda SuccessFactors-attribut ska mappas till Active Directory attribut.

  >[!NOTE]
  >En fullständig lista över SuccessFactors-attribut som stöds av programmet finns i referens för [SuccessFactors-attribut](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Klicka på en befintlig attributmappning för att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En mappning för enskilda attribut stöder följande egenskaper:

      * **Mappnings typ**

         * **Direct** – skriver värdet för attributet SuccessFactors till attributet AD, utan ändringar

         * **Konstant** – Skriv ett statiskt, konstant sträng värde till attributet AD

         * **Uttryck** – gör att du kan skriva ett anpassat värde till attributet AD, baserat på ett eller flera SuccessFactors-attribut. [Mer information finns i den här artikeln om uttryck](../app-provisioning/functions-for-customizing-application-data.md).

      * **Källattribut** – attributet User från SuccessFactors

      * **Standardvärde** – valfritt. Om källattributet har ett tomt värde, skrivs värdet i stället av mappningen.
            Den vanligaste konfigurationen är att lämna detta tomt.

      * **Target-attribut** – användarattribut i Active Directory.

      * **Matcha objekt med det här attributet** – om mappningen ska användas för att unikt identifiera användare mellan SuccessFactors och Active Directory. Det här värdet anges vanligt vis i fältet Worker-ID för SuccessFactors, som vanligt vis mappas till något av de anställdas ID-attributen i Active Directory.

      * **Matchnings prioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som definieras av det här fältet. Så fort en matchning hittas utvärderas inga ytterligare matchande attribut.

      * **Använd den här mappningen**

         * **Alltid** – Använd den här mappningen för både skapande av användare och uppdaterings åtgärder

         * **Endast vid skapande** – Använd endast den här mappningen för åtgärder för att skapa användare

1. Spara dina mappningar genom att klicka på **Spara** överst i avsnittet attribut-mappning.

När du har slutfört konfigurationen av attributmappning kan du nu [Aktivera och starta användar etablerings tjänsten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Aktivera och starta användar etablering

När SuccessFactors-konfigurationen har slutförts kan du aktivera etablerings tjänsten i Azure Portal.

> [!TIP]
> Som standard när du aktiverar etablerings tjänsten kommer den att initiera etablerings åtgärder för alla användare i omfånget. Om det uppstår fel i mappnings-eller data frågor för data lagret kan etablerings jobbet Miss Miss kan och gå in i karantäns läget. För att undvika detta rekommenderar vi att du konfigurerar **käll objekt omfångs** filter och testar dina mappningar av attribut med några test användare innan du startar den fullständiga synkroniseringen för alla användare. När du har kontrollerat att mappningarna fungerar och ger dig önskade resultat kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

1. På fliken **etablering** ställer du in **etablerings status** på **på**.

2. Klicka på **Spara**.

3. Den här åtgärden startar den inledande synkroniseringen, vilket kan ta ett variabelt antal timmar beroende på hur många användare som finns i SuccessFactors-klienten. Du kan kontrol lera förlopps indikatorn för att följa synkroniseringens förlopp. 

4. Gå till fliken **gransknings loggar** i Azure Portal för att se vilka åtgärder som etablerings tjänsten har utfört. I gransknings loggarna visas alla enskilda synkroniseringsfel som utförs av etablerings tjänsten, t. ex. vilka användare som ska läsas ut från Workday och därefter läggs till eller uppdateras till Active Directory. 

5. När den inledande synkroniseringen har slutförts skrivs en gransknings sammanfattnings rapport på fliken **etablering** , som visas nedan.

   > [!div class="mx-imgBorder"]
   > ![Förlopps indikator för etablering](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Nästa steg

* [Läs mer om SuccessFactors-attribut som stöds för inkommande etablering](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Lär dig hur du konfigurerar tillbakaskrivning av e-post till SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan SuccessFactors och Azure Active Directory](successfactors-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du exporterar och importerar dina etablerings konfigurationer](../app-provisioning/export-import-provisioning-configuration.md)


