---
title: Använd Azure CLI för att skapa Service Bus ämnen och prenumerationer
description: I den här snabb starten får du lära dig hur du skapar ett Service Bus ämne och prenumerationer på det avsnittet med hjälp av Azure CLI
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90069704"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Använd Azure CLI för att skapa ett Service Bus ämne och prenumerationer på ämnet
I den här snabb starten använder du Azure CLI för att skapa ett Service Bus ämne och sedan skapa prenumerationer på det avsnittet. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Vad är Service Bus-ämnen och -prenumerationer?
Service Bus-ämnen och -prenumerationer stöder en *publicera/prenumerera*-modell för meddelandekommunikation. När du använder ämnen och prenumerationer så kommunicerar inte komponenterna i ett distribuerat program direkt med varandra. Istället så utbyter de meddelanden via ett ämne, som agerar mellanhand.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

I motsats till Service Bus köer, där varje meddelande bearbetas av en enskild konsument, innehåller ämnen och prenumerationer en en-till-många-form av kommunikation med ett mönster för publicering/prenumeration. Det är möjligt att registrera flera prenumerationer för ett ämne. När ett meddelande skickas till ett ämne så görs det tillgängligt för varje prenumeration för oberoende hantering/bearbetning. En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Du kan välja att registrera filter regler för ett ämne per prenumeration, vilket gör att du kan filtrera eller begränsa vilka meddelanden till ett ämne som tas emot av vilka ämnes prenumerationer.

Med hjälp av Service Bus ämnen och prenumerationer kan du skala för att bearbeta ett stort antal meddelanden över ett stort antal användare och program.

## <a name="prerequisites"></a>Förutsättningar
Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto][free account] innan du börjar.

I den här snabb starten använder du Azure Cloud Shell som du kan starta när du har loggat in på Azure Portal. Mer information om Azure Cloud Shell finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md). Du kan också [Installera](/cli/azure/install-azure-cli) och använda Azure PowerShell på din dator. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Skapa ett Service Bus-ämne och prenumerationer
Varje [prenumeration på ett ämne](service-bus-messaging-overview.md#topics) får en kopia av varje meddelande. Ämnen är fullständigt protokollmässigt och semantiskt kompatibla med Service Bus-köer. Service Bus-ämnen stöder en mängd olika urvalsregler med filtervillkor, med valfria åtgärder som anger eller ändrar meddelandeegenskaperna. Varje gång en regel matchar så genererar den ett meddelande. Mer information om regler, filter och åtgärder, finns på den här [länken](topic-filters.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Starta Azure Cloud Shell genom att välja ikonen som visas i följande bild. Växla till **bash** -läge om Cloud Shell är i **PowerShell** -läge. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Starta Cloud Shell":::
3. Kör följande kommando för att skapa en Azure-resurs grupp. Uppdatera resurs gruppens namn och plats om du vill. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. Kör följande kommando för att skapa ett namn område för Service Bus meddelande tjänst. Uppdatera namnet på namn området som ska vara unikt. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. Kör följande kommando för att skapa ett ämne i namn området. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. Skapa den första prenumerationen på ämnet
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. Skapa den andra prenumerationen för ämnet
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. Skapa den tredje prenumerationen på ämnet
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. Skapa ett filter för den första prenumerationen med ett filter med anpassade egenskaper ( `StoreId` är en av `Store1` , `Store2` och `Store3` ).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. Skapa ett filter för den andra prenumerationen med ett filter med kund egenskaper ( `StoreId = Store4` )

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. Skapa ett filter för den tredje prenumerationen med ett filter med hjälp av kund egenskaper ( `StoreId` inte i `Store1` , `Store2` , `Store3` eller `Store4` ).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. Kör följande kommando för att hämta den primära anslutnings strängen för namn området. Du använder den här anslutnings strängen för att ansluta till kön och skicka och ta emot meddelanden. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Anteckna anslutnings strängen och ämnes namnet. Du kan använda dem för att skicka och ta emot meddelanden. 
    

## <a name="next-steps"></a>Nästa steg
Information om hur du skickar meddelanden till ett ämne och tar emot dessa meddelanden via en prenumeration finns i följande artikel: Välj programmeringsspråket i innehålls förteckningen. 

> [!div class="nextstepaction"]
> [Publicera och prenumerera på meddelanden](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
