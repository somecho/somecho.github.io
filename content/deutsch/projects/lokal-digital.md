---
title: "Lokal/Digital - Performance mit Machine Learning"
emoji: "📺"
date: 2020-08-29T14:40:27+01:00
tags: ["C++","Python","Keras","OpenFrameworks","Machine Learning","PureData"]
summary: "Lokal/Digital ist eine interdisziplinäre Performance, die zeitgenössischen Tanz, freie Improvisation und Live-Audiovisuals durch den Einsatz von Machine Learning kombiniert."
description: "Lokal/Digital ist eine interdisziplinäre Performance, die zeitgenössischen Tanz, freie Improvisation und Live-Audiovisuals durch den Einsatz von Machine Learning kombiniert."
---

Lokal/Digital ist eine interdisziplinäre Performance, die zeitgenössischen
Tanz, freie Improvisation und Live-Audiovisuelles kombiniert. Die Performance
wurde in Zusammenarbeit mit der Tänzerin und Choreografin [Evelin
Stadler](https://atelierhaus-stadler-gerhardt.jimdo.com/atelierhaus/stadler)
konzipiert.

{{< image 
src="/images/lokaldigital/hero.JPG" 
alt="Somē spielt Klavier">}}

Die Performance beinhaltete verschiedene Elemente. Zum einen trat Evelin mit
verschiedenen Requisiten in einem separaten Raum auf und wurde live auf eine
Leinwand auf der Bühne übertragen. Auf der Bühne improvisierte ich am Klavier
und manipulierte in Echtzeit elektronische Effekte des Klangs, während eine
visuelle Projektion auf mein Spiel reagierte.

{{< video "/videos/lokaldigital/physarum-test.mp4" "perf" >}}

Dieser Artikel handelt hauptsächlich von dem visuellen Aspekt der Performance.

## Das Visuals

Die Live-Visualisierungen bestanden aus zwei Teilen:
- Eine OpenFrameworks-Anwendung, die eine stilisierte Physarum-Simulation ausführte.
- 8 neuronale Netze, die parallel liefen, mein Spiel über PureData hörten und die Simulation kontrollierten.

{{< tweet 1100809319136804864 >}} 

Die Idee, eine Physarum-Simulation zu verwenden, wurde von der Künstler_in [Sage
Jenson](https://www.sagejenson.com) inspiriert, die einen [umfassenden
Artikel](https://cargocollective.com/sagejenson/physarum) über die
Implementierung der Simulation geschrieben hat. Ich fand den visuellen Ausdruck
von Physarum besonders passend für unsere Performance und entwickelte [meine
eigene Implementierung](https://github.com/somecho/openframeworks-physarum) in
OpenFrameworks.

## Ein Oktett von neuronalen Netzen

Der Grund, warum es 8 neuronale Netzwerke gibt, besteht darin, dass jedes
Netzwerk einen separaten Parameter in der Physarum-Anwendung steuern kann. Die
freigegebenen Parameter sind:

- Die Anzahl der Richtungen, in die sich ein Teilchen umschauen kann
- Die Menge an hinterlassener Ablagerung
- Die maximale Geschwindigkeit eines Teilchens
- Die maximale Kraft, die auf ein Teilchen angewendet werden kann
- Die Abbaugeschwindigkeit der Ablagerungsspuren
- Wie weit ein Teilchen voraussehen kann
- Wie steil der Unterschied der Spuren sein soll
- Die Größe der Verschiebung der Spuren

Die Netzwerke steuern diese Parameter separat wie ein Ensemble, um die visuelle
Ausdrucksweise der Physarum in Echtzeit zu verändern.

## Training

{{< video "/videos/lokaldigital/physarum-train.mp4" "train1" >}}

Die Netzwerke sind auf Aufnahmen meines eigenen Spielens
[trainiert](https://github.com/somecho/lokal-digital/blob/master/notebooks/csvtonumpy2.ipynb).
Ich habe einen PureData-Patch erstellt, der mein Spiel analysiert und die
Analysen als CSV-Datei aufzeichnet. Die Analyse liefert die folgenden Merkmale:

- 37 Mel-Frequenz-Cepstral-Koeffizienten
- Onset Anzahl
- Durchschnittlicher RMS-Pegel
- Spektralzentrum
- Spektrale Unregelmäßigkeit
- Spektrale Geschwindigkeit
- Spektrale Helligkeit
- Harmonizität
- Spektrale Schiefe

## Setup

{{< video "/videos/lokaldigital/physarum-train-2.mp4" "train2" >}}

Ein OSC-Server, der Live-Inputs von der PureData-Patch empfangen hat, wurde mit
einem
[Python-Skript](https://github.com/somecho/lokal-digital/blob/master/lokaldigital.py)
in Echtzeit ausfgeführt. Diese Inputs wurden an das neuronale Netzwerk zur
Vorhersage weitergeleitet und dann direkt an die Physarum-Anwendung gesendet.
