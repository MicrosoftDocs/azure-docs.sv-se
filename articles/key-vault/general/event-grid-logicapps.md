---
title: E-postmeddelande när de hemliga ändringarnas status Key Vault
description: Guide för att använda Logic Apps för att svara på ändringar i Key Vault hemligheter
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: c0121c42c5d498aa79109c874981b9de0f8f4b7c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588882"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Använd Logic Apps för att ta emot e-post om status ändringar för Key Vault-hemligheter

I den här guiden får du lära dig hur du svarar på Azure Key Vault händelser som tas emot via [Azure Event Grid](../../event-grid/index.yml) med [Azure Logic Apps](../../logic-apps/index.yml). När du är klar har du en Azure Logic app som är konfigurerad för att skicka ett e-postmeddelande varje gång en hemlighet skapas i Azure Key Vault.

En översikt över Azure Key Vault/Azure Event Grid-integrering finns i [övervaknings Key Vault med Azure Event Grid (för hands version)](event-grid-overview.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett e-postkonto från valfri e-postleverantör som stöds av Azure Logic Apps (till exempel Office 365 Outlook). Det här e-postkontot används för att skicka händelsemeddelandena. En fullständig lista över Logic App-anslutningsprogram som stöds finns i [Översikt över anslutningsappar](/connectors)
- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Ett nyckel valv i din Azure-prenumeration. Du kan snabbt skapa ett nytt nyckel valv genom att följa stegen i [Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure CLI](../secrets/quick-create-cli.md).
- Registrerade Event Grid som en resurs leverantör, se [resurs leverantörs registreringar](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)

## <a name="create-a-logic-app-via-event-grid"></a>Skapa en Logic app via Event Grid

Börja med att skapa en Logic app med event Grid-hanteraren och prenumerera på Azure Key Vault "SecretNewVersionCreated"-händelser.

Följ dessa steg om du vill skapa en Azure Event Grid-prenumeration:

1. I Azure Portal går du till ditt nyckel valv, väljer **händelser > kom igång** och klickar på **Logic Apps**

    
    ![Sidan Key Vault-händelser](../media/eventgrid-logicapps-kvsubs.png)

1. Verifiera anslutningen i **Logic Apps designer** och klicka på **Fortsätt** 
 
    ![Logic App Designer – anslutning](../media/eventgrid-logicappdesigner1.png)

1. På skärmen **när en resurs händelse inträffar** gör du följande:
    - Lämna kvar **prenumerations** -och **resurs namn** som standard.
    - Välj **Microsoft. nyckel valv. valv** för **resurs typen**.
    - Välj **Microsoft. SecretNewVersionCreated** för **händelse typ objekt – 1**.

    ![Logic App Designer – händelse hanterare](../media/eventgrid-logicappdesigner2.png)

1. Välj **+ nytt steg** då öppnas ett fönster där du kan välja en åtgärd.
1. Sök efter **E-post**. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. I den här självstudien används **Office 365 Outlook**. Stegen för andra e-postleverantörer liknar dessa.
1. Välj åtgärden **Skicka ett e-postmeddelande (v2)** .

   ![Logic App Designer – Lägg till e-post](../media/eventgrid-logicappdesigner3.png)

1. Bygg din e-postmall:
    - **För att:** Ange e-postadressen som e-postmeddelandena ska skickas till. I den här självstudien använder du ett e-postkonto som du kan komma åt för testning.
    - **Ämne** och **Brödtext**: Skriv e-postmeddelandets text. Välj JSON-egenskaper från valverktyget för att ta med dynamiskt innehåll baserat på händelsedata. Du kan hämta data om händelsen med hjälp av `@{triggerBody()?['Data']}` .

    Din e-postmall kan se ut som i det här exemplet.

    ![Logic App Designer – Lägg till e-post](../media/eventgrid-logicappdesigner4.png)

8. Klicka på **Spara som**.
9. Ange ett **namn** på den nya Logic-appen och klicka på **skapa**.
    
    ![Logic App Designer – Lägg till e-post](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testa och verifiera

1.  Gå till ditt nyckel valv på Azure Portal och välj **händelser > händelse prenumerationer**.  Verifiera att en ny prenumeration har skapats
    
    ![Logic App Designer – Lägg till e-post](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Gå till ditt nyckel valv, Välj **hemligheter**och välj **+ generera/importera**. Skapa en ny hemlighet för testnings ändamål och ge nyckeln och Behåll de återstående parametrarna i standardinställningarna.

    ![Key Vault – skapa hemlighet](../media/eventgrid-logicapps-kv-create-secret.png)

1. På skärmen **skapa en hemlighet** anger du valfritt namn, valfritt värde och väljer **skapa**.

När hemligheten skapas tas ett e-postmeddelande emot via de konfigurerade adresserna.

## <a name="next-steps"></a>Nästa steg

- Översikt: [övervaka Key Vault med Azure Event Grid (förhands granskning)](event-grid-overview.md)
- Gör så här: [skicka meddelanden till nyckel valv till Azure Automation](event-grid-tutorial.md).
- [Azure Event Grid händelse schema för Azure Key Vault (förhands granskning)](../../event-grid/event-schema-key-vault.md)
- Läs mer om [Azure Event Grid](../../event-grid/index.yml).
- Läs mer om [Logic Apps-funktionen i Azure App Service](../../logic-apps/index.yml).
