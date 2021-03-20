---
title: Data platser för Windows Virtual Desktop – Azure
description: En kort översikt över vilka platser som Windows Virtual Desktops data och metadata lagras i.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12ec71a86a5df5954c14097e6a0ec5c8a5138fc5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652436"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Platser för data och metadata för virtuella Windows-datorer

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/data-locations-2019.md).

Det virtuella Windows-skrivbordet är för närvarande tillgängligt för alla geografiska platser. Administratörer kan välja plats för att lagra användar data när de skapar värdar för virtuella datorer och associerade tjänster, till exempel fil servrar. Lär dig mer om Azures geografiska områden i [Azure datacenter-kartan](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft kontrollerar eller begränsar inte de regioner där du eller dina användare kan komma åt dina användar-och appdata.

>[!IMPORTANT]
>Windows Virtual Desktop lagrar global metadatainformation som klient namn, värdnamn, namn på App-grupp och användarens huvud namn i ett Data Center. Varje gång en kund skapar ett tjänst objekt måste de ange en plats för tjänst objekt. Platsen som de anger avgör var metadata för objektet ska lagras. Kunden väljer en Azure-region och metadata kommer att lagras i relaterad geografi. En lista över alla Azure-regioner och relaterade geografiska områden finns i [Azure](https://azure.microsoft.com/global-infrastructure/geographies/)-geografiska områden.

Vi stöder för närvarande lagring av metadata i följande geografiska områden:

- USA (US) (allmänt tillgänglig)
- Europa (EU) (offentlig för hands version) 

>[!NOTE]
> När du väljer en region för att skapa Windows Virtual Desktop Service-objekt i, ser du regioner under både amerikanska och EU: s geografiska områden. För att se till att du förstår vilken region som passar bäst för din distribution, kan du ta en titt på [vår globala infrastruktur karta i Azure](https://azure.microsoft.com/global-infrastructure/geographies/#geographies).

Lagrade metadata är krypterade i vila och geo-redundanta speglar behålls i geografien. Alla kunddata, till exempel appinställningar och användar data, finns på den plats kunden väljer och hanteras inte av tjänsten. Fler geografiska områden kommer att bli tillgängliga när tjänsten växer.

Tjänste-metadata replikeras i Azure geografi för haveri beredskap.