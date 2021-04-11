---
title: Aktivera hanterad identitet på Azure Event Grid anpassade ämnen och domäner
description: I den här artikeln beskrivs hur du aktiverar hanterad tjänst identitet för ett Azure Event Grid anpassat ämne eller domän.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 06fd4d6e472b33496e773596b0f3afc8e70be948
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630607"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Tilldela en Systemhanterad identitet till ett Event Grid anpassat ämne eller domän 
Den här artikeln visar hur du aktiverar en Systemhanterad identitet för ett Event Grid anpassat ämne eller en domän. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="enable-identity-at-the-time-of-creation"></a>Aktivera identitet vid skapande tillfället

### <a name="using-azure-portal"></a>Använda Azure Portal
Du kan aktivera systemtilldelad identitet för ett anpassat ämne eller en domän när du skapar den i Azure Portal. Följande bild visar hur du aktiverar en Systemhanterad identitet för ett anpassat ämne. I princip väljer du alternativet **Aktivera en systemtilldelad identitet** på sidan **Avancerat** i guiden skapa ämne. Du ser det här alternativet på sidan **Avancerat** i guiden skapa domän också. 

![Aktivera identitet när du skapar ett anpassat ämne](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan också använda Azure CLI för att skapa ett anpassat ämne eller en domän med en tilldelad identitet. Använd `az eventgrid topic create` kommandot med `--identity` parametern inställt på `systemassigned` . Om du inte anger något värde för den här parametern används standardvärdet `noidentity` . 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

På samma sätt kan du använda `az eventgrid domain create` kommandot för att skapa en domän med en Systemhanterad identitet.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Aktivera identitet för ett befintligt anpassat ämne eller en befintlig domän
I det här avsnittet får du lära dig hur du aktiverar en Systemhanterad identitet för ett befintligt anpassat ämne eller en befintlig domän. 

### <a name="using-azure-portal"></a>Använda Azure Portal
Följande procedur visar hur du aktiverar Systemhanterad identitet för ett anpassat ämne. Stegen för att aktivera en identitet för en domän är liknande. 

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Sök efter **händelse rutnäts ämnen** i Sök fältet högst upp.
3. Välj det **anpassade ämne** som du vill aktivera den hanterade identiteten för. 
4. Växla till fliken **identitet** . 
5. Aktivera **växeln** för att aktivera identiteten. 
1. Spara inställningen genom att välja **Spara** i verktygsfältet. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Sidan identitet för ett anpassat ämne"::: 

Du kan använda liknande steg för att aktivera en identitet för en Event Grid-domän.

### <a name="use-the-azure-cli"></a>Använda Azure CLI
Använd `az eventgrid topic update` kommandot med `--identity` set to `systemassigned` för att aktivera systemtilldelad identitet för ett befintligt anpassat ämne. Ange som värde om du vill inaktivera identiteten `noidentity` . 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Kommandot för att uppdatera en befintlig domän är liknande ( `az eventgrid domain update` ).


## <a name="next-steps"></a>Nästa steg
Lägg till identiteten i en lämplig roll (till exempel Service Bus data avsändare) på målet (till exempel en Service Bus kö). Detaljerade anvisningar finns i [lägga till identitet till Azure-roller på mål platser](add-identity-roles.md). 
