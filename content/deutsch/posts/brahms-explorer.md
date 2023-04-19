---
title: "B.R.A.H.M.S. Explorer"
date: 2023-03-05T12:56:14+02:00
summary: "Der B.R.A.H.M.S. Explorer ist ein durchsuchbares Katalog für zeitgenössische Musik, der mit einer Microservices-Architektur entwickelt wurde."
tags: ["React","Flask","MySQL","JavaScript","Python","Microservices"]
emoji: "📇"
---

[Der B.R.A.H.M.S. Explorer](https://github.com/somecho/brahms-explorer) ist
ein durchsuchbarer Katalog für zeitgenössische Musik, dessen Daten von der
[B.R.A.H.M.S.-Datenbank](https://brahms.ircam.fr/en/) von IRCAM gescraped
werden. Die Live-Anwendung kann unter [diesem
Link](https://brahmsexplorer.onrender.com) besucht werden.

{{< image src="/images/brahms/ui.png"
alt="Benutzeroberfläche für mobile Geräte und Desktop"
caption="UI für mobile Geräte und Desktop">}}

## Architektur

Die Anwendung ist mit einer modularen Komponentarchitektur aufgebaut. Der
Benutzer interagiert mit einer mit React erstellten Frontend-Anwendung. Alle
Daten, die das Frontend benötigt, werden über das Backend abgerufen, eine
Flask-Anwendung, die für die Kommunikation zwischen Frontend und Datenbank
verantwortlich ist. Sowohl Front- als auch Backend werden auf Render gehostet.

{{< image src="/images/brahms/diagram.de.png"
alt="Ein Diagramm, das die Beziehung zwischen den Microservices zeigt"
caption="Beziehung zwischen den Services">}}

Um immer auf dem neuesten Stand der Daten von IRCAM zu bleiben, wird täglich
automatisch eine Github-Aktion gestartet, die die Datenbank synchronisiert. Das
bedeutet, dass die Informationen in der Anwendung immer aktuell sind.

## Ziel
Das ursprüngliche Suchformular auf der Website von IRCAM war überladen mit
vielen erweiterten Optionen, wie z.B. ob ein Stück in IRCAM geschrieben wurde
oder wer die musikalischen Assistenten sind. Die Suche nach Instrumentation war
auch unintuitiv, da willkürliche Gruppierungen von Instrumentenkategorien
verwendet wurden. Die Ergebnisse der Suche konnten auch nicht sortiert werden.

{{< image src="/images/brahms/ircam.png"
alt="Screenshot von den Instrumentationen auf ircam"
caption="Instrumentationsgruppierungen von IRCAM" >}}

Als Musiker_in wollte ich einfach einige lose verwandte Begriffe wie "Viola Tuba
Klarinette" eingeben und sehen, welche Stücke sie haben. Ich wollte auch die
Ergebnisse nach Komponistennamen, Jahr oder Titel sortieren können.

## Suche
Der Suchalgorithmus ist einfach. Die Schlüsselwortbegriffe werden nach
Leerzeichen aufgeteilt. Dann wird eine SQL-Abfrage an die Datenbank gestellt,
wobei sichergestellt wird, dass alle Begriffe in den Schlüsselwörtern entweder
im Titel, im Untertitel, im Komponistennamen oder im Jahr erscheinen. Dies
bedeutet, dass eine breitere Palette von Ergebnissen verfügbar sein wird. Die
Ergebnisse können auch in aufsteigender oder absteigender Reihenfolge sortiert
werden.

{{< image src="/images/brahms/searchbar.png"
alt="Screenshot der Suchleiste"
caption="Die Suchleiste" >}}
