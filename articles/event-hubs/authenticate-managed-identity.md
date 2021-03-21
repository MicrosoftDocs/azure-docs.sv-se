---
title: Autentisera en hanterad identitet med Azure Active Directory
description: Den här artikeln innehåller information om hur du autentiserar en hanterad identitet med Azure Active Directory för åtkomst till Azure Event Hubs-resurser
ms.topic: conceptual
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2070cfd94b39a08afb86ffd3579f1116faac72d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98805300"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser
Azure Event Hubs stöder Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan ge åtkomst till Event Hubs resurser med hjälp av Azure AD-autentiseringsuppgifter från program som körs i Azure Virtual Machines (VM), Function-appar, Virtual Machine Scale Sets och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.

Den här artikeln visar hur du auktoriserar åtkomst till en Event Hub genom att använda en hanterad identitet från en virtuell Azure-dator.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator
Innan du kan använda hanterade identiteter för Azure-resurser för att auktorisera Event Hubs resurser från din virtuella dator måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Bevilja behörighet till en hanterad identitet i Azure AD
Om du vill auktorisera en begäran om att Event Hubs tjänst från en hanterad identitet i ditt program måste du först konfigurera inställningarna för Azure-rollbaserad åtkomst kontroll (Azure RBAC) för den hanterade identiteten. Azure Event Hubs definierar Azure-roller som omfattar behörigheter för att skicka och läsa från Event Hubs. När Azure-rollen tilldelas till en hanterad identitet beviljas den hanterade identiteten åtkomst till Event Hubs data i lämplig omfattning.

