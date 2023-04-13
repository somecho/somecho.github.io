---
title: "B.R.A.H.M.S. Explorer"
date: 2023-03-05T12:56:14+02:00
summary: "Die B.R.A.H.M.S. Explorer ist ein durchsuchbares Katalog für zeitgenössische Musik. Sie verwendet React für die Benutzeroberfläche, Flask für den Backend und Sqlite als Datenbank und wird als Webservice auf Render ausgeführt."
tags: ["React","Flask","Sqlite","JavaScript","Python","Sql"]
emoji: "📇"
---

[The B.R.A.H.M.S.
Explorer](https://github.com/somecho/catalog-for-contemporary-music) ist ein
durchsuchbarer Katalog für zeitgenössische Musik mit einer React-Frontend und
einem Flask-Backend. Es verwendet Sqlite als Datenbank, deren Daten von
[IRCAM's Website](https://brahms.ircam.fr/en/) gescraped werden. Die
Live-Anwendung wird auf
[Render](https://catalogforcontemporarymusic.onrender.com/) als
Webservice gehostet[^1].

{{< image src="/images/brahms/ui.png"
alt="Benutzeroberfläche für mobile Geräte und Desktop"
caption="UI für mobile Geräte und Desktop">}}

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

## Aktualisierung der Datenbank
Da der gesamte Katalog auf Render als Webservice ausgeführt wird,
gibt es keinen persistenten Speicher. Wenn ich die Datenbank aktualisieren
wollte, müsste ich meine Sqlite-Datenbank lokal aktualisieren und auf Github
pushen.

Die von mir gewählte Lösung war, einen Github-Action zu schreiben, der IRCAM's
Website wöchentlich scraped, neue Stücke und Komponisten zur Datenbank
hinzufügt und sie dann ins Repo pushed. Der Webservice auf Render würde dann
mit diesem neuen Update neu bereitgestellt.

[^1]: Bei Hosting in der kostenlosen Stufe kann die Leistung des Webservices
	langsam sein.
