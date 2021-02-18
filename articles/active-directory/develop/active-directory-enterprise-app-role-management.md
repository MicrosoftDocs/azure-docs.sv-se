---
title: Konfigurera roll anspråk för Enterprise Azure AD-appar | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar det roll anspråk som utfärdats i SAML-token för företags program i Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: aab1f99984ed5286692cbf9dae39fb4f7d28599c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652470"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Gör så här: konfigurera det roll anspråk som utfärdats i SAML-token för företags program

Genom att använda Azure Active Directory (Azure AD) kan du anpassa anspråks typen för roll anspråk i svars-token som du får när du har auktoriserat en app.

## <a name="prerequisites"></a>Förutsättningar

- En Azure AD-prenumeration med katalog konfiguration.
- En prenumeration som har enkel inloggning aktive rad (SSO). Du måste konfigurera SSO med ditt program.

> [!NOTE]
> Den här artikeln beskriver hur du skapar/uppdaterar/tar bort program roller för tjänstens huvud namn med hjälp av API: er i Azure AD. Om du vill använda det nya användar gränssnittet för app-roller kan du se informationen [här](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

## <a name="when-to-use-this-feature"></a>När du ska använda den här funktionen

Använd den här funktionen om programmet förväntar sig anpassade roller i SAML-svaret som returneras av Azure AD. Du kan skapa så många roller du behöver.

## <a name="create-roles-for-an-application"></a>Skapa roller för ett program

1. I <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>i det vänstra fönstret väljer du ikonen **Azure Active Directory** .

    ![Azure Active Directory ikon][1]

2. Välj **företags program**. Välj sedan **alla program**.

    ![Fönstret företags program][2]

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i dialog rutan.

    ![Knappen nytt program][3]

4. I sökrutan skriver du namnet på programmet och väljer sedan ditt program i resultat panelen. Välj knappen **Lägg** till för att lägga till programmet.

    ![Program i resultat listan](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. När du har lagt till programmet går du till sidan **Egenskaper** och kopierar objekt-ID: t.

    ![Egenskaps sida](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Öppna [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster och utför följande steg:

    1. Logga in på Graph Explorer-webbplatsen med hjälp av den globala administratörs-eller medadministratörens autentiseringsuppgifter för din klient organisation.

    1. Du måste ha tillräcklig behörighet för att skapa roller. Välj **ändra behörigheter** för att hämta behörigheterna.

        ![Knappen "ändra behörigheter"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

        > [!NOTE]
        > Rollen som Cloud App-administratör och app-administratör fungerar inte i det här scenariot eftersom vi behöver den globala administratörs behörighet för katalog läsning och skrivning.

    1. Välj följande behörigheter i listan (om du inte redan har dem) och välj **ändra behörigheter**.

        ![Lista över behörigheter och knappen "ändra behörigheter"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    1. Godkänn godkännandet. Du är inloggad på systemet igen.

    1. Ändra versionen till **beta** och hämta listan över tjänstens huvud namn från din klient med hjälp av följande fråga:

        `https://graph.microsoft.com/beta/servicePrincipals`

        Om du använder flera kataloger följer du det här mönstret: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

        ![Dialog rutan Graph Explorer med frågan för att hämta tjänstens huvud namn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

    1. I listan med hämtade tjänst huvud namn, hämtar du det du behöver ändra. Du kan också använda Ctrl + F för att söka i programmet från alla angivna tjänst huvud namn. Sök efter det objekt-ID som du kopierade från sidan **Egenskaper** och Använd följande fråga för att komma till tjänstens huvud namn:

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

        ![Fråga för att hämta tjänstens huvud namn som du måste ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    1. Extrahera egenskapen **appRoles** från objektet tjänstens huvud namn.

        ![Information om egenskapen appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

        Om du använder den anpassade appen (inte Azure Marketplace-appen) visas två standard roller: användar-och msiam_access. Msiam_access är den enda standard rollen för Marketplace-appen. Du behöver inte göra några ändringar i standard rollerna.

    1. Skapa nya roller för ditt program.

        Följande JSON är ett exempel på **appRoles** -objektet. Skapa ett liknande objekt för att lägga till de roller som du vill använda för ditt program.

        ```json
        {
          "appRoles": [
            {
               "allowedMemberTypes": [
                  "User"
                ],
                "description": "msiam_access",
                "displayName": "msiam_access",
                "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
                "isEnabled": true,
                "origin": "Application",
                "value": null
            },
            {
                "allowedMemberTypes": [
                    "User"
                ],
                "description": "Administrators Only",
                "displayName": "Admin",
                "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
                "isEnabled": true,
                "origin": "ServicePrincipal",
                "value": "Administrator"
            }
         ]
        }
        ```

        Du kan bara lägga till nya roller efter msiam_access för korrigerings åtgärden. Du kan också lägga till så många roller som din organisation behöver. Azure AD skickar värdet för dessa roller som anspråks värde i SAML-svaret. Om du vill generera GUID-värden för ID för nya roller använder du webb verktyg som [det här](https://www.guidgenerator.com/)

    1. Gå tillbaka till Graph Explorer och ändra-metoden från **Hämta** till **patch**. Korrigera tjänstens huvud namns objekt så att de fungerar som de ska genom att uppdatera egenskapen **appRoles** som den som visas i föregående exempel. Välj **Kör fråga** för att köra korrigerings åtgärden. Ett meddelande om att rollen har skapats bekräftas.

        ![Åtgärd för korrigering med slutfört meddelande](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

1. När tjänstens huvud namn har uppdaterats med fler roller kan du tilldela användare till respektive roller. Du kan tilldela användarna genom att gå till portalen och bläddra till programmet. Välj fliken **användare och grupper** . På den här fliken visas alla användare och grupper som redan är tilldelade till appen. Du kan lägga till nya användare på de nya rollerna. Du kan också välja en befintlig användare och välja **Redigera** för att ändra rollen.

    ![Fliken "användare och grupper"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Om du vill tilldela rollen till en användare väljer du den nya rollen och väljer knappen **tilldela** längst ned på sidan.

    ![Fönstret "Redigera tilldelning" och "Välj roll"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    
    Uppdatera sessionen i Azure Portal för att se nya roller.

1. Uppdatera **attribut** -tabellen för att definiera en anpassad mappning av roll anspråket.

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Attributnamn | Attributvärde |
    | -------------- | ----------------|
    | Rollnamn  | user.assignedroles |

    Om roll anspråkets värde är null skickar Azure AD inte det här värdet i token och detta är standard enligt design.

    1. Klicka på ikonen **Redigera** för att öppna dialog rutan **användarattribut & anspråk** .

        ![Skärm bild som visar redigerings ikonen som används för att öppna dialog rutan användarattribut &-anspråk.](./media/active-directory-enterprise-app-role-management/editattribute.png)

    1. I dialog rutan **hantera användar anspråk** lägger du till attributet SAML-token genom att klicka på **Lägg till nytt anspråk**.

        ![Knappen Lägg till attribut](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

        ![Fönstret Lägg till attribut](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    1. I rutan **namn** skriver du namnet på attributet vid behov. I det här exemplet används **roll namnet** som anspråks namn.

    1. Lämna rutan **namn område** tom.

    1. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    1. Välj **Spara**.

10. Om du vill testa programmet i en enkel inloggning som initieras av en identitetsprovider loggar du in på [åtkomst panelen](https://myapps.microsoft.com) och väljer din program panel. I SAML-token bör du se alla tilldelade roller för användaren med det angivna anspråks namnet.

## <a name="update-an-existing-role"></a>Uppdatera en befintlig roll

Utför följande steg för att uppdatera en befintlig roll:

1. Öppna [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

1. Logga in på Graph Explorer-webbplatsen med hjälp av den globala administratörs-eller medadministratörens autentiseringsuppgifter för din klient organisation.

1. Ändra versionen till **beta** och hämta listan över tjänstens huvud namn från din klient med hjälp av följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger följer du det här mönstret: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialog rutan Graph Explorer med frågan för att hämta tjänstens huvud namn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. I listan med hämtade tjänst huvud namn, hämtar du det du behöver ändra. Du kan också använda Ctrl + F för att söka i programmet från alla angivna tjänst huvud namn. Sök efter det objekt-ID som du kopierade från sidan **Egenskaper** och Använd följande fråga för att komma till tjänstens huvud namn:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Fråga för att hämta tjänstens huvud namn som du måste ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Extrahera egenskapen **appRoles** från objektet tjänstens huvud namn.

    ![Information om egenskapen appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

1. Använd följande steg för att uppdatera den befintliga rollen.

    ![Brödtext för "PATCH", med "Beskrivning" och "DisplayName" markerat](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    1. Ändra-metoden från **Hämta** till **patch**.

    1. Kopiera befintliga roller och klistra in dem i **begär ande texten**.

    1. Uppdatera värdet för en roll genom att uppdatera roll beskrivningen, roll svärdet eller rollens visnings namn efter behov.

    1. När du har uppdaterat alla nödvändiga roller väljer du **Kör fråga**.

## <a name="delete-an-existing-role"></a>Ta bort en befintlig roll

Utför följande steg för att ta bort en befintlig roll:

1. Öppna [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

1. Logga in på Graph Explorer-webbplatsen med hjälp av den globala administratörs-eller medadministratörens autentiseringsuppgifter för din klient organisation.

1. Ändra versionen till **beta** och hämta listan över tjänstens huvud namn från din klient med hjälp av följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger följer du det här mönstret: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialog rutan Graph Explorer med frågan för att hämta listan över tjänstens huvud namn](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. I listan med hämtade tjänst huvud namn, hämtar du det du behöver ändra. Du kan också använda Ctrl + F för att söka i programmet från alla angivna tjänst huvud namn. Sök efter det objekt-ID som du kopierade från sidan **Egenskaper** och Använd följande fråga för att komma till tjänstens huvud namn:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Fråga för att hämta tjänstens huvud namn som du måste ändra](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Extrahera egenskapen **appRoles** från objektet tjänstens huvud namn.

    ![Information om egenskapen appRoles från objektet tjänstens huvud namn](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

1. Använd följande steg för att ta bort den befintliga rollen.

    ![Brödtext för "PATCH", med IsEnabled inställt på falskt](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    1. Ändra-metoden från **Hämta** till **patch**.

    1. Kopiera befintliga roller från programmet och klistra in dem i **begär ande texten**.

    1. Ställ in värdet för **IsEnabled** på **false** för den roll som du vill ta bort.

    1. Välj **Kör fråga**.

    Kontrol lera att du har msiam_accesss rollen och att ID: t matchar i den genererade rollen.

1. När rollen har inaktiverats tar du bort roll blocket från **appRoles** -avsnittet. Behåll metoden som **korrigering** och välj **Kör fråga**.

1. När du har kört frågan tas rollen bort.

    Rollen måste inaktive ras innan den kan tas bort.

## <a name="next-steps"></a>Nästa steg

Ytterligare anvisningar finns i dokumentationen till [appen](../saas-apps/tutorial-list.md).

<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