Mer information om hur du tilldelar Azure-roller finns i [autentisera med Azure Active Directory för åtkomst till Event Hubs resurser](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Använd Event Hubs med hanterade identiteter
Om du vill använda Event Hubs med hanterade identiteter måste du tilldela rollen och lämplig omfattning till identiteten. I proceduren i det här avsnittet används ett enkelt program som körs under en hanterad identitet och som har åtkomst till Event Hubs resurser.

Här ska vi använda ett exempel på ett webb program som finns i [Azure App Service](https://azure.microsoft.com/services/app-service/). Stegvisa instruktioner för hur du skapar ett webb program finns i [skapa en ASP.net Core webbapp i Azure](../app-service/quickstart-dotnetcore.md)

Följ dessa steg när programmet har skapats: 

1. Gå till **Inställningar** och välj **identitet**. 
1. Välj den **status** som ska **användas.** 
1. Spara inställningen genom att välja **Spara**. 

    :::image type="content" source="./media/authenticate-managed-identity/identity-web-app.png" alt-text="Hanterad identitet för en webbapp":::
4. Välj **Ja** i informations meddelandet. 

    När du har aktiverat den här inställningen skapas en ny tjänst identitet i din Azure Active Directory (Azure AD) och konfigureras i App Service-värden.

    Tilldela nu den här tjänst identiteten till en roll i det begärda omfånget i Event Hubs-resurser.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Så här tilldelar du Azure-roller med hjälp av Azure Portal
Om du vill tilldela en roll till Event Hubs resurser navigerar du till resursen i Azure Portal. Visa inställningarna för Access Control (IAM) för resursen och följ de här anvisningarna för att hantera roll tilldelningar:

> [!NOTE]
> Följande steg tilldelar en tjänst identitets roll till dina Event Hubs namnrum. Du kan följa samma steg för att tilldela en roll begränsad till en Event Hubs resurs. 

1. I Azure Portal navigerar du till Event Hubs namn området och visar **översikten** för namn området. 
1. Välj **Access Control (IAM)** på den vänstra menyn för att Visa inställningar för åtkomst kontroll för händelsehubben.
1.  Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar.
3.  Välj **Lägg till** och välj sedan * * Lägg till roll tilldelning * * *.
4.  På sidan **Lägg till roll tilldelning** följer du dessa steg:
    1. För **roll** väljer du den Event Hubs roll som du vill tilldela. I det här exemplet är det **Azure Event Hubs data ägare**.
    1. I fältet **tilldela åtkomst till väljer du** **App Service** under **systemtilldelad hanterad identitet**. 
    1. Välj den **prenumeration** där den hanterade identiteten för webbappen skapades.
    1. Välj den **hanterade identiteten** för den webbapp som du skapade. Standard namnet för identiteten är samma som namnet på webbappen. 
    1. Välj sedan **Spara**. 
    
        ![Sidan Lägg till roll tilldelning](./media/authenticate-managed-identity/add-role-assignment-page.png)

    När du har tilldelat rollen får webb programmet åtkomst till Event Hubs resurser under det definierade omfånget. 

    > [!NOTE]
    > En lista över tjänster som stöder hanterade identiteter finns i [tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="test-the-web-application"></a>Testa webbprogrammet
1. Skapa ett Event Hubs-namnområde och en Event Hub. 
2. Distribuera webbappen till Azure. Se följande flikade avsnitt för länkar till webb programmet på GitHub. 
3. Se till att SendReceive. aspx har angetts som standard dokument för webbappen. 
3. Aktivera **identitet** för webbappen. 
4. Tilldela den här identiteten till rollen **Event Hubs data ägare** på namn områdes nivå eller händelse navnivå. 
5. Kör webb programmet, ange namn på namnrymd och namn på händelsehubben, ett meddelande och välj **Skicka**. Om du vill ta emot händelsen väljer du **ta emot**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure. Messaging. EventHubs (senaste)](#tab/latest)
Nu kan du starta ditt webb program och peka webbläsaren på exempel-aspx-sidan. Du kan hitta det exempel webb program som skickar och tar emot data från Event Hubs resurser i [GitHub-lagrings platsen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Installera det senaste paketet från [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)och börja skicka händelser till Event Hubs att använda **EventHubProducerClient** och ta emot händelser med hjälp av **EventHubConsumerClient**. 

> [!NOTE]
> Ett Java-exempel som använder en hanterad identitet för att publicera händelser till en händelsehubben finns i [publicera händelser med Azure Identity-exempel på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (äldre)](#tab/old)
Nu kan du starta ditt webb program och peka webbläsaren på exempel-aspx-sidan. Du kan hitta det exempel webb program som skickar och tar emot data från Event Hubs resurser i [GitHub-lagrings platsen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Installera det senaste paketet från [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)och börja skicka till och ta emot data från Event Hub med hjälp av EventHubClient som visas i följande kod: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs för Kafka
Du kan använda Apache Kafka program för att skicka meddelanden till och ta emot meddelanden från Azure Event Hubs med hanterad identitet OAuth. Se följande exempel på GitHub: [Event Hubs för Kafka – skicka och ta emot meddelanden med hanterad identitet OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Exempel
- Exempel för **Azure. Messaging. EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Microsoft. Azure. EventHubs-exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    I de här exemplen används det gamla biblioteket **Microsoft. Azure. EventHubs** , men du kan enkelt uppdatera det så att det använder det senaste **Azure. Messaging. EventHubs** -biblioteket. Om du vill flytta exemplet från att använda det gamla biblioteket till ett nytt, se [guiden för att migrera från Microsoft. Azure. EventHubs till Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Det här exemplet har uppdaterats för att använda det senaste **Azure. Messaging. EventHubs** -biblioteket.
- [Event Hubs för Kafka – skicka och ta emot meddelanden med hanterad identitet OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Nästa steg
- I följande artikel finns information om hanterade identiteter för Azure-resurser: [Vad är Managed identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)
- Se följande relaterade artiklar:
    - [Autentisera begär anden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
    - [Autentisera begär anden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
    - [Ge åtkomst till Event Hubs resurser med Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Ge åtkomst till Event Hubs resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)
