---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) säker export i Azure Monitor
description: Den här artikeln visar hur du ansluter dina ITSM-produkter/-tjänster med säker export i Azure Monitor för att centralt övervaka och hantera ITSM-arbetsobjekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: ee56d65452cb8535c5197e1b3524bd4e9c9ab9ea
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857417"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Ansluta Azure till ITSM-verktyg med säker export

Den här artikeln visar hur du konfigurerar anslutningen mellan din ITSM-produkt (IT Service Management) eller tjänst med säker export.

Säker export är en uppdaterad version av [anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC)](./itsmc-overview.md). Med båda versionerna kan du skapa arbets objekt i ett ITSM-verktyg när Azure Monitor skickar aviseringar. Funktionerna omfattar statistik, logg och aktivitets logg aviseringar.

ITSMC använder autentiseringsuppgifter för användar namn och lösen ord. Säker export har starkare autentisering eftersom den använder Azure Active Directory (Azure AD). Azure AD är Microsofts molnbaserade hanteringstjänst för identiteter och åtkomst. Det hjälper användarna att logga in och komma åt interna eller externa resurser. Med hjälp av Azure AD med ITSM kan du identifiera Azure-aviseringar (via Azure AD-programid: t) som skickades till det externa systemet.

> [!NOTE]
> Möjligheten att ansluta Azure till ITSM-verktyg med hjälp av säker export är i för hands version.

## <a name="secure-export-architecture"></a>Säker export arkitektur

Den säkra export arkitekturen introducerar följande nya funktioner:

* **Ny åtgärds grupp**: aviseringar skickas till verktyget ITSM via åtgärds gruppen säker webhook, i stället för ITSM-som ITSMC använder.
* **Azure AD-autentisering**: autentisering sker via Azure AD i stället för autentiseringsuppgifter för användar namn/lösen ord.

## <a name="secure-export-data-flow"></a>Data flöde för säker export

Stegen i data flödet för säker export är:

1. Azure Monitor skickar en avisering som är konfigurerad för att använda säker export.
2. Aviserings nytto lasten skickas via en säker webhook-åtgärd till ITSM-verktyget.
3. ITSM-programmet kontrollerar med Azure AD om aviseringen har behörighet att ange ITSM-verktyget.
4. Om aviseringen är auktoriserad kan programmet:
   
   1. Skapar ett arbets objekt (till exempel en incident) i ITSM-verktyget.
   2. Binder ID: t för konfigurationsobjektet (CI) till Customer Management-databasen (CMDB).

![Diagram som visar hur verktyget ITSM kommunicerar med Azure A D, Azure-aviseringar och en åtgärds grupp.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Fördelar med säker export

De främsta fördelarna med integrationen är:

* **Bättre autentisering**: Azure AD ger mer säker autentisering utan tids gränser som vanligt vis uppstår i ITSMC.
* **Aviseringar som löses i ITSM-verktyget**: mått varningar implementerar "utlöst" och "löst" tillstånd. När villkoret är uppfyllt är aviserings tillståndet "utlöst". När villkoret inte uppfylls längre är aviserings tillståndet "löst". I ITSMC kan aviseringar inte lösas automatiskt. Med säker export flödar det lösta tillstånd till ITSM-verktyget och uppdateras automatiskt.
* **[Vanligt aviserings schema](./alerts-common-schema.md)**: i ITSMC varierar schemat för aviserings nytto lasten baserat på aviserings typen. I säker export finns det ett gemensamt schema för alla aviserings typer. Det här gemensamma schemat innehåller CI för alla aviserings typer. Alla aviserings typer kan binda sitt CI med CMDB.

## <a name="next-steps"></a>Nästa steg

* [Skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-overview.md)
