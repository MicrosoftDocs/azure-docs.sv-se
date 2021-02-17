---
title: Använda namngivna värden i Azure API Management-principer
description: Lär dig hur du använder namngivna värden i Azure API Management-principer. Namngivna värden kan innehålla litterala strängar, princip uttryck och hemligheter som lagras i Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 2bc9b1c5724fa7bab1fdf5ac9332d87ba03a6d11
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545844"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Använd namngivna värden i Azure API Management-principer

[API Management-principer](api-management-howto-policies.md) är en kraftfull funktion i systemet som gör att utgivaren kan ändra beteendet för API: et genom konfigurationen. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Princip satser kan konstrueras med hjälp av textuella text värden, princip uttryck och namngivna värden.

*Namngivna värden* är en global samling med namn/värde-par i varje API Management instans. Det finns ingen begränsning för antalet objekt i samlingen. Namngivna värden kan användas för att hantera konstanta sträng värden och hemligheter i alla API-konfigurationer och-principer. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Namngivna värden i Azure Portal":::

## <a name="value-types"></a>Värde typer

|Typ  |Description  |
|---------|---------|
|Administrera     |  Literal sträng eller princip uttryck     |
|Hemlighet     |   Litteral sträng eller ett princip uttryck som är krypterat med API Management      |
|[Nyckel valv](#key-vault-secrets)     |  Identifierare för en hemlighet som lagras i ett Azure Key Vault.      |

Vanliga värden och hemligheter kan innehålla [princip uttryck](./api-management-policy-expressions.md). Uttrycket returnerar till exempel `@(DateTime.Now.ToString())` en sträng som innehåller aktuellt datum och aktuell tid.

Mer information om namngivna värde-attribut finns i [referensen](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)till API Management REST API.

## <a name="key-vault-secrets"></a>Nyckel valvs hemligheter

Hemliga värden kan lagras antingen som krypterade strängar i API Management (anpassade hemligheter) eller genom att referera till hemligheter i [Azure Key Vault](../key-vault/general/overview.md). 

Det rekommenderas att du använder Key Vault-hemligheter eftersom det bidrar till att förbättra API Management säkerhet:

* Hemligheter som lagras i nyckel valv kan återanvändas mellan tjänster
* Detaljerade [åtkomst principer](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) kan tillämpas på hemligheter
* Hemligheter som uppdateras i nyckel valvet roteras automatiskt i API Management. Efter uppdateringen i nyckel valvet uppdateras ett namngivet värde i API Management inom 4 timmar. Du kan också uppdatera hemligheten manuellt med hjälp av Azure Portal eller via hanterings REST API.

### <a name="prerequisites-for-key-vault-integration"></a>Krav för Key Vault-integrering

1. Anvisningar för hur du skapar ett nyckel valv finns i [snabb start: skapa ett nyckel valv med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).
1. Aktivera en tilldelad eller användardefinierad [hanterad identitet](api-management-howto-use-managed-service-identity.md) i API Management-instansen.
1. Tilldela en [åtkomst princip för nyckel valvet](../key-vault/general/assign-access-policy-portal.md) till den hanterade identiteten med behörigheter för att hämta och lista hemligheter från valvet. Så här lägger du till principen:
    1. I portalen navigerar du till ditt nyckel valv.
    1. Välj **inställningar > åtkomst principer > + Lägg till åtkomst princip**.
    1. Välj **hemliga behörigheter** och välj sedan **Hämta** och **lista**.
    1. I **Välj huvud** namn väljer du resurs namnet för den hanterade identiteten. Om du använder en tilldelad identitet är huvud kontot namnet på din API Management-instans.
1. Skapa eller importera en hemlighet till nyckel valvet. Se [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](../key-vault/secrets/quick-create-portal.md).

Om du vill använda Key Vault Secret, [lägger du till eller redigerar ett namngivet värde](#add-or-edit-a-named-value)och anger en typ av **nyckel valv**. Välj hemligheten från nyckel valvet.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Lägg till eller redigera ett namngivet värde

### <a name="add-a-key-vault-secret"></a>Lägg till en nyckel valvs hemlighet

Se [krav för Key Vault-integrering](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> När du använder en Key Vault-hemlighet i API Management bör du vara noga med att inte ta bort hemligheten, nyckel valvet eller hanterad identitet som används för att komma åt nyckel valvet.

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Under **API: er** väljer du **namngivna värden**  >  **+ Lägg till**.
1. Ange en **namn** identifierare och ange ett **visnings namn** som används för att referera till egenskapen i principer.
1. I **Värdetyp** väljer du **Key Vault**.
1. Ange identifieraren för en nyckel valv hemlighet (utan version) eller Välj **Välj** för att välja en hemlighet från ett nyckel valv.
    > [!IMPORTANT]
    > Om du anger ett hemligt ID för Key Vault själv kontrollerar du att det inte finns någon versions information. Annars roteras hemligheten inte automatiskt i API Management efter en uppdatering i nyckel valvet.
1. I **klient identitet** väljer du en tilldelad dator eller en befintlig användardefinierad hanterad identitet. Lär dig hur du [lägger till eller ändrar hanterade identiteter i din API Management-tjänst](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Identiteten måste ha behörighet att hämta och lista hemligheter från nyckel valvet. Om du inte redan har konfigurerat åtkomst till nyckel valvet meddelar API Management dig så att den automatiskt kan konfigurera identiteten med de behörigheter som krävs.
1. Lägg till en eller flera valfria taggar för att organisera dina namngivna värden och **Spara** sedan.
1. Välj **Skapa**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Lägg till hemligt värde för Key Vault":::

### <a name="add-a-plain-or-secret-value"></a>Lägg till ett oformaterat eller hemligt värde

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Under **API: er** väljer du **namngivna värden**  >  **+ Lägg till**.
1. Ange en **namn** identifierare och ange ett **visnings namn** som används för att referera till egenskapen i principer.
1. I **Värdetyp** väljer du **Plain** eller **Secret**.
1. I **värde** anger du en sträng eller ett princip uttryck.
1. Lägg till en eller flera valfria taggar för att organisera dina namngivna värden och **Spara** sedan.
1. Välj **Skapa**.

När det namngivna värdet har skapats kan du redigera det genom att välja namnet. Om du ändrar visnings namnet, uppdateras alla principer som refererar till det namngivna värdet automatiskt för att använda det nya visnings namnet.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Börja använda Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Om du vill lägga till ett namngivet värde använder du kommandot [AZ APIM NV Create](/cli/azure/apim/nv#az_apim_nv_create) :

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

När du har skapat ett namngivet värde kan du uppdatera det med hjälp av kommandot [AZ APIM NV Update](/cli/azure/apim/nv#az_apim_nv_update) . Om du vill se alla dina namngivna värden kör du kommandot [AZ APIM NV List](/cli/azure/apim/nv#az_apim_nv_list) :

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Om du vill se information om det namngivna värdet som du skapade i det här exemplet kör du kommandot [AZ APIM NV show](/cli/azure/apim/nv#az_apim_nv_show) :

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Det här exemplet är ett hemligt värde. Föregående kommando returnerar inte värdet. Om du vill se värdet kör du kommandot [AZ APIM NV show-Secret](/cli/azure/apim/nv#az_apim_nv_show_secret) :

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Om du vill ta bort ett namngivet värde använder du kommandot [AZ APIM NV Delete](/cli/azure/apim/nv#az_apim_nv_delete) :

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Använd ett namngivet värde

I exemplen i det här avsnittet används de namngivna värden som visas i följande tabell.

| Name               | Värde                      | Hemlighet | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Falskt  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Sant   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Falskt  | 

Om du vill använda ett namngivet värde i en princip, placerar du dess visnings namn i ett dubbelt par klammerparenteser `{{ContosoHeader}}` som, som du ser i följande exempel:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

I det här exemplet `ContosoHeader` används som namn på en rubrik i en `set-header` princip och `ContosoHeaderValue` används som värde för den rubriken. När den här principen utvärderas under en begäran eller ett svar på API Management Gateway `{{ContosoHeader}}` och `{{ContosoHeaderValue}}` ersätts med deras respektive värden.

Namngivna värden kan användas som fullständiga attribut eller element värden, som du ser i föregående exempel, men de kan också infogas i eller kombineras med en del av ett sträng text uttryck som visas i följande exempel: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Namngivna värden kan också innehålla princip uttryck. I följande exempel `ExpressionProperty` används uttrycket.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

När den här principen utvärderas `{{ExpressionProperty}}` ersätts med dess värde `@(DateTime.Now.ToString())` . Eftersom värdet är ett princip uttryck utvärderas uttrycket och principen fortsätter med körningen.

Du kan testa detta i Azure Portal eller i [Developer-portalen](api-management-howto-developer-portal.md) genom att anropa en åtgärd som har en princip med namngivna värden i omfånget. I följande exempel anropas en åtgärd med de två tidigare exempel `set-header` principerna med namngivna värden. Observera att svaret innehåller två anpassade huvuden som har kon figurer ATS med hjälp av principer med namngivna värden.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="Test-API-svar":::

Om du tittar på den utgående [API-spårningen](api-management-howto-api-inspector.md) för ett anrop som innehåller de två tidigare exempel principerna med namngivna värden, kan du se de två `set-header` principerna med de namngivna värdena infogade samt utvärdering av princip uttryck för det namngivna värde som innehöll princip uttrycket.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="Spårning av API-kontroll":::

> [!CAUTION]
> Om en princip refererar till en hemlighet i Azure Key Vault, visas värdet från nyckel valvet för användare som har åtkomst till prenumerationer som är aktiverade för [spårning av API-begär Anden](api-management-howto-api-inspector.md).

Namngivna värden kan innehålla princip uttryck, de får inte innehålla andra namngivna värden. Om text som innehåller en namngiven värde referens används för ett värde, till exempel `Text: {{MyProperty}}` , kommer den här referensen inte att matchas och ersättas.

## <a name="delete-a-named-value"></a>Ta bort ett namngivet värde

Om du vill ta bort ett namngivet värde väljer du namnet och väljer sedan **ta bort** från snabb menyn (**...**).

> [!IMPORTANT]
> Om det namngivna värdet refereras till av en API Management-princip kan du inte ta bort det tills du tar bort det namngivna värdet från alla principer som använder det.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om att arbeta med principer
    -   [Principer i API Management](api-management-howto-policies.md)
    -   [Principreferens](./api-management-policies.md)
    -   [Principuttryck](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

