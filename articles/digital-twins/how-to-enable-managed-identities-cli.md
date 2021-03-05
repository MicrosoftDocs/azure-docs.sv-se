---
title: Aktivera en hanterad identitet för vägvals händelser (för hands version) – CLI
titleSuffix: Azure Digital Twins
description: Se hur du aktiverar en tilldelad identitet för Azure Digitals och använder den för att vidarebefordra händelser med hjälp av Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9ce87584373bd87a8f89ecb4ea692b44d3fab4d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202968"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Aktivera en hanterad identitet för routning av Azure Digitals sammanflätade händelser (för hands version): Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Den här artikeln beskriver hur du aktiverar en [systemtilldelad identitet för en Azure Digitals-instans](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (för närvarande i för hands version) och använder identiteten vid vidarebefordran av händelser till destinationer som stöds, till exempel [händelsehubben](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   destinationer och [Azure Storage container](../storage/blobs/storage-blobs-introduction.md).

Den här artikeln vägleder dig genom processen med [**Azure CLI**](/cli/azure/what-is-azure-cli).

Här följer de steg som beskrivs i den här artikeln: 

1. Skapa en digital Azure-instans med en tilldelad identitet eller aktivera systemtilldelad identitet på en befintlig Azure Digitals-instans. 
1. Lägg till en lämplig roll eller roller i identiteten. Du kan till exempel tilldela identiteten för **data avsändaren för Azure Event Hub** till identiteten om slut punkten är händelsehubben eller **Azure Service Bus data avsändarens roll** om slut punkten är Service Bus.
1. Skapa en slut punkt i Azures digitala dubbla nätverk som kan använda systemtilldelade identiteter för autentisering.

## <a name="enable-system-managed-identities-for-an-instance"></a>Aktivera systemhanterade identiteter för en instans 

