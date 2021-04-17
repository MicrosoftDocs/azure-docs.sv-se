---
title: 'Snabbstart: Skapa ett Azure Purview-konto i Azure Portal (förhandsversion)'
description: Den här snabbstarten beskriver hur du skapar ett Azure Purview-konto och konfigurerar behörigheter för att börja använda det.
author: nayenama
ms.author: nayenama
ms.date: 10/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-portal
ms.openlocfilehash: 72f4ac8df39b9511fd98a1dd5a3eca76e11e34bf
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535152"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Snabbstart: Skapa ett Azure Purview-konto i Azure Portal

> [!IMPORTANT]
> Azure Purview finns för närvarande i FÖRHANDSVERSION. De [kompletterande användningsvillkoren för Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som inte har släppts allmänt ännu.

I den här snabbstarten skapar du ett Azure Purview-konto.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* En egen [klientorganisation i Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Ditt konto måste ha behörighet att skapa resurser i prenumerationen

* Om du har **Azure Policy** blockera alla program från att skapa **lagringskonto** och **EventHub-namnrymd** måste du göra principfel med hjälp av taggen , som kan anges när du skapar ett Purview-konto. Huvudorsaken är att för varje Purview-konto som skapas måste det skapa en hanterad resursgrupp och i den här resursgruppen ett lagringskonto och ett EventHub-namnområde.

    > [!important]
    > Du behöver inte följa det här steget om du inte har Azure Policy eller om en befintlig Azure Policy inte blockerar skapandet av **lagringskontot** och **EventHub-namnområdet**.

    1. Gå till Azure Portal och sök efter **princip**
    1. Följ [Skapa en anpassad principdefinition eller](../governance/policy/tutorials/create-custom-policy-definition.md) ändra befintlig princip för att lägga till två undantag med `not` operatorn och `resourceBypass` taggen :

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > Taggen kan vara vad som helst bredvid och det är upp till dig att definiera värdet när du skapar Purview i senare steg så länge principen `resourceBypass` kan identifiera taggen.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="Skärmbild som visar hur du skapar en principdefinition.":::

    1. [Skapa en principtilldelning med](../governance/policy/assign-policy-portal.md) hjälp av den anpassade principen som skapats.

        [![Skärmbild som visar hur du skapar principtilldelning](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="configure-your-subscription"></a>Konfigurera din prenumeration

Om det behövs följer du dessa steg för att konfigurera din prenumeration så att Azure Purview kan köras i din prenumeration:

   1. I Azure Portal du efter och väljer **Prenumerationer**.

   1. I listan över prenumerationer väljer du den prenumeration som du vill använda. Administrativ åtkomstbehörighet för prenumerationen krävs.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Skärmbild som visar hur du väljer en prenumeration i Azure Portal.":::

   1. För din prenumeration väljer du **Resursproviders.** I fönstret **Resursproviders** söker du efter och registrerar alla tre resursproviders: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Om de inte är registrerade registrerar du den genom att välja **Registrera**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Skärmbild som visar hur du registrerar Resursprovidern Microsoft dot Azure Purview i Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Skapa en Azure Purview-kontoinstans

1. Gå till sidan **Rensa konton i** Azure Portal och välj sedan Lägg till **för** att skapa ett nytt Azure Purview-konto. Du kan också gå till Marketplace-sökning efter **Purview-konton** och välja **Skapa.** Observera att du bara kan lägga till ett Azure Purview-konto i taget.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Skärmbild som visar hur du skapar en Azure Purview-kontoinstans i Azure Portal.":::

    > [!Note] 
    > Azure Purview stöder inte flytt av sitt konto mellan regioner. Mer information om detta finns på sidan [för Azure-tjänster som stöds.](../azure-resource-manager/management/region-move-support.md)

1. Gör **följande på** fliken Grundläggande inställningar:
    1. Välj en **Resursgrupp**.
    1. Ange ett **Purview-kontonamn** för katalogen. Blanksteg och symboler tillåts inte.
    1. Välj en **Plats** och välj sedan **Nästa: Konfiguration.**
1. På fliken **Konfiguration** väljer du önskad **plattformsstorlek** – de tillåtna värdena är 4 kapacitetsenheter (CU) och 16 CU. Välj **Nästa: Taggar**.
1. På fliken **Taggar** kan du lägga till en eller flera taggar. De här taggarna är endast till för Azure Portal, inte Azure Purview. 

    > [!Note] 
    > Om du har **Azure Policy** och behöver lägga till undantag som **i Krav** måste du lägga till rätt tagg. Du kan till exempel lägga till `resourceBypass` taggen: :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Lägg till tagg i Purview-kontot.":::

1. Välj **Granska & Skapa** och välj sedan **Skapa.** Det tar några minuter att slutföra skapandet. Den nyligen skapade Azure Purview-kontoinstansen visas i listan på **sidan Rensa** konton.
1. När den nya kontoetablering är klar väljer **du Gå till resurs**.

    > [!Note]
    > Om etableringen misslyckades med status innebär det att det finns en Azure-princip som blockerar Purview från att skapa ett `Conflict` **lagringskonto** och **EventHub-namnområdet**. Du måste gå igenom stegen **för förutsättningar för** att lägga till undantag.
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Felmeddelande för vykonflikt":::

1. Välj **Starta vykonto**.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Skärmbild av valet för att starta Azure Purview-kontokatalogen.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Lägga till ett säkerhetsobjekt i en dataplansroll

Innan du eller ditt team kan börja använda Azure Purview måste en eller flera säkerhetsobjekt läggas till i någon av de fördefinierade dataplansrollerna: **Purview Data Reader**, **Purview Data Intendt** eller **Purview Data Source Administrator**. Mer information om Azure Purview Data Catalog behörigheter finns i [Katalogbehörigheter.](catalog-permissions.md)

Så här lägger du till ett säkerhetsobjekt **i dataplanrollen Purview Data Intendent** i ett Azure Purview-konto:

1. Gå till [**sidan Rensa konton**](https://aka.ms/purviewportal) i Azure Portal.

1. Välj det Azure Purview-konto som du vill ändra.

1. På sidan **Purview-konto** väljer du fliken **Åtkomstkontroll (IAM)**

1. Klicka **på + Lägg till**

Om du när du klickar på Lägg till visas två alternativ som visar båda markerade (inaktiverade) så innebär det att du inte har rätt behörighet att lägga till någon i en dataplansroll på Azure Purview-kontot. Du måste hitta en ägare, administratör för användaråtkomst eller någon annan med rolltilldelningsbehörighet på ditt Azure Purview-konto. Du kan leta efter rätt  personer genom att välja fliken Rolltilldelningar och sedan rulla ned för att leta efter Ägare eller Administratör för användaråtkomst och kontakta dessa personer.

1. Välj **Lägg till rolltilldelning**.

1. För rolltypen i **rollen Purview Data Intendent** eller **Purview Data Source Administrator Role** beroende [](catalog-permissions.md) på vad säkerhetsobjekt kommer att användas för (mer information finns i Katalogbehörigheter och program- och tjänsthuvudnamnsobjekt [i Azure Active Directory).](../active-directory/develop/app-objects-and-service-principals.md)

1. För **Tilldela åtkomst lämnar** du standardvärdet **Användare, Grupp eller Tjänstens huvudnamn**.

1. För **Välj** anger du namnet på användaren Azure Active Directory grupp eller tjänstens huvudnamn som du vill tilldela och klickar sedan på användarens namn i resultatfönstret.

1. Klicka på **Spara.**

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver det här Azure Purview-kontot tar du bort det med följande steg:

1. Gå till **sidan Rensa konton** i Azure Portal.

2. Välj det Azure Purview-konto som du skapade i början av den här snabbstarten. Välj **Ta** bort, ange namnet på kontot och välj sedan Ta **bort.**

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Purview-konto.

Gå vidare till nästa artikel om du vill lära dig hur du ger användare åtkomst till ditt Azure Purview-konto. 

> [!div class="nextstepaction"]
> [Lägga till användare i ditt Azure Purview-konto](catalog-permissions.md)
