---
title: 'Arbeta via fjärr anslutning med skydds: Azure skydds'
description: På den här sidan beskrivs hur du kan använda Azure-skydds för att aktivera fjärrhantering på grund av COVID-19-Pandemic.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077957"
---
# <a name="working-remotely-using-azure-bastion"></a>Arbeta via fjärr anslutning med Azure skydds

Azure skydds spelar en pivot-roll i stödjande scenarier för fjärran vändare genom att tillåta användare med Internet anslutning att komma åt Azure Virtual Machines. Det gör det möjligt för IT-administratörer att hantera sina program som körs i Azure när som helst och var som helst runtom i hela världen.

>[!NOTE]
>Den här artikeln beskriver hur du kan använda Azure skydds, Azure, Microsoft Network och Azure-partnerns eko system för att arbeta fjärran slutet och minimera nätverks problem som du står inför på grund av COVID-19-kris.
>

## <a name="securely-access-virtual-machines"></a>Få säker åtkomst till virtuella datorer

Mer specifikt ger Azure skydds säker och sömlös RDP/SSH-anslutning till virtuella datorer i det virtuella Azure-nätverket, direkt i Azure Portal utan att använda en offentlig IP-adress. Mer information om Azure skydds-arkitekturen och viktiga funktioner finns i [Vad är Azure skydds](bastion-overview.md).

Azure-skydds distribueras per virtuellt nätverk, vilket innebär att företag kan konfigurera och hantera en Azure-skydds för att snabbt stödja fjärran vändare till virtuella datorer i ett virtuellt Azure-nätverk. Anvisningar om hur du skapar och hanterar Azure-skydds finns i [skapa en skydds-värd](./tutorial-create-host-portal.md).

## <a name="next-steps"></a>Nästa steg

* Konfigurera Azure-skydds med hjälp av [Azure Portal](./tutorial-create-host-portal.md), [POWERSHELL](bastion-create-host-powershell.md)eller Azure CLI.

* Mer information finns i [vanliga frågor och svar om skydds](bastion-faq.md) .