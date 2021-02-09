---
title: Självstudie – anpassa mappningar av Azure Active Directory-attribut
description: Lär dig mer om vilka filmappningar för SaaS-appar i Azure Active Directory hur du kan ändra dem så att de passar dina affärs behov.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: kenwith
ms.openlocfilehash: 03eacf4405217ee883689a088499d86d6f2262b5
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99830710"
---
# <a name="tutorial---customize-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Självstudie – anpassa användar etablering attribut-mappningar för SaaS-program i Azure Active Directory

Microsoft Azure AD ger stöd för användar etablering för SaaS-program från tredje part, till exempel Salesforce, G Suite och andra. Om du aktiverar användar etablering för ett SaaS-program från tredje part styr Azure Portal dess attributvärden via attribut-mappningar.

Innan du börjar ska du kontrol lera att du är bekant med koncepten för appars hantering och **enkel Sign-On (SSO)** , kolla in följande länkar:
- [Snabb starts serie för app Management i Azure AD](../manage-apps/view-applications-portal.md)
- [Vad är enkel Sign-On (SSO)?](../manage-apps/what-is-single-sign-on.md)

Det finns en förkonfigurerad uppsättning attribut och attribut mappningar mellan Azure AD-användarkonton och varje SaaS-Apps användar objekt. Vissa appar hanterar andra typer av objekt tillsammans med användare, t. ex. grupper.

Du kan anpassa standardattributen – mappningar efter dina affärs behov. Så du kan ändra eller ta bort befintliga attribut-mappningar eller skapa nya attribut-mappningar.

## <a name="editing-user-attribute-mappings"></a>Redigera användarattribut – mappningar

Följ de här stegen för att få åtkomst till **mappnings** funktionen för användar etablering:

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com).
1. Välj **företags program** i det vänstra fönstret. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
1. Välj en app för att läsa in fönstret för hantering av appar där du kan visa rapporter och hantera appinställningar.
1. Välj **etablering** för att hantera inställningar för etablering av användar konto för den valda appen.
1. Expandera **mappningar** för att visa och redigera användarattribut som flödar mellan Azure AD och mål programmet. Om mål programmet stöder det kan du med hjälp av det här avsnittet Konfigurera etablering av grupper och användar konton.

   ![Använd mappningar för att visa och redigera användarattribut](./media/customize-application-attributes/21.png)

1. Välj en **mappnings** konfiguration för att öppna skärmen relaterad **Mappning av attribut** . Vissa attribut-mappningar krävs av ett SaaS-program för att fungera korrekt. **Borttagnings** funktionen är inte tillgänglig för obligatoriska attribut.

   ![Använd attributmappning för att konfigurera attribut mappningar för appar](./media/customize-application-attributes/22.png)

   I den här skärm bilden kan du se att attributet **username** för ett hanterat objekt i Salesforce fylls med värdet **userPrincipalName** för det länkade Azure Active Directory-objektet.

