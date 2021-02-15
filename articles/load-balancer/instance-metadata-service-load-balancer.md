---
title: Hämta information om belastnings utjämning med hjälp av Azure Instance Metadata Service
titleSuffix: Azure Load Balancer
description: Kom igång med att använda Azure-Instance Metadata Service för att hämta information om belastnings utjämning.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 0d7d08eb5e77e744fb6ce0abefc550bc79de4c8c
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519090"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Hämta information om belastnings utjämning med hjälp av Azure Instance Metadata Service

IMDS (Azure Instance Metadata Service) innehåller information om de virtuella dator instanser som körs. Tjänsten är en REST API som är tillgänglig på en välkänd, icke-flyttbar IP-adress (169.254.169.254). 

När du placerar instanser av virtuella datorer eller virtuella datorer bakom en Azure-Standard Load Balancer använder du IMDS för att hämta metadata relaterade till belastningsutjämnaren och instanserna.

Metadata innehåller följande information för de virtuella datorerna eller skalnings uppsättningarna för virtuella datorer:

* Offentlig IP-adress för standard-SKU.
* Konfigurationer för inkommande regler för belastningsutjämnaren för varje privat IP-adress i nätverks gränssnittet.
* Konfigurationer för utgående regler för belastningsutjämnaren för varje privat IP-adress i nätverks gränssnittet.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>Få åtkomst till metadata för belastningsutjämnaren med hjälp av IMDS

Mer information om hur du kommer åt metadata för belastningsutjämnaren finns i [använda Azure-instance metadata service för att få åtkomst till information om belastnings utjämning](howto-load-balancer-imds.md).

## <a name="troubleshoot-common-error-codes"></a>Felsök vanliga felkoder

Mer information om vanliga felkoder och deras metoder finns i [Felsöka vanliga felkoder när du använder IMDS](troubleshoot-load-balancer-imds.md). 

## <a name="support"></a>Support

Om du inte kan hämta ett svar efter flera försök, skapar du ett support ärende i Azure Portal.

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure instance metadata service](../virtual-machines/windows/instance-metadata-service.md)

[Distribuera en standard Load Balancer](quickstart-load-balancer-standard-public-portal.md)

