---
title: Programvarukostnader för reservationer för Azure
description: Lär dig vilka programvarumätare som inte ingår i kostnader för Azure Reserved VM Instances.
author: yashesvi
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/28/2021
ms.author: banders
ms.openlocfilehash: 464e129314a369c14dcc6bc6c16eadf5f8bcf01f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052997"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Programvarukostnader som inte ingår med Azure Reserved VM Instances

Rabatter för reserverad VM-instans och reserverad SQL-kapacitet tillämpas endast på infrastrukturkostnader, inte på programvarukostnaderna. Om du använder en virtuell Windows-dator och inte har en Azure Hybrid-förmån på dina reserverade VM-instanser debiteras du för de programvarumätare som anges i följande avsnitt. För SQL PaaS-distributioner fortsätter IP-kostnaden att debiteras via separat mätare om Azure Hybrid-förmån inte har valts.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Windows-programvarumätare som inte ingår i reservationskostnaden

| MeterId | MeterName i användningsfilen | Används av virtuell dator |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservation-Windows Svr Burst (1 kärna) | B-serien |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservation-Windows Svr Burst (2 kärnor) | B-serien |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservation-Windows Svr Burst (4 kärnor) | B-serien |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservation-Windows Svr Burst (8 kärnor) | B-serien |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservation-Windows Svr (1 kärna) | Alla förutom B-serien |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservation-Windows Svr (2 kärnor) | Alla förutom B-serien |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservation-Windows Svr (4 kärnor) | Alla förutom B-serien |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservation-Windows Svr (6 kärnor) | Alla förutom B-serien |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservation-Windows Svr (8 kärnor) | Alla förutom B-serien |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservation-Windows Svr (12 kärnor) | Alla förutom B-serien |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservation-Windows Svr (16 kärnor) | Alla förutom B-serien |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation-Windows Svr (20 kärnor) | Alla förutom B-serien |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservation-Windows Svr (24 kärnor) | Alla förutom B-serien |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservation-Windows Svr (32 kärnor) | Alla förutom B-serien |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservation-Windows Svr (40 kärnor) | Alla förutom B-serien |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservation-Windows Svr (64 kärnor) | Alla förutom B-serien |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservation-Windows Svr (72 kärnor) | Alla förutom B-serien |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservation-Windows Svr (128 kärnor) | Alla förutom B-serien |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows Svr (256 kärnor) | Alla förutom B-serien |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservation-Windows Svr (96 kärnor) | Alla förutom B-serien |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Programvarumätare för molntjänster som inte ingår i reservationskostnaden

| MeterId | MeterName i användningsfilen |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU License|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU License|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU License|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU License|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU License|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU License|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU License|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 vCPU License|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU License|

## <a name="get-rates-for-azure-meters"></a>Få priser för Azure-mätare

Du kan få kostnaden för var och en av mätarena med Azures API för åter försäljnings priser. Information om hur du hämtar priserna för en Azure-mätare finns i [Översikt över Azures åter försäljnings priser](/rest/api/cost-management/retail-prices/azure-retail-prices).

## <a name="next-steps"></a>Nästa steg
Mer information om reservationer för Azure finns i följande artiklar:

- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Hantera reservationer för Azure](manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](../manage/understand-vm-reservation-charges.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).