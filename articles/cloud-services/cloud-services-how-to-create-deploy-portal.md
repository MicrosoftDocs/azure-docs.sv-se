---
title: Så här skapar och distribuerar du en moln tjänst (klassisk) | Microsoft Docs
description: Lär dig hur du använder snabb skapande metoden för att skapa en moln tjänst och använda upload för att ladda upp och distribuera ett moln tjänst paket i Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 879b86714adf50b5a4da4398389405063ac046dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743414"
---
# <a name="how-to-create-and-deploy-an-azure-cloud-service-classic"></a>Så här skapar och distribuerar du en Azure Cloud Service (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

I Azure Portal kan du skapa och distribuera en moln tjänst på två sätt: *snabb registrering* och *anpassad skapande*.

Den här artikeln förklarar hur du använder metoden snabb registrering för att skapa en ny moln tjänst och sedan använder du **upload** för att ladda upp och distribuera ett moln tjänst paket i Azure. När du använder den här metoden gör Azure Portal tillgängliga praktiska länkar för att slutföra alla krav när du går. Om du är redo att distribuera moln tjänsten när du skapar den kan du göra båda samtidigt med hjälp av anpassade skapa.

> [!NOTE]
> Om du planerar att publicera moln tjänsten från Azure DevOps använder du Quick Create och konfigurerar sedan Azure DevOps Publishing från Azure snabb start eller instrument panelen. Mer information finns i [kontinuerlig leverans till Azure med hjälp av Azure DevOps][TFSTutorialForCloudService], eller mer information finns på sidan **Snabbstart** .
>
>

## <a name="concepts"></a>Begrepp
Det krävs tre komponenter för att distribuera ett program som en moln tjänst i Azure:

* **Tjänst definition**  
  I moln tjänst definitions filen (. csdef) definieras tjänst modellen, inklusive antalet roller.
* **Tjänst konfiguration**  
  Moln tjänst konfigurations filen (. cscfg) innehåller konfigurations inställningar för moln tjänsten och enskilda roller, inklusive antalet roll instanser.
* **Tjänst paket**  
  Tjänst paketet (. cspkg) innehåller program koden och konfigurationerna och tjänst definitions filen.

Du kan lära dig mer om dessa och hur du skapar ett paket [här](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Förbered din app
Innan du kan distribuera en moln tjänst måste du skapa moln tjänst paketet (. cspkg) från din program kod och en moln tjänst konfigurations fil (. cscfg). Azure SDK innehåller verktyg för att förbereda de nödvändiga distributions filerna. Du kan installera SDK från sidan [Azure-nedladdningar](https://azure.microsoft.com/downloads/) på det språk som du vill utveckla din program kod på.

Tre moln tjänst funktioner kräver särskilda konfigurationer innan du exporterar ett service paket:

* Om du vill distribuera en moln tjänst som använder Transport Layer Security (TLS), som tidigare kallades Secure Sockets Layer (SSL) för data kryptering, [konfigurerar du ditt program](cloud-services-configure-ssl-certificate-portal.md#modify) för TLS.
* Om du vill konfigurera fjärr skrivbords anslutningar till roll instanser [konfigurerar du rollerna](cloud-services-role-enable-remote-desktop-new-portal.md) för fjärr skrivbord.
* Aktivera Azure-diagnostik för moln tjänsten om du vill konfigurera utförlig övervakning för din moln tjänst. *Minimal övervakning* (standard övervaknings nivån) använder prestanda räknare som samlats in från värd operativ systemen för roll instanser (virtuella datorer). *Utförlig övervakning* samlar in ytterligare mått baserat på prestanda data i roll instanserna för att möjliggöra en närmare analys av problem som uppstår under program bearbetningen. Information om hur du aktiverar Azure-diagnostik finns i [Aktivera diagnostik i Azure](cloud-services-dotnet-diagnostics.md).

Om du vill skapa en moln tjänst med distributioner av webb roller eller arbets roller måste du [skapa tjänst paketet](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Innan du börjar
* Om du inte har installerat Azure SDK klickar du på **Installera Azure SDK** för att öppna [sidan Azure-nedladdningar](https://azure.microsoft.com/downloads/)och laddar sedan ned SDK för det språk som du vill utveckla din kod för. (Du kommer att ha möjlighet att göra detta senare.)
* Om några roll instanser kräver ett certifikat skapar du certifikaten. Cloud Services kräver en. pfx-fil med en privat nyckel. Du kan ladda upp certifikaten till Azure när du skapar och distribuerar moln tjänsten.

## <a name="create-and-deploy"></a>Skapa och distribuera
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **skapa en resurs > Compute** och bläddra sedan ned till och klicka på **moln tjänst**.

    ![Publicera din moln Service1](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. I fönstret ny **moln tjänst** anger du ett värde för **DNS-namnet**.
4. Skapa en ny **resurs grupp** eller Välj en befintlig.
5. Välj en **plats**.
6. Klicka på **paket**. Då öppnas fönstret **Ladda upp ett paket** . Fyll i de obligatoriska fälten. Om någon av dina roller innehåller en enda instans, se till att **distribuera även om en eller flera roller innehåller en enda instans** har valts.
7. Kontrol lera att **Starta distribution** är markerat.
8. Klicka på **OK** för att stänga fönstret **Ladda upp ett paket** .
9. Om du inte har några certifikat att lägga till klickar du på **skapa**.

    ![Publicera din moln service2](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Ladda upp ett certifikat
Om distributions paketet har [kon figurer ATS för att använda certifikat](cloud-services-configure-ssl-certificate-portal.md#modify)kan du ladda upp certifikatet nu.

1. Välj **certifikat** och i fönstret **Lägg till certifikat** väljer du filen TLS/SSL Certificate. pfx och anger sedan **lösen ordet** för certifikatet.
2. Klicka på **bifoga certifikat** och sedan på **OK** i fönstret **Lägg till certifikat** .
3. Klicka på **skapa** i fönstret **moln tjänst** . När distributionen har nått statusen **klar** kan du fortsätta till nästa steg.

    ![Publicera din moln service3](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Verifiera att distributionen har slutförts
1. Klicka på moln tjänst instansen.

    Statusen bör visa att tjänsten **körs**.
2. Under **Essentials** klickar du på **webbplatsens URL** för att öppna moln tjänsten i en webbläsare.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: ./cloud-services-choose-me.md

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av din moln tjänst](cloud-services-how-to-configure-portal.md).
* Konfigurera ett [anpassat domän namn](cloud-services-custom-domain-name-portal.md).
* [Hantera din moln tjänst](cloud-services-how-to-manage-portal.md).
* Konfigurera [TLS/SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).