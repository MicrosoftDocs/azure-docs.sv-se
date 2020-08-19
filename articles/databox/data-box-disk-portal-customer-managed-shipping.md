---
title: Microsoft Azure Data Box Disk självhanterad leverans | Microsoft Docs i data
description: Beskriver självhanterade leverans arbets flöden för Azure Data Box Disk enheter
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 95c0a884b335920df9803a9b468ab522646b26a1
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258748"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Använd självhanterad leverans för Azure Data Box Disk i Azure Portal

I den här artikeln beskrivs självhanterade leverans uppgifter för order, hämtning och utlämning av Azure Data Box Disk. Du kan hantera Data Box Disk med hjälp av Azure Portal.

## <a name="prerequisites"></a>Krav

Självhanterad leverans är tillgängligt som ett alternativ när du [beställer Azure Data Box disk](data-box-disk-deploy-ordered.md). Självhanterad leverans är endast tillgängligt i följande regioner:

* US Government
* Västra Europa
* Japan
* Singapore
* Sydkorea
* Sydafrika
* Indien (för hands version)

## <a name="use-self-managed-shipping"></a>Använd självhanterad frakt

När du placerar en Data Box Disk order kan du välja alternativet för självhanterad leverans.

1. I din Azure Data Box Disk ordning väljer du **+ Lägg till leverans adress**under **kontakt uppgifter**.

   ![Egenhanterad frakt](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. När du väljer leverans typ väljer du alternativet för **själv hantering av leverans** . Det här alternativet är bara tillgängligt om du befinner dig i en region som stöds enligt beskrivningen i kraven.

3. När du har angett din leverans adress måste du verifiera den och slutföra beställningen.

   ![Egenhanterad frakt](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. När enheten har förberetts och du fått ett e-postmeddelande kan du schemalägga en hämtning. I din Azure Data Box Disk ordning går du till **Översikt** och väljer sedan **Schemalägg hämtning**.

   ![Beställa en Data Box-enhet enhet för upphämtning](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Följ instruktionerna i **schemat för Azure**. Innan du kan hämta din auktoriseringskod måste du ha ett e-postmeddelande [adbops@microsoft.com](mailto:adbops@microsoft.com) för att schemalägga enheten från din regions Data Center.

   ![Boka upphämtning](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. När du har schemalagt enhets upphämtningen kan du Visa din auktoriseringskod i  **schemat pickup för Azure**.

   ![Visa din auktoriseringskod](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Anteckna den här **auktoriseringskod**.

   Enligt säkerhets kraven, vid tidpunkten för schemaläggning, är det nödvändigt att presentera namnet på den person som kommer att inkomma för hämtning.

   Du måste också ange information om vem som ska gå till data centret för hämtning. Du eller kontakt punkten måste ha ett godkänt foto-ID för myndigheter som ska val IDE ras i data centret.

   Dessutom måste den person som söker efter enheten också ha **auktoriseringskod**. Auktoriseringskod är unik för hämtning eller en avhämtning och bekräftas i data centret.

7. Din beställning flyttas automatiskt till det **plockade** läget när enheten har hämtats från data centret.

   ![Hämtat](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. När enheten har hämtats kan du kopiera data till Data Box Disk på din webbplats. När data kopieringen är klar kan du förbereda för att leverera Data Box Disk.

   När du är färdig med data kopieringen måste du kontakta åtgärder för att schemalägga en avtalad tid för List rutan. Du måste dela information om den person som kommer till data centret för att kunna ta bort diskarna. Data centret kommer också att behöva verifiera auktoriseringskod vid tidpunkten för avlämning. Auktoriseringskod för listering är tillgänglig i Azure Portal under **Schemaläggning**.

   > [!NOTE]
   > Dela inte auktoriseringskod via e-post. Detta är endast att verifieras i data centret under avlämning.

9. Om du har fått en avtalad tid för att ta bort beställningen bör du nu vara i läget **redo att ta emot på Azure Data Center** i Azure Portal.

   ![Visa din auktoriseringskod](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. När ditt ID och din auktoriseringskod har verifierats och du har tappat bort enheten i data centret ska order statusen **tas emot**.

    ![Har tagits emot](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. När enheten har tagits emot fortsätter data kopieringen. När kopieringen är klar är ordern slutförd.

## <a name="next-steps"></a>Nästa steg

* [Snabb start: Distribuera Azure Data Box Disk med hjälp av Azure Portal](data-box-disk-quickstart-portal.md)
