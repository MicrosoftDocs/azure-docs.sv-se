---
title: Felsök problem under den här insikts upplevelsen av Azure percept DK
description: Få fel söknings tips för några av de vanligaste problemen som påträffas under den här insikts upplevelsen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101663815"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Fel söknings guide för Azure percept DK onboarding

Här följer några problem som du kan stöta på när du använder Azure percept DK-onboarding. Om du har använt stegen i den här hand boken kvarstår problemet fortfarande. Kontakta Azures kund support.

|Problem|Anledning|Lösning|
|:-----|:------|:----------|
|När du ansluter till Azure-kontots registrerings sidor eller Azure Portal kan du logga in automatiskt med ett cachelagrat konto. Om detta inte är det konto du avsåg att använda kan det leda till en upplevelse som är inkonsekvent med dokumentationen.|Detta beror vanligt vis på att en inställning i webbläsaren är "kom ihåg" ett konto som du tidigare har använt.|På sidan Azure klickar du på det övre högra hörnet på sidan där det visar ditt konto namn och klickar sedan på Logga ut. Du kommer sedan att kunna logga in med rätt konto.|
|Azure percept DK Access Point-nätverket (SCZ-xxxx) visas inte i listan över tillgängliga Wi-Fi nätverk.|Detta är vanligt vis ett tillfälligt problem som löses med en liten stund.|Vänta tills nätverket visas. Om den inte är längre än 15 minuter startar du om enheten.|
|Anslutningen till Azure percept DK-åtkomst punkten kopplas ofta från.|Detta kan bero på en dålig anslutning mellan enheten och värddatorn. Det kan också orsakas av störningar från andra Wi-Fi anslutningar på värddatorn.|Se till att antennerna är korrekt kopplade till dev-paketet. Om dev-paketet är långt bort från värddatorn kan du försöka att flytta det närmare. Stäng av alla andra Internet anslutningar, till exempel LTE/5G, om de körs på värddatorn.|
|Värddatorn visar en säkerhets varning om anslutningen till Azure percept DK-åtkomst punkten.|Detta är ett känt problem som kommer att åtgärdas i en senare uppdatering.|Det är säkert att gå vidare genom onboarding-upplevelsen över devkit Wi-Fi åtkomst punkt.|
|Azure percept DK Access Point (SCZ-xxxx)-nätverket visas i listan över nätverk, men det går inte att ansluta.|Detta kan bero på ett tillfälligt fel i devkit Wi-Fi åtkomst punkt.|Starta om devkit och försök igen.|
|Det går inte att ansluta till ett Wi-Fi nätverk under installations miljön.|Det Wi-Fi nätverket måste för närvarande ha Internet anslutning för att vi ska kunna kommunicera med Azure. EAP [PEAP/MSCHAP], inbyggda portaler och Enterprise EAP-TLS-anslutning stöds inte för närvarande.|Se till att typen Wi-Fi nätverk som du ansluter stöds och är ansluten till Internet.|