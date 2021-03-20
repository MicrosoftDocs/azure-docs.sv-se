---
title: Skicka händelser från Auth0 till Azure med hjälp av Azure Event Grid
description: Hur du slutför händelser från Auth0 till Azure-tjänster med Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a66a60cb926b933a6b0628a67506d0d52ab7a905
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077871"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Integrera Azure Event Grid med Auth0

I den här artikeln beskrivs hur du ansluter dina Auth0-och Azure-konton genom att skapa ett Event Grid-partner ämne.

I [händelse typ koderna för Auth0](https://auth0.com/docs/logs/references/log-event-type-codes) finns en fullständig lista över de händelser som Auth0 stöder

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Skicka händelser från Auth0 till Azure Event Grid
Så här skickar du Auth0-händelser till Azure:

1. Aktivera Event Grid-resursprovider
1. Konfigurera ett Auth0-partner ämne på Auth0-instrumentpanelen
1. Aktivera partner ämnet i Azure
1. Prenumerera på händelser från Auth0

Mer information om dessa begrepp finns i Event Grid [begrepp](concepts.md).

### <a name="enable-event-grid-resource-provider"></a>Aktivera Event Grid-resursprovider
Om du inte har använt Event Grid tidigare måste du registrera Event Grid Resource Provider. Om du har använt Event Grid tidigare kan du gå vidare till nästa avsnitt.

I Azure-portalen:
1. Välj prenumerationer på den vänstra menyn
1. Välj den prenumeration som du använder för Event Grid
1. På den vänstra menyn under Inställningar väljer du resurs leverantörer
1. Hitta Microsoft. EventGrid
1. Välj register
1. Uppdatera för att se till att statusen ändras till registrerad

### <a name="set-up-an-auth0-partner-topic"></a>Konfigurera ett Auth0-partner ämne
En del av integrerings processen är att konfigurera Auth0 för användning som en händelse källa (det här steget sker i din [Auth0-instrumentpanel](https://manage.auth0.com/)).

1. Logga in på [Auth0-instrumentpanelen](https://manage.auth0.com/).
1. Navigera till loggar > strömmar.
1. Klicka på + Skapa ström.
1. Välj Azure Event Grid och ange ett unikt namn för din nya data ström.
1. Skapa händelse källan genom att ange ditt Azure-prenumerations-ID, Azure-region och ett resurs grupp namn. 
1. Klicka på Spara.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Aktivera ditt Auth0-partner-ämne i Azure
Om du aktiverar Auth0-avsnittet i Azure kan händelser flöda från Auth0 till Azure.

1. Logga in på Azure Portal.
1. Sök `Partner Topics` högst upp och klicka på `Event Grid Partner Topics` under tjänster.
1. Klicka på det avsnitt som matchar den data ström som du skapade i Auth0-instrumentpanelen.
1. Bekräfta att `Source` fältet matchar ditt Auth0-konto.
1. Klicka på Aktivera.

### <a name="subscribe-to-auth0-events"></a>Prenumerera på Auth0-händelser

#### <a name="create-an-event-handler"></a>Skapa en händelse hanterare
Du behöver en händelse hanterare för att testa ditt partner ämne. Gå till din Azure-prenumeration och skapa en tjänst som stöds som en [händelse hanterare](event-handlers.md) , till exempel en [Azure-funktion](custom-event-to-function.md).

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Prenumerera på ditt Auth0-partner ämne
När du prenumererar på ditt Auth0-partner avsnitt kan du se Event Grid var du vill att dina Auth0-händelser ska gå.

1. På bladet partner ämne för din Auth0-integrering väljer du + händelse prenumeration längst upp.
1. På sidan Skapa händelse prenumeration:
    1. Ange ett namn för händelse prenumerationen.
    1. Välj den Azure-tjänst eller webhook som du skapade för slut punkts typen.
    1. Följ anvisningarna för den aktuella tjänsten.
    1. Klicka på Skapa.

## <a name="testing"></a>Testning
Din Auth0-partners ämnes integrering med Azure bör vara redo att använda.

### <a name="verify-the-integration"></a>Verifiera integreringen
Kontrol lera att integrationen fungerar som förväntat:

1. Logga in på Auth0-instrumentpanelen.
1. Navigera till loggar > strömmar.
1. Klicka på din Event Grid-dataström.
1. Klicka på fliken hälsa en gång på data strömmen. Data strömmen måste vara aktiv och så länge du inte ser några fel, fungerar strömmen.

Försök att [anropa någon av Auth0-åtgärderna som utlöser en händelse](https://auth0.com/docs/logs/references/log-event-type-codes) som ska publiceras för att se händelse flödet.

## <a name="delivery-attempts-and-retries"></a>Leverans försök och nya försök
Auth0-händelser levereras till Azure via en strömmande mekanism. Varje händelse skickas när den utlöses i Auth0. Om Event Grid inte kan ta emot händelsen, kommer Auth0 att försöka igen till tre gånger för att leverera händelsen. Annars kommer Auth0 att logga felen för att leverera i systemet.

## <a name="next-steps"></a>Nästa steg

- [Auth0-partner ämne](auth0-overview.md)
- [Översikt över partner ämnen](partner-events-overview.md)
- [Bli en Event Grid-partner](partner-onboarding-overview.md)