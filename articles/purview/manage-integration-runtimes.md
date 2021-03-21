---
title: Skapa och hantera integrerings körningar
description: I den här artikeln beskrivs stegen för att skapa och hantera integrerings körningar i Azure avdelningens kontroll.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 9276f845c95b5e736180159b282ddedc33523c17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980753"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Skapa och hantera en integration runtime med egen värd

Den här artikeln beskriver hur du skapar och hanterar en egen värd för integration Runtime (SHIR) som gör att du kan söka igenom data källor i Azure avdelningens kontroll.

> [!NOTE]
> Avdelningens kontroll-Integration Runtime kan inte delas med en Azure Synapse-analys eller Azure Data Factory Integration Runtime på samma dator. Den måste installeras på en avgränsad dator.

## <a name="create-a-self-hosted-integration-runtime"></a>Skapa en lokal Integration Runtime

1. På Start sidan i avdelningens kontroll Studio väljer du **hanterings Center** i det vänstra navigerings fönstret.

2. Under **källor och skanning** i det vänstra fönstret väljer du **integrerings körningar** och väljer sedan **+ ny**.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Klicka på IR.":::

3. På sidan **installation av integration runtime** väljer du **egen värd** för att skapa en Self-Hosted IR och väljer sedan **Fortsätt**.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Skapa en ny SHIR.":::

4. Ange ett namn för din IR och välj Skapa.

5. Följ stegen under avsnittet **manuell installation** på sidan **integration runtime inställningar** . Du måste ladda ned integration runtime från nedladdnings platsen till en virtuell dator eller dator där du tänker köra den.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Hämta nyckel":::

   - Kopiera och klistra in nyckeln Authentication.

   - Ladda ned integration runtime med egen värd från [Microsoft integration runtime](https://www.microsoft.com/download/details.aspx?id=39717) på en lokal Windows-dator. Kör installationsprogrammet.

   - På sidan **registrera integration Runtime (lokal installation)** klistrar du in en av de två nycklarna som du sparade tidigare och väljer **Registrera**.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="inmatad nyckel.":::

   - På sidan **ny integration Runtime (lokal installation) nod** väljer du **Slutför**.

6. När den egen värdbaserade integrerings körningen har registrerats visas följande fönster:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="har registrerats.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Hantera en integration runtime med egen värd

Du kan redigera en integration runtime med egen värd genom att gå till **integrerings körningar** i **hanterings centret**, välja IR och sedan klicka på Redigera. Nu kan du uppdatera beskrivningen, Kopiera nyckeln eller återskapa nya nycklar.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="redigera IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="redigera IR-information.":::

Du kan ta bort en integration runtime med egen värd genom att gå till **integrerings körningar** i hanterings centret, välja IR och sedan klicka på **ta bort**. När en IR har tagits bort kommer pågående genomsökningar att förlita sig på den inte att fungera.

## <a name="next-steps"></a>Nästa steg

[Så identifierar genomsökningar borttagna tillgångar](concept-detect-deleted-assets.md)
