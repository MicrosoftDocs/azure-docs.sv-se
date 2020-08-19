---
title: Distributions översikt – aver vFXT för Azure
description: Lär dig hur du distribuerar ett AVERT vFXT för Azure-kluster med den här översikten. Relaterade artiklar har olika distributions anvisningar.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271234"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Aver vFXT för Azure – distributions översikt

Den här artikeln ger en översikt över de steg som krävs för att hämta ett AVERT vFXT för Azure-kluster igång.

Flera aktiviteter behövs innan och efter att du har skapat vFXT-klustret från Azure Marketplace. Att ha en tydlig uppfattning om start-till-slut-processen hjälper dig att begränsa den insats som behövs.

## <a name="deployment-steps"></a>Distributionssteg

När du har [planerat systemet](avere-vfxt-deploy-plan.md)kan du börja skapa ditt AVERT vFXT-kluster.

En Azure Resource Manager-mall på Azure Marketplace samlar in nödvändig information och distribuerar automatiskt hela klustret.

När vFXT-klustret är igång finns det fortfarande vissa konfigurations steg som du kan vidta innan du använder det. Om du har skapat en ny Blob Storage-behållare vill du flytta dina data till den. Om du använder ett NAS-lagrings system måste du lägga till det när klustret har skapats. Du kommer att vilja ansluta klienter till klustret.

Här följer en översikt över alla steg.

1. Konfigurera krav

   Innan du skapar en virtuell dator måste du skapa en ny prenumeration för ditt AVERT vFXT-projekt, konfigurera prenumerationens ägarskap, kontrol lera kvoterna och begära en ökning om det behövs, och acceptera villkoren för att använda programmet AVERT vFXT. Mer information finns i [förbereda för att skapa ett AVERT-vFXT](avere-vfxt-prereqs.md) .

1. Skapa ett AVERT vFXT-kluster

   Använd Azure Marketplace för att skapa ett AVERT-vFXT för Azure-kluster. En mall samlar in nödvändig information och kör skript för att skapa den slutliga produkten.

   Kluster skapandet omfattar de här stegen, som alla görs av Marketplace-mallen:

   * Skapa ny nätverks infrastruktur och resurs grupper, om det behövs
   * Skapa en kluster styrenhet

     Kluster styrenheten är en enkel virtuell dator som finns i samma virtuella nätverk som det AVERT vFXT-klustret och har den anpassade program vara som krävs för att skapa och hantera klustret. Kontrollanten skapar vFXT-noderna och formaterar klustret och ger också ett kommando rads gränssnitt för att hantera klustret under dess livs längd.

     Om du skapar ett nytt virtuellt nätverk eller undernät under distributionen kommer din kontrollant att ha en offentlig IP-adress. Det innebär att styrenheten kan fungera som en hopp värd för att ansluta till klustret utanför det virtuella nätverket.

   * Skapa de virtuella klusternoderna

   * Skapa klustret från enskilda noder

   * Du kan också skapa en ny BLOB-behållare och konfigurera den som server dels lagring för klustret

   Skapande av kluster beskrivs i detalj i [distribuera vFXT-klustret](avere-vfxt-deploy.md).

1. Konfigurera klustret

   Anslut till konfigurations gränssnittet AVERT vFXT (AVERT kontroll panel) om du vill anpassa klustrets inställningar. Anmäl dig till support övervakning och Lägg till ditt lagrings system om du använder maskin varu lagring eller ytterligare BLOB-behållare.

   * [Komma åt vFXT-klustret](avere-vfxt-cluster-gui.md)
   * [Aktivera stöd](avere-vfxt-enable-support.md)
   * [Konfigurera lagring](avere-vfxt-add-storage.md) (om det behövs)

1. Montera klienter

   Följ rikt linjerna i [montera vFXT-klustret](avere-vfxt-mount-clients.md) för att lära dig mer om belastnings utjämning och hur klient datorer ska montera klustret.

1. Lägg till data (om det behövs)

   Eftersom AVERT vFXT är ett skalbart cacheminne för flera klienter, är det bästa sättet att flytta data till en ny server dels lagrings behållare med en fil kopierings strategi med flera klienter och flera trådar.

   Om du behöver flytta arbets mängds data till en ny BLOB-behållare eller ett annat Server dels lagrings system, följer du anvisningarna i [Flytta data till vFXT-klustret](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Nästa steg

Fortsätt att [förbereda för att skapa ett AVERT-vFXT](avere-vfxt-prereqs.md) för att slutföra de nödvändiga uppgifterna.
