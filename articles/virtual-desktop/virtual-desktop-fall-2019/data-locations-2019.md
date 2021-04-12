---
title: Data platser för Windows Virtual Desktop (klassisk) – Azure
description: En kort översikt över vilka platser Windows Virtual Desktop (klassiska) data och metadata lagras i.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7726795582ffa3d85601ec123b58ab705442ef67
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445013"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Data platser för virtuella Windows-datorer (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../data-locations.md).

Det virtuella Windows-skrivbordet är för närvarande tillgängligt för alla geografiska platser. Först kan tjänste-metadata endast lagras i geografin USA (US). Administratörer kan välja plats för att lagra användar data när de skapar värdar för virtuella datorer och associerade tjänster, till exempel fil servrar. Lär dig mer om Azures geografiska områden i [Azure datacenter-kartan](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft kontrollerar eller begränsar inte de regioner där du eller dina användare kan komma åt dina användar-och appdata.

>[!IMPORTANT]
>Windows Virtual Desktop lagrar global metadatainformation som klient namn, värdnamn, namn på App-grupp och användarens huvud namn i ett Data Center som finns i USA. Lagrade metadata är krypterade i vila och geo-redundanta speglar behålls i USA. Alla kunddata, till exempel appinställningar och användar data, finns på den plats kunden väljer och hanteras inte av tjänsten.

Tjänste-metadata replikeras i USA för katastrof återställning.