När du aktiverar en tilldelad identitet på din Azure Digital-instansen skapar Azure automatiskt en identitet för den i [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Identiteten kan sedan användas för att autentisera till Azure Digitals-slutpunkter för vidarebefordran av händelser.

Du kan aktivera systemhanterade identiteter för en digital Azure-instans **som en del av instansens första installation**, eller **Aktivera den senare på en instans som redan finns**.

Någon av dessa metoder för att skapa kommer att ge samma konfigurations alternativ och samma slut resultat för din instans. I det här avsnittet beskrivs hur du gör båda.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Lägg till en Systemhanterad identitet när instansen skapas

I det här avsnittet får du lära dig hur du aktiverar en Systemhanterad identitet på en digital Azure-instans som håller på att skapas. 

Detta görs genom att lägga till en `--assign-identity` parameter till `az dt create` kommandot som används för att skapa instansen. (Mer information om det här kommandot finns i [referens dokumentationen](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) eller de [allmänna anvisningarna för att konfigurera en digital Azure-instans](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Om du vill skapa en instans med en Systemhanterad identitet lägger du till  `--assign-identity` parametern så här:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Lägga till en Systemhanterad identitet i en befintlig instans

I det här avsnittet ska du lägga till en Systemhanterad identitet till en digital Azure-instans som redan finns.

Detta görs också med `az dt create` kommandot och `--assign-identity` parametern. I stället för att ange ett nytt namn på en instans att skapa, kan du ange namnet på en instans som redan finns för att uppdatera värdet `--assign-identity` för för den instansen.

Kommandot för att **Aktivera** hanterad identitet är detsamma som kommandot för att skapa en instans med en Systemhanterad identitet. Alla ändringar är värdet för parametern instans namn:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Om du vill **inaktivera** hanterad identitet på en instans där den är aktive rad, använder du följande liknande kommando för att ställa in `--assign-identity` på `false` .

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Tilldela Azure-roller till identiteten 

När en systemtilldelad identitet har skapats för din Azure Digital-instansen måste du tilldela den lämpliga roller för att autentisera med olika typer av [slut punkter](concepts-route-events.md) för att vidarebefordra händelser till stödda destinationer. I det här avsnittet beskrivs roll alternativen och hur du tilldelar dem till den systemtilldelade identiteten.

>[!NOTE]
> Det här är ett viktigt steg – utan den, kan identiteten inte komma åt dina slut punkter och händelser levereras inte.

### <a name="supported-destinations-and-azure-roles"></a>Destinationer och Azure-roller som stöds 

Här är de minsta roller som en identitet behöver för att få åtkomst till en slut punkt, beroende på typen av mål. Roller med högre behörigheter (t. ex. data ägar roller) kommer också att fungera.

| Mål | Azure-roll |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs data avsändare |
| Azure Service Bus | Azure Service Bus data avsändare |
| Azure Storage-behållare | Storage Blob Data-deltagare |

Mer information om slut punkter, vägar och typer av destinationer som stöds för routning i digitala Azure-platser finns i [*begrepp: händelse vägar*](concepts-route-events.md).

### <a name="assign-the-role"></a>Tilldela rollen

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Du kan lägga till `--scopes` parametern `az dt create` i kommandot för att tilldela identiteten till ett eller flera omfång med en angiven roll. Detta kan användas när du först skapar instansen eller senare genom att skicka namnet på en instans som redan finns.

Här är ett exempel som skapar en instans med en Systemhanterad identitet och tilldelar identiteten en anpassad roll som anropas `MyCustomRole` i en händelsehubben.

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Fler exempel på roll tilldelningar med det här kommandot finns i [ **AZ DT Create** Reference documentation](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create).

Du kan också använda kommando gruppen [**AZ roll tilldelning**](/cli/azure/role/assignment) för att skapa och hantera roller. Detta kan användas för att stödja ytterligare scenarier där du inte vill gruppera roll tilldelning med kommandot CREATE.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Skapa en slut punkt med Identity-baserad autentisering

När du har konfigurerat en Systemhanterad identitet för din Azure Digital-instansen och tilldelar den rätt roll (er) kan du skapa Azure Digital- [slut punkter](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) som kan använda identiteten för autentisering. Det här alternativet är endast tillgängligt för Event Hub-och Service Bus-typ slut punkter (det stöds inte för Event Grid).

>[!NOTE]
> Du kan inte redigera en slut punkt som redan har skapats med nyckelbaserad identitet för att ändra till Identity-baserad autentisering. Du måste välja autentiseringstypen när slut punkten skapas först.

Detta görs genom att lägga till en `--auth-type` parameter till `az dt endpoint create` kommandot som används för att skapa slut punkten. (Mer information om det här kommandot finns i [referens dokumentationen](/cli/azure/ext/azure-iot/dt/endpoint/create) eller de [allmänna anvisningarna för att konfigurera en Azure Digitals-slutpunkt](how-to-manage-routes-apis-cli.md#create-the-endpoint)).

Om du vill skapa en slut punkt som använder Identity-baserad autentisering anger du `IdentityBased` autentiseringstypen med  `--auth-type` parametern. Exemplet nedan illustrerar detta för en Event Hubs slut punkt.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Att tänka på när du inaktiverar systemhanterade identiteter

Eftersom en identitet hanteras separat från slut punkterna som använder den, är det viktigt att överväga vilka effekter som alla ändringar i identiteten eller dess roller kan ha på slut punkterna i din Azure Digital-instansen. Om identiteten är inaktive rad eller om en nödvändig roll för en slut punkt tas bort från den, kan slut punkten bli otillgänglig och händelse flödet avbryts.

Om du vill fortsätta att använda en slut punkt som har kon figurer ATS med en hanterad identitet som nu har inaktiverats måste du ta bort slut punkten och återskapa [den](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) med en annan autentiseringstyp. Det kan ta upp till en timme för händelser att återuppta leverans till slut punkten efter den här ändringen.

## <a name="next-steps"></a>Nästa steg

Läs mer om hanterade identiteter i Azure AD: 
* [*Hanterade identiteter för Azure-resurser*](../active-directory/managed-identities-azure-resources/overview.md)