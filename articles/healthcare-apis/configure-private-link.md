---
title: Privat länk för Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar en privat slut punkt för Azure API för FHIR Services
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/12/2020
ms.author: matjazl
ms.openlocfilehash: cdb41f12e8f050e3c74fccddb392d7a816c15b2f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621886"
---
# <a name="configure-private-link"></a>Konfigurera privat länk

Med privat länk kan du komma åt Azure API för FHIR över en privat slut punkt, ett nätverks gränssnitt som ansluter dig privat och på ett säkert sätt med en privat IP-adress från det virtuella nätverket. Med privat länk kan du komma åt våra tjänster på ett säkert sätt från ditt VNet som en första parts tjänst utan att behöva gå igenom en offentlig DNS. Den här artikeln vägleder dig genom hur du skapar, testar och hanterar din privata slut punkt för Azure API för FHIR.

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar en privat slut punkt finns det några Azure-resurser som du måste skapa först:

- Resurs grupp – den Azure-resurs grupp som ska innehålla det virtuella nätverket och den privata slut punkten.
- Azure API för FHIR – den FHIR-resurs som du vill använda bakom en privat slut punkt.
- Virtual Network – det virtuella nätverk som dina klient tjänster och privata slut punkter ska anslutas till.

Mer information finns i [dokumentationen till privat länk](../private-link/index.yml).

## <a name="disable-public-network-access"></a>Inaktivera offentlig nätverks åtkomst

Att skapa en privat slut punkt för FHIR-resursen inaktiverar inte den offentliga trafiken automatiskt till den. Om du vill göra det måste du uppdatera din FHIR-resurs för att ange en ny "offentlig åtkomst"-egenskap från "Enabled" till "Disabled". Var försiktig när du inaktiverar offentlig nätverks åtkomst eftersom alla förfrågningar till din FHIR-tjänst inte kommer från en korrekt konfigurerad privat slut punkt nekas. Endast trafik från dina privata slut punkter kommer att tillåtas.

![Inaktivera offentlig nätverks åtkomst](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Skapa privat slut punkt

För att skapa en privat slut punkt kan en utvecklare med RBAC-behörigheter på FHIR-resursen använda Azure Portal, [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)eller [Azure CLI](../private-link/create-private-endpoint-cli.md). I den här artikeln får du stegvisa anvisningar om hur du använder Azure Portal. Att använda Azure Portal rekommenderas eftersom det automatiserar skapandet och konfigurationen av Privat DNSs zonen. Du kan referera till den [privata länken Snabbstart guider](../private-link/create-private-endpoint-portal.md) för mer information.

Det finns två sätt att skapa en privat slut punkt. Flöde för automatisk godkännande tillåter en användare som har RBAC-behörigheter på FHIR-resursen för att skapa en privat slut punkt utan att behöva godkännas. Manuellt godkännande flöde gör det möjligt för en användare utan behörigheter för FHIR-resursen att begära att en privat slut punkt godkänns av ägare av FHIR-resursen.

### <a name="auto-approval"></a>Automatiskt godkännande

Se till att regionen för den nya privata slut punkten är samma som regionen för Virtual Network. Regionen för din FHIR-resurs kan vara olika.

![Fliken Azure Portal grunder](media/private-link/private-link-portal2.png)

För resurs typ söker du och väljer "Microsoft. HealthcareApis/Services". För resurs väljer du resursen FHIR. För mål under resurs väljer du "FHIR".

![Fliken Azure Portal resurs](media/private-link/private-link-portal1.png)

Om du inte har konfigurerat en befintlig Privat DNS zon väljer du "(ny) privatelink. azurehealthcareapis. com". Om du redan har konfigurerat din Privat DNS zon kan du välja den i listan. Det måste ha formatet "privatelink.azurehealthcareapis.com".

![Fliken Azure Portal konfiguration](media/private-link/private-link-portal3.png)

När distributionen är klar kan du gå tillbaka till fliken "anslutningar för privata slut punkter" där du kommer att se "godkänd" som anslutnings status.

### <a name="manual-approval"></a>Manuellt godkännande

För manuellt godkännande väljer du det andra alternativet under resurs, "Anslut till en Azure-resurs med resurs-ID eller alias". För under resurs för mål anger du "FHIR" som i automatiskt godkännande.

![Manuellt godkännande](media/private-link/private-link-manual.png)

När distributionen är klar kan du gå tillbaka till fliken "anslutningar för privata slut punkter" där du kan godkänna, avvisa eller ta bort anslutningen.

![Alternativ](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Testa privat slut punkt

För att se till att FHIR-servern inte tar emot offentlig trafik efter inaktive ring av offentlig nätverks åtkomst, kan du försöka med/metadata-slutpunkten för servern från datorn. Du bör få en 403 som är förbjuden. Observera att det kan ta upp till 5 minuter efter uppdateringen av åtkomst flaggan för offentliga nätverk innan offentlig trafik blockeras.

För att se till att din privata slut punkt kan skicka trafik till servern:

1. Skapa en virtuell dator som är ansluten till det virtuella nätverket och under nätet som din privata slut punkt är konfigurerad på. För att säkerställa att trafiken från den virtuella datorn endast använder det privata nätverket kan du inaktivera utgående Internet trafik via NSG-regeln.
2. RDP till den virtuella datorn.
3. Försök att använda FHIR-serverns/metadata-slutpunkt från den virtuella datorn. du bör få kapacitets satsen som ett svar.

## <a name="manage-private-endpoint"></a>Hantera privat slut punkt

### <a name="view"></a>Visa

Privata slut punkter och tillhör ande nätverkskort visas i Azure Portal från resurs gruppen de skapades i.

![Visa i resurser](media/private-link/private-link-view.png)

### <a name="delete"></a>Ta bort

Privata slut punkter kan bara tas bort från Azure Portal via översikts bladet (enligt nedan) eller via fliken ta bort under nätverk (för hands version) fliken "anslutningar för privata slut punkter". Om du klickar på ta bort tas den privata slut punkten och det associerade NÄTVERKSKORTet bort. Om du tar bort alla privata slut punkter till FHIR-resursen och den offentliga nätverks åtkomsten är inaktive rad kommer ingen begäran att göra den till din FHIR-Server.

![Ta bort privat slut punkt](media/private-link/private-link-delete.png)
