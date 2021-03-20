---
title: Hitta klient-ID, objekt-ID och information om partner associationer i Azure Marketplace
description: Hur du hittar klient-ID, objekt-ID och partner associerings information om ett prenumerations-ID i Azure Marketplace.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 2b1ba0779649c4955987c7dae9802cefaba89b79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97109354"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Hitta klient-ID, objekt-ID och information om partner associationer

Den här artikeln beskriver hur du hittar klient-ID, objekt-ID och information om partner associationer, tillsammans med deras respektive prenumerations-ID.

Om du behöver ta skärm bilder av dessa objekt i Azure Cloud Shell för att kunna använda fel söknings hjälp, kan du gå vidare till [hitta klient, objekt och partner-ID Association för fel sökning](#find-ids-for-debugging).

>[!Note]
> Endast ägaren till en prenumeration har behörighet att utföra dessa steg.

## <a name="find-tenant-id"></a>Sök efter klient-ID

1. Gå till [Azure-portalen](https://ms.portal.azure.com/).
2. Välj **Azure Active Directory**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Ikonen Azure Active Directory i Azure Portal.":::

3. Välj **Översikt**. Ditt klient-ID bör visas under **grundläggande information**.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Välj grupper i Azure Portal.":::

## <a name="find-subscriptions-and-roles"></a>Hitta prenumerationer och roller

1. Gå till Azure Portal och välj **Azure Active Directory** som anges i steg 1 och 2 ovan.
2. Välj **Prenumerationer**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Prenumerations ikonen i Azure Portal.":::

3. Visa prenumerationer och roller.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Sidan prenumerationer i Azure Portal.":::

## <a name="find-partner-id"></a>Hitta partner-ID

1. Gå till prenumerations sidan enligt beskrivningen i föregående avsnitt.
2. Välj en prenumeration.
3. Under **fakturering** väljer du **partner information**.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Partner information på menyn till vänster-navigerings.":::

## <a name="find-user-object-id"></a>Hitta användare (objekt-ID)

1. Logga in på [administrations portalen för Office 365](https://portal.office.com/adminportal/home) med ett konto i önskad klient med lämpliga administrativa rättigheter.
2. På den vänstra menyn expanderar du avsnittet **Admin Center** längst ned och väljer sedan alternativet Azure Active Directory för att starta administrations konsolen i ett nytt webbläsarfönster.
3. Välj **Användare**.
4. Bläddra till eller Sök efter önskad användare och välj sedan konto namnet om du vill visa användar kontots profil information.
5. Objekt-ID: t finns i identitets avsnittet till höger.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Active Directory administrations Center.":::

6. Hitta **roll tilldelningar** genom att välja **åtkomst kontroll (IAM)** på den vänstra menyn och sedan **roll tilldelningar**.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Roll tilldelningar för Azure-resurser.":::

## <a name="find-ids-for-debugging"></a>Hitta ID: n för fel sökning

I det här avsnittet beskrivs hur du hittar klient-, objekt-och partner-ID-associationer för fel sökning.

1. Gå till [Azure-portalen](https://ms.portal.azure.com/).
2. Öppna Azure Cloud Shell genom att välja PowerShell-ikonen längst upp till höger.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="PowerShell-ikon överst till höger på skärmen.":::

3. Välj **PowerShell**.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Välj PowerShell-länken.":::

4. Välj rutan **prenumeration** för att välja den som partnern är länkad till och **skapa sedan lagring**. Detta är en engångs åtgärd. Fortsätt till nästa steg om du redan har konfigurerat lagring.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Välj knappen Skapa lagring.":::

5. Om du skapar (eller redan har) lagring öppnas fönstret Azure Cloud Shell.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Fönstret Azure Cloud Shell.":::

6. För partner associerings information installerar du tillägget med följande kommando:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell kommer att notera om tillägget redan är installerat:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Fönstret Azure Cloud Shell som visar tillägget är redan installerat.":::

7. Kontrol lera partner informationen med hjälp av det här kommandot:<br>`az managementpartner show --partner-id xxxxxx`<br>Exempel: `az managementpartner show --partner-id 4760962`.<br>Fäst en skärm bild av kommando resultaten, som ser ut ungefär så här:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Exempel skärm som visar resultatet från föregående kommando för att visa delar-ID.":::

>[!NOTE]
>Om flera prenumerationer kräver en skärm bild, använder du det här kommandot för att växla mellan prenumerationer:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Nästa steg

- [Länder och regioner som stöds](sell-from-countries.md)