---
title: Konfigurera Microsoft Endpoint Configuration Manager – Azure
description: Så här konfigurerar du Microsoft Endpoint Configuration Manager att distribuera program uppdateringar till Windows 10 Enterprise multi-session på virtuella Windows-datorer.
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88010131"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Konfigurera Microsoft Endpoint Configuration Manager

Den här artikeln förklarar hur du konfigurerar Microsoft Endpoint Configuration Manager att automatiskt tillämpa uppdateringar på en Windows-värd för virtuella skriv bord som kör Windows 10 Enterprise multi-session.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera den här inställningen behöver du följande saker:

   - Kontrol lera att du har installerat Microsoft Endpoint Configuration Manager-agenten på dina virtuella datorer.
   - Kontrol lera att din version av Microsoft Endpoint Configuration Manager är minst på grennivå 1906. För bästa resultat bör du använda grennivå 1910 eller högre.

## <a name="configure-the-software-update-point"></a>Konfigurera programuppdateringsplatsen

För att få uppdateringar för Windows 10 Enterprise multi-session måste du aktivera Windows Server, version 1903 och senare som en produkt i Microsoft Endpoint Configuration Manager. Den här produkt inställningen gäller även om du använder Windows Server Update-tjänsten för att distribuera uppdateringar till dina system.

Ta emot uppdateringar:

1. Öppna Microsoft Endpoint Configuration Manager och välj **platser**.
2. Välj **Konfigurera plats komponenter**.
3. Välj **program uppdaterings plats** på den nedrullningsbara menyn.
4. Välj fliken **Produkter**.
5. Markera kryss rutan med texten **Windows Server, version 1903 och senare**.
6. Gå till program **bibliotek**  >  **Översikt**  >  **program uppdateringar**  >  **alla program uppdateringar** och välj **Synkronisera program uppdateringar**.
7. Kontrol lera att ändringarna har sparats i filen wsyncmgr. log i **program** filen  >  **Microsoft Configuration Manager**  >  **logs** . Det kan ta några minuter att synkronisera uppdateringarna.

## <a name="create-a-query-based-collection"></a>Skapa en fråge baserad samling

Om du vill skapa en samling av virtuella datorer med Windows 10 Enterprise multi-session kan du använda en fråge-baserad samling för att identifiera SKU: n för det angivna operativ systemet.

Så här skapar du en samling:

1. Välj **till gångar och efterlevnad**.
2. Gå till **Översikt över**  >  **enhets samlingar** och högerklicka på **enhets samlingar** och välj **skapa enhets samling** på den nedrullningsbara menyn.
3. På fliken **Allmänt** i menyn som öppnas anger du ett namn som beskriver din samling i fältet **namn** . I fältet **kommentar** kan du ge ytterligare information som beskriver vad samlingen är. I **begränsa insamling** definierar du vilka datorer som du vill inkludera i samlings frågan.
4. På fliken **medlemskaps regler** lägger du till en regel för din fråga genom att välja **Lägg till regel** och sedan **fråga regel**.
5. I **Egenskaper för frågeregel** anger du ett namn för regeln och definierar sedan regelns parametrar genom att välja **Redigera frågeuttryck**.
6. Välj **Visa frågeuttryck**.
7. Ange följande sträng i instruktionen:

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. Klicka på **OK** för att skapa samlingen.
9. Du kan kontrol lera om du har skapat samlingen genom att gå till **till gångar och efterlevnad**  >  **Översikt över**  >  **enhets samlingar**.
