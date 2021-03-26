---
title: Felsöka problem i installations upplevelsen för Azure percept DK
description: Få fel söknings tips för några av de vanligaste problemen som påträffas under installations upplevelsen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608501"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Fel söknings guide för Azure percept DK-installation

I tabellen nedan finns lösningar på vanliga problem som kan uppstå under installationen av [Azure PERCEPT DK](./quickstart-percept-dk-set-up.md). Kontakta Azures kund support om problemet kvarstår.

|Problem|Anledning|Lösning|
|:-----|:------|:----------|
|När du ansluter till Azure-kontots registrerings sidor eller Azure Portal kan du logga in automatiskt med ett cachelagrat konto. Om detta inte är det konto som du vill använda kan det leda till en upplevelse som är inkonsekvent med dokumentationen.|Detta beror vanligt vis på att en inställning i webbläsaren är "kom ihåg" ett konto som du tidigare har använt.|På sidan Azure klickar du på ditt konto namn i det övre högra hörnet och väljer **Logga ut**. Du kommer sedan att kunna logga in med rätt konto.|
|Azure percept DK Wi-Fi Access Point (SCZ-xxxx eller APD-xxxx) visas inte i listan över tillgängliga Wi-Fi nätverk.|Detta är vanligt vis ett tillfälligt problem som löses inom 15 minuter.|Vänta tills nätverket visas. Om den inte visas efter mer än 15 minuter startar du om enheten.|
|Anslutningen till Azure percept DK Wi-Fi åtkomst punkten kopplas ofta från.|Detta kan bero på en dålig anslutning mellan enheten och värddatorn. Det kan också orsakas av störningar från andra Wi-Fi anslutningar på värddatorn.|Se till att antennerna är korrekt kopplade till dev-paketet. Om dev-paketet är långt bort från värddatorn kan du försöka att flytta det närmare. Stäng av alla andra Internet anslutningar, till exempel LTE/5G, om de körs på värddatorn.|
|Värddatorn visar en säkerhets varning om anslutningen till Azure percept DK-åtkomst punkten.|Detta är ett känt problem som kommer att åtgärdas i en senare uppdatering.|Det är säkert att gå vidare i installations miljön.|
|Azure percept DK Wi-Fi åtkomst punkten (SCZ-xxxx eller APD-xxxx) visas i listan över nätverk, men det går inte att ansluta.|Detta kan bero på ett tillfälligt fel i dev-paketets Wi-Fi åtkomst punkt.|Starta om dev kit och försök igen.|
|Det går inte att ansluta till ett Wi-Fi nätverk under installations miljön.|Wi-Fi nätverket måste för närvarande ha Internet anslutning för att kunna kommunicera med Azure. EAP [PEAP/MSCHAP], inbyggda portaler och Enterprise EAP-TLS-anslutning stöds inte för närvarande.|Se till att din Wi-Fi nätverks typ stöds och att den är ansluten till Internet.|