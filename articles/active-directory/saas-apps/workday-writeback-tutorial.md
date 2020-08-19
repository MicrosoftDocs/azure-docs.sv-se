---
title: 'Självstudie: Konfigurera tillbakaskrivning av arbets dagar i Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar tillbakaskrivning av attribut från Azure AD till Workday
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 324d3a21e1694d243f03beca28ac8376bedffa4d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88526838"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Självstudie: Konfigurera tillbakaskrivning av attribut från Azure AD till Workday
Syftet med den här självstudien är att visa de steg du behöver utföra för att göra om attribut från Azure AD till Workday. Tillbakaskrivning av tillbakaskrivning-appen för Workday stöder tilldelning av värden till följande Workday-attribut:
* E-postadress till arbetet 
* Workday-användarnamn
* Telefonnummer till arbets Landline (inklusive landskod, rikt nummer, nummer och tillägg)
* Primär flagga för arbets Landline telefonnummer
* Arbets mobiltelefon nummer (inklusive landskod, rikt nummer, nummer)
* Primär mobil flagga för arbete

## <a name="overview"></a>Översikt

När du har installerat integrering av inkommande etablering med hjälp av en [arbets dag till en lokal AD](workday-inbound-tutorial.md) -etablerings app eller [arbets dag till Azure AD](workday-inbound-cloud-only-tutorial.md) Provisioning-appen, kan du konfigurera arbets dagar tillbakaskrivning-appen för att skriva kontakt information, till exempel e-post och telefonnummer till arbets dagar. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här användar etablerings lösningen som passar bäst för?

Den här arbets dagen för tillbakaskrivning av användar etablering passar utmärkt för:

* Organisationer som använder Office 365 som vill kunna tillbakaskrivning av auktoritativa attribut som hanteras av IT (till exempel e-postadress, användar namn och telefonnummer) tillbaka till Workday

## <a name="configure-integration-system-user-in-workday"></a>Konfigurera användare av integrations systemet på arbets dagen

Se avsnittet [Konfigurera integrations system användare](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) för att skapa ett användar konto för en Workday-integrerings system med behörighet att hämta arbets data. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Konfigurera Azure AD-attributet tillbakaskrivning till Workday

Följ de här anvisningarna för att konfigurera tillbakaskrivning av användarens e-postadresser och användar namn från Azure Active Directory till Workday.

