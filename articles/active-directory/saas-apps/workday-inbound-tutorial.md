---
title: 'Självstudie: Konfigurera arbets dag för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Workday.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 51ab05a995ba5b620b759f419fb5b4594873d2f5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527816"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Självstudie: Konfigurera arbets dag för automatisk användar etablering

Syftet med den här självstudien är att visa de steg som du måste utföra för att etablera Worker-profiler från Workday till lokala Active Directory (AD).

>[!NOTE]
>Använd den här självstudien om de användare som du vill etablera från Workday behöver ett lokalt AD-konto och ett Azure AD-konto. 
>* Om användarna från Workday bara behöver Azure AD-konto (endast molnbaserade användare) kan du läsa självstudien om hur du [konfigurerar arbets dag till Azure AD](workday-inbound-cloud-only-tutorial.md) -etableringen. 
>* Om du vill konfigurera tillbakaskrivning av attribut, till exempel e-postadress, användar namn och telefonnummer från Azure AD till Workday, kan du läsa självstudien om hur du [konfigurerar tillbakaskrivning av workday](workday-writeback-tutorial.md).


## <a name="overview"></a>Översikt

[Azure Active Directory användar etablerings tjänsten](../app-provisioning/user-provisioning.md) integreras med [personalavdelningen-API: t för arbets dagar](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) för att etablera användar konton. Arbets flöden för användar etablering av arbets dagar som stöds av Azure AD-tjänsten för användar etablering möjliggör automatisering av följande personal-och identitets cykel hanterings scenarier:

* **Anställning av nya anställda** – när en ny medarbetare läggs till i arbets dagen skapas ett användar konto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md), med skriv skydd för IT-hanterad kontakt information till Workday.

* **Uppdateringar av anställda och profiler** – när en medarbetar post uppdateras i Workday (t. ex. namn, titel eller chef) uppdateras deras användar konto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

* **Anställdas uppsägningar** – när en medarbetare avslutas på arbets dagen inaktive ras användar kontot automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

* **Anställdas återställningar** – när en medarbetare återställs på arbets dagen kan deras gamla konto automatiskt återaktiveras eller etableras på nytt (beroende på din preferens) för att Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

### <a name="whats-new"></a>Nyheter
Det här avsnittet innehåller de senaste förbättringarna för arbets dag integrering. Om du vill ha en lista över omfattande uppdateringar, planerade ändringar och arkiv, kan du besöka sidan [Nyheter i Azure Active Directory?](../fundamentals/whats-new.md) 

* **Maj 2020 – möjlighet att göra tillbakaskrivning av telefonnummer till Workday:** Förutom e-post och användar namn kan du nu tillbakaskrivning av arbets telefonnummer och mobiltelefon nummer från Azure AD till Workday. Mer information finns i [själv studie kursen om tillbakaskrivning av appar](workday-writeback-tutorial.md).

* **April 2020 – stöd för den senaste versionen av WWS-API: t (Workday Web Services):** Med två gånger om året i mars och september levererar arbets dagar funktions rika uppdateringar som hjälper dig att möta dina affärs mål och ändra personal kraven. För att hålla dig uppdaterad med de nya funktionerna som levereras av workday kan du nu ange den WWS-API-version som du vill använda i anslutnings-URL: en. Mer information om hur du anger dag-API-versionen finns i avsnittet om hur du [konfigurerar arbets dag anslutning](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory). 

