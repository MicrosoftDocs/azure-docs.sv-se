---
title: Ta emot och svara på Key Vault-meddelanden med Azure Event Grid
description: Lär dig hur du integrerar Key Vault med Azure Event Grid.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e46161812d122a1d5647e8589c58f9528578b878
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749842"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Ta emot och svara på Key Vault-meddelanden med Azure Event Grid

Azure Key Vault integrering med Azure Event Grid användaraviseringar när statusen för en hemlighet som lagras i ett nyckelvalv har ändrats. En översikt över den här funktionen finns i [Monitoring Key Vault with Event Grid](event-grid-overview.md).

Den här guiden beskriver hur du tar Key Vault meddelanden via Event Grid och hur du svarar på statusändringar via Azure Automation.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Ett nyckelvalv i din Azure-prenumeration. Du kan snabbt skapa ett nytt nyckelvalv genom att följa stegen i Ange och hämta en hemlighet från [Azure Key Vault hjälp av Azure CLI](../secrets/quick-create-cli.md).

## <a name="concepts"></a>Begrepp

Event Grid är en händelsetjänst för molnet. Genom att följa stegen i den här guiden prenumererar du på händelser för att Key Vault dirigera händelser till Automation. När en av hemligheterna i nyckelvalvet är på väg att upphöra Event Grid meddelas om statusändringen och gör en HTTP POST till slutpunkten. En web hook utlöser sedan en Automation-körning av ett PowerShell-skript.

