---
title: Che cos'è un punteggio BLEU? - Custom Translator
titleSuffix: Azure Cognitive Services
description: BLEU è una misura delle differenze tra una traduzione automatica e una o più traduzioni umane di riferimento della stessa frase di origine. L'algoritmo BLEU confronta espressioni consecutive della traduzione automatica con le espressioni consecutive che trova nella traduzione di riferimento e conta il numero di corrispondenze in modo ponderato.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 1993819ad227d7e9aa5ef899045e00447a6740b8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595410"
---
# <a name="what-is-a-bleu-score"></a>Che cos'è un punteggio BLEU?

[BLEU (Bilingual Evaluation Understudy)](https://en.wikipedia.org/wiki/BLEU) è una misura delle differenze tra una traduzione automatica e una o più traduzioni umane di riferimento della stessa frase di origine.

## <a name="scoring-process"></a>Processo di assegnazione dei punteggi

L'algoritmo BLEU confronta espressioni consecutive della traduzione automatica con le espressioni consecutive che trova nella traduzione di riferimento e conta il numero di corrispondenze in modo ponderato. Queste corrispondenze sono indipendenti dalla posizione. Un grado di corrispondenza superiore indica un livello di somiglianza superiore con la traduzione di riferimento e di conseguenza un punteggio più elevato. La chiarezza e la correttezza grammaticale non vengono prese in considerazione.

## <a name="how-bleu-works"></a>Come funziona BLEU?

Il punto di forza di BLEU è la sua capacità di stabilire una correlazione con il giudizio umano calcolando la media degli errori di giudizio di singole frasi su una raccolta di test, invece di cercare di definire l'esatto giudizio umano per ogni frase.

Una discussione più approfondita sui punteggi BLEU è disponibile [qui](https://youtu.be/-UqDljMymMg).

I risultati di BLEU dipendono largamente dall'ampiezza del dominio, dalla coerenza dei dati di test con i dati di training e ottimizzazione e dalla quantità di dati disponibili per il training. Se il training dei modelli è stato eseguito in un dominio ristretto e i dati del training sono coerenti con i dati di test, il punteggio BLEU dovrebbe essere elevato.

>[!NOTE]
>Un confronto tra punteggi BLEU è giustificabile solo quando i risultati BLEU vengono confrontati con lo stesso set di test, la stessa coppia di lingue e lo stesso motore di traduzione automatica. Un punteggio BLEU di un set di test diverso sarà necessariamente diverso.
