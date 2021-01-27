---
title: Inaktivera PTA när du använder Azure AD Connect Konfigurera inte | Microsoft Docs
description: I den här artikeln beskrivs hur du inaktiverar PTA med funktionen Azure AD Connect Konfigurera inte.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26112b1e799cbde3145e7137c686b4b336db4bab
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919943"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>Inaktivera PTA när du använder Azure AD Connect

Om du använder direktautentisering med Azure AD Connect och har inställningen **"Konfigurera inte"** kan du inaktivera den. 

>[!NOTE]
>Om du redan har aktiverat PHS och inaktiverar PTA leder klient återställningen till PHS.

Inaktive ring av PTA kan göras med följande cmdletar. 

## <a name="prerequisites"></a>Förutsättningar
Följande krav måste vara uppfyllda:
- En Windows-dator som har PTA-agenten installerad. 
- Agenten måste vara av version 1.5.1742.0 eller senare. 
- Ett globalt administratörs konto för Azure för att kunna köra PowerShell-cmdlets för att inaktivera PTA.

>[!NOTE]
> Om agenten är äldre kanske den inte har de cmdletar som krävs för att slutföra den här åtgärden. Du kan få en ny agent från Azure-portalen genom att installera den på valfri Windows-dator och ange autentiseringsuppgifter för administratörer. (Installation av agenten påverkar inte PTA-status i molnet)

> [!IMPORTANT]
> Om du använder Azure Government molnet måste du skicka in parametern ENVIRONMENTNAME med följande värde. 
>
>| Namn på miljö | Moln |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Så här inaktiverar du PTA
I en PowerShell-session använder du följande för att inaktivera PTA:
1. PS C:\Program\Microsoft Azure AD Connect Authentication agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` eller `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` eller `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Om du inte har åtkomst till en agent

Om du inte har en agent dator kan du använda följande kommando för att installera en agent.

1. Ladda ned den senaste auth-agenten från portal.azure.com.
2. Installera funktionen: `.\AADConnectAuthAgentSetup.exe` eller `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Nästa steg

- [Användarinloggning med Azure Active Directory-direktautentisering](how-to-connect-pta.md)