* **Jan 2020 – möjlighet att ställa in AD accountExpires-attribut:** Med funktionen [NumFromDate](../app-provisioning/functions-for-customizing-application-data.md#numfromdate) kan du nu mappa datum fält för arbets dagar som *EndContractDate* eller *StatusTerminationDate*. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här användar etablerings lösningen som passar bäst för?

Den här användar etablerings lösningen i Workday passar utmärkt för:

* Organisationer som vill ha en förbyggd, molnbaserad lösning för användar etablering i Workday

* Organisationer som kräver direkt användar etablering från Workday till Active Directory eller Azure Active Directory

* Organisationer som kräver att användare tillhandahålls med data som hämtats från HCM-modulen för arbets dagar (se [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationer som behöver ansluta, flytta och lämna användare som ska synkroniseras till en eller flera Active Directory skogar, domäner och organisationsenheter som endast baseras på ändrings information som identifieras i HCM-modulen för arbets dagar (se [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationer som använder Office 365 för e-post

## <a name="solution-architecture"></a>Lösnings arkitektur

I det här avsnittet beskrivs den grundläggande användar etablerings lösnings arkitekturen för vanliga hybrid miljöer. Det finns två relaterade flöden:

* **Auktoritativt Tim data flöde – från Workday till lokal Active Directory:** I dessa flödes händelser (t. ex. nya anställningar, överföringar, uppsägningar) sker först i molnets arbets dag, HR-klient och händelse data flödar till lokala Active Directory via Azure AD och etablerings agenten. Beroende på händelsen kan det leda till att du skapar/uppdaterar/aktiverar/inaktiverar-åtgärder i AD.
* **Tillbakaskrivning flöde – från lokala Active Directory till arbets dag:** När kontot har skapats i Active Directory synkroniseras det med Azure AD via Azure AD Connect och information som e-post, användar namn och telefonnummer kan skrivas tillbaka till arbets dagen.

![Översikt](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Slut punkt till slut punkt för användar data flöde

1. HR-teamet utför arbets transaktioner (anslutare/flytter/Lämnare eller nya anställningar/överföringar/uppsägningar) i Workday HCM
2. Azure AD Provisioning-tjänsten kör schemalagda synkroniseringar av identiteter från arbets dagen HR och identifierar ändringar som behöver bearbetas för synkronisering med lokala Active Directory.
3. Azure AD Provisioning-tjänsten anropar den lokala Azure AD Connect etablerings agenten med en nytto last för begäran som innehåller AD-konto skapa/uppdatera/aktivera/inaktivera åtgärder.
4. Azure AD Connect etablerings agenten använder ett tjänst konto för att lägga till/uppdatera AD-konto data.
5. Azure AD Connect/AD Sync-motorn kör delta-synkronisering för att hämta uppdateringar i AD.
6. Active Directory uppdateringarna synkroniseras med Azure Active Directory.
7. Om tillbakaskrivning-appen för [Workday](workday-writeback-tutorial.md) har kon figurer ATS skriver den tillbaka attribut som e-post, användar namn och telefonnummer till Workday.

## <a name="planning-your-deployment"></a>Planera distributionen

Att konfigurera arbets dagar för att Active Directory användar etablering kräver betydande planering som omfattar olika aspekter, till exempel:
* Konfiguration av Azure AD Connect etablerings agenten 
* Antal arbets dagar till AD User Provisioning-appar som ska distribueras
* Välja rätt matchnings-ID, attributmappning, transformering och omfångs filter

I [Cloud HR Deployment plan](../app-provisioning/plan-cloud-hr-provision.md) finns det en omfattande rikt linjer och rekommenderade metod tips. 

## <a name="configure-integration-system-user-in-workday"></a>Konfigurera användare av integrations systemet på arbets dagen

Ett gemensamt krav på alla tilldelnings anslutningar för arbets dagar är att de kräver autentiseringsuppgifter för en användare av en Workday-integrerings system för att ansluta till personal-API: t för arbets dagar. I det här avsnittet beskrivs hur du skapar en integrations system användare i Workday och innehåller följande avsnitt:

* [Skapa en integrations system användare](#creating-an-integration-system-user)
* [Skapa en integrerings säkerhets grupp](#creating-an-integration-security-group)
* [Konfigurera behörigheter för domän säkerhets princip](#configuring-domain-security-policy-permissions)
* [Konfigurera säkerhets princip behörigheter för affärs process](#configuring-business-process-security-policy-permissions)
* [Aktivera säkerhets princips ändringar](#activating-security-policy-changes)

> [!NOTE]
> Det är möjligt att kringgå den här proceduren och istället använda ett globalt administratörs konto för Workday som system integrations konto. Detta kan fungera bra för demonstrationer, men rekommenderas inte för produktions distributioner.

### <a name="creating-an-integration-system-user"></a>Skapa en integrations system användare

**Så här skapar du en integrations system användare:**

1. Logga in på din Workday-klient med ett administratörs konto. I **programmet Workday**anger du skapa användare i rutan Sök och klickar sedan på **skapa integrations system användare**.

   >[!div class="mx-imgBorder"] 
   >![Skapa användare](./media/workday-inbound-tutorial/wd_isu_01.png "Skapa användare")
2. Slutför **användar uppgiften skapa integrations system** genom att ange ett användar namn och lösen ord för en ny integrations system användare.  
  
   * Lämna alternativet **Kräv nytt lösen ord vid nästa inloggning** omarkerat, eftersom den här användaren kommer att logga in program mässigt.
   * Lämna timeout-värdet i **sessionen** med standardvärdet 0, vilket hindrar användarens sessioner från timeout för tidigt.
   * Välj alternativet **Tillåt inte UI-sessioner** eftersom det ger ett extra säkerhets lager som förhindrar att en användare med lösen ordet för integrations systemet loggar in på arbets dagen.

   > [!div class="mx-imgBorder"]
   > ![Skapa integrations system användare](./media/workday-inbound-tutorial/wd_isu_02.png "Skapa integrations system användare")

### <a name="creating-an-integration-security-group"></a>Skapa en integrerings säkerhets grupp

I det här steget ska du skapa en obegränsad eller begränsad integrerings system säkerhets grupp på arbets dagen och tilldela integrations system användaren som skapades i föregående steg till den här gruppen.

**Så här skapar du en säkerhets grupp:**

1. Ange skapa säkerhets grupp i rutan Sök och klicka sedan på **skapa säkerhets grupp**.

   > [!div class="mx-imgBorder"]
   > ![CreateSecurity-grupp](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity-grupp")
2. Slutför aktiviteten **skapa säkerhets grupp** . 

   * Det finns två typer av säkerhets grupper på arbets dagen:
     * **Obegränsad:** Alla medlemmar i säkerhets gruppen kan komma åt alla data instanser som skyddas av säkerhets gruppen.
     * **Begränsat:** Alla säkerhets grupp medlemmar har sammanhangsbaserad åtkomst till en delmängd av data instanser (rader) som säkerhets gruppen har åtkomst till.
   * Kontrol lera med din arbets kamrats integrations partner att du väljer lämplig säkerhets grupp typ för integreringen.
   * När du känner till grupp typen väljer du **säkerhets grupp för integrerings systemet (** ej begränsad) eller **integrations systemets säkerhets grupp (begränsat)** från List rutan **typ av säkerhets grupp för innehavare** .

     > [!div class="mx-imgBorder"]
     >![CreateSecurity-grupp](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity-grupp")

3. När säkerhets gruppen har skapats visas en sida där du kan tilldela medlemmar till säkerhets gruppen. Lägg till den nya integrations system användaren som skapades i föregående steg i den här säkerhets gruppen. Om du använder *begränsad* säkerhets grupp måste du också välja rätt organisations omfång.

   >[!div class="mx-imgBorder"]
   >![Redigera säkerhets grupp](./media/workday-inbound-tutorial/wd_isu_05.png "Redigera säkerhets grupp")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurera behörigheter för domän säkerhets princip

I det här steget beviljar du princip behörigheter för domän säkerhet för Worker-data till säkerhets gruppen.

**Så här konfigurerar du behörigheter för domän säkerhets princip:**

1. Ange **domän säkerhets konfiguration** i sökrutan och klicka sedan på **säkerhets konfigurations rapport**för länk domän.  
   >[!div class="mx-imgBorder"]
   >![Domän säkerhets principer](./media/workday-inbound-tutorial/wd_isu_06.png "Domän säkerhets principer")  
2. I text rutan **domän** söker du efter följande domäner och lägger till dem i filtret en i taget.  
   * *Etablering av externt konto*
   * *Worker-data: arbetare*
   * *Worker-data: rapporter om offentliga arbetare*
   * *Person uppgifter: arbets kontakt information*
   * *Worker-data: alla positioner*
   * *Worker-data: aktuell bemannings information*
   * *Worker-data: affärs titel i arbets profil*
   * *Workday-konton*
   
     >[!div class="mx-imgBorder"]
     >![Domän säkerhets principer](./media/workday-inbound-tutorial/wd_isu_07.png "Domän säkerhets principer")  

     >[!div class="mx-imgBorder"]
     >![Domän säkerhets principer](./media/workday-inbound-tutorial/wd_isu_08.png "Domän säkerhets principer") 

     Klicka på **OK**.

3. I rapporten som visas, väljer du ellipsen (...) som visas bredvid **externt konto etablering** och klickar på meny alternativet **domän-> redigera säkerhets princip behörigheter**
   >[!div class="mx-imgBorder"]
   >![Domän säkerhets principer](./media/workday-inbound-tutorial/wd_isu_09.png "Domän säkerhets principer")  

4. På sidan **Redigera behörigheter för domän säkerhets princip** rullar du ned till avsnittets **integrerings behörigheter**. Klicka på "+"-tecknet för att lägga till integrations system gruppen i listan över säkerhets grupper med **Get** -och **Placera** -integrerings behörigheter.
   >[!div class="mx-imgBorder"]
   >![Redigera behörighet](./media/workday-inbound-tutorial/wd_isu_10.png "Redigera behörighet")  

5. Klicka på "+"-tecknet för att lägga till integrations system gruppen i listan över säkerhets grupper med **Get** -och **Placera** -integrerings behörigheter.

   >[!div class="mx-imgBorder"]
   >![Redigera behörighet](./media/workday-inbound-tutorial/wd_isu_11.png "Redigera behörighet")  

6. Upprepa steg 3-5 ovan för var och en av dessa återstående säkerhets principer:

   | Åtgärd | Säkerhets princip för domän |
   | ---------- | ---------- |
   | Hämta och placera | Worker-data: rapporter om offentliga arbetare |
   | Hämta och placera | Person uppgifter: arbets kontakt information |
   | Hämta | Worker-data: arbetare |
   | Hämta | Worker-data: alla positioner |
   | Hämta | Worker-data: aktuell bemannings information |
   | Hämta | Worker-data: affärs titel i arbets profil |
   | Hämta och placera | Workday-konton |

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurera säkerhets princip behörigheter för affärs process

I det här steget ska du bevilja princip behörigheter för affärs processer för Worker-data till säkerhets gruppen. 

> [!NOTE]
> Det här steget krävs endast för att ställa in tillbakaskrivning av tillbakaskrivning-appen för arbets dagar.

**Så här konfigurerar du säkerhets princip behörigheter för affärs processer:**

1. Ange **affärs process princip** i sökrutan och klicka sedan på aktiviteten **Redigera säkerhets princip för affärs process** .  

   >[!div class="mx-imgBorder"]
   >![Säkerhets principer för affärs processer](./media/workday-inbound-tutorial/wd_isu_12.png "Säkerhets principer för affärs processer")  

2. I text rutan **affärs process typ** söker du efter *kontakt* och väljer affärs process för **arbets kontakt ändring** och klickar på **OK**.

   >[!div class="mx-imgBorder"]
   >![Säkerhets principer för affärs processer](./media/workday-inbound-tutorial/wd_isu_13.png "Säkerhets principer för affärs processer")  

3. På sidan **Redigera säkerhets princip för affärs process** bläddrar du till avsnittet **ändra arbets kontakt information (webb tjänst)** .
    

4. Välj och Lägg till den nya säkerhets gruppen för integrerings systemet i listan över säkerhets grupper som kan initiera begäran om webb tjänster. 

   >[!div class="mx-imgBorder"]
   >![Säkerhets principer för affärs processer](./media/workday-inbound-tutorial/wd_isu_15.png "Säkerhets principer för affärs processer")  

5. Klicka på **färdig**. 

### <a name="activating-security-policy-changes"></a>Aktivera säkerhets princips ändringar

**Så här aktiverar du säkerhets princip ändringar:**

1. Ange aktivera i sökrutan och klicka sedan på länken **Aktivera väntande säkerhets princip ändringar**.
   >[!div class="mx-imgBorder"]
   >![Inaktivera](./media/workday-inbound-tutorial/wd_isu_16.png "Aktivera")

1. Starta åtgärden aktivera väntande säkerhets princip ändringar genom att ange en kommentar för gransknings syfte och klicka sedan på **OK**.
1. Slutför uppgiften på nästa skärm genom att markera kryss rutan **Bekräfta**och klicka sedan på **OK**.

   >[!div class="mx-imgBorder"]
   >![Aktivera väntande säkerhet](./media/workday-inbound-tutorial/wd_isu_18.png "Aktivera väntande säkerhet")  

## <a name="configure-active-directory-service-account"></a>Konfigurera Active Directory tjänst konto

I det här avsnittet beskrivs de behörigheter för AD-tjänstkontot som krävs för att installera och konfigurera Azure AD Connect etablerings agenten.

### <a name="permissions-required-to-run-the-provisioning-agent-installer"></a>Behörigheter som krävs för att köra installations programmet för etablerings agenten
När du har identifierat Windows Server som ska vara värd för etablerings agenten loggar du in på Server värden med hjälp av antingen lokal administratör eller autentiseringsuppgifter för domän administratören. Agent installations processen skapar säkra filer för nyckel lagring och uppdaterar tjänst profil konfigurationen på värd servern. Detta kräver administratörs åtkomst på den server som är värd för agenten. 

### <a name="permissions-required-to-configure-the-provisioning-agent-service"></a>Behörigheter som krävs för att konfigurera etablerings Agent tjänsten
Använd stegen nedan för att konfigurera ett tjänst konto som kan användas för etablering av agent åtgärder. 
1.  Öppna snapin-modulen *Active Directory användare och datorer* på AD-domänkontrollanten. 
2.  Skapa en ny domän användare (exempel: *provAgentAdmin*)  
3.  Högerklicka på ORGANISATIONSENHETen eller domän namnet och välj *delegera kontroll* som ska öppna *guiden Delegera kontroll*. 

> [!NOTE] 
> Om du vill begränsa etablerings agenten till att endast skapa och läsa användare från en viss ORGANISATIONSENHET i testnings syfte, rekommenderar vi att du delegerar kontrollen på rätt ORGANISATIONSENHET under test körningarna.

4. Klicka på **Nästa** på Välkomst skärmen. 
5. På skärmen **Välj användare eller grupper** lägger du till den domän användare som du skapade i steg 2. Klicka på **Nästa**.
   >[!div class="mx-imgBorder"]
   >![Lägg till skärm](./media/workday-inbound-tutorial/delegation-wizard-01.png "Lägg till skärm")

6. På skärmen **uppgifter som ska delegeras** väljer du följande aktiviteter: 
   * Skapa, ta bort och hantera användar konton
   * Läs all användar information

   >[!div class="mx-imgBorder"]
   >![Skärmen uppgifter](./media/workday-inbound-tutorial/delegation-wizard-02.png "Skärmen uppgifter")

7. Klicka på **Nästa** och **Spara** konfigurationen


## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurera användar etablering från arbets dagar till Active Directory

Det här avsnittet innehåller steg för etablering av användar konton från arbets dagar till varje Active Directory domän inom omfånget för din integrering.

* [Lägg till etablerings anslutnings programmet och ladda ned etablerings agenten](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Installera och konfigurera lokala etablerings agenter](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurera anslutning till arbets dagar och Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Konfigurera mappningar för attribut](#part-4-configure-attribute-mappings)
* [Aktivera och starta användar etablering](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Del 1: Lägg till etablerings anslutnings programmet och ladda ned etablerings agenten

**Konfigurera arbets dagar för att Active Directory etablering:**

1. Gå till <https://portal.azure.com>.

2. I Azure Portal söker du efter och väljer **Azure Active Directory**.

3. Välj **företags program**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj kategorin **alla** .

5. Sök efter **arbets dag för att Active Directory användar etablering**och Lägg till den appen från galleriet.

6. När appen har lagts till och skärmen information om appen visas väljer du **etablering**.

7. Ändra **etablerings** **läget** till **automatiskt**.

8. Klicka på den informations banderoll som visas för att ladda ned etablerings agenten. 

   >[!div class="mx-imgBorder"]
   >![Hämta agent](./media/workday-inbound-tutorial/pa-download-agent.png "Ladda ned agent skärm")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Del 2: installera och konfigurera lokala etablerings agenter

För att etablera till Active Directory lokalt måste etablerings agenten installeras på en server som har .NET 4.7.1 + Framework och nätverks åtkomst till önskad Active Directory domän (er).

> [!TIP]
> Du kan kontrol lera versionen av .NET Framework på servern med hjälp av anvisningarna [här](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Om inte .NET 4.7.1 eller senare är installerat på servern kan du ladda ned den [här](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Överför de hämtade agent installations programmet till Server värden och följ stegen nedan för att slutföra Agent konfigurationen.

1. Logga in på den Windows Server där du vill installera den nya agenten.

1. Starta installations programmet för etablerings agenten, Godkänn villkoren och klicka på knappen **Installera** .

   >[!div class="mx-imgBorder"]
   >![Installations skärm](./media/workday-inbound-tutorial/pa_install_screen_1.png "Installations skärm")
   
1. När installationen är klar startas guiden och du kan se fönstret **Anslut Azure AD** . Klicka på knappen **autentisera** för att ansluta till Azure AD-instansen.

   >[!div class="mx-imgBorder"]
   >![Ansluta till Azure Active Directory](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ansluta till Azure Active Directory")
   
1. Autentisera till Azure AD-instansen med autentiseringsuppgifter för Hybrid identitets administratör.

   >[!div class="mx-imgBorder"]
   >![Admin-autentisering](./media/workday-inbound-tutorial/pa_install_screen_3.png "Admin-autentisering")

   > [!NOTE]
   > Autentiseringsuppgifter för Azure AD-Admin används endast för att ansluta till din Azure AD-klient. Agenten lagrar inte autentiseringsuppgifterna lokalt på servern.

1. Efter en lyckad autentisering med Azure AD visas skärmen **anslut Active Directory** . I det här steget anger du ditt AD-domännamn och klickar på knappen **Lägg till katalog** .

   >[!div class="mx-imgBorder"]
   >![Lägg till katalog](./media/workday-inbound-tutorial/pa_install_screen_4.png "Lägg till katalog")
  
1. Nu uppmanas du att ange de autentiseringsuppgifter som krävs för att ansluta till AD-domänen. På samma skärm kan du använda **prioriteten Välj** domänkontrollant för att ange domänkontrollanter som agenten ska använda för att skicka etablerings begär Anden.

   >[!div class="mx-imgBorder"]
   >![Domänautentiseringsuppgifter](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. När du har konfigurerat domänen visas en lista över konfigurerade domäner i installations programmet. På den här skärmen kan du upprepa steg #5 och #6 för att lägga till fler domäner eller klicka på **Nästa** för att fortsätta med agent registreringen.

   >[!div class="mx-imgBorder"]
   >![Konfigurerade domäner](./media/workday-inbound-tutorial/pa_install_screen_6.png "Konfigurerade domäner")

   > [!NOTE]
   > Om du har flera AD-domäner (t. ex. na.contoso.com, emea.contoso.com) lägger du till varje domän separat i listan.
   > Det räcker bara att lägga till den överordnade domänen (t. ex. contoso.com). Du måste registrera varje underordnad domän med agenten.
   
1. Granska konfigurations informationen och klicka på **Bekräfta** för att registrera agenten.
  
   >[!div class="mx-imgBorder"]
   >![Bekräfta skärm](./media/workday-inbound-tutorial/pa_install_screen_7.png "Bekräfta skärm")
   
1. Konfigurations guiden visar förloppet för agent registreringen.
  
   >[!div class="mx-imgBorder"]
   >![Agent registrering](./media/workday-inbound-tutorial/pa_install_screen_8.png "Agent registrering")
   
1. När agent registreringen är klar kan du klicka på **Avsluta** för att avsluta guiden.

   >[!div class="mx-imgBorder"]
   >![Avsluta skärm](./media/workday-inbound-tutorial/pa_install_screen_9.png "Avsluta skärm")
   
1. Verifiera installationen av agenten och se till att den körs genom att öppna snapin-modulen "tjänster" och leta efter tjänsten "Microsoft Azure AD ansluta etablerings agent"

   >[!div class="mx-imgBorder"]
   >![Tjänster](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Del 3: Konfigurera anslutning till arbets dagar och Active Directory i etablerings appen
I det här steget upprättar vi anslutningen till arbets dagar och Active Directory i Azure Portal. 

1. Gå tillbaka till arbets dagen i Azure Portal och Active Directory användar etablerings appen som skapats i [del 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Slutför avsnittet **admin credentials** enligt följande:

   * **Workday-användarnamn** – Ange användar namnet för kontot för arbets dag integrerings systemet, med namnet på klient organisationen som lagts till. Det bör se ut ungefär så här: **användar namn \@ tenant_name**

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
     > <br> Om du använder en WWS API v-30.0 +, innan du aktiverar etablerings jobbet, uppdaterar du **XPath API-uttryck** under **attribut mappning-> avancerade alternativ-> redigera attributlistan för arbets dagar** som refererar till avsnittet [Hantera din konfiguration och ditt](#managing-your-configuration) Workday- [attribut referens](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **Active Directory skog –** "Namnet på din Active Directory domän, som registrerat hos agenten. Använd List rutan för att välja mål domän för etablering. Det här värdet är vanligt vis en sträng som: *contoso.com*

   * **Active Directory container –** Ange behållar-DN där agenten ska skapa användar konton som standard.
        Exempel: *OU = standard användare, OU = användare, DC = contoso, DC = test*
        
     > [!NOTE]
     > Den här inställningen kommer bara att spelas upp för att skapa användar konton om attributet *parentDistinguishedName* inte har kon figurer ATS i mappningarna för attribut. Den här inställningen används inte för användar Sök-eller uppdaterings åtgärder. Hela domänens under träd omfattas av Sök åtgärdens omfång.

   * **E-postavisering –** Ange din e-postadress och markera kryss rutan "skicka e-post om fel inträffar".

     > [!NOTE]
     > Azure AD Provisioning-tjänsten skickar e-postavisering om etablerings jobbet hamnar i [karantän](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) .

   * Klicka på knappen **Testa anslutning** . Om anslutnings testet lyckas, klickar du på knappen **Spara** längst upp. Om det Miss lyckas, kontrol lera att autentiseringsuppgifterna för arbets dag och de AD-autentiseringsuppgifter som kon figurer ATS för Agent installationen är giltiga.

     >[!div class="mx-imgBorder"]
     >![Azure-portalen](./media/workday-inbound-tutorial/wd_1.png)

   * När autentiseringsuppgifterna har sparats visar avsnittet **mappningar** standard mappningen **Synkronisera arbets uppgifter för arbets dagar till lokalt Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Del 4: Konfigurera mappningar för attribut

I det här avsnittet ska du konfigurera hur användar data flödar från arbets dagar till Active Directory.

1. På fliken etablering under **mappningar**klickar du på **Synkronisera arbets dagar arbetare till lokalt Active Directory**.

1. I fältet **käll objekt omfånget** kan du välja vilka uppsättningar av användare i arbets dagar som ska ingå i omfånget för etablering till AD, genom att definiera en uppsättning attributbaserade filter. Standard omfånget är "alla användare i Workday". Exempel filter:

   * Exempel: scope till användare med Worker-ID: n mellan 1000000 och 2000000 (exklusive 2000000)

      * Attribut: WorkerID

      * Operator: REGEX-matchning

      * Värde: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Exempel: endast anställda och inte eventualtillgångar

      * Attribut: Anställningsnr

      * Operator: är inte NULL

   > [!TIP]
   > När du konfigurerar etablerings appen för första gången måste du testa och verifiera dina mappningar och uttryck för att kontrol lera att det ger önskat resultat. Microsoft rekommenderar att du använder omfångs filter under **käll objekt omfånget** för att testa dina mappningar med några test användare från Workday. När du har kontrollerat att mappningarna fungerar kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

   > [!CAUTION] 
   > Standard beteendet för etablerings motorn är att inaktivera/ta bort användare som omfattas av omfånget. Detta kanske inte är önskvärt i din arbets dag till AD-integrering. Om du vill åsidosätta det här standard beteendet läser du artikeln [hoppa över borttagning av användar konton som omfattas av omfånget](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. I fältet **mål objekts åtgärder** kan du globalt filtrera vilka åtgärder som utförs på Active Directory. **Skapa** och **Uppdatera** är de vanligaste.

1. I avsnittet **mappningar för attribut** kan du definiera hur enskilda Workday-attribut mappar ska Active Directory attribut.

1. Klicka på en befintlig attributmappning för att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En mappning för enskilda attribut stöder följande egenskaper:

      * **Mappnings typ**

         * **Direct** – skriver värdet för attributet Workday till attributet AD, utan ändringar

         * **Konstant** – Skriv ett statiskt, konstant sträng värde till attributet AD

         * **Uttryck** – gör att du kan skriva ett anpassat värde till attributet AD, baserat på ett eller flera Workday-attribut. [Mer information finns i den här artikeln om uttryck](../app-provisioning/functions-for-customizing-application-data.md).

      * **Källattribut** – attributet användare från Workday. Om det attribut som du letar efter inte finns kan du läsa mer i [Anpassa listan med användar](#customizing-the-list-of-workday-user-attributes)-och Workday-användarattribut.

      * **Standardvärde** – valfritt. Om källattributet har ett tomt värde, skrivs värdet i stället av mappningen.
            Den vanligaste konfigurationen är att lämna detta tomt.

      * **Target-attribut** – användarattribut i Active Directory.

      * **Matcha objekt med det här attributet** – om mappningen ska användas för att unikt identifiera användare mellan arbets dagar och Active Directory. Det här värdet anges vanligt vis i fältet Worker-ID för arbets dag, som vanligt vis mappas till ett av ID-attributen för personal i Active Directory.

      * **Matchnings prioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som definieras av det här fältet. Så fort en matchning hittas utvärderas inga ytterligare matchande attribut.

      * **Använd den här mappningen**

         * **Alltid** – Använd den här mappningen för både skapande av användare och uppdaterings åtgärder

         * **Endast vid skapande** – Använd endast den här mappningen för åtgärder för att skapa användare

1. Spara dina mappningar genom att klicka på **Spara** överst i avsnittet attribut-mappning.
   >[!div class="mx-imgBorder"]
   >![Azure-portalen](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Nedan visas några exempel på attribut mappningar mellan arbets dagar och Active Directory, med några vanliga uttryck

* Det uttryck som mappar till *parentDistinguishedName* -attributet används för att etablera en användare till olika organisationsenheter baserat på en eller flera Workday-källfiler. I det här exemplet placeras användare i olika organisationsenheter baserat på vilken stad de finns i.

* Attributet *userPrincipalName* i Active Directory genereras med funktionen unduplicering [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) som söker efter ett genererat värde i mål-AD-domänen och bara anger det om det är unikt.  

* [Det finns dokumentation om hur du skriver uttryck här](../app-provisioning/functions-for-customizing-application-data.md). Det här avsnittet innehåller exempel på hur du tar bort specialtecken.

| WORKDAY-ATTRIBUT | ACTIVE DIRECTORY-ATTRIBUT |  MATCHNINGS-ID? | SKAPA/UPPDATERA |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Endast skrivet vid skapande |
| **PreferredNameData**    |  nomenklatur    |   |   Endast skrivet vid skapande |
| **SelectUniqueValue (JOIN (" \@ ", Anslut (".",  \[ FirstName \] , \[ lastname \] ), "contoso.com"), JOIN (" \@ ", JOIN (".", MID ( \[ FirstName \] , 1, 1), \[ LastName \] ), "contoso.com"), JOIN (" \@ ", JOIN (".", MID ( \[ FirstName \] , 1, 2), \[ \] "contoso.com"))**   | userPrincipalName     |     | Endast skrivet vid skapande 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         Endast skrivet vid skapande |
| **Växel ( \[ aktiv \] ,, "0", "sant", "1", "falskt")** |  accountDisabled      |     | Skapa + uppdatera |
| **FirstName**   | förnamn       |     |    Skapa + uppdatera |
| **LastName**   |   sn   |     |  Skapa + uppdatera |
| **PreferredNameData**  |  displayName |     |   Skapa + uppdatera |
| **Firm**         | company   |     |  Skapa + uppdatera |
| **SupervisoryOrganization**  | avdelning  |     |  Skapa + uppdatera |
| **ManagerReference**   | manager  |     |  Skapa + uppdatera |
| **BusinessTitle**   |  rubrik     |     |  Skapa + uppdatera | 
| **AddressLineData**    |  streetAddress  |     |   Skapa + uppdatera |
| **Kommuner**   |   l   |     | Skapa + uppdatera |
| **CountryReferenceTwoLetter**      |   co |     |   Skapa + uppdatera |
| **CountryReferenceTwoLetter**    |  c  |     |         Skapa + uppdatera |
| **CountryRegionReference** |  st     |     | Skapa + uppdatera |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Skapa + uppdatera |
| **Postnummer**  |   Post nummer  |     | Skapa + uppdatera |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Skapa + uppdatera |
| **Fax**      | facsimileTelephoneNumber     |     |    Skapa + uppdatera |
| **Mobilt**  |    mobil       |     |       Skapa + uppdatera |
| **LocalReference** |  preferredLanguage  |     |  Skapa + uppdatera |                                               
| **Switch ( \[ kommun \] , "OU = standard användare, DC = contoso, DC = com", "Borås", "OU = Borås, OU = användare, DC = contoso, DC = com", "Austin", "OU = Austin, OU = användare, DC = contoso, DC = com", "Seattle", "OU = Seattle, OU = användare, DC = contoso, DC = com", "London", "OU = London, OU = användare, DC = contoso, DC = com")**  | parentDistinguishedName     |     |  Skapa + uppdatera |

När du har slutfört konfigurationen av attributmappning kan du nu [Aktivera och starta användar etablerings tjänsten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Aktivera och starta användar etablering

När du har slutfört konfigurationen av appar för arbets dag etablering kan du aktivera etablerings tjänsten i Azure Portal.

> [!TIP]
> Som standard när du aktiverar etablerings tjänsten kommer den att initiera etablerings åtgärder för alla användare i omfånget. Om det uppstår fel i mappnings-eller data frågor för data lagret kan etablerings jobbet Miss Miss kan och gå in i karantäns läget. För att undvika detta rekommenderar vi att du konfigurerar **käll objekt omfångs** filter och testar dina mappningar av attribut med några test användare innan du startar den fullständiga synkroniseringen för alla användare. När du har kontrollerat att mappningarna fungerar och ger dig önskade resultat kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

1. På fliken **etablering** ställer du in **etablerings status** på **på**.

2. Klicka på **Spara**.

3. Den här åtgärden startar den inledande synkroniseringen, vilket kan ta ett variabelt antal timmar beroende på hur många användare som finns i arbets belastnings klienten. 

4. Gå till fliken **gransknings loggar** i Azure Portal för att se vilka åtgärder som etablerings tjänsten har utfört. I gransknings loggarna visas alla enskilda synkroniseringsfel som utförs av etablerings tjänsten, t. ex. vilka användare som ska läsas ut från Workday och därefter läggs till eller uppdateras till Active Directory. Mer information om hur du granskar gransknings loggarna och åtgärda etablerings felen finns i avsnittet fel sökning.

5. När den inledande synkroniseringen har slutförts skrivs en gransknings sammanfattnings rapport på fliken **etablering** , som visas nedan.
   > [!div class="mx-imgBorder"]
   > ![Förlopps indikator för etablering](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)



## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar

* **Frågor om lösnings kapacitet**
  * [Hur ställer lösningen in lösen ordet för det nya användar kontot i Active Directory när en ny anställd bearbetas från arbets dagen?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Har lösningen stöd för att skicka e-postmeddelanden när etablerings åtgärderna har slutförts?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Hur gör jag för att hantera leverans av lösen ord för nya anställningar och på ett säkert sätt förse en mekanism med att återställa sitt lösen ord?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Använder Solution cache arbets dagar användar profiler i Azure AD-molnet eller på etablerings agent skiktet?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Har lösningen stöd för att tilldela användare lokala AD-grupper till användaren?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Vilka API: er för Workday använder lösningen för att fråga och uppdatera arbets dag profiler?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Kan jag konfigurera min Workday HCM-klient med två Azure AD-klienter?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Varför "Workday till Azure AD"-appen för användar etablering stöds inte om vi har distribuerat Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Hur gör jag för att föreslå förbättringar eller begär nya funktioner relaterade till Workday och Azure AD-integrering?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Tillhandahålla agent frågor**
  * [Vad är en GA-version av etablerings agenten?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Hur gör jag för att känner du till versionen av etablerings agenten?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Push-överför Microsoft automatiskt Provisioning agent-uppdateringar?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Kan jag installera etablerings agenten på samma server som kör Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Hur gör jag för att konfigurerar du etablerings agenten så att den använder en proxyserver för utgående HTTP-kommunikation?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hur gör jag för att se till att etablerings agenten kan kommunicera med Azure AD-klienten och inga brand väggar blockerar portar som krävs av agenten?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hur gör jag för att den domän som är kopplad till min etablerings agent?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Hur gör jag för att avinstallera etablerings agenten?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Mappning och konfigurations frågor för Workday till AD-attribut**
  * [Hur gör jag för att säkerhetskopiera eller exportera en arbets kopia av mappningen och schemat för min Workday-etablering](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Jag har anpassade attribut i Workday och Active Directory. Hur gör jag för att konfigurerar du lösningen så att den fungerar med mina anpassade attribut?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Kan jag etablera användarens foto från Workday för att Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Hur gör jag för att synkronisera mobil nummer från Workday baserat på användar medgivande för offentlig användning?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Hur gör jag för att format visar namn i AD baserat på användarens attribut för avdelning/land/ort och hanterar regionala varianser?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Hur kan jag använda SelectUniqueValue för att generera unika värden för samAccountName-attribut?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Hur gör jag för att ta bort tecken med dia kritiska tecken och konvertera dem till vanliga engelska alfabetet?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Frågor om lösnings kapacitet

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Hur ställer lösningen in lösen ordet för det nya användar kontot i Active Directory när en ny anställd bearbetas från arbets dagen?

När den lokala etablerings agenten tar emot en begäran om att skapa ett nytt AD-konto genererar det automatiskt ett komplext slumpmässigt lösen ord som utformats för att uppfylla kraven på lösen ords komplexitet som definierats av AD-servern och anger detta för användarobjektet. Det här lösen ordet loggas inte var som helst.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Har lösningen stöd för att skicka e-postmeddelanden när etablerings åtgärderna har slutförts?

Nej, det finns inte stöd för att skicka e-postaviseringar när du har slutfört etablerings åtgärder i den aktuella versionen.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Hur gör jag för att hantera leverans av lösen ord för nya anställningar och på ett säkert sätt förse en mekanism med att återställa sitt lösen ord?

Ett av de sista stegen som ingår i den nya AD-konto etableringen är leveransen av det tillfälliga lösen ordet som tilldelats användarens AD-konto. Många företag använder fortfarande det traditionella sättet att leverera det tillfälliga lösen ordet till användarens chef, som sedan går över lösen ordet till den nya anställningen/den andra personen. Den här processen har en säkerhets fel och det finns ett alternativ som kan användas för att implementera en bättre metod med Azure AD-funktioner.

Som en del av anställnings processen kör HR-teamen vanligt vis en bakgrunds kontroll och Undersök det mobila numret för den nya anställningen. Med hjälp av integreringen arbets dag till AD User Provisioning kan du bygga vidare på detta faktum och distribuera en funktion för återställning av lösen ord för användaren dag 1. Detta åstadkommer du genom att sprida attributet "Mobile Number" för den nya anställningen från Workday till AD och sedan från AD till Azure AD med hjälp av Azure AD Connect. När "mobiltelefon numret" finns i Azure AD kan du aktivera [lösen ords återställning via självbetjäning (SSPR)](../authentication/howto-sspr-authenticationdata.md) för användarens konto, så att en ny anställning kan använda det registrerade och verifierade mobiltelefon numret för autentisering på dag 1.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Använder Solution cache arbets dagar användar profiler i Azure AD-molnet eller på etablerings agent skiktet?

Nej, lösningen upprätthåller inte ett cacheminne för användar profiler. Azure AD Provisioning-tjänsten fungerar bara som en data processor, läser in data från Workday och skriver till mål Active Directory eller Azure AD. Se avsnittet [Hantera person uppgifter](#managing-personal-data) för information som rör användar sekretess och data lagring.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Har lösningen stöd för att tilldela användare lokala AD-grupper till användaren?

Den här funktionen stöds inte för närvarande. Rekommenderad lösning är att distribuera ett PowerShell-skript som frågar Microsoft Graph API-slutpunkten för [Gransknings logg data](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) och använder det för att utlösa scenarier som till exempel grupp tilldelning. Det här PowerShell-skriptet kan kopplas till en Schemaläggaren och distribueras i samma ruta som kör etablerings agenten.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Vilka API: er för Workday använder lösningen för att fråga och uppdatera arbets dag profiler?

Lösningen använder för närvarande följande API: er för arbets dagar:

* **URL-formatet för URL: en för webb tjänster för Workday** som används i avsnittet admin- **AUTENTISERINGSUPPGIFTER** bestämmer vilken API-version som används för Get_Workers
  * Om URL-formatet är: https:// \# \# \# \# \. Workday \. com/CCX/service/tenantName används API v 21.1. 
  * Om URL-formatet är: https:// \# \# \# \# \. Workday \. com/CCX/service/tenantName/personal- \_ resurser används API v-21.1 
  * Om URL-formatet är: https:// \# \# \# \# \. Workday \. com/CCX/service/tenantName/personal \_ /v \# \# \. \# , används den angivna API-versionen. (Exempel: om v-34.0 anges används den.)  
   
* Funktionen för e-posttillbakaskrivning i Workday använder Change_Work_Contact_Information (v 30.0) 
* Funktionen för tillbakaskrivning av workday-användare använder Update_Workday_Account (v 31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Kan jag konfigurera min Workday HCM-klient med två Azure AD-klienter?

Ja, den här konfigurationen stöds. Här följer de steg som krävs för att konfigurera det här scenariot:

* Distribuera etablerings agent #1 och registrera den med Azure AD-klienten #1.
* Distribuera etablerings Agent #2 och registrera den med Azure AD-klienten #2.
* Konfigurera varje agent med domän (er) baserat på underordnade domäner som varje etablerings agent ska hantera. En agent kan hantera flera domäner.
* I Azure Portal ställer du in arbets dagen till AD User Provisioning-appen i varje klient organisation och konfigurerar den med respektive domän.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Varför "Workday till Azure AD"-appen för användar etablering stöds inte om vi har distribuerat Azure AD Connect?

När Azure AD används i hybrid läge (där det innehåller en blandning av moln + lokala användare) är det viktigt att ha en tydlig definition av "källan till auktoritet". Vanligt vis kräver hybrid scenarier distribution av Azure AD Connect. När Azure AD Connect distribueras är den lokala AD källan till auktoriteten. Att introducera en arbets dag till Azure AD-anslutning i blandningen kan leda till en situation där Workday-attributvärden kan skriva över värdena som angetts av Azure AD Connect. Det går därför inte att använda "etablerings appen för" Workday to Azure AD "när Azure AD Connect är aktive rad. I sådana situationer rekommenderar vi att du använder "arbets dag till AD User"-etablering för att hämta användare till lokala AD och sedan synkronisera dem till Azure AD med hjälp av Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Hur gör jag för att föreslå förbättringar eller begär nya funktioner relaterade till Workday och Azure AD-integrering?

Din feedback är mycket värdefull eftersom det hjälper oss att ange riktning för framtida versioner och förbättringar. Vi välkomnar all feedback och uppmanar dig att skicka in din idé-eller förbättrings förslag i [feedback-forumet för Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Om du vill ha mer information om arbets dags integrering väljer du kategorin *SaaS program* och söker med hjälp av nyckelordet *Workday* för att hitta befintlig feedback som rör arbets dagen.

> [!div class="mx-imgBorder"]
> ![UserVoice SaaS-appar](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![UserVoice-arbetsdag](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

När du föreslår en ny idé bör du kontrol lera om någon annan redan har föreslagit en liknande funktion. I så fall kan du rösta på funktionen eller förbättrings förfrågan. Du kan också lämna en kommentar om ditt speciella användnings fall för att visa ditt stöd för idén och demonstrera hur funktionen kommer att vara värdefull för dig.

### <a name="provisioning-agent-questions"></a>Tillhandahålla agent frågor

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Vad är en GA-version av etablerings agenten?

Se [Azure AD Connect etablerings agent: versions historik](../app-provisioning/provisioning-agent-release-version-history.md) för den senaste ga-versionen av etablerings agenten.  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Hur gör jag för att känner du till versionen av etablerings agenten?

* Logga in på Windows Server där etablerings agenten är installerad.
* Gå till **kontroll panelen**  ->  **Avinstallera eller ändra en program** meny
* Leta efter den version som motsvarar posten **Microsoft Azure AD ansluta etablerings agenten**

  >[!div class="mx-imgBorder"]
  >![Azure-portalen](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Push-överför Microsoft automatiskt Provisioning agent-uppdateringar?

Ja, Microsoft uppdaterar automatiskt etablerings agenten om uppdaterings agenten för Windows-tjänsten **Microsoft Azure AD Connect-agenten** körs.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Kan jag installera etablerings agenten på samma server som kör Azure AD Connect?

Ja, du kan installera etablerings agenten på samma server som kör Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Vid konfigurationen av Provisioning-agenten uppmanas du att ange autentiseringsuppgifter för Azure AD admin. Lagrar agenten autentiseringsuppgifterna lokalt på servern?

Under konfigurationen uppmanas etablerings agenten för Azure AD admin-autentiseringsuppgifter bara att ansluta till din Azure AD-klient. De lagrar inte autentiseringsuppgifterna lokalt på servern. Det behåller dock de autentiseringsuppgifter som används för att ansluta till den lokala *Active Directory-domänen* i ett lokalt Windows-lösenord.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hur gör jag för att konfigurerar du etablerings agenten så att den använder en proxyserver för utgående HTTP-kommunikation?

Etablerings agenten stöder användning av utgående proxy. Du kan konfigurera den genom att redigera agentens konfigurations fil **C:\Program Files\Microsoft Azure AD Connect etablerings Agent\AADConnectProvisioningAgent.exe.config**. Lägg till följande rader i den i slutet av filen precis innan den avslutande `</configuration>` taggen.
Ersätt variablerna [proxy-server] och [proxy-port] proxyserverns namn och portvärden.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Hur gör jag för att se till att etablerings agenten kan kommunicera med Azure AD-klienten och inga brand väggar blockerar portar som krävs av agenten?

Du kan också kontrol lera om alla [nödvändiga portar](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports) är öppna.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Kan en etablerings agent konfigureras för att etablera flera AD-domäner?

Ja, en etablerings agent kan konfigureras för att hantera flera AD-domäner så länge agenten har detaljerad information för respektive domän kontrol Lanterna. Microsoft rekommenderar att du konfigurerar en grupp av 3 etablerings agenter som betjänar samma uppsättning AD-domäner för att säkerställa hög tillgänglighet och ge stöd för redundans.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hur gör jag för att den domän som är kopplad till min etablerings agent?

* Hämta *klient-ID: t* för din Azure AD-klient från Azure Portal.
* Logga in på Windows Server som kör etablerings agenten.
* Öppna PowerShell som Windows-administratör.
* Ändra till den katalog som innehåller registrerings skripten och kör följande kommandon och Ersätt \[ klient-ID- \] parametern med värdet för klient-ID: t.

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* I listan över agenter som visas – kopierar du värdet för `id` fältet från den resurs vars *resourceName* motsvarar ditt AD-domännamn.
* Klistra in ID-värdet i det här kommandot och kör kommandot i PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Kör guiden agent konfiguration igen.
* Andra agenter som tidigare var tilldelade till den här domänen måste konfigureras om.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Hur gör jag för att avinstallera etablerings agenten?

* Logga in på Windows Server där etablerings agenten är installerad.
* Gå till **kontroll panelen**  ->  **Avinstallera eller ändra en program** meny
* Avinstallera följande program:
  * Microsoft Azure AD ansluta etablerings agent
  * Microsoft Azure AD ansluta Agent uppdatering
  * Microsoft Azure AD ansluta Agent paket för etablering

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Mappning och konfigurations frågor för Workday till AD-attribut

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Hur gör jag för att säkerhetskopiera eller exportera en arbets kopia av mappningen och schemat för min Workday-etablering

Du kan använda Microsoft Graph API för att exportera din användar etablerings konfiguration för Workday. Se stegen i avsnittet [Exportera och importera konfigurationen av konfigurations inställningarna för din Workday-användarkonto](#exporting-and-importing-your-configuration) för information.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Jag har anpassade attribut i Workday och Active Directory. Hur gör jag för att konfigurerar du lösningen så att den fungerar med mina anpassade attribut?

Lösningen stöder anpassade Workday-och Active Directory-attribut. Om du vill lägga till dina anpassade attribut i mappnings schemat öppnar du bladet **Mappning av attribut** och bläddrar ned för att expandera avsnittet **Visa avancerade alternativ**. 

![Redigera attributlistan](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Om du vill lägga till dina anpassade Workday-attribut väljer du alternativet *Redigera attributlistan för arbets dag* och lägger till dina anpassade AD-attribut genom att välja alternativet *Redigera attributlistan för lokalt Active Directory*.

Se även:

* [Anpassa listan över användarattribut för Workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Hur gör jag för att konfigurera lösningen till att bara uppdatera attribut i AD baserat på arbets dagar ändringar och inte skapa några nya AD-konton?

Den här konfigurationen kan uppnås genom att ange **mål objekts åtgärder** på bladet **attribut mappningar** enligt nedan:

![Uppdatera åtgärd](./media/workday-inbound-tutorial/wd_target_update_only.png)

Markera kryss rutan uppdatera för endast uppdaterings åtgärder som ska flödas från Workday till AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Kan jag etablera användarens foto från Workday för att Active Directory?

Lösningen stöder för närvarande inte inställning av binära attribut som *ThumbnailPhoto* och *jpegPhoto* i Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Hur gör jag för att synkronisera mobil nummer från Workday baserat på användar medgivande för offentlig användning?

* Gå till bladet "etablering" i din arbets dag etablerings app.
* Klicka på mappningar för attribut 
* Under **mappningar**väljer **du synkronisera arbets dagar arbetare till lokalt Active Directory** (eller **Synkronisera arbets dagar arbetare till Azure AD**).
* Rulla nedåt på sidan mappningar av attribut och markera kryss rutan Visa avancerade alternativ.  Klicka på **Redigera attributlistan för arbets dag**
* På bladet som öppnas letar du upp attributet "Mobile" och klickar på raden så att du kan redigera GDPR för **API-uttryck** ![](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Ersätt **API-uttrycket** med följande nya uttryck, som bara hämtar arbetsmobiltelefon numret om "offentlig användnings flagga" är inställt på "true" i Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Spara attributlistan.
* Spara mappningen av attribut.
* Rensa aktuell status och starta om den fullständiga synkroniseringen.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Hur gör jag för att format visar namn i AD baserat på användarens attribut för avdelning/land/ort och hanterar regionala varianser?

Det är ett vanligt krav att konfigurera attributet *DisplayName* i AD så att det även ger information om användarens avdelning och land/region. T. ex. om John Svensson arbetar i marknadsförings avdelningen i USA kanske du vill att hans *visnings* namn ska visas som *Smith, John (Marketing-US)*.

Här kan du hantera sådana krav för att skapa *CN* eller *DisplayName* för att inkludera attribut som företag, affär senhet, stad eller land/region.

* Varje Workday-attribut hämtas med ett underliggande XPATH API-uttryck, som kan konfigureras i  **Attribute Mapping-> Advanced section-> redigera attributlistan för arbets dagar**. Här är standard-XPATH API-uttrycket för *PreferredFirstName*-, *PreferredLastName*-, *Company* -och *SupervisoryOrganization* -attribut.

     | Workday-attribut | API XPATH-uttryck |
     | ----------------- | -------------------- |
     | PreferredFirstName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: First_Name/text () |
     | PreferredLastName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: Last_Name/text () |
     | Företag | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' Company ']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' övervakande ']/WD: ORGANIZATION_NAME/text () |
  
   Bekräfta med ditt Workday-team att API-uttrycket ovan är giltigt för din arbets grupps klient konfiguration. Om det behövs kan du redigera dem enligt beskrivningen i avsnittet [Anpassa listan med användar](#customizing-the-list-of-workday-user-attributes)-och Workday-användarattribut.

* På samma sätt hämtas lands-och region information som finns i Workday med följande XPATH: *WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference*

     Det finns fem lands-eller regionbaserade attribut som är tillgängliga i avsnittet för attributet för Workday-attribut.

     | Workday-attribut | API XPATH-uttryck |
     | ----------------- | -------------------- |
     | CountryReference | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = ' ISO_3166-1_Alpha-3_Code ']/text () |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = ' ISO_3166-1_Numeric-3_Code ']/text () |
     | CountryReferenceTwoLetter | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = ' ISO_3166-1_Alpha-2_Code ']/text () |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Bekräfta med ditt Workday-team att API-uttrycken ovan är giltiga för din konfiguration av din arbets dag. Om det behövs kan du redigera dem enligt beskrivningen i avsnittet [Anpassa listan med användar](#customizing-the-list-of-workday-user-attributes)-och Workday-användarattribut.

* För att bygga uttryck för mappning av rätt attribut, identifiera vilket Workday-attribut "auktoritativt" representerar användarens förnamn, efter namn, land/region och avdelning. Anta att attributen är *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* respektive *SupervisoryOrganization* . Du kan använda detta för att skapa ett uttryck för attributet AD *DisplayName* på följande sätt för att få ett visnings namn som *Smith, John (Marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    När du har rätt uttryck redigerar du mappnings tabellen för attribut och ändrar attributet *DisplayName* -attribut så som visas nedan:   ![ DisplayName-mappning](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Utöka exemplet ovan, låt oss säga att du vill konvertera Orts namn från *arbets dagar till* stenografiska värden och sedan använda det för att bygga visnings namn som *Smith, John (Chi)* eller *berg, Jane (NYC)*. sedan kan det här resultatet uppnås med ett switch-uttryck med attributet Workday som Determinant variabel.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Se även:
  * [Syntax för växel funktion](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Syntax för kopplings funktion](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Lägg till Function-syntax](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hur kan jag använda SelectUniqueValue för att generera unika värden för samAccountName-attribut?

Anta att du vill generera unika värden för *sAMAccountName* -attribut med en kombination av attributen *FirstName* och *LastName* från Workday. Nedan anges ett uttryck som du kan börja med:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Så här fungerar uttrycket ovan: om användaren är John Smith försöker den först skapa JSmith, om JSmith redan finns, genererar den JoSmith, om det finns, genereras JohSmith. Uttrycket säkerställer också att värdet som genereras uppfyller begränsningen för längd begränsning och specialtecken som är associerade med *sAMAccountName*.

Se även:

* [Mid Function-syntax](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Byt syntax för funktion](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Syntax för SelectUniqueValue-funktion](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hur gör jag för att ta bort tecken med dia kritiska tecken och konvertera dem till vanliga engelska alfabetet?

Använd funktionen [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) för att ta bort specialtecken i förnamn och efter namn på användaren, medan du konstruerar e-postadressen eller CN-värdet för användaren.

## <a name="troubleshooting-tips"></a>Felsökningstips

Det här avsnittet innehåller information om hur du felsöker etablerings problem med din arbets dags integrering med hjälp av Azure AD audit-loggar och Windows Server Loggboken loggar. Den bygger vidare på allmänna fel söknings steg och koncept som samlas in i [självstudien: rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md)

I det här avsnittet beskrivs följande aspekter av fel sökning:

* [Konfigurera Windows Loggboken för agent fel sökning](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Konfigurera Azure Portal gransknings loggar för fel sökning av tjänst](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Förstå loggar för skapande åtgärder i AD-användarkontot](#understanding-logs-for-ad-user-account-create-operations)
* [Förstå loggar för uppdaterings åtgärder i Manager](#understanding-logs-for-manager-update-operations)
* [Lösa vanliga fel som har påträffats](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Konfigurera Windows Loggboken för agent fel sökning

* Logga in på den Windows Server-dator där etablerings agenten har distribuerats
* Öppna **Windows Server Loggboken** Desktop-appen.
* Välj **Windows-loggar > programmet**.
* Använd den **aktuella loggen för filter...** alternativ för att visa alla händelser som loggats under AAD-källan **. Anslut. ProvisioningAgent** och undanta händelser med händelse-ID "5" genom att ange filtret "-5" som visas nedan.

  ![Windows Loggboken](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Klicka på **OK** och sortera vyn resultat efter **datum och tid** .

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Konfigurera Azure Portal gransknings loggar för fel sökning av tjänst

* Starta [Azure Portal](https://portal.azure.com)och navigera till avsnittet **gransknings loggar** i ditt etablerings program för arbets dag.
* Använd knappen **kolumner** på sidan gransknings loggar om du bara vill visa följande kolumner i vyn (datum, aktivitet, status, status orsak). Den här konfigurationen garanterar att du bara fokuserar på data som är relevanta för fel sökning.

  ![Gransknings logg kolumner](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Använd fråge parametrarna för **mål** och **datum intervall** för att filtrera vyn. 
  * Ange **mål** Frågeparametern till "Worker ID" eller "anställnings-ID" för arbetsobjektet arbets dag.
  * Ange **datum intervallet** till en lämplig tids period som du vill undersöka för fel eller problem med etableringen.

  ![Gransknings logg filter](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Förstå loggar för skapande åtgärder i AD-användarkontot

När en ny anställning i arbets dagen identifieras (vi säger med medarbetar-ID *21023*) försöker Azure AD Provisioning-tjänsten att skapa ett nytt AD-användarkonto för arbets tagaren och i processen skapas fyra Gransknings logg poster enligt beskrivningen nedan:

  [![Skapa Ops för gransknings logg](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

När du klickar på någon av Gransknings logg posterna öppnas sidan **aktivitets information** . Så här visas sidan **aktivitets information** för varje logg post typ.

* **Import** post för Workday: den här logg posten visar arbets uppgifter som hämtats från arbets dagen. Använd informationen i avsnittet *Ytterligare information* i logg posten för att felsöka problem med att hämta data från Workday. En exempel post visas nedan tillsammans med pekare om hur du tolkar varje fält.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD-import** post: den här logg posten visar information om kontot som hämtats från AD. Eftersom det inte finns något AD-konto när den första användaren skapas, indikerar *aktivitets status orsaken* att inget konto med det matchande ID-attributvärdet påträffades i Active Directory. Använd informationen i avsnittet *Ytterligare information* i logg posten för att felsöka problem med att hämta data från Workday. En exempel post visas nedan tillsammans med pekare om hur du tolkar varje fält.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Om du vill hitta etablerings agentens logg poster som motsvarar denna AD import-åtgärd öppnar du Windows Loggboken loggar och använder **Sök...** meny alternativ för att hitta logg poster som innehåller det matchande ID/koppla attributets egenskaps värde (i det här fallet *21023*).

  ![Hitta](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Leta efter posten med *händelse-ID = 9*, vilket ger dig det LDAP-sökfilter som används av agenten för att hämta AD-kontot. Du kan kontrol lera om det här är rätt Sök filter för att hämta unika användar poster.

  ![LDAP-sökning](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Posten som omedelbart följer med *händelse-ID = 2* fångar resultatet av Sök åtgärden och om den returnerade några resultat.

  ![LDAP-resultat](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Åtgärds** post för Synkroniseringsregel: den här logg posten visar resultatet av attribut mappnings reglerna och konfigurerade omfångs filter, tillsammans med den etablerings åtgärd som ska vidtas för att bearbeta inkommande arbets dag-händelse. Använd informationen i avsnittet *Ytterligare information* i logg posten för att felsöka problem med synkroniseringsåtgärden. En exempel post visas nedan tillsammans med pekare om hur du tolkar varje fält.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Om det finns problem med mappnings uttryck för attribut eller om inkommande data för Workday har problem (till exempel: tomt eller null-värde för obligatoriska attribut), kommer du att se ett fel i det här steget med felkoden ger information om felet.

* **AD-export** post: den här logg posten visar resultatet av åtgärden för att skapa AD-konto tillsammans med de attributvärden som angavs i processen. Använd informationen i avsnittet *Ytterligare information* i logg posten för att felsöka problem med åtgärden Skapa åtgärd i kontot. En exempel post visas nedan tillsammans med pekare om hur du tolkar varje fält. I avsnittet "Ytterligare information" är "EventName" inställd på "EntryExportAdd", "JoiningProperty" är inställt på värdet för attributet matchande ID, "SourceAnchor" är inställt på WorkdayID (WID) som är associerad med posten och "TargetAnchor" anges till värdet för AD "ObjectGuid"-attributet för den nya användaren. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Om du vill hitta etablerings agentens logg poster som motsvarar den här AD-export åtgärden öppnar du Windows Loggboken loggar och använder **Sök...** meny alternativ för att hitta logg poster som innehåller det matchande ID/koppla attributets egenskaps värde (i det här fallet *21023*).  

  Sök efter en HTTP POST-post som motsvarar tidsstämpeln för export åtgärden med *händelse-ID = 2*. Den här posten kommer att innehålla de attributvärden som skickas av etablerings tjänsten till etablerings agenten.

  [![SCIM Lägg till](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Direkt efter händelsen ovan bör det finnas en annan händelse som fångar svaret från åtgärden skapa AD-konto. Den här händelsen returnerar det nya objectGuid som skapats i AD och anges som TargetAnchor-attribut i etablerings tjänsten.

  [![SCIM Lägg till](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Förstå loggar för uppdaterings åtgärder i Manager

Attributet Manager är ett referens-attribut i AD. Etablerings tjänsten anger inte attributet Manager som en del av åtgärden för att skapa användare. I stället anges attributet Manager som en del av en *uppdaterings* åtgärd efter att AD-kontot har skapats för användaren. Om du expanderar exemplet ovan kan vi säga att en ny anställning med anställnings-ID "21451" har Aktiver ATS i Workday och att den nya anställnings chefen (*21023*) redan har ett AD-konto. I det här scenariot visar gransknings loggarna för användare 21451 5 poster.

  [![Uppdatering av Manager](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

De första fyra posterna liknar de som vi utforskar som en del av åtgärden för att skapa användare. Den femte posten är den export som är associerad med uppdatering av Manager-attribut. Logg posten visar resultatet av uppdaterings åtgärden för AD-konto Manager, som utförs med hjälp av hanterarens *objectGUID* -attribut.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Lösa vanliga fel som har påträffats

I det här avsnittet beskrivs vanliga fel med användar etablering för arbets dagar och hur du kan lösa det. Felen grupperas på följande sätt:

* [Fel vid etablering av agent](#provisioning-agent-errors)
* [Anslutningsfel](#connectivity-errors)
* [Fel när AD-användarkonto skapades](#ad-user-account-creation-errors)
* [Uppdaterings fel för AD-användarkonto](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Fel vid etablering av agent

|#|Felscenario |Troliga orsaker|Rekommenderad lösning|
|--|---|---|---|
|1.| Det gick inte att installera etablerings agenten med fel meddelandet:  *Det gick inte att starta tjänsten Microsoft Azure AD ansluta etablerings agenten (AADConnectProvisioningAgent). Kontrol lera att du har behörighet att starta systemet.* | Det här felet visas vanligt vis om du försöker installera etablerings agenten på en domänkontrollant och grup princip förhindrar att tjänsten startar.  Den visas också om du har en tidigare version av agenten som körs och du inte har avinstallerat den innan du påbörjar en ny installation.| Installera etablerings agenten på en server som inte är en DOMÄNKONTROLLANT. Se till att tidigare versioner av agenten avinstalleras innan du installerar den nya agenten.|
|2.| Windows-tjänsten Microsoft Azure AD ansluta etablerings agenten är i *Start* läge och växlar inte till *körnings* tillstånd. | Som en del av installationen skapar agent guiden ett lokalt konto (**NT- \\ AADConnectProvisioningAgent**) på servern och det är det inloggnings konto som används för att starta tjänsten. Om en säkerhets princip på Windows Server förhindrar att lokala konton kör tjänsterna, kommer du att stöta på det här felet. | Öppna *konsolen tjänster*. Högerklicka på Windows-tjänsten Microsoft Azure AD Anslut etablerings agenten och på fliken inloggning anger du kontot för en domän administratör som ska köra tjänsten. Starta om tjänsten. |
|3.| När du konfigurerar etablerings agenten med AD-domänen i steget *anslut Active Directory*, tar det längre tid att läsa in AD-schemat i guiden och tids gränsen går ut. | Det här felet visas vanligtvis om det inte går att kontakta AD-domänkontrollantservern på grund av problem med brandväggen. | På skärmen *anslut Active Directory* guiden, medan du anger autentiseringsuppgifterna för din AD-domän, finns ett alternativ som kallas *Välj domänkontrollantens prioritet*. Använd det här alternativet för att välja en domänkontrollant som finns på samma plats som agent servern och se till att det inte finns några brand Väggs regler som blockerar kommunikationen. |

#### <a name="connectivity-errors"></a>Anslutningsfel

Om etablerings tjänsten inte kan ansluta till Workday eller Active Directory kan det leda till att etableringen försätts i karantän. Använd tabellen nedan för att felsöka anslutnings problem.

|#|Felscenario |Troliga orsaker|Rekommenderad lösning|
|--|---|---|---|
|1.| När du klickar på **Testa anslutning**får du ett fel meddelande: *det uppstod ett fel när du anslöt till Active Directory. Kontrol lera att den lokala etablerings agenten körs och att den är konfigurerad med rätt Active Directory domän.* | Det här felet visas vanligt vis om etablerings agenten inte körs eller om en brand vägg blockerar kommunikationen mellan Azure AD och etablerings agenten. Du kan också se det här felet om domänen inte har kon figurer ATS i agent guiden. | Öppna konsolen *tjänster* på Windows-servern för att bekräfta att agenten körs. Öppna guiden för etablerings agent och bekräfta att rätt domän är registrerad hos agenten.  |
|2.| Etablerings jobbet går i karantäns tillstånd över helgerna (fre – söt) och vi får ett e-postmeddelande om att det finns ett fel i synkroniseringen. | En av de vanligaste orsakerna till felet är planerade Workday-driftstopp. Om du använder en klient för implementering av Workday ska du notera att Workday har schemalagt driftstopp för sina implementeringsklienter under helger (vanligtvis från fredag kväll till lördag morgon). Under den perioden kan Workday-etableringsappar försättas i karantäntillstånd eftersom det inte går att ansluta till Workday. Workday återfår sitt normala tillstånd när Workday-implementeringsklienten är online igen. I sällsynta fall kan du också se det här felet om lösenordet för integreringssystemanvändaren har ändrats på grund av uppdatering av klienten eller om kontot är låst eller har upphört att gälla. | Kontakta din Workday-administratör eller integreringspartner för att höra efter när Workday schemalägger driftstopp. Sedan kan du ignorera varningsmeddelanden under driftstoppsperioden och få en bekräftelse om tillgänglighet när Workday-instansen är online igen.  |


#### <a name="ad-user-account-creation-errors"></a>Fel när AD-användarkonto skapades

|#|Felscenario |Troliga orsaker|Rekommenderad lösning|
|--|---|---|---|
|1.| Export åtgärds fel i gransknings loggen med meddelande *felet: OperationsError-SvcErr: ett åtgärds fel uppstod. Ingen överordnad referens har kon figurer ATS för katalog tjänsten. Katalog tjänsten kan därför inte utfärda hänvisningar till objekt utanför den här skogen.* | Det här felet visas vanligt vis om *Active Directory containerns* organisationsenhet inte är korrekt inställd eller om det finns problem med uttrycks mappningen som används för *parentDistinguishedName*. | Kontrol lera *Active Directory containerns* ou-parameter för skrivningar. Om du använder *parentDistinguishedName* i attributmappningen kontrollerar du att det alltid utvärderas till en känd container i AD-domänen. Kontrol lera *export* händelsen i gransknings loggarna för att se det genererade värdet. |
|2.| Det gick inte att exportera åtgärds fel i gransknings loggen med felkoden: *SystemForCrossDomainIdentityManagementBadResponse* och meddelande *fel: ConstraintViolation-AtrErr: ett värde i begäran är ogiltigt. Ett värde för attributet fanns inte i det acceptabla värde intervallet. \nError information: CONSTRAINT_ATT_TYPE-Company*. | Även om det här felet är särskilt för attributet *Company* kan du se det här felet för andra attribut som även *CN* . Det här felet visas på grund av en Tvingad schema begränsning i AD. Som standard har attribut som *företag* och *CN* i AD en övre gräns på 64 tecken. Om värdet som kommer från Workday är mer än 64 tecken visas det här fel meddelandet. | Kontrol lera *export* händelsen i gransknings loggarna för att se värdet för attributet som rapporteras i fel meddelandet. Överväg att trunkera värdet som kommer från Workday med funktionen [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) eller att ändra mappningarna till ett AD-attribut som inte har liknande längd begränsningar.  |

#### <a name="ad-user-account-update-errors"></a>Uppdaterings fel för AD-användarkonto

Under uppdaterings processen för AD-användarkonto läser etablerings tjänsten information från både Workday och AD, kör attribut mappnings reglerna och avgör om några ändringar måste börja gälla. En uppdaterings händelse utlöses därför. Om något av dessa steg påträffar ett haveri loggas det i gransknings loggarna. Använd tabellen nedan för att felsöka vanliga uppdaterings fel.

|#|Felscenario |Troliga orsaker|Rekommenderad lösning|
|--|---|---|---|
|1.| Åtgärds fel i synkroniseringsregeln i gransknings loggen med meddelandet *EventName = EntrySynchronizationError och ErrorCode = EndpointUnavailable*. | Det här felet visas om etablerings tjänsten inte kan hämta användar profil data från Active Directory på grund av ett bearbetnings fel som påträffats av den lokala etablerings agenten. | Kontrol lera etablerings agentens Loggboken loggar för fel händelser som indikerar problem med Läs åtgärden (filtrera efter händelse-ID #2). |
|2.| Attributet Manager i AD uppdateras inte för vissa användare i AD. | Den mest sannolika orsaken till det här felet är om du använder omfångs regler och användarens chef inte ingår i omfånget. Du kan också stöta på det här problemet om hanterarens matchande ID-attribut (t. ex. Anställningsnr) inte hittas i mål-AD-domänen eller inte har angetts till rätt värde. | Granska omfångs filtret och Lägg till Manager-användaren i omfånget. Kontrol lera chefens profil i AD för att se till att det finns ett värde för attributet matchande ID. |

## <a name="managing-your-configuration"></a>Hantera konfigurationen

I det här avsnittet beskrivs hur du kan utöka, anpassa och hantera din arbets Dagbaserade användar etablerings konfiguration. Den omfattar följande ämnen:

* [Anpassa listan över användarattribut för Workday](#customizing-the-list-of-workday-user-attributes)  
* [Exportera och importera konfigurationen](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Anpassa listan över användarattribut för Workday

Program för etablering av arbets dagar för Active Directory och Azure AD innehåller båda en standard lista med användarattribut som du kan välja mellan. Listorna är dock inte omfattande. Workday har stöd för många hundratals möjliga användarattribut, som antingen kan vara standard eller unika för din arbets enhets klient.

Azure AD Provisioning-tjänsten har stöd för möjligheten att anpassa dina list-eller Workday-attribut så att de innehåller attribut som exponeras i [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) -funktionen för personal-API: et.

För att göra den här ändringen måste du använda [Workday Studio](https://community.workday.com/studio-download) för att extrahera de XPath-uttryck som representerar de attribut som du vill använda och sedan lägga till dem i etablerings konfigurationen med hjälp av redigeraren för redigeraren i Azure Portal.

**Hämta ett XPath-uttryck för ett användar-attribut för en arbets dag:**

1. Hämta och installera [Workday Studio](https://community.workday.com/studio-download). Du behöver ett konto för Workday-grupp för att komma åt installations programmet.

2. Hämta **Human_Resources** WSDL-filen för Workday som är unik för den WWS API-version som du planerar att använda från [arbets platsens webb tjänst katalog](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)

3. Starta Workday Studio.

4. I kommando fältet väljer du arbets  **dag > test-webbtjänst i testare** .

5. Välj **extern**och välj den Human_Resources WSDL-fil som du laddade ned i steg 2.

    ![Workday-Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Ange fältet **plats** som `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` , men Ersätt "implementering-CC" med din faktiska instans typ och "TENANT" med ditt riktiga klient namn.

7. Ange **åtgärd** för att **Get_Workers**

8.    Klicka på den lilla **Konfigurera** -länken under fönstret för svar/svar för att ange dina autentiseringsuppgifter för din arbets dag. Kontrol lera **autentiseringen**och ange sedan användar namn och lösen ord för ditt system konto för ditt system konto för system. Se till att formatera användar namnet som \@ klient organisation och lämna alternativet **WS-Security UsernameToken** markerat.
   ![Workday-Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Välj **OK**.

10. I rutan **begär** klistrar du in i XML-filen nedan. Ange **Employee_ID** till anställnings-ID för en riktig användare i din arbets grupps klient. Ange **WD: version** till den version av WWS som du planerar att använda. Välj en användare som har attributet ifylld som du vill extrahera.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Klicka på **skicka begäran** (grön pil) för att köra kommandot. Om det lyckas bör svaret visas i **svars** fönstret. Kontrol lera svaret för att se till att det har data för det användar-ID som du angav och inte ett fel.

12. Om det lyckas kopierar du XML-filen från **svars** fönstret och sparar den som en XML-fil.

13. I kommando fältet i Workday Studio väljer du **fil > öppna fil...** och öppnar XML-filen som du sparade. Den här åtgärden öppnar filen i XML-redigeraren för Workday Studio.

    ![Workday-Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. I fil trädet navigerar du genom **/env: kuvert > kuvert: brödtext > WD: Get_Workers_Response > WD: Response_Data > WD: Worker** för att hitta användarens data.

15. Under **WD: Worker**, letar du upp attributet som du vill lägga till och väljer det.

16. Kopiera XPath-uttrycket för det valda attributet från **dokument Sök vägs** fältet.

17. Ta bort **/env: Envelope/miljö: Body/WD: Get_Workers_Response/WD: Response_Data/** prefix från det kopierade uttrycket.

18. Om det sista objektet i det kopierade uttrycket är en nod (exempel: "/WD: Birth_Date"), lägger du till **/text ()** i slutet av uttrycket. Detta är inte nödvändigt om det sista objektet är ett-attribut (exempel: " /@wd: typ").

19. Resultatet bör vara något som liknar `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()` . Det här värdet är det du kopierar till Azure Portal.

**För att lägga till ditt anpassade Workday-användarattribut i etablerings konfigurationen:**

1. Starta [Azure Portal](https://portal.azure.com)och navigera till etablerings avsnittet i ditt Workday-etablerings program, enligt beskrivningen ovan i den här självstudien.

2. Ange **etablerings statusen** till **av**och välj **Spara**. Det här steget hjälper dig att se till att ändringarna börjar gälla endast när du är klar.

3. Under **mappningar**väljer **du synkronisera arbets dagar arbetare till lokalt Active Directory** (eller **Synkronisera arbets dagar arbetare till Azure AD**).

4. Rulla längst ned på nästa skärm och välj **Visa avancerade alternativ**.

5. Välj **Redigera attributlistan för arbets dag**.

    ![Workday-Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Rulla längst ned i attributlistan till den plats där infälten finns.

7. I **namn**anger du ett visnings namn för attributet.

8. I **typ**väljer du typ som motsvarar ditt attribut (**sträng** är vanligast).

9. För **API-uttryck**anger du det XPath-uttryck som du kopierade från Workday Studio. Exempel: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Välj **Lägg till attribut**.

    ![Workday-Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Välj **Spara** ovan och sedan **Ja** i dialog rutan. Stäng skärmen för attributs mappning om den fortfarande är öppen.

12. Tillbaka på fliken huvud **etablering** väljer du **Synkronisera arbets dagar arbetare till lokalt Active Directory** (eller **Synkronisera arbetare till Azure AD**) igen.

13. Välj **Lägg till ny mappning**.

14. Det nya attributet bör nu visas i listan **källattribut** .

15. Lägg till en mappning för det nya attributet efter behov.

16. När du är klar ska du komma ihåg att ange **etablerings status** tillbaka **till och spara** .

### <a name="exporting-and-importing-your-configuration"></a>Exportera och importera konfigurationen

Läs artikeln [Exportera och importera etablerings konfiguration](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Hantera personliga data

Etablerings lösningen för arbets dag för Active Directory kräver att en etablerings Agent installeras på en lokal Windows Server och att den här agenten skapar loggar i Windows-händelseloggen som kan innehålla personliga data beroende på din arbets dag till mappning av AD-attribut. Om du vill följa användar sekretess avtalen kan du se till att inga data bevaras i händelse loggarna efter 48 timmar genom att konfigurera en schemalagd schemalagd aktivitet i Windows för att rensa händelse loggen.

Azure AD Provisioning-tjänsten omfattas av **data processor** kategorin i GDPR-klassificeringen. Som data behandlings pipeline tillhandahåller tjänsten data bearbetnings tjänster för viktiga partner och slut konsumenter. Azure AD Provisioning-tjänsten genererar inte användar data och har ingen oberoende kontroll över vilka personliga data som samlas in och hur de används. Data hämtning, agg regering, analys och rapportering i Azure AD Provisioning-tjänsten baseras på befintliga företags data.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

I samband med datakvarhållning genererar inte Azure AD Provisioning-tjänsten rapporter, utför analyser eller ger insikter mer än 30 dagar. Därför lagrar inte Azure AD Provisioning-tjänsten, bearbetar eller behåller några data längre än 30 dagar. Den här designen är kompatibel med reglerna för GDPR, Microsofts sekretess regler för efterlevnad och Azure AD-datakvarhållning.

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan arbets dagar och Azure Active Directory](workday-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du använder Microsoft Graph API: er för att hantera etablerings konfiguration](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
