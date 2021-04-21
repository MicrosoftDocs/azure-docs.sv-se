---
title: Använda namngivna värden i Azure API Management principer
description: Lär dig hur du använder namngivna värden i Azure API Management principer. Namngivna värden kan innehålla literalsträngar, principuttryck och hemligheter som lagras i Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: b0e076f3b248942870ba58a51c85c3df1f1277a4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750616"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Använda namngivna värden i Azure API Management principer

[API Management är](api-management-howto-policies.md) en kraftfull funktion i systemet som gör att utgivaren kan ändra API:ets beteende genom konfiguration. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Principuttryck kan konstrueras med hjälp av literaltextvärden, principuttryck och namngivna värden.

*Namngivna* värden är en global samling namn/värde-par i varje API Management instans. Det finns ingen begränsning för antalet objekt i samlingen. Namngivna värden kan användas för att hantera konstanta strängvärden och hemligheter i alla API-konfigurationer och principer. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Namngivna värden i Azure Portal":::

## <a name="value-types"></a>Värdetyper

|Typ  |Description  |
|---------|---------|
|Vanligt     |  Literalsträng eller principuttryck     |
|Hemlighet     |   Literalsträng eller principuttryck som krypteras av API Management      |
|[Nyckelvalv](#key-vault-secrets)     |  Identifierare för en hemlighet som lagras i ett Azure-nyckelvalv.      |

Oformaterad värden eller hemligheter kan innehålla [principuttryck](./api-management-policy-expressions.md). Uttrycket returnerar till exempel `@(DateTime.Now.ToString())` en sträng som innehåller aktuellt datum och tid.

Mer information om namngivna värdeattribut finns i API Management [REST API referens .](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)

## <a name="key-vault-secrets"></a>Key Vault-hemligheter

Hemliga värden kan lagras antingen som krypterade strängar i API Management (anpassade hemligheter) eller genom att referera till hemligheter i [Azure Key Vault](../key-vault/general/overview.md). 

Vi rekommenderar att du använder key vault-hemligheter eftersom det hjälper API Management säkerhet:

* Hemligheter som lagras i nyckelvalv kan återanvändas i olika tjänster
* Detaljerade [åtkomstprinciper](../key-vault/general/security-overview.md#privileged-access) kan tillämpas på hemligheter
* Hemligheter som uppdateras i nyckelvalvet roteras automatiskt i API Management. Efter uppdateringen i nyckelvalvet uppdateras ett namngivet värde API Management inom 4 timmar. Du kan också uppdatera hemligheten manuellt med hjälp Azure Portal eller via hanterings-REST API.

### <a name="prerequisites-for-key-vault-integration"></a>Förutsättningar för Key Vault-integrering

1. Anvisningar för hur du skapar ett nyckelvalv finns [i Snabbstart: Skapa ett nyckelvalv med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).
1. Aktivera en system-tilldelad eller användar [tilldelad hanterad identitet](api-management-howto-use-managed-service-identity.md) i API Management instansen.
1. Tilldela en [åtkomstprincip för nyckelvalv](../key-vault/general/assign-access-policy-portal.md) till den hanterade identiteten med behörighet att hämta och lista hemligheter från valvet. Så här lägger du till principen:
    1. Navigera till ditt nyckelvalv i portalen.
    1. Välj **Inställningar > Åtkomstprinciper > +Lägg till åtkomstprincip**.
    1. Välj **Hemliga behörigheter** och välj sedan **Hämta** och **Lista.**
    1. I **Välj huvudnamn** väljer du resursnamnet för din hanterade identitet. Om du använder en system tilldelad identitet är huvudnamnet namnet på din API Management instansen.
1. Skapa eller importera en hemlighet till nyckelvalvet. Se [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](../key-vault/secrets/quick-create-portal.md).

Om du vill använda hemligheten för [nyckelvalvet lägger du till eller redigerar ett namngivet](#add-or-edit-a-named-value)värde och anger en typ av **nyckelvalv.** Välj hemligheten från nyckelvalvet.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Lägga till eller redigera ett namngivet värde

### <a name="add-a-key-vault-secret"></a>Lägga till en nyckelvalvshemlighet

Se [Krav för Key Vault-integrering.](#prerequisites-for-key-vault-integration)

> [!CAUTION]
> När du använder en nyckelvalvshemlighet i API Management bör du vara noga med att inte ta bort hemligheten, nyckelvalvet eller den hanterade identitet som används för att få åtkomst till nyckelvalvet.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din API Management instans.
1. Under **API:er** väljer du **Namngivna värden**  >  **+Lägg till**.
1. Ange ett **Namn-ID** och ange ett **Visningsnamn som används** för att referera till egenskapen i principer.
1. I **Värdetyp** väljer du **Nyckelvalv.**
1. Ange identifieraren för en nyckelvalvshemlighet (utan version) eller välj **Välj** för att välja en hemlighet från ett nyckelvalv.
    > [!IMPORTANT]
    > Om du själv anger ett nyckelvalvshemlighets-ID måste du se till att det inte har versionsinformation. Annars roteras hemligheten inte automatiskt i API Management efter en uppdatering i nyckelvalvet.
1. I **Klientidentitet** väljer du en system tilldelad eller en befintlig användar tilldelad hanterad identitet. Lär dig hur [du lägger till eller ändrar hanterade identiteter i API Management tjänsten](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Identiteten måste ha behörighet att hämta och lista hemligheter från nyckelvalvet. Om du inte redan har konfigurerat åtkomst till nyckelvalvet uppmanas du API Management så att den automatiskt kan konfigurera identiteten med nödvändiga behörigheter.
1. Lägg till en eller flera valfria taggar för att organisera dina namngivna värden och sedan **Spara**.
1. Välj **Skapa**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Lägga till hemligt värde för nyckelvalv":::

### <a name="add-a-plain-or-secret-value"></a>Lägga till ett oformaterad eller hemligt värde

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)navigerar du till din API Management instans.
1. Under **API:er** väljer du **Namngivna värden**  >  **+Lägg till**.
1. Ange ett **Namn-ID** och ange ett **Visningsnamn som används** för att referera till egenskapen i principer.
1. I **Värdetyp** väljer du **Oformaterad** eller **Hemlig.**
1. I **Värde** anger du en sträng eller ett principuttryck.
1. Lägg till en eller flera valfria taggar för att organisera dina namngivna värden och sedan **Spara**.
1. Välj **Skapa**.

När det namngivna värdet har skapats kan du redigera det genom att välja namnet. Om du ändrar visningsnamnet uppdateras automatiskt alla principer som refererar till det namngivna värdet för att använda det nya visningsnamnet.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Så här börjar du använda Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Om du vill lägga till ett namngivet värde använder [du kommandot az apim nv create:](/cli/azure/apim/nv#az_apim_nv_create)

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

När du har skapat ett namngivet värde kan du uppdatera det med kommandot [az apim nv update.](/cli/azure/apim/nv#az_apim_nv_update) Om du vill se alla namngivna värden kör du [kommandot az apim nv list:](/cli/azure/apim/nv#az_apim_nv_list)

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Om du vill se information om det namngivna värde som du skapade för det här exemplet kör du [kommandot az apim nv show:](/cli/azure/apim/nv#az_apim_nv_show)

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Det här exemplet är ett hemligt värde. Föregående kommando returnerar inte värdet. Om du vill se värdet kör du [kommandot az apim nv show-secret:](/cli/azure/apim/nv#az_apim_nv_show_secret)

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Om du vill ta bort ett namngivet värde använder [du kommandot az apim nv delete:](/cli/azure/apim/nv#az_apim_nv_delete)

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Använda ett namngivet värde

Exemplen i det här avsnittet använder de namngivna värden som visas i följande tabell.

| Name               | Värde                      | Hemlighet | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Falskt  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Sant   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Falskt  | 

Om du vill använda ett namngivet värde i en princip placerar du dess visningsnamn i ett dubbelt par med kparenteser som , som `{{ContosoHeader}}` du ser i följande exempel:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

I det här `ContosoHeader` exemplet används som namn på en rubrik i en princip och används som värde för `set-header` `ContosoHeaderValue` rubriken. När den här principen utvärderas under en begäran eller ett svar API Management gatewayen `{{ContosoHeader}}` `{{ContosoHeaderValue}}` och ersätts med deras respektive värden.

Namngivna värden kan användas som fullständiga attribut- eller elementvärden som visas i föregående exempel, men de kan också infogas i eller kombineras med en del av ett literalt textuttryck enligt följande exempel: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Namngivna värden kan också innehålla principuttryck. I följande exempel används `ExpressionProperty` uttrycket .

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

När den här principen utvärderas `{{ExpressionProperty}}` ersätts med värdet `@(DateTime.Now.ToString())` . Eftersom värdet är ett principuttryck utvärderas uttrycket och principen fortsätter med körningen.

Du kan testa detta i Azure Portal eller [utvecklarportalen](api-management-howto-developer-portal.md) genom att anropa en åtgärd som har en princip med namngivna värden i omfånget. I följande exempel anropas en åtgärd med de två föregående `set-header` exempelprinciperna med namngivna värden. Observera att svaret innehåller två anpassade huvuden som har konfigurerats med principer med namngivna värden.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="Testa API-svar":::

Om du tittar på [](api-management-howto-api-inspector.md) den utgående API-spårningen för ett anrop som innehåller de två föregående exempelprinciperna med namngivna värden kan du se de två principerna med de namngivna värdena infogade samt utvärderingen av principuttryck för det namngivna värdet som innehöll `set-header` principuttrycket.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API Inspector-spårning":::

> [!CAUTION]
> Om en princip refererar till en hemlighet i Azure Key Vault är värdet från nyckelvalvet synligt för användare som har åtkomst till prenumerationer som är aktiverade för [spårning av API-förfrågningar.](api-management-howto-api-inspector.md)

Namngivna värden kan innehålla principuttryck, men de får inte innehålla andra namngivna värden. Om text som innehåller en namngiven värdereferens används för ett värde, till exempel , matchas `Text: {{MyProperty}}` inte den referensen och ersätts inte.

## <a name="delete-a-named-value"></a>Ta bort ett namngivet värde

Om du vill ta bort ett namngivet värde väljer du namnet och sedan **Ta** bort på snabbmenyn (**...**).

> [!IMPORTANT]
> Om det namngivna värdet refereras av API Management principer kan du inte ta bort det förrän du tar bort det namngivna värdet från alla principer som använder det.

## <a name="next-steps"></a>Nästa steg

-   Läs mer om att arbeta med principer
    -   [Principer i API Management](api-management-howto-policies.md)
    -   [Principreferens](./api-management-policies.md)
    -   [Principuttryck](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

