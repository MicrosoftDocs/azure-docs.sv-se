---
title: Skydda en länkad tjänst
description: Lär dig hur du etablerar och skyddar en länkad tjänst med hanterat VNet
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 6be76878a9a07c5f4a1e2a9348bb7b09cb1b10eb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567595"
---
# <a name="secure-a-linked-service-with-private-links"></a>Skydda en länkad tjänst med privata länkar

I den här artikeln får du lära dig hur du skyddar en länkad tjänst i Synapse med en privat slutpunkt.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration:** Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage konto:** Du använder Azure Data Lake Gen2 som *källdatalager.* Om du inte har ett lagringskonto kan du gå till [Skapa ett Azure Storage konto](../../storage/common/storage-account-create.md) för anvisningar om hur du skapar ett. Kontrollera att lagringskontot har ip Synapse Studio filtrering för åtkomst till det och att du endast tillåter **valda** nätverk att komma åt lagringskontot. Inställningen under bladet **Brandväggar och virtuella nätverk bör** se ut som på bilden nedan.

![Skyddat lagringskonto](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Skapa en länkad tjänst med privata länkar

I Azure Synapse Analytics är en länkad tjänst där du definierar din anslutningsinformation till andra tjänster. I det här avsnittet lägger du till Azure Synapse Analytics Azure Data Lake Gen 2 som länkade tjänster.

1. Öppna Azure Synapse Studio och gå till **fliken** Hantera.
1. Under **Externa anslutningar** väljer du Länkade **tjänster**.
1. Om du vill lägga till en länkad tjänst väljer du **Ny.**
1. Välj Azure Data Lake Storage Gen2 panelen i listan och välj **Fortsätt.**
1. Kontrollera att du aktiverar **interaktiv redigering.** Det kan ta cirka 1 minut att aktiveras. 
1. Ange dina autentiseringsuppgifter. Kontonyckel, tjänstens huvudnamn och hanterad identitet stöds för närvarande av autentiseringstyper. Välj testanslutning för att verifiera att dina autentiseringsuppgifter är korrekta.
1. Välj **Testa anslutning.** Det bör misslyckas eftersom lagringskontot inte ger åtkomst till den utan att skapa och godkänna en privat slutpunkt. I felmeddelandet bör du se en länk för att skapa en **privat slutpunkt som** du kan följa för att gå till nästa del. Om du följer den länken hoppar du över nästa del.
1. Välj **Skapa** när du är klar.

## <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slutpunkt

Om du inte har valt hyperlänken när du testade anslutningen ovan följer du följande sökväg. Skapa en hanterad privat slutpunkt som du ansluter till den länkade tjänst som skapades ovan.

1. Gå till **fliken** Hantera.
1. Gå till **avsnittet Hanterade virtuella** nätverk.
1. Välj **+ Ny** under Hanterad privat slutpunkt.
1. Välj Azure Data Lake Storage Gen2 panelen i listan och välj **Fortsätt.**
1. Ange namnet på lagringskontot som du skapade ovan.
1. Välj **Skapa**
1. Du bör se efter att ha väntat några sekunder att den privata länk som skapats behöver ett godkännande.

## <a name="private-link-approval"></a>Godkännande av privat länk
1. Välj den privata slutpunkt som du skapade ovan. Du kan se en hyperlänk som gör att du kan godkänna den privata slutpunkten på lagringskontonivå. *Ett alternativ är att gå direkt till Azure Portal storage-konto och gå till **bladet Privata slutpunktsanslutningar.***
1. Markera den privata slutpunkt som du skapade i Studio och välj **Godkänn.**
1. Lägg till en beskrivning och välj **Ja**
1. Gå tillbaka du Synapse Studio under avsnittet **Hanterade virtuella** nätverk på **fliken** Hantera.
1. Det bör ta cirka 1 minut att få det godkännande som återspeglas för din privata slutpunkt.

## <a name="check-the-connection-works"></a>Kontrollera att anslutningen fungerar
1. Gå till fliken **Hantera** och välj den länkade tjänst som du skapade.
1. Kontrollera att **Interaktiv redigering är** aktiv.
1. Välj **Testanslutning**. Du bör se att anslutningen lyckades.

Nu har du upprättat en säker och privat anslutning mellan Synapse och din länkade tjänst.

## <a name="next-steps"></a>Nästa steg


Mer information om hanterade privata slutpunkter i Azure Synapse Analytics finns i [Hanterade privata slutpunkter.](../security/synapse-workspace-managed-private-endpoints.md)


Mer information om dataintegrering för Azure Synapse Analytics finns i [artikeln Mata in data i en Data Lake.](data-integration-data-lake.md)