---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102603327"
---
SMB Multichannel för Azure-filresurser har för närvarande följande begränsningar:
- Kan endast användas med lokalt redundanta FileStorage-konton.
- Stöds endast för Windows-klienter. 
- Maximalt antal kanaler är fyra.
- SMB Direct stöds inte.
- Privata slut punkter för lagrings konton stöds inte.
- För lagrings konton med lokal Active Directory Domain Services (AD DS) eller Azure AD DS [Identity-baserad autentisering](../articles/storage/files/storage-files-active-directory-overview.md) aktive rad för Azure Files kan SMB-klienter inte använda Windows Utforskaren för att konfigurera NTFS-behörigheter för kataloger och filer.
    - Använd Windows [icacls](/windows-server/administration/windows-commands/icacls) -verktyget eller [set-ACL-](/powershell/module/microsoft.powershell.security/set-acl) kommandot i stället för att konfigurera behörigheter.

