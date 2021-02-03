---
title: Microsoft Azure Data Box-enhet självhanterad leverans | Microsoft Docs i data
description: Beskriver självhanterade leverans arbets flöden för Azure Data Box enheter
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 07529b18191c71776a9a36edbfa4cfd8ded5af4f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524557"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Använd självhanterad leverans för Azure Data Box i Azure Portal

I den här artikeln beskrivs självhanterade leverans uppgifter för att beställa, hämta och ta bort en Azure Data Box enhet. Du kan hantera Data Box-enhet-enheten med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Självhanterad leverans är tillgängligt som ett alternativ när du [beställer Azure Data Box](data-box-deploy-ordered.md). Självhanterad leverans är endast tillgängligt i följande regioner:

* US Government
* Storbritannien
* Västra Europa
* Japan
* Singapore
* Sydkorea
* Indien
* Sydafrika
* Australien

## <a name="use-self-managed-shipping"></a>Använd självhanterad frakt

När du placerar en Data Box-enhet order kan du välja alternativet för självhanterad leverans.

1. I din Azure Data Box ordning väljer du **+ Lägg till leverans adress** under **kontakt uppgifter**.
 
   ![Självhanterad leverans, Lägg till leverans adress](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. När du väljer en leverans typ väljer du alternativet för **själv hantering av leverans** . Det här alternativet är bara tillgängligt om du befinner dig i en region som stöds enligt beskrivningen i kraven.

3. När du har angett din leverans adress måste du verifiera den och slutföra beställningen.

   ![Själv hanterad leverans, verifiera och Lägg till adress](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. När enheten har förberetts och du får ett e-postmeddelande för den kan du schemalägga en hämtning.

   I din Azure Data Box ordning går du till **Översikt** och väljer sedan **Schemalägg hämtning**.

   ![Data Box-enhets ordning, schema hämtnings alternativ](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Följ instruktionerna i **schemat för Azure**.

   Innan du kan hämta din auktoriseringskod måste du ha ett e-postmeddelande [adbops@microsoft.com](mailto:adbops@microsoft.com) för att schemalägga enheten från din regions Data Center.

   ![Schemalägg hämtning för Azure-instruktioner](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. När du har schemalagt enhets upphämtningen kan du Visa enhetens auktoriseringskod i fönstret **Schemalägg hämtning för Azure** .

   ![Visa enhetens auktoriseringskod](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Anteckna den här **auktoriseringskod**. Enligt säkerhets kraven, vid tidpunkten för schemaläggning, är det nödvändigt att presentera namnet på den person som skulle anlända till en hämtning.

   Du måste också ange information om vem som ska gå till data centret för hämtning. Du eller kontakt punkten måste ha ett godkänt foto-ID för myndigheter som ska val IDE ras i data centret.

   Dessutom måste den person som söker efter enheten också ha **auktoriseringskod**. Auktoriseringskod verifieras i Data Center tiden för hämtning.

7. Din beställning flyttas automatiskt till det **plockade** läget när enheten har hämtats från data centret.

    ![En order i hämtat tillstånd](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. När enheten har hämtats kopierar du data till Data Box-enhet på din plats. När data kopieringen är klar kan du förbereda för att leverera Data Box-enhet. Mer information finns i [Förbered för att skicka](data-box-deploy-picked-up.md#prepare-to-ship).

   **Förbered för att skicka** steget måste slutföras utan kritiska fel. Annars måste du köra det här steget igen när du har gjort nödvändiga korrigeringar. När **Förbered för att skicka** steget har slutförts kan du Visa auktoriseringskod för List rutan på enhetens lokala användar gränssnitt.

   > [!NOTE]
   > Dela inte auktoriseringskod via e-post. Detta är endast att verifieras i data centret under avlämning.

9. Om du har fått en avtalad tid för att ta bort den, bör den vara **redo att ta emot på Azure Data Center** -status i Azure Portal. Följ anvisningarna under **Schemaläggning** för att returnera enheten.

   ![Instruktioner för enhets listering](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. När ditt ID och din auktoriseringskod har verifierats och du har tappat bort enheten i data centret ska order statusen **tas emot**.

    ![En order med mottagen status](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. När enheten har tagits emot fortsätter data kopieringen. När kopieringen är klar är ordern slutförd.

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Data Box](data-box-quickstart-portal.md)
