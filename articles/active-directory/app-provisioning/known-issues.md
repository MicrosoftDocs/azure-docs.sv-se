---
title: Kända problem för program etablering i Azure AD
description: Lär dig om kända problem när du arbetar med automatiserad applikations etablering i Azure AD.
author: kenwith
ms.author: kenwith
manager: celestedg
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.reviewer: arvinh
ms.openlocfilehash: bde5cecc4052724f795cac03f6efceb3451ea2ee
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914356"
---
# <a name="known-issues-application-provisioning"></a>Kända problem: program etablering
Kända problem som kan vara medvetna om när du arbetar med app-etablering. Du kan ge feedback om program etablerings tjänsten på UserVoice, se [Azure AD Application-etablering UserVoice](https://aka.ms/appprovisioningfeaturerequest). Vi tittar på UserVoice så att vi kan förbättra tjänsten. 

> [!NOTE]
> Detta är inte en omfattande lista över kända problem. Om du vet ett problem som inte finns med i listan ger du feedback längst ned på sidan.

## <a name="authorization"></a>Auktorisering 

**Det går inte att spara efter lyckad anslutnings test**

Om du kan testa en anslutning, men inte spara, har du överskridit den tillåtna lagrings gränsen för autentiseringsuppgifter. Mer information finns i problem med att [Spara administratörs behörighet](./user-provisioning.md).

**Det går inte att spara**

Klient webb adressen, den hemliga token och e-postmeddelandet måste fyllas i för att spara. Du kan inte ange bara en av dem. 

**Det går inte att ändra etablerings läget tillbaka till manuell**

När du har konfigurerat etableringen för första gången ser du att etablerings läget har växlats från manuell till automatisk. Du kan inte ändra tillbaka till manuellt. Men du kan inaktivera etableringen genom användar gränssnittet. Att inaktivera etableringen i användar gränssnittet är i praktiken detsamma som att ställa in List rutan till manuell.  


## <a name="attribute-mappings"></a>Mappningar för attribut 

**Attributet SamAccountName eller userType är inte tillgängligt som källattribut**

Attributen SamAccountName och userType är inte tillgängliga som ett Source-attribut som standard. Utöka schemat och Lägg till attributet. Du kan lägga till attributen i listan över tillgängliga källattribut genom att utöka schemat. Mer information finns i [käll-attribut som saknas](user-provisioning-sync-attributes-for-mapping.md). 

**List rutan för källattribut saknas för schema tillägget**

Tillägg till schemat kan ibland saknas i list rutan källattribut i användar gränssnittet. Gå till de avancerade inställningarna för dina mappningar av attribut och Lägg till attributen manuellt. Mer information finns i [Anpassa mappningar av attribut](customize-application-attributes.md).

**Det går inte att tillhandahålla null-attribut**

Azure AD kan för närvarande inte etablera null-attribut. Om ett attribut är null för användarobjektet hoppas det över. 

**Max tecken för attribut mappnings uttryck**

Attribut-mappnings uttryck får innehålla högst 10 000 tecken. 

**Omfångs filter som inte stöds**

Katalog tillägg, appRoleAssignments, userType och accountExpires stöds inte som omfångs filter.


## <a name="service-issues"></a>Tjänst-problem 

**Scenarier som inte stöds**

- Det finns inte stöd för att konfigurera lösen ord. 
- Det finns inte stöd för etablering av kapslade grupper. 
- Etablering av B2C-klienter stöds inte på grund av innehavarens storlek.
- Alla etablerings appar är inte tillgängliga i alla moln. Till exempel är Atlassian ännu inte tillgängligt i det offentliga molnet. Vi arbetar med utvecklare av appar för att publicera sina appar till alla moln.

**Automatisk etablering är inte tillgängligt i mitt OIDC-baserade program**

Om du skapar en app-registrering, aktive ras inte motsvarande tjänst huvud namn i företags program för automatisk användar etablering. Du måste antingen begära att appen läggs till i galleriet, om den är avsedd att användas av flera organisationer, eller skapa en andra app som inte är en Galleri för etablering. 

**Etablerings intervallet är fast**

[Tiden](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) mellan etablerings cykler kan för närvarande inte konfigureras. 

**Ändringar som inte flyttas från mål programmet till Azure AD**

App Provisioning-tjänsten är inte medveten om ändringar som gjorts i externa appar. Därför vidtas ingen åtgärd för att återställa igen. App Provisioning-tjänsten förlitar sig på ändringar som gjorts i Azure AD. 

**Växlar från synkronisering all till synkronisering som är tilldelad fungerar inte**

När du har ändrat omfånget från synkronisera alla till synkronisera tilldelad, se till att även utföra en omstart för att säkerställa att ändringen börjar gälla. Du kan starta om från användar gränssnittet.

**Etablerings cykeln fortsätter tills den har slutförts**

När du ställer in etablering `enabled = off` eller stannar fortsätter den aktuella etablerings cykeln att köras tills den har slutförts. Tjänsten slutar att köra alla framtida cykler tills du har aktiverat etableringen igen.

**Medlem i gruppen har inte tillhandahållits**

När en grupp är inom omfånget och en medlem ligger utanför omfånget, kommer gruppen att tillhandahållas. Det går inte att tillhandahålla out of scope-användaren. Om medlemmen kommer tillbaka i omfånget kommer tjänsten inte att upptäcka ändringen omedelbart. Att starta om etableringen löser problemet. Vi rekommenderar att du startar om tjänsten regelbundet för att säkerställa att alla användare är korrekt etablerade.  

**Manager har inte tillhandahållits**

Om en användare och deras chef både är inom omfånget för etablering, etablerar tjänsten användaren och uppdaterar sedan chefen. Men om den dag då användaren är i omfånget och chefen saknar räckvidd, kommer vi att etablera användaren utan chefs referensen. När chefen kommer till omfånget uppdateras inte chefs referensen förrän du startar om etableringen och gör att tjänsten kan utvärdera alla användare igen. 

## <a name="next-steps"></a>Nästa steg
- [Så här fungerar etablering](how-provisioning-works.md)
