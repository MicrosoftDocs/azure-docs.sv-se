---
title: Skapa FQDN för en virtuell dator i Azure Portal
description: Lär dig hur du skapar ett fullständigt kvalificerat domän namn (FQDN) för en virtuell dator i Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42c53e72de7f6b2943086544f607042957f4b849
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220087"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Skapa ett fullständigt kvalificerat domän namn i Azure Portal för en virtuell Linux-dator

När du skapar en virtuell dator (VM) i [Azure Portal](https://portal.azure.com)skapas automatiskt en offentlig IP-resurs för den virtuella datorn. Du använder den här IP-adressen för att fjärrans luta till den virtuella datorn. Även om portalen inte skapar ett [fullständigt kvalificerat domän namn eller fullständigt domän namn](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)kan du lägga till en när den virtuella datorn har skapats. I den här artikeln beskrivs stegen för att skapa ett DNS-namn eller fullständigt domän namn. 

## <a name="create-a-fqdn"></a>Skapa ett fullständigt domän namn
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du skapa en virtuell [Linux](./linux/quick-create-portal.md) -eller [Windows](./windows/quick-create-portal.md) -dator i portalen. Följ de här stegen när den virtuella datorn är igång:


1. Välj din virtuella dator i portalen. 
1. På den vänstra menyn väljer du **Egenskaper**
1. Under **namn etikett för offentlig IP-address\DNS** väljer du din IP-adress.
2. Under **DNA-namn etikett** anger du det prefix som du vill använda.
3. Välj **Spara** längst upp på sidan.
4. Välj **Översikt** på den vänstra menyn för att återgå till BLADet VM-översikt.
5. Kontrol lera att **DNS-namnet** visas korrekt. 

## <a name="next-steps"></a>Nästa steg

Du kan också hantera DNS med [Azure DNS zoner](../dns/dns-getstarted-portal.md).