1. Välj en befintlig **attributmappning** för att öppna skärmen **Redigera attribut** . Här kan du redigera användarattribut som flödar mellan Azure AD och mål programmet.

   ![Använd Edit-attribut för att redigera användarattribut](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Förstå attribut-mappnings typer

Med attribut-mappningar kan du styra hur attribut fylls i i ett SaaS program från tredje part.
Det finns fyra olika mappnings typer som stöds:

- **Direct** – målattributet är ifyllt med värdet för ett attribut för det länkade objektet i Azure AD.
- **Konstant** – målattributet är ifyllt med en specifik sträng som du har angett.
- **Uttryck** – målattributet är ifyllt baserat på resultatet av ett skript som liknar uttryck.
  Mer information finns i [skriva uttryck för Attribute-Mappings i Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Inget** – målattributet lämnas oförändrat. Men om målattributet skulle vara tomt fylls det med det standardvärde som du anger.

Tillsammans med de här fyra grundläggande typerna stöder anpassade attribut-mappningar konceptet med en valfri **Standardvärde** tilldelning. Standardvärdes tilldelningen säkerställer att ett målattribut fylls med ett värde om det inte finns ett värde i Azure AD eller på målobjektet. Den vanligaste konfigurationen är att lämna detta tomt.

### <a name="understanding-attribute-mapping-properties"></a>Förstå attribut-mappnings egenskaper

I föregående avsnitt har du redan lanserats till egenskaps typen attribut-mappning.
Tillsammans med den här egenskapen stöder attribut-mappningar även följande attribut:

- **Källattribut** – attributet användare från käll systemet (exempel: Azure Active Directory).
- **Target** -attribut – användarattribut i mål systemet (exempel: ServiceNow).
- **Standardvärde om null (valfritt)** – det värde som skickas till mål systemet om källattributet är null. Det här värdet kommer endast att tillhandahållas när en användare skapas. "Standardvärdet när null" kommer inte att tillhandahållas när en befintlig användare uppdateras. Om du till exempel vill etablera alla befintliga användare i mål systemet med en viss befattning (när det är null i käll systemet) kan du använda följande [uttryck](../app-provisioning/functions-for-customizing-application-data.md): switch (IsPresent ([befattning]), "DefaultValue", "true", [befattning]). Se till att ersätta "standardvärde" med det du vill etablera när null i käll systemet. 
- **Matcha objekt med det här attributet** – om mappningen ska användas för att unikt identifiera användare mellan käll-och mål systemen. Den anges vanligt vis på userPrincipalName-eller e-postattributet i Azure AD, som vanligt vis mappas till ett användar namns fält i ett mål program.
- **Matchnings prioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som definieras av det här fältet. Så fort en matchning hittas utvärderas inga ytterligare matchande attribut. Även om du kan ange så många matchande attribut som du vill, bör du överväga om attributen som du använder som matchande attribut verkligen är unika och måste matcha attribut. Kunder har vanligt vis 1 eller 2 matchande attribut i konfigurationen. 
- **Använd den här mappningen**
  - **Alltid** – Använd den här mappningen för både skapande av användare och uppdaterings åtgärder.
  - **Endast vid skapande** – Använd endast den här mappningen för åtgärder för att skapa användare.

## <a name="matching-users-in-the-source-and-target--systems"></a>Matcha användare i käll-och mål systemen
Azure AD Provisioning-tjänsten kan distribueras i både "gröna fält"-scenarier (där användare inte avslutas i mål systemet) och "brownfield"-scenarier (där användare redan finns i mål systemet). För att stödja båda scenarierna använder etablerings tjänsten begreppet matchande attribut. Med matchande attribut kan du avgöra hur du unikt identifierar en användare i källan och matchar användaren i målet. Som en del av planeringen av distributionen identifierar du det attribut som kan användas för att unikt identifiera en användare i käll-och mål systemen. Saker att Observera:

- **Matchande attribut ska vara unika:** Kunder använder ofta attribut som userPrincipalName, mail eller objekt-ID som matchande attribut.
- **Du kan använda flera attribut som matchande attribut:** Du kan definiera flera attribut som ska utvärderas vid matchning av användare och i vilken ordning de utvärderas (definieras som matchnings prioritet i användar gränssnittet). Om du till exempel definierar tre attribut som matchande attribut, och en användare är unikt matchade efter utvärdering av de två första attributen, kommer tjänsten inte att utvärdera det tredje attributet. Tjänsten utvärderar matchande attribut i den angivna ordningen och slutar att utvärdera när en matchning hittas.  
- **Värdet i källan och målet behöver inte matcha exakt:** Värdet i målet kan vara en enkel funktion av värdet i källan. Det kan därför ha ett emailAddress-attribut i källan och userPrincipalName i målet, och matcha med en funktion av attributet emailAddress som ersätter vissa tecken med ett konstant värde.  
- **Matchning baserat på en kombination av attribut stöds inte:** De flesta program har inte stöd för frågor som baseras på två egenskaper. Därför är det inte möjligt att matcha baserat på en kombination av attribut. Det går att utvärdera enskilda egenskaper på efter en annan.
- **Alla användare måste ha ett värde för minst ett matchande attribut:** Om du definierar ett matchande attribut måste alla användare ha ett värde för det attributet i käll systemet. Om du till exempel definierar userPrincipalName som matchande attribut, måste alla användare ha ett userPrincipalName. Om du definierar flera matchande attribut (t. ex. extensionAttribute1 och mail) måste inte alla användare ha samma matchande attribut. En användare kan ha en extensionAttribute1 men inte e-post medan en annan användare kan ha e-post men inte extensionAttribute1. 
- **Mål programmet måste ha stöd för filtrering av matchande attribut:** Programutvecklare tillåter filtrering av en delmängd av attribut i deras användar-eller grupp-API. För program i galleriet ser vi till att standardattributets mappning är för ett attribut som mål programmets API stöder filtrering på. När du ändrar standardvärdet för det matchande attributet för mål programmet kontrollerar du API-dokumentationen från tredje part för att se till att attributet kan filtreras.  

## <a name="editing-group-attribute-mappings"></a>Redigera Gruppattribut – mappningar

Ett valt antal program, till exempel ServiceNow, Box och G Suite, stöder möjligheten att etablera grupp objekt och användar objekt. Grupp objekt kan innehålla grupp egenskaper, till exempel visnings namn och e-postalias, tillsammans med grupp medlemmar.

![Exempel visar ServiceNow med etablerade grupp-och användar objekt](./media/customize-application-attributes/24.png)

Grupp etablering kan aktive ras eller inaktive ras genom att du väljer grupp mappningen under **mappningar** och inställningen **aktive rad** till det alternativ som du vill använda på skärmen för **Mappning av attribut** .

De attribut som tillhandahålls som en del av grupp objekt kan anpassas på samma sätt som användar objekt, som beskrivs ovan. 

> [!TIP]
> Etablering av grupp objekt (egenskaper och medlemmar) är ett distinkt begrepp för att [tilldela grupper](../manage-apps/assign-user-or-group-access-portal.md) till ett program. Det går att tilldela en grupp till ett program, men endast etablera de användar objekt som finns i gruppen. Det krävs ingen etablering av fullständiga grupp objekt för att använda grupper i tilldelningar.

## <a name="editing-the-list-of-supported-attributes"></a>Redigera listan över attribut som stöds

Användarattribut som stöds för ett angivet program är förkonfigurerade. De flesta programs API: er för användar hantering stöder inte schema identifiering. Azure AD Provisioning-tjänsten kan därför inte dynamiskt generera listan över attribut som stöds genom att ringa till programmet.

Vissa program stöder dock anpassade attribut och Azure AD Provisioning-tjänsten kan läsa och skriva till anpassade attribut. Om du vill ange deras definitioner i Azure Portal markerar du kryss rutan **Visa avancerade alternativ** längst ned på skärmen **Mappning av attribut** och väljer sedan **Redigera attributlistan för** din app.

Program och system som stöder anpassning av attributlistan är:

- Salesforce
- ServiceNow
- Workday till Active Directory/arbets dag att Azure Active Directory
- SuccessFactors för att Active Directory/SuccessFactors till Azure Active Directory
- Azure Active Directory ([Azure AD Graph API standardattribut](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) och anpassade katalog tillägg stöds)
- Appar som stöder [SCIM 2,0](https://tools.ietf.org/html/rfc7643)
- För att Azure Active Directory tillbakaskrivning till Workday eller SuccessFactors, stöds det att uppdatera relevanta metadata för attribut som stöds (XPATH och JSONPath), men det finns inte stöd för att lägga till nya Workday-eller SuccessFactors-attribut utöver de som ingår i standard schemat


> [!NOTE]
> Redigering av listan över attribut som stöds rekommenderas endast för administratörer som har anpassat schemat för sina program och system, och som har första kunskap om hur deras egna attribut har definierats. Detta kräver ibland välbekanthet med de API: er och utvecklingsverktyg som tillhandahålls av ett program eller system. Möjligheten att redigera listan över attribut som stöds är låst som standard, men kunder kan aktivera funktionen genom att gå till följande URL: https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true . Du kan sedan navigera till programmet för att Visa attributlistan enligt beskrivningen [ovan](#editing-the-list-of-supported-attributes). 

När du redigerar listan över attribut som stöds anges följande egenskaper:

- **Namn** – system namnet för attributet, enligt definitionen i målobjektet.
- **Typ** – den typ av data som attributet lagrar, enligt definitionen i målobjektets schema, som kan vara någon av följande typer:
  - *Binary* -Attribute innehåller binära data.
  - *Boolean* -Attribute innehåller ett true-eller false-värde.
  - *Datetime* -attributet innehåller en datum sträng.
  - *Integer* -attributet innehåller ett heltal.
  - *Reference* -Attribute innehåller ett ID som refererar till ett värde som lagras i en annan tabell i mål programmet.
  - *String*  -Attribute innehåller en text sträng.
- **Primär nyckel?** – Anger om attributet definieras som ett primär nyckel fält i målobjektets schema.
- **Krävs?** – Anger om attributet måste fyllas i mål programmet eller systemet.
- **Flera värden?** – Anger om attributet stöder flera värden.
- **Exakt fall?** – Om attributvärdena utvärderas på ett skift läges känsligt sätt.
- **API-uttryck** – Använd inte, om det inte instrueras att göra det i dokumentationen för en speciell etablerings anslutning (till exempel Workday).
- **Refererat objektattribut** – om det är ett attribut för referens typ kan du välja tabell och attribut i mål programmet som innehåller värdet som är kopplat till attributet. Om du till exempel har ett attribut med namnet "avdelning" vars lagrade värde refererar till ett objekt i en separat "avdelnings tabell" väljer du "Departments.Name". Referens tabellerna och de primära ID-fälten som stöds för ett angivet program är förkonfigurerade och kan för närvarande inte redige ras med hjälp av Azure Portal, men det går att redigera med hjälp av [Microsoft Graph API](/graph/api/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Etablering av ett anpassat tilläggs-attribut för ett SCIM-kompatibelt program
SCIM RFC definierar en kärn användare och ett grupp schema, samtidigt som tilläggen till schemat kan uppfylla ditt programs behov. Så här lägger du till ett anpassat attribut i ett SCIM-program:
   1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com), Välj **företags program**, Välj ditt program och välj sedan **etablering**.
   2. Under **mappningar** väljer du det objekt (användare eller grupp) som du vill lägga till ett anpassat attribut för.
   3. Längst ned på sidan väljer du **Visa avancerade alternativ**.
   4. Välj **Redigera attributlistan för APPNAME**.
   5. Längst ned i listan attribut anger du information om det anpassade attributet i de angivna fälten. Välj sedan **Lägg till attribut**.

För SCIM-program måste attributnamnet följa mönstret som visas i exemplet nedan. Du kan anpassa "CustomExtensionName" och "CustomAttribute" enligt programmets krav, till exempel: urn: IETF: params: scim: schemas: tillägg: CustomExtensionName: 2.0: användare: CustomAttribute 

Dessa anvisningar gäller endast för SCIM-aktiverade program. Program som ServiceNow och Salesforce är inte integrerade med Azure AD med hjälp av SCIM, och därför behövs inte det här särskilda namn området när du lägger till ett anpassat attribut.

Anpassade attribut kan inte vara referens attribut, flervärdesfält eller komplexa-skrivna attribut. Anpassade attribut för flera värden och komplext typ stöds för närvarande endast för program i galleriet.  
 
**Exempel på representation av en användare med ett attribut för tillägg:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "id": "48af03ac28ad4fb88478",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Etablering av en roll för en SCIM-app
Använd stegen nedan för att etablera roller för en användare till ditt program. Observera att beskrivningen nedan är speciell för anpassade SCIM-program. För Galleri program som Salesforce och ServiceNow använder du fördefinierade roll mappningar. Punkterna nedan beskriver hur du transformerar attributet AppRoleAssignments till formatet som programmet förväntar sig.

- Att mappa en appRoleAssignment i Azure AD till en roll i programmet kräver att du transformerar attributet med ett [uttryck](../app-provisioning/functions-for-customizing-application-data.md). Attributet appRoleAssignment **ska inte mappas direkt** till ett roll-attribut utan att använda ett uttryck för att parsa roll informationen. 

- **SingleAppRoleAssignment** 
  - **När du ska använda:** Använd SingleAppRoleAssignment-uttrycket för att etablera en enskild roll för en användare och för att ange den primära rollen. 
  - **Så här konfigurerar du:** Använd stegen som beskrivs ovan för att navigera till sidan mappningar för attribut och Använd SingleAppRoleAssignment-uttrycket för att mappa till roles-attributet. Det finns tre roll-attribut att välja mellan: (roller [Primary EQ "true"]. display, roles [Primary EQ "true]. type och Roles [Primary EQ" true "]. Value). Du kan välja att inkludera alla eller alla roll-attribut i dina mappningar. Om du vill inkludera mer än en lägger du bara till en ny mappning och inkluderar den som målattribut.  
  
  ![Lägg till SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Saker att tänka på**
    - Se till att flera roller inte är tilldelade till en användare. Vi kan inte garantera vilken roll som ska tillhandahållas.
    
  - **Exempel förfrågan (POST)** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
  - **Exempel på utdata (korrigering)** 
    
   ```
   "Operations": [
   {
   "op": "Add",
   "path": "roles",
   "value": [
   {
   "value": "{\"id\":\"06b07648-ecfe-589f-9d2f-6325724a46ee\",\"value\":\"25\",\"displayName\":\"Role1234\"}"
   }
   ]
   ```  
Förfrågnings formatet i korrigerings filen och POST skiljer sig. För att säkerställa att inlägget och KORRIGERINGen skickas i samma format kan du använda funktions flaggan som beskrivs [här](./application-provisioning-config-problem-scim-compatibility.md#flags-to-alter-the-scim-behavior). 

- **AppRoleAssignmentsComplex** 
  - **När du ska använda:** Använd AppRoleAssignmentsComplex-uttrycket för att etablera flera roller för en användare. 
  - **Så här konfigurerar du:** Redigera listan över attribut som stöds enligt beskrivningen ovan för att inkludera ett nytt attribut för roller: 
  
    ![Lägga till roller](./media/customize-application-attributes/add-roles.png)<br>

    Använd sedan AppRoleAssignmentsComplex-uttrycket för att mappa till attributet anpassad roll som visas på bilden nedan:

    ![Lägg till AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Saker att tänka på**
    - Alla roller kommer att tillhandahållas som primär = falskt.
    - INLÄGGET innehåller roll typen. PATCH-begäran innehåller ingen typ. Vi arbetar med att skicka in typen i både POST-och PATCH-begäranden.
    
  - **Exempel på utdata** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Etablering av ett flervärdesattribut
Vissa attribut, till exempel phoneNumbers och e-postmeddelanden, är flervärdesattribut där du kan behöva ange olika typer av telefonnummer eller e-postmeddelanden. Använd uttrycket nedan för attribut med flera värden. Det gör att du kan ange attributtypen och mappningen till motsvarande Azure AD-användarattribut för värdet. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers [Type EQ "fax"]. värde

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Återställa standardattribut och attribut mappningar

Om du behöver börja om och återställa dina befintliga mappningar till deras standard tillstånd, kan du markera kryss rutan **Återställ standard mappningar** och spara konfigurationen. Om du gör det anges alla mappningar och omfångs filter som om programmet precis har lagts till i din Azure AD-klient från program galleriet.

Om du väljer det här alternativet tvingas en omsynkronisering av alla användare när etablerings tjänsten körs.

> [!IMPORTANT]
> Vi rekommenderar starkt att **etablerings status** är **inaktive rad** innan du anropar det här alternativet.

## <a name="what-you-should-know"></a>Det här bör du veta

- Microsoft Azure AD ger en effektiv implementering av en synkroniseringsprocess. I en initierad miljö bearbetas endast objekt som kräver uppdateringar under en synkroniseringsprocess.
- Om du uppdaterar attribut-mappningar påverkas prestandan för en synkroniseringsprocess. En uppdatering av konfigurationen för attribut-mappning kräver att alla hanterade objekt utvärderas igen.
- En rekommenderad metod är att behålla antalet ändringar i följd av dina attribut-mappningar minst.
- Det går inte att lägga till ett foto attribut som ska tillhandahållas till en app idag eftersom du inte kan ange formatet för att synkronisera fotot. Du kan begära funktionen på [användarens röst](https://feedback.azure.com/forums/169401-azure-active-directory)
- Attributet IsSoftDeleted är ofta en del av standard mappningarna för ett program. IsSoftdeleted kan vara sant i ett av fyra scenarier (användaren är utanför omfånget eftersom den inte är tilldelad från programmet, användaren är utanför definitions området eftersom den inte uppfyller ett omfångs filter, användaren har varit mjuk borttagen i Azure AD, eller så har egenskapen AccountEnabled angetts till false för användaren). Vi rekommenderar inte att du tar bort attributet IsSoftDeleted från dina mappningar av attribut.
- Azure AD Provisioning-tjänsten har inte stöd för etablering av null-värden.
- De primära nycklarna, vanligt vis "ID", ska inte inkluderas som målattribut i dina Attribute-mappningar. 
- Attributet Role måste vanligt vis mappas med ett uttryck i stället för en direkt mappning. Se avsnittet ovan för mer information om roll mappning. 
- Även om du kan inaktivera grupper från dina mappningar stöds inte inaktive ring av användare. 

## <a name="next-steps"></a>Nästa steg

- [Automatisera användar etablering/avetablering för SaaS-appar](user-provisioning.md)
- [Skriva uttryck för attribut-mappningar](functions-for-customizing-application-data.md)
- [Omfångs filter för användar etablering](define-conditional-rules-for-provisioning-user-accounts.md)
- [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
- [Lista över guider om hur man integrerar SaaS-appar](../saas-apps/tutorial-list.md)