* [Lägga till appen tillbakaskrivning Connector och skapa anslutningen till arbets dagen](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurera mappningar av tillbakaskrivning av attribut](#part-2-configure-writeback-attribute-mappings)
* [Aktivera och starta användar etablering](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Del 1: lägga till appen tillbakaskrivning Connector och skapa anslutningen till arbets dagen

**Konfigurera tillbakaskrivning av workday-anslutning:**

1. Gå till <https://portal.azure.com>.

2. I Azure Portal söker du efter och väljer **Azure Active Directory**.

3. Välj **företags program**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj sedan kategorin **alla** .

5. Sök efter **tillbakaskrivning av arbets dagar**och Lägg till den appen från galleriet.

6. När appen har lagts till och skärmen information om appen visas väljer du **etablering**.

7. Ändra **etablerings** **läget** till **automatiskt**.

8. Slutför avsnittet **admin credentials** enligt följande:

   * **Administratörens användar** namn – Ange användar namnet för kontot för arbets dag integrerings systemet med klient domän namnet tillagt. Bör se ut ungefär så här: *username \@ contoso4*

   * **Administratörs lösen ord –** Ange lösen ordet för system kontot för Workday-integrering

   * **Klient-URL –** Ange URL: en till slut punkten för webb tjänster för arbets dag för din klient. Det här värdet bör se ut så här: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources` , där *contoso4* ersätts med rätt klient namn och *WD3-implementering* ersätts med rätt miljö sträng (om det behövs).

   * **E-postavisering –** Ange din e-postadress och markera kryss rutan "skicka e-post om fel inträffar".

   * Klicka på knappen **Testa anslutning** . Om anslutnings testet lyckas, klickar du på knappen **Spara** längst upp. Om det Miss lyckas, kontrol lera att URL: en för arbets dagar och autentiseringsuppgifter är giltiga i arbets dagen.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Del 2: Konfigurera mappningar för tillbakaskrivning av attribut

I det här avsnittet ska du konfigurera hur tillbakaskrivning av attribut flödar från Azure AD till Workday. 

1. På fliken etablering under **mappningar**klickar du på mappnings namnet.

2. I fältet **käll objekt omfånget** kan du välja att filtrera, vilka uppsättningar med användare i Azure Active Directory ska vara en del av tillbakaskrivning. Standard omfånget är "alla användare i Azure AD".

3. I avsnittet **mappningar av attribut** uppdaterar du matchnings-ID: t för att ange attributet i Azure Active Directory där arbets dagar Worker-ID eller medarbetar-ID lagras. En populär matchnings metod är att synkronisera arbets dagens ID för arbets dagar eller anställnings-ID till extensionAttribute1-15 i Azure AD och sedan använda det här attributet i Azure AD för att matcha användare tillbaka till arbets dagen.

4. Vanligt vis mappar du Azure AD *userPrincipalName* -attributet till ett Workday *UserID* -attribut och mappar Azure AD *mail-* attributet till attributet Workday *EmailAddress* . 

     >[!div class="mx-imgBorder"]
     >![Azure-portalen](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Använd rikt linjerna som delas nedan för att mappa telefonnummerets attributvärden från Azure AD till Workday. 

     | Attribut för Workday-telefon | Förväntat värde | Kart vägledning |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | True/false | Konstant-eller uttrycks mappning vars utdata är "sant" eller "falskt" sträng värde. |
     | WorkphoneLandlineCountryCodeName | [ISO 3166-1-landskod med tre bokstäver](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Konstant-eller uttrycks mappning vars utdata är en landskod med tre bokstäver. |
     | WorkphoneLandlineCountryCodeNumber | [Internationell lands anrops kod](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Konstant-eller uttrycks mappning vars utdata är en giltig landskod (utan +-tecknet). |
     | WorkphoneLandlineNumber | Fullständigt telefonnummer inklusive rikt nummer | Mappa till *telephoneNumber* -attribut. Använd regex för att ta bort blank steg, hakparenteser och landskod. Se exemplet nedan. |
     | WorkphoneLandlineExtension | Tilläggs nummer | Om *telephoneNumber* innehåller tillägg använder du regex för att extrahera värdet. |
     | WorkphoneMobileIsPrimary | True/false | Konstant mappning eller uttrycks mappning vars utdata är "sant" eller "falskt" sträng värde |
     | WorkphoneMobileCountryCodeName | [ISO 3166-1-landskod med tre bokstäver](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Konstant-eller uttrycks mappning vars utdata är en landskod med tre bokstäver. |
     | WorkphoneMobileCountryCodeNumber | [Internationell lands anrops kod](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Konstant-eller uttrycks mappning vars utdata är en giltig landskod (utan +-tecknet). |
     | WorkphoneMobileNumber | Fullständigt telefonnummer inklusive rikt nummer | Mappa till *mobilt* attribut. Använd regex för att ta bort blank steg, hakparenteser och landskod. Se exemplet nedan. |

     > [!NOTE]
     > När du anropar Change_Work_Contact Workday-webbtjänsten skickar Azure AD följande konstanta värden: <br>
     > * **Communication_Usage_Type_ID** har angetts till konstant sträng "arbete" <br>
     > * **Phone_Device_Type_ID** har angetts till konstant sträng "mobil" för mobiltelefon nummer och "Landline" för Landline-telefonnummer. <br>
     > 
     > Du får problem med tillbakaskrivning om din Workday-klient använder olika Type_IDs. För att förhindra sådana fel kan du använda åtgärden arbets dag **Behåll referens-ID: n** och uppdatera Type_IDs så att den matchar de värden som används av Azure AD. <br>
     >  

     **Referens regex-uttryck – exempel 1**

     Använd det reguljära uttrycket nedan om telefonnumret i Azure AD anges med det format som krävs för lösen ords återställning via självbetjäning (SSPR). <br>
     Exempel: om telefonnumret är + 1 1112223333->, kommer regex-uttrycket att resultera i 1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Referens regex-uttryck – exempel 2**

     Använd det reguljära uttrycket nedan om telefonnumret i Azure AD anges med formatet (XXX) XXX-XXXX. <br>
     Exempel: om telefonnumret är (111) 222-3333-> kommer regex-uttrycket att returnera 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Spara dina mappningar genom att klicka på **Spara** överst i avsnittet attribut-mappning.

## <a name="enable-and-launch-user-provisioning"></a>Aktivera och starta användar etablering

När du har slutfört konfigurationen av appar för arbets dag etablering kan du aktivera etablerings tjänsten i Azure Portal.

> [!TIP]
> Som standard när du aktiverar etablerings tjänsten kommer den att initiera etablerings åtgärder för alla användare i omfånget. Om det uppstår fel i mappnings-eller data frågor för data lagret kan etablerings jobbet Miss Miss kan och gå in i karantäns läget. För att undvika detta rekommenderar vi att du konfigurerar **käll objekt omfångs** filter och testar dina mappningar av attribut med några test användare innan du startar den fullständiga synkroniseringen för alla användare. När du har kontrollerat att mappningarna fungerar och ger dig önskade resultat kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

1. På fliken **etablering** ställer du in **etablerings status** på **på**.

2. Klicka på **Spara**.

3. Den här åtgärden startar den inledande synkroniseringen, vilket kan ta ett variabelt antal timmar beroende på hur många användare som finns i käll katalogen. Du kan kontrol lera förlopps indikatorn för att följa synkroniseringens förlopp. 

4. Gå till fliken **gransknings loggar** i Azure Portal för att se vilka åtgärder som etablerings tjänsten har utfört. I gransknings loggarna visas alla enskilda synkroniseringsfel som utförs av etablerings tjänsten, till exempel vilka användare som importeras från källan och som exporteras till mål programmet.  

5. När den inledande synkroniseringen har slutförts skrivs en sammanfattnings rapport på fliken **etablering** , som visas nedan.

     > [!div class="mx-imgBorder"]
     > ![Förlopps indikator för etablering](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan arbets dagar och Azure Active Directory](workday-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du exporterar och importerar dina etablerings konfigurationer](../app-provisioning/export-import-provisioning-configuration.md)

