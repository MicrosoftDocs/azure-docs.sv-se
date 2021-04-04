---
title: Hantera Azure Traffic Manager-profiler | Microsoft Docs
description: Den här artikeln hjälper dig att skapa, inaktivera, aktivera och ta bort en Azure Traffic Manager-profil.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
manager: twooley
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: duau
ms.openlocfilehash: a39120b1305022739aaef3407aa6c2621a97e842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184161"
---
# <a name="manage-an-azure-traffic-manager-profile"></a>Hantera en Azure Traffic Manager-profil

Traffic Manager-profiler använder trafikroutningsmetoder för att kontrollera trafikdistributionen till dina molntjänster eller webbplats-slutpunkter. Den här artikeln förklarar hur du skapar och hanterar de här profilerna.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Du kan skapa en Traffic Manager-profil med Azure Portal. När du har skapat din profil kan du konfigurera slutpunkter, övervakning och andra inställningar på Azure Portal. Traffic Manager har stöd för upp till 200 slutpunkter per profil. De flesta användningsscenarier kräver dock endast ett litet antal slutpunkter.

### <a name="to-create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. Klicka på **skapa en resurs**  >  **nätverk**  >  **Traffic Manager profil**  >  **skapa**.
4. I **Skapa Traffic Manager-profil** gör du följande:
    1. Ge profilen ett namn i **Namn**. Namnet måste var unikt inom trafficmanager.net-zonen och genererar DNS-namnet `<name>`, trafficmanager.net, som används för att öppna din Traffic Manager-profil.
    2. I **Routningsmetod** väljer du routningsmetoden **Priority** (Prioritet).
    3. I **Prenumeration** väljer du den prenumeration du vill skapa profilen under
    4. I **Resursgrupp** skapar du en ny resursgrupp att placera profilen under.
    5. I **Resursgruppsplats** väljer du plats för resursgruppen. Inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.
    6. Klicka på **Skapa**.
    7. När den globala distribueringen av din Traffic Manager-profil är klar listas den i respektive resursgrupp som en av resurserna.

## <a name="disable-enable-or-delete-a-profile"></a>Inaktivera, aktivera eller ta bort en profil

Du kan inaktivera en befintlig profil så att Traffic Manager inte refererar användarbegäranden till de konfigurerade slutpunkterna. När du inaktiverar en Traffic Manager-profil, finns själva profilen och informationen den innehåller kvar och kan redigeras i Traffic Manager-gränssnittet.  Referenser återupptas när du återaktiverar profilen. När du skapar en Traffic Manager-profil i Azure-portalen så aktiveras den automatiskt. Om en bestämmer att en profil inte längre behövs, kan du ta bort den.

### <a name="to-disable-a-profile"></a>Så här inaktiverar du en profil

1. Om du använder ett anpassat domännamn, ändra då CNAME-posten på din Internet-DNS-server så att den inte längre pekar mot din Traffic Manager-profil.
2. Trafiken slutar att dirigeras till slutpunkterna via Traffic Manager-profilinställningarna.
3. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare.
2. Leta efter namnet på **Traffic Manager-profilen** som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. Klicka på **Översikt**  >  **inaktivera**.
4. Bekräfta att du vill inaktivera Traffic Manager-profilen.

### <a name="to-enable-a-profile"></a>Så här aktiverar du en profil

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare.
2. Leta efter namnet på **Traffic Manager-profilen** som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. Klicka på **Översikt**  >  **Aktivera**.
1. Om du använder ett anpassat domännamn, skapar du en CNAME-resurspost på din Internet-DNS-server som pekar mot domännamnet för din Traffic Manager-profil.
2. Trafiken börjar dirigeras till slutpunkterna igen.

### <a name="to-delete-a-profile"></a>Så här tar du bort en profil

1. Kontrollera att DNS-resursposten på Internet-DNS-servern inte längre använder en CNAME-resurspost som pekar på domännamnet för Traffic Manager-profilen.
2. Leta efter namnet på **Traffic Manager-profilen** som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. Klicka på **Översikt**  >  **ta bort**.
4. Bekräfta att du vill ta bort Traffic Manager-profilen.

## <a name="next-steps"></a>Nästa steg

* [Lägg till en slutpunkt](./traffic-manager-manage-endpoints.md)
* [Konfigurera prioriterad routningsmetod](traffic-manager-configure-priority-routing-method.md)
* [Konfigurera geografisk routningsmetod](traffic-manager-configure-geographic-routing-method.md) 
* [Konfigurera viktad routningsmetod](traffic-manager-configure-weighted-routing-method.md)
* [Konfigurera routningsmetod för prestanda](traffic-manager-configure-performance-routing-method.md)