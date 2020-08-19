---
title: Så här får du support för AVERT vFXT för Azure
description: Lär dig hur du löser problem som kan uppstå när du distribuerar eller använder AVERT vFXT för Azure genom att skapa ett support ärende via Azure Portal.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 522d29505d7d10f5f6d97136f270f07a63053d19
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271115"
---
# <a name="get-help-with-your-system"></a>Få hjälp med systemet

För hjälp med ditt AVERT vFXT för Azure-system kan du få support på följande sätt:

* **Aver vFXT-problem** – Använd Azure Portal för att öppna ett support ärende för ditt AVERT vFXT enligt beskrivningen [nedan](#open-a-support-ticket-for-your-avere-vfxt).
* **Kvot** – om du har ett kvot problem kan du [begära en kvot ökning](#request-a-quota-increase)
* **Dokumentation och exempel** – om du hittar problem med den här dokumentationen eller exemplen bläddrar du längst ned på sidan med problemet och använder **feedback** -avsnittet för att söka efter befintliga problem och sedan på en ny fil om det behövs.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Öppna ett support ärende för ditt AVERT vFXT

Om du får problem när du distribuerar eller använder AVERT vFXT kan du be om hjälp via Azure Portal.

Följ dessa steg för att se till att ditt support ärende är taggat med en resurs från klustret. Att tagga biljetten hjälper oss att vidarebefordra den till rätt support resurs.

1. Från [https://portal.azure.com](https://portal.azure.com) väljer du **resurs grupper**. Bläddra till den resurs grupp som innehåller vFXT-klustret där problemet uppstod och klicka på något av de AVERT klusterbaserade virtuella datorerna.

    ![skärm bild av översikts panelen för Azure Portal resurs gruppen med en viss virtuell dator som är inringad](media/avere-vfxt-ticket-vm.png)

1. På sidan VM rullar du längst ned i den vänstra panelen och klickar på **ny supportbegäran**.

    ![Skärm bild av den Azure Portal VM-sidan för den virtuella datorn från föregående skärm bild. Den vänstra menyn rullas längst ned och "New Support Request" är inringad.](media/avere-vfxt-ticket-request.png)

1. På den första sidan i support förfrågan väljer du typ av problem och kontrollerar att rätt prenumeration har valts.

   Under **tjänst**klickar du på **alla tjänster** och tittar under **lagring** för att välja **aver vFXT**.

   Lägg till en kort sammanfattning och välj problem typen.

    ![skärm bild av en ny support förfrågan-skärm i Azure Portal. Fliken grundläggande är markerad. Skärm objekt är ärende typ, prenumeration, tjänst, Sammanfattning och problem typ.](media/ticket-basics.png)

   Klicka på **Nästa** för att fortsätta.

1. Den andra sidan i support formuläret innehåller förslag på hur du kan åtgärda problemet baserat på din sammanfattnings beskrivning. Klicka på knappen **Nästa** längst ned om du fortfarande behöver skapa ett support ärende.

   ![skärm bild av den nya support förfrågan-fliken med fliken lösningar valt. Ett textfält i mitten har rubriken "Rekommenderad lösning" och förklarar möjliga lösningar.](media/ticket-solutions.png)

1. På den tredje sidan anger du information – detta omfattar information om klustret, den tid då problemet inträffade, allvarlighets grad och hur du kontaktar dig. Fyll i informationen och klicka på knappen **Nästa** längst ned.

   ![skärm bild av sidan ny support förfrågan med fliken information vald. Informations fält är indelade i kategorierna "problem information", "support metod" och "kontakt information".](media/ticket-details.png)

1. Granska informationen på den sista sidan och klicka på **skapa**. Ett bekräftelse-och biljett nummer skickas till din e-postadress och en support personal medlem kommer att kontakta dig.

## <a name="request-a-quota-increase"></a>Begär en kvot ökning

Läs [kvoten för vFXT-klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) för att lära dig vilka komponenter som behövs för att distribuera AVERT VFXT för Azure. Du kan [begära en kvot ökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) från Azure Portal.