![HTTP POST-flödesschema](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Skapa ett Automation-konto

Skapa ett Automation-konto via [Azure Portal](https://portal.azure.com):

1.  Gå till portal.azure.com och logga in på din prenumeration.

1.  I sökrutan anger du **Automation-konton**.

1.  Under avsnittet **Tjänster** i listrutan i sökfältet väljer du **Automation-konton.**

1.  Välj **Lägg till**.

    ![Fönstret Automation-konton](../media/event-grid-tutorial-2.png)

1.  Ange nödvändig information i fönstret **Lägg till Automation-konto** och välj sedan **Skapa.**

## <a name="create-a-runbook"></a>Skapa en runbook

När ditt Automation-konto är klart skapar du en runbook.

![Skapa ett runbook-användargränssnitt](../media/event-grid-tutorial-3.png)

1.  Välj det Automation-konto som du nyss skapade.

1.  Välj **Runbooks** under **Processautomatisering.**

1.  Välj **Skapa en runbook.**

1.  Namnge din runbook och välj **PowerShell** som runbooktyp.

1.  Välj den runbook som du skapade och välj sedan **knappen** Redigera.

1.  Ange följande kod (i testsyfte) och välj **knappen** Publicera. Den här åtgärden returnerar resultatet av den MOTTAGNA POST-begäran.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Publicera runbook-användargränssnitt](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>Skapa en webhook

Skapa en webhook för att utlösa din nyligen skapade runbook.

1.  Välj **Webhooks** i avsnittet **Resurser i** den runbook som du nyss publicerade.

1.  Välj **Lägg till webhook.**

    ![Knappen Lägg till webhook](../media/event-grid-tutorial-5.png)

1.  Välj **Skapa ny webhook.**

1. Namnge webhooken, ange ett förfallodatum och kopiera URL:en.

    > [!IMPORTANT] 
    > Du kan inte visa URL:en när du har skapat den. Se till att du sparar en kopia på en säker plats där du kan komma åt den under resten av den här guiden.

1. Välj **Parametrar och körningsinställningar** och välj **sedan OK.** Ange inga parametrar. Då aktiveras **knappen** Skapa.

1. Välj **OK** och sedan **Skapa.**

    ![Skapa nytt webhook-användargränssnitt](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

Skapa en Event Grid-prenumeration via [Azure Portal](https://portal.azure.com).

1.  Gå till nyckelvalvet och välj **fliken** Händelser.

    ![Fliken Händelser i Azure Portal](../media/event-grid-tutorial-7.png)

1.  Välj knappen **Händelseprenumeration.**

1.  Skapa ett beskrivande namn för prenumerationen.

1.  Välj **Event Grid Schema**.

1.  **Ämnesresursen** ska vara det nyckelvalv som du vill övervaka för statusändringar.

1.  För **Filtrera till händelsetyper** lämnar du alla alternativ markerade (**9 valda**).

1.  Som **Typ av slutpunkt** väljer du **Webhook**.

1.  Välj **Välj en slutpunkt.** I det nya kontextfönstret klistrar du in webhook-URL:en från steget [Skapa en webhook](#create-a-webhook) i fältet **Prenumerantslutpunkt.**

1.  Välj **Bekräfta markering** i kontextfönstret.

1.  Välj **Skapa**.

    ![Skapa händelseprenumeration](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Testa och verifiera

Kontrollera att Event Grid prenumerationen är korrekt konfigurerad. Det här testet förutsätter att du prenumererar på meddelandet "Secret New Version Created" (Hemligt ny version har skapats) i Create [an Event Grid subscription](#create-an-event-grid-subscription)(Skapa en Event Grid-prenumeration) och att du har de behörigheter som krävs för att skapa en ny version av en hemlighet i ett nyckelvalv.

![Testa konfiguration av Event Grid prenumeration](../media/event-grid-tutorial-9.png)

![Fönstret Skapa en hemlighet](../media/event-grid-tutorial-10.png)

1.  Gå till ditt nyckelvalv på Azure Portal.

1.  Skapa en ny hemlighet. I testsyfte anger du förfallodatumet till nästa dag.

1.  På fliken **Händelser** i nyckelvalvet väljer du den prenumeration Event Grid du skapade.

1.  Under **Mått kontrollerar** du om en händelse har avbildats. Två händelser förväntas: SecretNewVersion och SecretNearExpiry. Dessa händelser verifierar Event Grid har fångat statusändringen av hemligheten i ditt nyckelvalv.

    ![Fönstret Mått: sök efter avbildade händelser](../media/event-grid-tutorial-11.png)

1.  Gå till ditt Automation-konto.

1.  Välj fliken **Runbooks** och välj sedan den runbook som du skapade.

1.  Välj fliken **Webhooks** och bekräfta att tidsstämpeln "senast utlöst" är inom 60 sekunder från det att du skapade den nya hemligheten. Det här resultatet bekräftar Event Grid har gjort en POST till webhooken med händelseinformationen för statusändringen i nyckelvalvet och att webhooken utlöstes.

    ![Fliken Webhooks, tidsstämpeln Senast utlöst](../media/event-grid-tutorial-12.png)

1. Gå tillbaka till din runbook och välj **fliken** Översikt.

1. Titta på listan **Senaste** jobb. Du bör se att ett jobb har skapats och att statusen är slutförd. Detta bekräftar att webhooken utlöste runbooken för att börja köra dess skript.

    ![Webhook senaste jobblistan](../media/event-grid-tutorial-13.png)

1. Välj det senaste jobbet och titta på POST-begäran som skickades från Event Grid till webhooken. Granska JSON och kontrollera att parametrarna för nyckelvalvet och händelsetypen är korrekta. Om parametern "händelsetyp" i JSON-objektet matchar händelsen som inträffade i nyckelvalvet (i det här exemplet Microsoft.KeyVault.SecretNearExpiry) lyckades testet.

## <a name="troubleshooting"></a>Felsökning

### <a name="you-cant-create-an-event-subscription"></a>Du kan inte skapa en händelseprenumeration

Registrera om Event Grid nyckelvalvsprovidern i dina azure-prenumerationsresursproviders. Se [Resursproviders och typer i Azure.](../../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="next-steps"></a>Nästa steg

Grattis! Om du har följt alla de här stegen på rätt sätt är du nu redo att programmatiskt svara på statusändringar av hemligheter som lagras i ditt nyckelvalv.

Om du har använt ett avsökningsbaserat system för att söka efter statusändringar av hemligheter i dina nyckelvalv kan du nu börja använda den här meddelandefunktionen. Du kan också ersätta testskriptet i din runbook med kod för att programmatiskt förnya dina hemligheter när de snart upphör att gälla.

Läs mer:


- Översikt: [Övervaka Key Vault med Azure Event Grid](event-grid-overview.md)
- Gör så här: Ta [emot e-post när en nyckelvalvshemlighet ändras](event-grid-logicapps.md)
- [Azure Event Grid händelseschema för Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Azure Key Vault översikt](overview.md)
- [Översikt för Azure Event Grid](../../event-grid/overview.md)
- [Azure Automation översikt](../../automation/index.yml)
