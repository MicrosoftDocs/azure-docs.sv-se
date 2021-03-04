---
title: 'Självstudie: Konfigurera TravelPerk för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till TravelPerk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 7e3e119c81f6417110d34b7b9b97af9fa1738d35
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055070"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>Självstudie: Konfigurera TravelPerk för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både TravelPerk och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras etablerar och avetablerar Azure AD automatiskt användare och grupper i [TravelPerk](https://www.travelperk.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Funktioner som stöds

> [!div class="checklist"]
>
> - Skapa användare i TravelPerk
> - Ta bort användare i TravelPerk när de inte behöver åtkomst längre
> - Behåll användarattribut synkroniserade mellan Azure AD och TravelPerk
> - [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/travelperk-tutorial) till TravelPerk (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

- [En Azure AD-klientorganisation](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
- Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör).
- Ett aktivt [TravelPerk](https://app.travelperk.com/signup) administratörs konto.
- En Premium-/Pro- [plan](https://www.travelperk.com/pricing/).


## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen

1. Lär dig mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som finns i [etableringsomfånget](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och TravelPerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera TravelPerk för att ge stöd för etablering med Azure AD

1. Logga in på [TravelPerk](https://app.travelperk.com/company/integrations/scim) -programmet med ditt administratörs konto.

2. Navigera till **företags inställningar**  >  **integreringar**  >  **scim**

3. Klicka på **Aktivera scim-API**

   ![Aktivera](./media/travelperk-provisioning-tutorial/configuration.png)

4. Du kan också aktivera godkännanden via SCIM. Godkännanden hjälper dig att ställa in ytterligare styrning genom att se till att resan godkänns först av de angivna god kännarna. Du kan läsa mer [om det här](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-).

5. Du kan ange om du vill att varje persons chef automatiskt ska bli den användare som ansvarar för godkännande av resor. Därför tilldelas en god kännare i motsvarande automatiska godkännande process. TravelPerk kommer att mappa värdet för Azures **chef** till användarens önskade god kännare. Användaren måste finnas på plattformen innan den kan bli den etablerade användar god kännaren.
God kännare kommer inte att skapas om de inte är korrekt konfigurerade på TravelPerk.

6. Det går inte att skapa automatiskt godkännande processen i **scim-inställningarna** när du har aktiverat scim på sidan integrationer. Om du vill aktivera det väljer du **via en identitetsprovider** och växlar över att **Aktivera skapande av automatiskt godkännande processen**.

7. Klicka på **Spara ändringar** när den nödvändiga godkännande processen har kon figurer ATS.

   ![Automatisera](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till TravelPerk från Azure AD-programgalleriet

Lägg till TravelPerk från Azure AD-programgalleriet för att börja hantera etablering till TravelPerk. Om du tidigare har konfigurerat TravelPerk för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

- När du tilldelar användare till TravelPerk måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) och lägga till fler roller.

- Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>Steg 5. Konfigurera automatisk användar etablering till TravelPerk

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Konfigurera automatisk användar etablering för TravelPerk i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

   ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **TravelPerk**.

   ![TravelPerk-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

   ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

   ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** klickar du på **auktorisera**. Du omdirigeras till inloggnings sidan för **TravelPerk**. Mata in ditt **användar namn** och **lösen ord** och klicka på knappen **Logga in** . Klicka på **Auktorisera appen** på sidan auktorisering. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till TravelPerk. Om anslutningen Miss lyckas kontrollerar du att SecureLogin-kontot har administratörs behörighet och försöker igen.

   ![Admin-autentiseringsuppgifter](./media/travelperk-provisioning-tutorial/authorize.png)

   ![Välkommen](./media/travelperk-provisioning-tutorial/login.png)

   ![Access](./media/travelperk-provisioning-tutorial/authorization.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

   ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till TravelPerk**.

9. Granska de användarattribut som synkroniseras från Azure AD till TravelPerk i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i TravelPerk för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att TravelPerk-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   | Attribut                                                                         | Typ      | Stöds för filtrering |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | Sträng    | &check;                 |
   | externalId                                                                        | Sträng    |
   | aktiv                                                                            | Boolesk   |
   | name.honorificPrefix                                                              | Sträng    |
   | name.familyName                                                                   | Sträng    |
   | name.givenName                                                                    | Sträng    |
   | Name. middleName                                                                   | Sträng    |
   | preferredLanguage                                                                 | Sträng    |
   | locale                                                                            | Sträng    |
   | phoneNumbers[type eq "work"].value                                                | Sträng    |
   | externalId                                                                        | Sträng    |
   | title                                                                             | Sträng    |
   | urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter             | Sträng    |
   | urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager                | Referens |
   | urn: IETF: params: scim: schemas: tillägg: travelperk: 2.0: användare: kön                 | Sträng    |
   | urn: IETF: params: scim: schemas: tillägg: travelperk: 2.0: användare: dateOfBirth            | Sträng    |
   | urn: IETF: params: scim: schemas: tillägg: travelperk: 2.0: användare: invoiceProfiles        | Matris     |
   | urn:IETF:params:scim:schemas:extension:travelperk: 2.0:User:emergencyContact. Name  | Sträng    |
   | urn: IETF: params: scim: schemas: tillägg: travelperk: 2.0: användare: emergencyContact. Phone | Sträng    |
   | urn: IETF: params: scim: schemas: tillägg: travelperk: 2.0: användare: travelPolicy           | Sträng    |

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för TravelPerk ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till TravelPerk genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen

När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ytterligare resurser

- [Hantera användarkontoetablering för Enterprise-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

- [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
