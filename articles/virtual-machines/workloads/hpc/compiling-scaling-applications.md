---
title: Skala HPC-program – Azure Virtual Machines | Microsoft Docs
description: Lär dig hur du skalar HPC-program på virtuella Azure-datorer.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d560b261e058d01040616f3c59ede60e5986c672
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666969"
---
# <a name="scaling-hpc-applications"></a>Skala HPC-program

Optimal skalbarhet och skalbar prestanda för HPC-program i Azure kräver prestanda justering och optimerings experiment för den speciella arbets belastningen. Det här avsnittet och de sidor för VM-serien innehåller allmänna rikt linjer för att skala dina program.

## <a name="compiling-applications"></a>Kompilera program

Om du inte behöver kompilera program med lämpliga optimerings flaggor får du bästa möjliga skalnings prestanda på virtuella datorer i HB och HC-serien.

### <a name="amd-optimizing-cc-compiler"></a>AMD-optimering C/C++-kompilator

AOCC-kompilatorsystemet (AMD Optimization C/C++ compiler) erbjuder en hög nivå av avancerad optimering, multi-threading och processor support som omfattar global optimering, vectorization, analys av slingor, loop-omvandlingar och kodgenerering. Binärfiler för AOCC-kompilator är lämpliga för Linux-system som har GNU C Library (glibc) version 2,17 och senare. Kompilator Suite består av en C/C++ compiler (clang), en Fortran-kompilator (FLANG) och en Fortran-frontend till clang (Dragon-ägg).

### <a name="clang"></a>Clang

Clang är en C-, C++-och mål-C-kompilator som hanterar för bearbetning, parsning, optimering, kodgenerering, sammansättning och länkning av kod. Clang stöder  `-march=znver1` flaggan för att möjliggöra bästa möjliga kodgenerering och justering för AMD: s Zen-baserade x86-arkitektur.

### <a name="flang"></a>FLANG

FLANG-kompilatorn är ett nyare tillägg till AOCC Suite (lades till 2018 april) och är för närvarande i för hands versionen för att utvecklare ska kunna hämta och testa. Med hjälp av Fortran 2008 utökar AMD GitHub-versionen av FLANG ( https://github.com/flang-compiler/flang) . FLANG-kompilatorn har stöd för alla clang compile-alternativ och ytterligare ett antal FLANG-angivna kompilator alternativ.

### <a name="dragonegg"></a>DragonEgg

DragonEgg är ett GCC-plugin-program som ersätter GCCs optimeringar och kod generatorer med dem från LLVM-projektet. DragonEgg som medföljer AOCC fungerar med gcc-4.8. x har testats för x86-32/x86-64-mål och har använts på olika Linux-plattformar.

GFortran är den faktiska klient delen för Fortran-program som ansvarar för för bearbetning, parsning och semantisk analys som genererar GCC GIMPLE intermediär representation (IR). DragonEgg är ett GNU-plugin-program, som ansluter till GFortran-kompilator flödet. Den implementerar API för GNU-plugin-programmet. Med plugin-arkitekturen blir DragonEgg kompilator driv rutinen, som kör de olika faserna i kompileringen.  När du har använt hämtnings-och installations anvisningarna kan Dragon-ägg anropas med: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>SGB-kompilator
SGB Community Edition ver. 17 bekräftas att fungera med AMD-EPYC. En SGB-kompilerad version av STREAM levererar plattformens fulla minnes bandbredd. Den nya Community Edition 18,10 (nov 2018) bör också fungera bra. Nedan är ett exempel på CLI som kompileras optimalt med Intel-kompilatorn:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel-kompilator
Intel compiler ver. 18 bekräftas att fungera med AMD-EPYC. Nedan är ett exempel på CLI som kompileras optimalt med Intel-kompilatorn.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC-kompilator 
För HPC rekommenderar AMD GCC compiler 7,3 eller senare. Äldre versioner, till exempel 4.8.5 som ingår i RHEL/CentOS 7,4, rekommenderas inte. GCC 7,3 och senare kommer att leverera betydligt högre prestanda på HPL-, HPCG-och DGEMM-tester.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Skala program 

Följande rekommendationer gäller för optimal effektivitet, prestanda och konsekvens för program skalning:

* Fäst processer till kärnor 0-59 med hjälp av ett rikt uppfästnings sätt (till skillnad från en automatisk balans metod). 
* Bindning efter NUMA/Core/HwThread är bättre än standard bindningen.
* För Hybrid parallella program (OpenMP + MPI) använder du 4 trådar och 1 MPI rang per CCX.
* För rena MPI-program kan du experimentera med 1-4 MPI-rankning per CCX för bästa prestanda.
* Vissa program med extrem känslighet för minnes bandbredd kan dra nytta av ett minskat antal kärnor per CCX. För dessa program kan du använda 3 eller 2 kärnor per CCX för att minska minnes bandbredds konkurrens och ge högre prestanda eller mer konsekvent skalbarhet. I synnerhet kan MPI Allreduce ha nytta av detta.
* För betydligt större skalnings körning rekommenderar vi att du använder UD-eller hybrid RC + UD-transporter. Många MPI-bibliotek/körnings bibliotek gör detta internt (till exempel UCX eller MVAPICH2). Kontrol lera dina transport konfigurationer för storskaliga körningar.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [HPC](/azure/architecture/topics/high-performance-computing/) i Azure.
