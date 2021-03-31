---
title: Felsöka Azure Migrate-projekt
description: Hjälper dig att felsöka problem med att skapa och hantera Azure Migrate projekt.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/01/2020
ms.openlocfilehash: f68a57d3780f388488d48835f322ff04ab7c7187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753390"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Felsöka Azure Migrate-projekt

Den här artikeln hjälper dig att felsöka problem när du skapar och hanterar [Azure Migrate](migrate-services-overview.md) projekt.

## <a name="how-to-add-new-project"></a>Hur lägger jag till nya projekt?

Du kan ha flera Azure Migrate projekt i en prenumeration. [Lär dig hur](./create-manage-projects.md) du skapar ett projekt för första gången eller [lägger till ytterligare](create-manage-projects.md#create-additional-projects) projekt.

## <a name="what-azure-permissions-are-needed"></a>Vilka Azure-behörigheter behövs?

Du måste ha deltagar-eller ägar behörigheter i prenumerationen för att kunna skapa ett Azure Migrate-projekt.

## <a name="cant-find-a-project"></a>Det går inte att hitta ett projekt

Att söka efter ett befintligt Azure Migrate projekt beror på om du använder den aktuella eller gamla versionen av Azure Migrate. [Följ](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Det går inte att hitta en geografi

Du kan skapa ett Azure Migrate-projekt i stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="what-are-vm-limits"></a>Vad är VM-gränser?

Du kan utvärdera upp till 35 000 virtuella VMware-datorer eller upp till 35 000 virtuella Hyper-V-datorer i ett enda projekt. Ett projekt kan innehålla både virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.

## <a name="can-i-upgrade-old-project"></a>Kan jag uppgradera gamla projekt?

Det går inte att uppdatera projekt från den tidigare versionen av Azure Migrate. Du måste [skapa ett nytt projekt](./create-manage-projects.md)och lägga till verktyg i det.

## <a name="cant-create-a-project"></a>Det går inte att skapa ett projekt

Om du försöker skapa ett projekt och påträffar ett distributions fel:

- Försök att skapa projektet igen om det är ett tillfälligt fel. I **distributioner** klickar du på **distribuera igen** för att försöka igen.
- Kontrol lera att du har deltagar-eller ägar behörigheter i prenumerationen.
- Om du distribuerar i en nyligen tillagd geografi väntar du en stund och försöker igen.
- Om du får felet "förfrågningar måste innehålla användar identitets rubriker", kan detta tyda på att du inte har åtkomst till den Azure Active Directory (Azure AD) av organisationen. Om så är fallet:
    - När du har lagt till en Azure AD-klient för första gången får du en e-postinbjudan om att ansluta till klienten.
    - Acceptera inbjudan som ska läggas till i klienten.
    - Om du inte kan se e-postmeddelandet kan du kontakta en användare med åtkomst till klienten och be dem att [Skicka inbjudan](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) till dig igen.
    - När du har tagit emot e-postinbjudan öppnar du den och väljer länken för att acceptera inbjudan. Logga sedan ut från Azure Portal och logga in igen. (att uppdatera webbläsaren fungerar inte.) Sedan kan du börja skapa migreringsjobbet.

## <a name="how-do-i-delete-a-project"></a>Hur gör jag för att ta bort ett projekt

Ta bort ett projekt [genom att följa dessa anvisningar](create-manage-projects.md#delete-a-project) . Observera att när du tar bort ett projekt raderas både projektet och metadata om identifierade datorer i projektet.

## <a name="added-tools-dont-show"></a>Verktyg som lagts till visas inte

Kontrol lera att du har valt rätt projekt. I Azure Migrate hubb >- **servrar** eller i **databaser** klickar du på **ändra** bredvid **migrera projekt (ändra)** i det övre högra hörnet på skärmen. Välj rätt prenumeration och projekt namn > **OK**. Sidan bör uppdateras med de verktyg som har lagts till i det valda projektet.

## <a name="next-steps"></a>Nästa steg

Lägg till [utvärderings](how-to-assess.md) -eller [migreringsverktyg](how-to-migrate.md) för att Azure Migrate projekt.