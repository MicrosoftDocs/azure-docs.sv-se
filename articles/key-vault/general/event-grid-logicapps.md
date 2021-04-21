---
title: Skicka e Key Vault när status för hemligheten ändras
description: Guide för att använda Logic Apps att svara på Key Vault ändringar av hemligheter
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: e6ec06e3df87e0ad0e50efca24439435d655f6a1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752110"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Använd Logic Apps att ta emot e-post om statusändringar för Key Vault-hemligheter

I den här guiden får du lära dig att svara på Azure Key Vault händelser som tas emot via [Azure Event Grid](../../event-grid/index.yml) med hjälp [av Azure Logic Apps](../../logic-apps/index.yml). I slutändan har du en Azure-logikapp konfigurerad för att skicka ett e-postmeddelande varje gång en hemlighet skapas i Azure Key Vault.

En översikt över integreringen Azure Key Vault/Azure Event Grid finns i [Monitoring Key Vault with Azure Event Grid](event-grid-overview.md)(Övervaka Key Vault med Azure Event Grid ).

## <a name="prerequisites"></a>Förutsättningar

- Ett e-postkonto från valfri e-postleverantör som stöds av Azure Logic Apps (till exempel Office 365 Outlook). Det här e-postkontot används för att skicka händelsemeddelandena. En fullständig lista över Logic App-anslutningsprogram som stöds finns i [Översikt över anslutningsappar](/connectors)
- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Ett nyckelvalv i din Azure-prenumeration. Du kan snabbt skapa ett nytt nyckelvalv genom att följa stegen i Ange och hämta en hemlighet från [Azure Key Vault hjälp av Azure CLI](../secrets/quick-create-cli.md).
- Registrerade Event Grid som en resursprovider finns i Registreringar [för resursproviders](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="create-a-logic-app-via-event-grid"></a>Skapa en logikapp via Event Grid

Skapa först logikappen med Event Grid-hanteraren och prenumerera på Azure Key Vault "SecretNewVersionCreated"-händelser.

Följ dessa steg Azure Event Grid skapa en prenumeration:

1. I Azure Portal du till nyckelvalvet, väljer **Events > Get Started (Händelser som > igång)** och klickar på **Logic Apps**

    
    ![Key Vault – händelsesida](../media/eventgrid-logicapps-kvsubs.png)

1. På **Logic Apps Designer anslutningen** och klickar på **Fortsätt** 
 
    ![Logic App Designer – anslutning](../media/eventgrid-logicappdesigner1.png)

1. På skärmen **När en resurshändelse inträffar** gör du följande:
    - Lämna **Prenumeration** och **Resursnamn** som standard.
    - Välj **Microsoft.KeyVault.vaults** som **Resurstyp.**
    - Välj **Microsoft.KeyVault.SecretNewVersionCreated** som **Händelsetypobjekt – 1**.

    ![Logic App Designer – händelsehanterare](../media/eventgrid-logicappdesigner2.png)

1. Välj **+ Nytt steg.** Då öppnas ett fönster där du kan välja en åtgärd.
1. Sök efter **E-post**. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. I den här **självstudien används Office 365 Outlook**. Stegen för andra e-postleverantörer liknar dessa.
1. Välj åtgärden **Skicka ett e-postmeddelande (V2).**

   ![Logic App Designer – skicka e-post](../media/eventgrid-logicappdesigner3.png)

1. Skapa din e-postmall:
    - **Så här gör du för att:** Ange den e-postadress som e-postmeddelandena ska skickas till. I den här självstudien använder du ett e-postkonto som du kan komma åt för testning.
    - **Ämne** och **Brödtext**: Skriv e-postmeddelandets text. Välj JSON-egenskaper från valverktyget för att ta med dynamiskt innehåll baserat på händelsedata. Du kan hämta data för händelsen med hjälp av `@{triggerBody()?['Data']}` .

    Din e-postmall kan se ut som i det här exemplet.

    ![Logic App Designer – e-postmeddelandetext](../media/eventgrid-logicappdesigner4.png)

8. Klicka **på Spara som**.
9. Ange ett **namn för** den nya logikappen och klicka på **Skapa.**
    
    ![Logic App Designer – skapa](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testa och verifiera

1.  Gå till ditt nyckelvalv på Azure Portal och välj **Events > Event Subscriptions (Händelseprenumerationer).**  Kontrollera att en ny prenumeration har skapats
    
    ![Logic App Designer – testa och verifiera](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Gå till nyckelvalvet, välj **Hemligheter** och välj **+ Generera/importera.** Skapa en ny hemlighet för testning och namnge nyckeln och behåll de återstående parametrarna i standardinställningarna.

    ![Key Vault – Skapa hemlighet](../media/eventgrid-logicapps-kv-create-secret.png)

1. På skärmen **Skapa en hemlighet** anger du ett namn, ett värde och väljer **Skapa.**

När hemligheten skapas tas ett e-postmeddelande emot på de konfigurerade adresserna.

## <a name="next-steps"></a>Nästa steg

- Översikt: [Övervaka Key Vault med Azure Event Grid](event-grid-overview.md)
- Gör så här: [Dirigera key vault-meddelanden till Azure Automation](event-grid-tutorial.md).
- [Azure Event Grid händelseschema för Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- Läs mer om [Azure Event Grid](../../event-grid/index.yml).
- Läs mer om [Logic Apps-funktionen i Azure App Service](../../logic-apps/index.yml).