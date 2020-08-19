---
title: Ta emot och svara på meddelanden om nyckel valv med Azure Event Grid
description: Lär dig hur du integrerar Key Vault med Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 283c66eb3b49b60b87283c5d94cc4f110adceffe
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588755"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Ta emot och svara på meddelanden om nyckel valv med Azure Event Grid (för hands version)

Azure Key Vault integration med Azure Event Grid (för närvarande i för hands version) aktiverar användar meddelande när status för en hemlighet som lagras i ett nyckel valv har ändrats. En översikt över den här funktionen finns i [övervaknings Key Vault med event Grid](event-grid-overview.md).

I den här guiden beskrivs hur du tar emot Key Vault-meddelanden via Event Grid och hur du svarar på status ändringar via Azure Automation.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Ett nyckel valv i din Azure-prenumeration. Du kan snabbt skapa ett nytt nyckel valv genom att följa stegen i [Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure CLI](../secrets/quick-create-cli.md).

## <a name="concepts"></a>Begrepp

Event Grid är en händelse tjänst för molnet. Genom att följa stegen i den här guiden kommer du att prenumerera på händelser för Key Vault och dirigera händelser till Automation. När en av hemligheterna i nyckel valvet håller på att gå ut får Event Grid ett meddelande om status ändringen och gör ett HTTP-inlägg till slut punkten. En webbhook utlöser sedan en Automation-körning av ett PowerShell-skript.

![HTTP POST-flödesschema](../media/image1.png)

## <a name="create-an-automation-account"></a>Skapa ett Automation-konto

