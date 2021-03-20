---
title: Aktiverar systemtilldelad hanterad identitet för Azure Spring Cloud-program
description: Så här aktiverar du systemtilldelad hanterad identitet för program.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2fbdd46b872c4c70999355d457045f4aac8aa34c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92742808"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Så här aktiverar du systemtilldelad hanterad identitet för Azure våren Cloud Application

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Hanterade identiteter för Azure-resurser ger en automatiskt hanterad identitet i Azure Active Directory till en Azure-resurs, till exempel ditt Azure våren Cloud-program. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod.

Den här artikeln visar hur du aktiverar och inaktiverar systemtilldelade hanterade identiteter för en Azure våren Cloud-App med hjälp av Azure Portal och CLI (tillgängligt från version 0.2.4).

## <a name="prerequisites"></a>Förutsättningar
Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](../active-directory/managed-identities-azure-resources/overview.md).
Du behöver en distribuerad Azure våren-moln instans. Följ [snabb starten för att distribuera med hjälp av Azure CLI](spring-cloud-quickstart.md).

## <a name="add-a-system-assigned-identity"></a>Lägg till en tilldelad identitet
Om du skapar en app med en systemtilldelad identitet måste du ange ytterligare en egenskap för programmet.

### <a name="using-azure-portal"></a>Använda Azure Portal
Om du vill konfigurera en hanterad identitet i [Azure Portal](https://portal.azure.com/)skapar du först en app och aktiverar sedan funktionen.

1. Skapa en app i portalen på vanligt sätt. Gå till den i portalen.
2. Rulla ned till **inställnings** gruppen i det vänstra navigerings fönstret.
3. Välj **identitet**.
4. Växla **status** till *på på* fliken **systemtilldelad** . Klicka på **Spara**.

 ![Hanterad identitet i portalen](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan aktivera systemtilldelad hanterad identitet när du skapar appar eller i en befintlig app.

**Aktivera systemtilldelad hanterad identitet när en app skapas**

I följande exempel skapas en app med namnet *APP_NAME* med en systemtilldelad hanterad identitet, enligt begäran från `--assign-identity` parametern.

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**Aktivera systemtilldelad hanterad identitet i en befintlig app** Använd `az spring-cloud app identity assign` kommandot för att aktivera den systemtilldelade identiteten i en befintlig app.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Hämta token för Azure-resurser
En app kan använda sin hanterade identitet för att hämta token för att få åtkomst till andra resurser som skyddas av Azure Active Directory, till exempel Azure Key Vault. Dessa tokens representerar programmet som använder resursen, inte någon specifik användare av programmet.

Du kan behöva [Konfigurera mål resursen för att tillåta åtkomst från ditt program](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Om du till exempel begär en token för att få åtkomst till Key Vault, se till att du har lagt till en åtkomst princip som innehåller programmets identitet. Annars avvisas anrop till Key Vault, även om de inkluderar token. Mer information om vilka resurser som stöder Azure Active Directory tokens finns i [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Azure våren Cloud delar samma slut punkt för token-förvärv med Azure Virtual Machine. Vi rekommenderar att du använder Java SDK eller våren Boot starter för att hämta en token.  Se [hur du använder VM-token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) för olika kod-och skript exempel och rikt linjer för viktiga ämnen som hantering av token för förfallo datum och HTTP-fel.

Rekommenderat: Använd Java SDK eller våren Boot starter för att hämta tokens.  Se exemplen i [Nästa steg](#next-steps).

## <a name="disable-system-assigned-identity-from-an-app"></a>Inaktivera systemtilldelad identitet från en app
Om du tar bort en tilldelad identitet tas även den bort från Azure AD. Om du tar bort app-resursen tas systemtilldelade identiteter bort automatiskt från Azure AD.

### <a name="using-azure-portal"></a>Använda Azure Portal
Ta bort systemtilldelad hanterad identitet från en app som inte längre behöver den:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som är kopplat till Azure-prenumerationen som innehåller Azure våren Cloud-instansen.
1. Navigera till önskad virtuell dator och välj **identitet**.
1. Under **systemtilldelad** / **status** väljer du **av** och klickar sedan på **Spara**:

 ![Hanterad identitet](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Använda Azure CLI
Om du vill ta bort systemtilldelad hanterad identitet från en app som inte längre behöver den, använder du följande kommando:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Nästa steg

* [Åtkomst Azure Key Vault med hanterade identiteter i våren Boot starter](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Lär dig mer om hanterade identiteter för Azure-resurser](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Använda hanterade identiteter med Java SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
