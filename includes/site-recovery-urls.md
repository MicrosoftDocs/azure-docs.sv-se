---
title: ta med fil
description: ta med fil
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999201"
---
Name | Kommersiell URL | Myndighets-URL | Beskrivning
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Används för åtkomst kontroll och identitets hantering med hjälp av Azure Active Directory. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Används för överföring av replikeringsdata och koordination.
Replikering | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Används för åtgärder för replikeringshantering och koordination.
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Används för åtkomst till lagringskontot som lagrar replikerade data.
Telemetri (valfritt) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Används för telemetri.
Tidssynkronisering | ``time.windows.com`` | ``time.nist.gov`` | Används för att kontrol lera tidssynkronisering mellan system och global tid i alla distributioner.