Skapa ett Automation-konto via [Azure Portal](https://portal.azure.com):

1.  Gå till portal.azure.com och logga in på din prenumeration.

1.  I rutan Sök anger du **Automation-konton**.

1.  Under avsnittet **tjänster** i list rutan i Sök fältet väljer du **Automation-konton**.

1.  Välj **Lägg till**.

    ![Fönstret Automation-konton](../media/image2.png)

1.  Ange den information som krävs i fönstret **Lägg till Automation-konto** och välj sedan **skapa**.

## <a name="create-a-runbook"></a>Skapa en runbook

När ditt Automation-konto är klart skapar du en Runbook.

![Skapa ett användar gränssnitt för Runbook](../media/image3.png)

1.  Välj det Automation-konto som du nyss skapade.

1.  Välj **Runbooks** under **process automatisering**.

1.  Välj **skapa en Runbook**.

1.  Namnge din Runbook och välj **PowerShell** som Runbook-typ.

1.  Välj den Runbook som du skapade och välj sedan knappen **Redigera** .

1.  Ange följande kod (i test syfte) och välj knappen **publicera** . Den här åtgärden returnerar resultatet av POST-begäran som tagits emot.

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

![Publicera Runbook-användargränssnitt](../media/image4.png)

## <a name="create-a-webhook"></a>Skapa en webhook

Skapa en webhook för att utlösa din nyligen skapade Runbook.

1.  Välj **Webhooks** från avsnittet **resurser** i den Runbook som du nyss publicerat.

1.  Välj **Lägg till webhook**.

    ![Knappen Lägg till webhook](../media/image5.png)

1.  Välj **Skapa ny webhook**.

1. Namnge webhooken, ange ett förfallo datum och kopiera URL: en.

    > [!IMPORTANT] 
    > Du kan inte Visa URL: en när du har skapat den. Se till att spara en kopia på en säker plats där du kan komma åt den i resten av den här guiden.

1. Välj **parametrar och kör inställningar** och välj sedan **OK**. Ange inga parametrar. Då aktive ras knappen **skapa** .

1. Välj **OK** och välj sedan **skapa**.

    ![Skapa nytt webhook-gränssnitt](../media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

Skapa en Event Grid-prenumeration via [Azure Portal](https://portal.azure.com).

1.  Gå till ditt nyckel valv och välj fliken **händelser** . Om du inte kan se det kontrollerar du att du använder för [hands versionen av portalen](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![Fliken händelser i Azure Portal](../media/image7.png)

1.  Välj knappen **händelse prenumeration** .

1.  Skapa ett beskrivande namn för prenumerationen.

1.  Välj **Event Grid schema**.

1.  **Ämnes resurs** ska vara det nyckel valv som du vill övervaka för status ändringar.

1.  För **filtrera till händelse typer låter du**alla alternativ vara markerade (**9 valda**).

1.  Som **Typ av slutpunkt** väljer du **Webhook**.

1.  Välj **Välj en slut punkt**. I fönstret ny kontext klistrar du in webhook-URL: en från [skapa en webhook](#create-a-webhook) -åtgärd i fältet för **prenumerationens slut punkt** .

1.  Välj **Bekräfta markering** i kontext fönstret.

1.  Välj **Skapa**.

    ![Skapa händelse prenumeration](../media/image8.png)

## <a name="test-and-verify"></a>Testa och verifiera

Kontrol lera att din Event Grid-prenumeration är korrekt konfigurerad. Det här testet förutsätter att du prenumererar på meddelandet "hemlig ny version skapad" i [prenumerationen skapa en Event Grid](#create-an-event-grid-subscription)och att du har de behörigheter som krävs för att skapa en ny version av en hemlighet i ett nyckel valv.

![Testa konfigurationen av Event Grid prenumeration](../media/image9.png)

![Skapa ett hemligt fönster](../media/image10.png)

1.  Gå till ditt nyckel valv på Azure Portal.

1.  Skapa en ny hemlighet. I test syfte anger du förfallo datum till nästa dag.

1.  På fliken **händelser** i ditt nyckel valv väljer du den Event Grid prenumeration som du skapade.

1.  Kontrol lera om en händelse har registrerats under **mått**. Två händelser förväntas: SecretNewVersion och SecretNearExpiry. Dessa händelser verifierar att Event Grid har fångat status ändringen för hemligheten i ditt nyckel valv.

    ![Mått fönstret: Sök efter insamlade händelser](../media/image11.png)

1.  Gå till ditt Automation-konto.

1.  Välj fliken **Runbooks** och välj sedan den Runbook som du skapade.

1.  Välj fliken **Webhooks** och bekräfta att tidsstämpeln "senast utlöst" är inom 60 sekunder när du skapade den nya hemligheten. Det här resultatet bekräftar att Event Grid gjort ett inlägg i webhooken med händelse information om status ändringen i ditt nyckel valv och att webhooken utlöstes.

    ![Webhooks-fliken, senast utlöst tidstämpel](../media/image12.png)

1. Gå tillbaka till din Runbook och välj fliken **Översikt** .

1. Titta på listan **senaste jobb** . Du bör se att ett jobb har skapats och att statusen är slutförd. Detta bekräftar att webhooken utlöste runbooken för att börja köra skriptet.

    ![Lista över senaste jobb för webhook](../media/image13.png)

1. Välj det senaste jobbet och titta på POST-begäran som skickades från Event Grid till webhooken. Undersök JSON och se till att parametrarna för nyckel valvet och händelse typen är korrekta. Om parametern "händelse typ" i JSON-objektet matchar händelsen som inträffat i nyckel valvet (i det här exemplet Microsoft. Key Vault. SecretNearExpiry) lyckades testet.

## <a name="troubleshooting"></a>Felsökning

### <a name="you-cant-create-an-event-subscription"></a>Du kan inte skapa en händelse prenumeration

Registrera Event Grid-och Key Vault-providern i Azure-prenumerationens resurs leverantörer. Se [leverantörer och typer av Azure-resurser](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Nästa steg

Grattis! Om du har följt alla dessa steg är du nu redo för att program mässigt svara på status ändringar för hemligheter som lagras i ditt nyckel valv.

Om du har använt ett avsöknings system för att söka efter status ändringar för hemligheter i dina nyckel valv kan du nu börja använda den här meddelande funktionen. Du kan också ersätta test skriptet i din Runbook med kod för att program mässigt förnya dina hemligheter när de håller på att gå ut.

Läs mer:


- Översikt: [övervaka Key Vault med Azure Event Grid (förhands granskning)](event-grid-overview.md)
- Gör så här: [ta emot e-post när hemliga nyckel valv ändringar](event-grid-logicapps.md)
- [Azure Event Grid händelse schema för Azure Key Vault (förhands granskning)](../../event-grid/event-schema-key-vault.md)
- [Översikt över Azure Key Vault](overview.md))
- [Översikt för Azure Event Grid](../../event-grid/overview.md)
- [Översikt över Azure Automation](../../automation/index.yml)
