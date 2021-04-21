---
title: Dataplatser för Windows Virtual Desktop – Azure
description: En kort översikt över vilka platser Windows Virtual Desktop och metadata lagras i.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: eeba3cb579c6ef9158379403a3206f99a2cfb060
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830636"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Data- och metadataplatser för Windows Virtual Desktop

>[!IMPORTANT]
>Det här innehållet gäller för Windows Virtual Desktop med Azure Resource Manager Windows Virtual Desktop objekt. Om du använder en Windows Virtual Desktop (klassisk) utan att Azure Resource Manager objekt kan du läsa den [här artikeln.](./virtual-desktop-fall-2019/data-locations-2019.md)

Windows Virtual Desktop är för närvarande tillgänglig för alla geografiska platser. Administratörer kan välja plats för att lagra användardata när de skapar värdpoolens virtuella datorer och associerade tjänster, till exempel filservrar. Läs mer om Geografiska områden i Azure på [azure-datacenterkartan](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft styr eller begränsar inte de regioner där du eller dina användare kan komma åt dina användar- och appspecifika data.

>[!IMPORTANT]
>Windows Virtual Desktop lagrar global metadatainformation som arbetsytenamn, värdpoolnamn, appgruppnamn och användarhuvudnamn i ett datacenter. När en kund skapar ett tjänstobjekt måste de ange en plats för tjänstobjektet. Den plats som de anger avgör var metadata för objektet ska lagras. Kunden väljer en Azure-region och metadata lagras i det relaterade geografiska området. En lista över alla Azure-regioner och relaterade geografiska områden finns i [Azures geografiska områden.](https://azure.microsoft.com/global-infrastructure/geographies/)

Vi stöder för närvarande lagring av metadata i följande geografiska områden:

- USA (USA) (allmänt tillgänglig)
- Europa (EU) (offentlig förhandsversion) 

>[!NOTE]
> När du väljer en region att skapa Windows Virtual Desktop tjänstobjekt i visas regioner under både usa och EU:s geografiska områden. För att se till att du förstår vilken region som skulle fungera bäst för din distribution kan du ta en titt på [vår globala infrastrukturkarta för Azure.](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)

Lagrade metadata krypteras i vila och geo-redundanta speglingar bevaras inom geografin. Alla kunddata, till exempel appinställningar och användardata, finns på den plats som kunden väljer och hanteras inte av tjänsten. Fler geografiska områden blir tillgängliga när tjänsten växer.

Tjänstmetadata replikeras inom Azure-geografin för haveriberedskap.
