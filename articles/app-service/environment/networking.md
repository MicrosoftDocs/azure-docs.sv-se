---
title: App Service-miljön nätverk
description: Information om App Service-miljön nätverk
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0a1221a8de10fd18768a1a0f0ac08277dc2901d6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735041"
---
# <a name="app-service-environment-networking"></a>App Service-miljön nätverk

> [!NOTE]
> Den här artikeln gäller App Service-miljön v3 (för hands version)
> 

App Service-miljön (ASE) är en enda klient distribution av Azure App Service som är värd för webbappar, API-appar och Function-appar. När du installerar en ASE väljer du den Azure Virtual Network (VNet) som du vill distribuera i. Alla inkommande och utgående trafik program kommer att finnas i det virtuella nätverk som du anger.  

I ASEv3 används två undernät.  Ett undernät används för den privata slut punkten som hanterar inkommande trafik. Detta kan vara ett redan befintligt undernät eller ett nytt.  Det inkommande under nätet kan användas för andra orsaker än den privata slut punkten. Det utgående under nätet kan bara användas för utgående trafik från ASE. Inget annat kan gå in i ASE utgående undernät.

## <a name="addresses"></a>Adresser 
ASE har följande adresser när de skapas:

| Adress typ | beskrivning |
|--------------|-------------|
| Inkommande adress | Den inkommande adressen är den privata slut punkts adress som används av din ASE. |
| Utgående undernät | Det utgående under nätet är också ASE-undernätet. Under för hands versionen används bara det här under nätet för utgående trafik. |
| Utgående Windows-adress | Windows-apparna i den här ASE använder den här adressen som standard när utgående samtal till Internet görs. |
| Linux utgående adress | Linux-apparna i den här ASE använder den här adressen som standard när utgående samtal till Internet görs. |

ASEv3 innehåller information om de adresser som används av ASE i **IP-adress** delen av ASE-portalen.

![Användar gränssnitt för ASE-adress](./media/networking/networking-ip-addresses.png)

Om du tar bort den privata slut punkten som används av ASE kan du inte komma åt apparna i din ASE.  

ASE använder adresser i det utgående under nätet för att stödja den infrastruktur som används av ASE. När du skalar dina App Service planer i din ASE använder du fler adresser. Appar i ASE har inga dedikerade adresser i det utgående under nätet. De adresser som används av en app i det utgående under nätet av en app ändras med tiden.

## <a name="ports"></a>Portar

ASE tar emot program trafik på portarna 80 och 443.  Det finns inga inkommande eller utgående port krav för ASE. 

## <a name="extra-configurations"></a>Extra konfigurationer

Till skillnad från ASEv2 kan du med ASEv3 ange nätverks säkerhets grupper (NSG: er) och routningstabeller (UDR) så som du ser anpassa utan begränsning. Om du vill tvinga fram tunnel all utgående trafik från din ASE till en NVA-enhet. Du kan lägga WAF-enheter framför all inkommande trafik till din ASE. 

## <a name="dns"></a>DNS

Apparna i din ASE använder DNS-nätverket som ditt VNet har kon figurer ATS med. Följ instruktionerna i [använda en app service-miljön](./using.md#dns-configuration) för att konfigurera DNS-servern så att den pekar på din ASE. Om du vill att vissa appar ska använda en annan DNS-server än vad ditt VNet har kon figurer ATS med, kan du manuellt ange det för varje app med hjälp av appens inställningar WEBSITE_DNS_SERVER och WEBSITE_DNS_ALT_SERVER. Appens inställning WEBSITE_DNS_ALT_SERVER konfigurerar den sekundära DNS-servern. Den sekundära DNS-servern används bara när det inte finns något svar från den primära DNS-servern. 

## <a name="preview-limitation"></a>Begränsning av för hands version

Det finns några nätverksfunktioner som inte är tillgängliga med ASEv3.  De saker som inte är tillgängliga i ASEv3 är:

• FTP • fjärrfelsökning • extern belastnings Utjämnings distribution • möjlighet att komma åt ett privat behållar register för behållar distributioner • möjlighet att göra anrop till globalt peered virtuella nätverk • möjlighet att säkerhetskopiera/återställa med en tjänst slut punkt eller privat slut punkt skyddad lagrings kontot • möjlighet att ha app-inställningar nyckel valv referenser på tjänst slut punkt eller privat slut för skyddade nyckel valv konton • möjlighet att använda BYOS till en tjänst slut punkt eller privat slut punkt för skyddat lagrings konto • användning av Network Watcher-eller NSG-flöde på utgående trafik
    
    