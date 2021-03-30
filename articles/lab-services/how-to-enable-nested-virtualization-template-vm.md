---
title: Aktivera kapslad virtualisering på en virtuell mall i Azure Lab Services | Microsoft Docs
description: I den här artikeln lär du dig hur du konfigurerar kapslad virtualisering på en mallfil i Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 319695088e967dc3156ecab5c1b9458e77fcc186
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251482"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Aktivera kapslad virtualisering på en virtuell mall på en virtuell dator i Azure Lab Services

För närvarande kan du med Azure Lab Services konfigurera en virtuell mall för virtuella datorer i ett labb och göra en enskild kopia tillgänglig för varje användare. Om du är en lärare-underordnad nätverks-, säkerhets-eller IT-klass kan du behöva ge varje studenter en miljö där flera virtuella datorer kan kommunicera med varandra via ett nätverk.

Kapslad virtualisering gör att du kan skapa en miljö med flera virtuella datorer i en labbs mall för virtuella datorer. Genom att publicera mallen får du varje användare i labbet med en virtuell dator som är konfigurerad med flera virtuella datorer i den.  Den här artikeln beskriver hur du konfigurerar kapslad virtualisering på en mallfil i Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Vad är kapslad virtualisering?

Kapslad virtualisering gör att du kan skapa virtuella datorer i en virtuell dator. Kapslad virtualisering görs via Hyper-V och är bara tillgänglig på virtuella Windows-datorer.

Mer information om kapslad virtualisering finns i följande artiklar:

- [Kapslad virtualisering i Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Så här aktiverar du kapslad virtualisering i en virtuell Azure-dator](../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Överväganden

Här är några saker att tänka på innan du konfigurerar ett labb med kapslad virtualisering.

- När du skapar ett nytt labb väljer du **medium (kapslad virtualisering)** eller **stor (kapslad virtualisering)** storlek för den virtuella dator storleken. De här virtuella dator storlekarna stöder kapslad virtualisering.
- Välj en storlek som ger bästa prestanda för både värd-och klientens virtuella datorer.  Kom ihåg att när du använder virtualisering måste den storlek du väljer vara tillräcklig för inte bara en dator, men både värden och Hyper-V-datorer som körs samtidigt.
- Klientens virtuella datorer har inte åtkomst till Azure-resurser, till exempel DNS-servrar, på det virtuella Azure-nätverket.
- Den virtuella värd datorn kräver att installations programmet tillåter att klient datorn har Internet anslutning.
- Virtuella klient datorer licensieras som oberoende datorer. Se [Microsoft-licensiering](https://www.microsoft.com/licensing/default) för information om licensiering för Microsoft operation Systems och produkter. Kontrol lera licens avtal för all annan program vara som används innan du konfigurerar mallen Machine.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Aktivera kapslad virtualisering på en virtuell malldator

Den här artikeln förutsätter att du har skapat ett labb konto och labb.  Mer information om hur du skapar ett nytt labb konto finns i [självstudier för att konfigurera ett labb konto](tutorial-setup-lab-account.md). Mer information om hur du skapar labb finns i avsnittet [Konfigurera en labb Guide för klass rummet](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Välj **stor (kapslad virtualisering)** eller **medium (kapslad virtualisering)** för den virtuella dator storleken när du skapar labbet.  Kapslad virtualisering kommer inte att fungera på annat sätt.  

Information om hur du ansluter till mallen finns i [skapa och hantera en klass rums mall](how-to-create-manage-template.md).

Om du vill aktivera kapslad virtualisering finns det några åtgärder att utföra.  

- **Aktivera Hyper-V-rollen**. Hyper-V-rollen måste vara aktive rad för att skapa och köra virtuella Hyper-V-datorer på den virtuella datorn för labb tjänster.
- **Aktivera DHCP**.  Om DHCP-rollen är aktive rad i labb tjänsternas virtuella dator kan virtuella Hyper-V-datorer automatiskt tilldelas en IP-adress.
- **Skapa NAT-nätverk för virtuella Hyper-V-datorer**.  NAT-nätverket är konfigurerat för att tillåta Internet åtkomst för virtuella Hyper-V-datorer.  Virtuella Hyper-V-datorer kan kommunicera med varandra.

>[!NOTE]
>NAT-nätverket som skapades på den virtuella labb tjänster-datorn gör att en virtuell Hyper-V-dator får åtkomst till Internet och andra virtuella Hyper-V-datorer på samma virtuella dator i labb tjänster.  Den virtuella Hyper-V-datorn kommer inte att kunna komma åt Azure-resurser, till exempel DNS-servrar, på det virtuella Azure-nätverket.

Att utföra de uppgifter som anges ovan kan göras med hjälp av ett skript eller med hjälp av Windows-verktyg.  Läs avsnitten nedan om du vill ha mer information.

### <a name="using-script-to-enable-nested-virtualization"></a>Aktivera kapslad virtualisering med hjälp av skript

Om du vill använda den automatiserade installationen av kapslad virtualisering med Windows Server 2016 eller Windows Server 2019, se [Aktivera kapslad virtualisering på en virtuell mall i Azure Lab Services med hjälp av ett skript](how-to-enable-nested-virtualization-template-vm-using-script.md). Du kommer att använda skript från [labb tjänster Hyper-v-skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) för att installera Hyper-v-rollen.  Skripten kan också konfigurera nätverk så att virtuella Hyper-V-datorer kan ha Internet åtkomst.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Aktivera kapslad virtualisering med hjälp av Windows-verktyg

Konfigurera kapslad virtualisering för Windows Server 2016 eller Windows Server 2019 med Windows-roller och administrations verktyg, se [Aktivera kapslad virtualisering på en virtuell mall i Azure Lab Services manuellt](how-to-enable-nested-virtualization-template-vm-ui.md).  Instruktioner beskriver också hur du konfigurerar nätverk så att virtuella Hyper-V-datorer kan ha Internet åtkomst.
