---
title: Azure VMware-lösning av CloudSimple – skapa CloudSimple privat moln
description: Beskriver hur du skapar ett CloudSimple privat moln för att utöka VMware-arbetsbelastningar till molnet med drift flexibilitet och kontinuitet
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2f4af4a36e719cbf15b3f0af77db81a32f2f2e42
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896285"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Skapa ett privat CloudSimple-moln

Ett privat moln är en isolerad VMware-stack som stöder ESXi-värdar, vCenter, virtuellt San och NSX. Privata moln hanteras via CloudSimple-portalen. De har sina egna vCenter-servrar i sin egen hanterings domän. Stacken körs på dedikerade noder och isolerade Bare Metal-maskinvarukonfigurationer.

Genom att skapa ett privat moln kan du hantera en mängd olika vanliga behov för nätverks infrastruktur:

* **Tillväxt**. Om du har nått en plats för maskin varu uppdatering för den befintliga infrastrukturen kan du med ett privat moln utöka utan att behöva ny maskin varu investering.

* **Snabb expansion**. Om det uppstår temporära eller oplanerade kapacitets behov kan du skapa ytterligare kapacitet utan fördröjning i ett privat moln.

* **Ökat skydd**. Med ett privat moln med tre eller fler noder får du automatisk redundans och skydd med hög tillgänglighet.

* **Långsiktiga infrastruktur behov**. Om dina data Center är i kapacitet eller om du vill strukturera om för att sänka kostnaderna, kan du använda ett privat moln för att dra tillbaka data Center och migrera till en molnbaserad lösning, samtidigt som de är kompatibla med dina företags åtgärder.

När du skapar ett privat moln får du ett enda vSphere-kluster och alla virtuella hanterings datorer som skapas i det klustret.

## <a name="before-you-begin"></a>Innan du börjar

Noder måste vara etablerade innan du kan skapa ditt privata moln. Mer information om hur du etablerar noder finns i [etablera noder för Azure VMware-lösning från CloudSimple](create-nodes.md).

Allokera ett CIDR-intervall för vSphere/virtuellt San-undernät för det privata molnet. Ett privat moln skapas som en isolerad VMware stack-miljö (med ESXi-värdar, vCenter, virtuellt San och NSX) som hanteras av en vCenter-Server. Hanterings komponenter distribueras i det nätverk som väljs för vSphere/virtuellt San-undernät CIDR. Nätverks-CIDR-intervallet är indelat i olika undernät under distributionen. Adress utrymmet för vSphere/virtuellt San-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljön. De nätverk som kommunicerar med CloudSimple omfattar lokala nätverk och virtuella Azure-nätverk. Mer information om vSphere/virtuellt San-undernät finns i Översikt över VLAN och undernät.

* Minsta vSphere/virtuellt San-undernät CIDR-intervall prefix:/24
* Maximalt vSphere/virtuellt San-undernät CIDR-intervall prefix:/21


## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

Få åtkomst till [CloudSimple-portalen](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Skapa ett nytt privat moln

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-tjänster**.
3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.
4. Från **Översikt** klickar du på **skapa privat moln** för att öppna en ny flik i webbläsaren för CloudSimple-portalen. Logga in med dina inloggnings uppgifter för Azure om du uppmanas till detta.

    ![Skapa ett privat moln från Azure](media/create-private-cloud-from-azure.png)

5. I CloudSimple-portalen anger du ett namn för ditt privata moln.
6. Välj **plats** för ditt privata moln.
7. Välj **nodtyp**, konsekvent med det du etablerade i Azure.
8. Ange **antal noder**.  Minst tre noder krävs för att skapa ett privat moln.

    ![Skapa privat moln – grundläggande information](media/create-private-cloud-basic-info.png)

9. Klicka på **Nästa: avancerade alternativ**.
10. Ange CIDR-intervallet för vSphere/virtuellt San-undernät. Se till att CIDR-intervallet inte överlappar något av dina lokala eller andra Azure-undernät (virtuella nätverk) eller med Gateway-undernätet.

    **Alternativ för CIDR-intervall:** /24,/23,/22 eller/21. Ett/24 CIDR-intervall stöder upp till nio noder, ett/23 CIDR-intervall stöder upp till 41 noder och ett/22-och/21 CIDR-intervall stöder upp till 64-noder (det maximala antalet noder i ett privat moln).

    > [!IMPORTANT]
    > IP-adresser i CIDR-intervallet vSphere/virtuellt San är reserverade för användning av den privata moln infrastrukturen.  Använd inte IP-adressen i det här intervallet på någon virtuell dator.

11. Klicka på **Nästa: granska och skapa**.
12. Granska inställningarna. Om du behöver ändra några inställningar klickar du på **föregående**.
13. Klicka på **Skapa**.

Etablerings processen för privata moln startar. Det kan ta upp till två timmar för det privata molnet att tillhandahållas.

Anvisningar om hur du expanderar ett befintligt privat moln finns i [expandera ett privat moln](expand-private-cloud.md).
