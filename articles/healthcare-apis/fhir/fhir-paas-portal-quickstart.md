---
title: 'Snabb start: Distribuera Azure API för FHIR med hjälp av Azure Portal'
description: I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR och konfigurerar inställningar med hjälp av Azure Portal.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 03/15/2020
ms.author: cavoeg
ms.openlocfilehash: cabacd5204f4a9ac5c17c7bd66924482b5bf688a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019616"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Snabb start: Distribuera Azure API för FHIR med hjälp av Azure Portal

I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR med hjälp av Azure Portal.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-new-resource"></a>Skapa ny resurs

Öppna [Azure Portal](https://portal.azure.com) och klicka på **skapa en resurs**

![Skapa en resurs](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Sök efter Azure API för FHIR

Du kan hitta Azure API för FHIR genom att skriva "FHIR" i sökrutan:

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Sök efter hälso vårds-API: er":::

## <a name="create-azure-api-for-fhir-account"></a>Skapa Azure API för FHIR-konto

Välj **skapa** för att skapa ett nytt Azure API för FHIR-konto:

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Skapa Azure API för FHIR-konto":::

## <a name="enter-account-details"></a>Ange konto information

Välj en befintlig resurs grupp eller skapa en ny, Välj ett namn för kontot och klicka slutligen på **Granska + skapa**:

:::image type="content" source="media/quickstart-paas-portal/portal-new-healthcareapi-details.png" alt-text="Ny hälso-API-information":::

Bekräfta skapandet och vänta på FHIR API-distribution.

## <a name="additional-settings-optional"></a>Ytterligare inställningar (valfritt)

Du kan också klicka på **Nästa: ytterligare inställningar** för att Visa autentiseringsinställningarna. Standard konfigurationen för Azure API för FHIR är att [använda Azure RBAC för att tilldela data Plans roller](configure-azure-rbac.md). När den konfigureras i det här läget kommer "auktoritet" för FHIR-tjänsten att ställas in på prenumerationens Azure Active Directory klient organisation:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode-create.png" alt-text="Standardinställningar för autentisering":::

Observera att rutan för att ange tillåtna objekt-ID är nedtonad, eftersom vi använder Azure RBAC för att konfigurera roll tilldelningar i det här fallet.

Om du vill konfigurera FHIR-tjänsten att använda en extern eller sekundär Azure Active Directory klient kan du ändra utfärdaren och ange objekt-ID: n för användare och grupper som ska få åtkomst till servern. Mer information finns i den [lokala konfigurations](configure-local-rbac.md) guiden för RBAC.

## <a name="fetch-fhir-api-capability-statement"></a>Hämta FHIR API Capability-instruktion

Om du vill kontrol lera att det nya FHIR API-kontot har tillhandahållits, hämtar du en kapacitets instruktion genom att peka en webbläsare till `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurs gruppen, Azure API för FHIR och alla relaterade resurser. Om du vill göra det väljer du den resurs grupp som innehåller Azure API för FHIR-kontot, väljer **ta bort resurs grupp** och bekräftar sedan namnet på den resurs grupp som ska tas bort.

## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden har du distribuerat Azure API för FHIR i din prenumeration. Om du vill ange ytterligare inställningar i Azure-API: et för FHIR går du vidare till ytterligare inställningar instruktions guide. Läs mer om hur du registrerar program om du är redo att börja använda Azure API för FHIR.

>[!div class="nextstepaction"]
>[Ytterligare inställningar i Azure API för FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Översikt över program register](fhir-app-registration.md)
