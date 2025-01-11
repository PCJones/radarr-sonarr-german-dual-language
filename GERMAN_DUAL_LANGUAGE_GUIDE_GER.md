# Anleitung: Radarr & Sonarr Deutsch & Englisch mehrsprachig - mit Quality Profiles & Custom Formats

In dieser Anleitung wird erklärt, wie Radarr und Sonarr konfiguriert werden können, um vollautomatisiert German Dual Language (Deutsche + Englische Tonspur) Releases zu bevorzugen.

Letztes Update: 30.01.2024

# [English Guide](https://github.com/PCJones/radarr-sonarr-german-dual-language/)

## Inhaltsverzeichnis
- [Mitwirken](#mitwirken)
- [Wichtiger Hinweis](#wichtiger-hinweis)
- [Allgemeine Informationen](#allgemeine-informationen)
- [Ich möchte keine doppelte Sprache, sondern Deutsch bevorzugen und Englisch als Fallback nutzen (oder umgekehrt)](#ich-möchte-keine-doppelte-sprache-sondern-deutsch-bevorzugen-und-englisch-als-fallback-nutzen-oder-umgekehrt)
- [Anleitung](#anleitung)
  - [1. Importiere die German DL Custom Formast](#1-importiere-die-german-dl-custom-formats)
  - [2. Importiere das Language: Not ENG/GER Custom Format](#2-importiere-das-language-not-engger-custom-format)
  - [3. Erstelle ein Quality Profile](#3-erstelle-ein-quality-profile)
  - [4. Qualitäten zusammenführen (merge qualities)](#4-qualit%C3%A4ten-zusammenf%C3%BChren-merge-qualities)
  - [5. Sprache einstellen](#5-sprache-einstellen)
  - [6. Setze Upgrade Until Custom](#6-setze-upgrade-until-custom)
  - [7. Lege Custom Format Scores fest](#7-lege-custom-format-scores-fest)
  - [8. Sprache bevorzugen](#8-sprache-bevorzugen) 
    - [Bevorzuge Deutsch vor Englisch, wenn kein DL Release vorhanden ist](#--bevorzuge-deutsch-vor-englisch-wenn-kein-dl-release-vorhanden-ist)
    - [Bevorzuge Englisch vor Deutsch, wenn kein DL Release vorhanden ist](#--bevorzuge-englisch-vor-deutsch-wenn-kein-dl-release-vorhanden-ist)
  - [9. Qualitäts-Upgrades durch Custom Formats](#9-qualitäts-upgrades-durch-custom-formats)
  - [10. Optional: Mic Dubbed Tonspuren vermeiden](#10-optional-mic-dubbed-tonspuren-vermeiden)
- [Kontakt & Support](#kontakt--support)

## Mitwirken
Wenn du Fragen, Probleme oder Verbesserungsvorschläge hast, erstelle gerne ein Issue oder einen Pull Request!

## Wichtiger Hinweis
Diese Anleitung erfordert Sonarr v4 Beta, Sonarr v3 funktionert nicht, da es dort keine Custom Formats gibt. Die Sonarr v4 Beta ist jedoch ziemlich stabil und lässt sich problemlos verwenden.

## Allgemeine Informationen
Um zuverlässig Releases mit Deutscher + Englischer (bzw. Originaler) Tonspur (Als "German DL" in der deutschen Szene bekannt) zu finden, ist es am besten, einen Usenet-Indexer bzw Torrent-Tracker zu verwenden, der auf diese Art von Releases spezialisiert ist.

## Ich möchte keine doppelte Sprache, sondern Deutsch bevorzugen und Englisch als Fallback nutzen (oder umgekehrt)
Kein Problem! Folge einfach jedem Schritt in dieser Anleitung, aber überspringe alle Schritte, die sich auf das "German DL" Custom Format beziehen. Bei [Sprache bevorzugen](#8-sprache-bevorzugen) 
importiere das, was zu deinem Setup passt und **entferne dann die Bedingung "NOT German DL"** bevor du speicherst. Entschuldige, wenn das kompliziert klingt, ich werde bald eine extra Seite nur für dieses Szenario erstellen!

## Anleitung

### 1. Importiere die German DL Custom Formats
Falls du nicht weißt, wie man Custom Formats importiert gibt es dafür eine kurze Anleitung:
[How to import Custom Formats - Trash Guides](https://trash-guides.info/Radarr/Radarr-import-custom-formats/#how-to-import-a-json-custom-format)


Importiere das Custom Format "German DL":

```json
{
  "name": "German DL",
  "includeCustomFormatWhenRenaming": true,
  "specifications": [
    {
      "name": "German DL",
      "implementation": "ReleaseTitleSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": "(?i)german\\s*\\.?dl|(?<=\\bGerman\\b.*)(?<!\\bWEB[-_. ])\\bDL\\b|\\[DE\\+[a-z]{2}\\]|\\[[a-z]{2}\\+DE\\]|ger,\\s*[a-z]{3}\\]|\\[[a-z]{3}\\s*,\\s*ger\\]"
      }
    }
  ]
}
```
Dieses Custom Format erkennt alle möglichen Kombinationen von "German DL" (ohne dass es fälschlicherweise von WEB-DL ausgelöst wird). Es erkennt auch Kombinationen von [ger,eng] und [DE+EN], die bei einigen Torrents zu finden sind.

Importiere das Custom Format "German DL 2":
```json
 {
  "name": "German DL 2",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "NOT German DL",
      "implementation": "ReleaseTitleSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": "(?i)german\\s*\\.?dl|(?<=\\bGerman\\b.*)(?<!\\bWEB[-_. ])\\bDL\\b|\\[DE\\+[a-z]{2}\\]|\\[[a-z]{2}\\+DE\\]|ger,\\s*[a-z]{3}\\]|\\[[a-z]{3}\\s*,\\s*ger\\]"
      }
    },
    {
      "name": "German",
      "implementation": "LanguageSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 4
      }
    },
    {
      "name": "English",
      "implementation": "LanguageSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1
      }
    }
  ]
}
```
Dieses Custom Format ist notwendig, um einige zweisprachige Veröffentlichungen zu erkennen, die nicht "German DL" in ihrem Namen haben.
**Anmerkung**: Dieses Custom Format wurde zu einem späteren Zeitpunkt hinzugefügt und fehlt auf den Screenshots, die später gezeigt werden.

### 2. Importiere das Language: Not ENG/GER Custom Format
Importiere das Custom Format "Language: Not ENG/GER":

```json
{
  "name": "Language: Not ENG/GER",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Not English Language",
      "implementation": "LanguageSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 1
      }
    },
    {
      "name": "Not German Language",
      "implementation": "LanguageSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 4
      }
    },
    {
      "name": "Not German in Title",
      "implementation": "ReleaseTitleSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": "(?i)\\bgerman\\b"
      }
    }
  ]
}
```
Dieses Custom Format dient dazu, alle anderen Sprachen außer Deutsch und Englisch auszuschließen.

### 3. Erstelle ein Quality Profile
Erstelle ein Quality Profile basierend auf den Trash Guides für [Radarr](https://trash-guides.info/Radarr/radarr-setup-quality-profiles/#trash-quality-profiles) und [Sonarr](https://trash-guides.info/Sonarr/sonarr-setup-quality-profiles/). Wenn du bereits Erfahrung mit Quality Profiles hast, musst du diese Anleitungen nicht genau befolgen, aber sie sind für Neueinsteiger empfehlenswert.

### 4. Qualitäten zusammenführen (merge qualities)

Um German DL gegenüber Qualität zu bevorzugen, müssen wir alle gewünschten Qualitäten in einer Gruppe zusammenführen (Merge qualities auf Englisch). 
![Merge qualities animation](https://trash-guides.info/Radarr/Tips/images/merge.gif)

How to merge Qualities - *Animation von [TRaSH Guides](https://trash-guides.info/Radarr/Tips/Merge-quality/) ([MIT License](https://github.com/TRaSH-Guides/Guides/blob/master/LICENSE))*.

In meinem Beispiel wollen wir die Qualität in dieser absteigenden Reihenfolge bevorzugen: Remux-1080p -> Bluray-1080p -> WEBDL-1080 -> Bluray-720p

Hier ist, wie es aussehen sollte, nachdem die Qualitäten gemerged wurden:

![Qualitäten zusammenführen 1](https://raw.githubusercontent.com/PCJones/radarr-sonarr-german-dual-language/main/img/merge_qualities_1.png)
![Qualitäten zusammenführen 2](https://raw.githubusercontent.com/PCJones/radarr-sonarr-german-dual-language/main/img/merge_qualities_2.png)

Obwohl die Qualitäten zusammengeführt sind, ist es immer noch möglich, sie über Custom Formats zu verbessern. Weitere Details dazu kommen im Abschnitt [Qualitäts-Upgrades durch Custom Formats](#8-qualitäts-upgrades-durch-custom-formats).

### 5. Sprache einstellen.
Die Sprache im Quality Profile muss auf `Any` gesetzt werden.

### 6. Setze Upgrade Until Custom
Setze im Quality Profile den Wert von "Upgrade Until Custom" auf `50000`

### 7. Lege Custom Format Scores fest
In den Einstellungen des Quality Profiles setze die Punktzahlen für die Custom Formats wie folgt:

| Custom Format         | Score |
|-----------------------|-----------|
| German DL             | 25000     |
| German DL 2           | 25000     |
| Language: Not ENG/GER | -30000    |

### 8. Sprache bevorzugen

#### - Bevorzuge Deutsch vor Englisch, wenn kein DL Release vorhanden ist
Dies führt zu folgender Priorität:
1. Deutsch + Englische Sprache
2. Deutsche Sprache
3. Englische Sprache

Importiere das Custom Format "Language: German Only":

```json
{
  "name": "Language: German Only",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Language GER",
      "implementation": "LanguageSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 4
      }
    },
    {
      "name": "NOT German DL",
      "implementation": "ReleaseTitleSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": "(?i)german\\s*\\.?dl|(?<=\\bGerman\\b.*)(?<!\\bWEB[-_. ])\\bDL\\b|\\[DE\\+[a-z]{2}\\]|\\[[a-z]{2}\\+DE\\]|ger,\\s*[a-z]{3}\\]|\\[[a-z]{3}\\s*,\\s*ger\\]"
      }
    },
    {
      "name": "Not English",
      "implementation": "LanguageSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 1
      }
    }
  ]
}
```
Setze die Punktzahl für das Custom Format "Language: German Only" im Quality Profile auf `15000`.


#### - Bevorzuge Englisch vor Deutsch, wenn kein DL Release vorhanden ist
Dies führt zu folgender Priorität:
1. Deutsch + Englische Sprache
2. Englische Sprache
3. Deutsche Sprache

Importiere das Custom Format "Language: English Only":

```json
{
  "name": "Language: English Only",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Language ENG",
      "implementation": "LanguageSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1
      }
    },
    {
      "name": "NOT German DL",
      "implementation": "ReleaseTitleSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": "(?i)german\\s*\\.?dl|(?<=\\bGerman\\b.*)(?<!\\bWEB[-_. ])\\bDL\\b|\\[DE\\+[a-z]{2}\\]|\\[[a-z]{2}\\+DE\\]|ger,\\s*[a-z]{3}\\]|\\[[a-z]{3}\\s*,\\s*ger\\]"
      }
    },
    {
      "name": "NOT German Language",
      "implementation": "LanguageSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 4
      }
    }
  ]
}
```
Setze die Punktzahl für das Custom Format "Language: English Only" im Quality Profile auf `15000`.

### 9. Qualitäts-Upgrades durch Custom Formats

Wir möchten German DL Releases priorisieren, aber trotzdem innerhalb dieser Releases auf höhere Qualitäten upgraden.

Importiere die Custom Formats, die du benötigst:
<details>
<summary><b>Bluray-1080p (Radarr)</b></summary>

```json
{
  "name": "Bluray-1080p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Bluray",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 9
      }
    },
    {
      "name": "1080p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1080
      }
    },
    {
      "name": "Not REMUX",
      "implementation": "QualityModifierSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 5
      }
    }
  ]
}
```
</details>

<details>
<summary><b>Bluray-1080p (Sonarr)</b></summary>

```json
{
  "name": "Bluray-1080p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Bluray",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 6
      }
    },
    {
      "name": "1080p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1080
      }
    },
    {
      "name": "Not REMUX",
      "implementation": "SourceSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 7
      }
    }
  ]
}
```
</details>

<details>
<summary><b>Bluray-2160p (Radarr)</b></summary>

```json
{
  "name": "Bluray-2160p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Bluray",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 9
      }
    },
    {
      "name": "2160p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 2160
      }
    },
    {
      "name": "Not REMUX",
      "implementation": "QualityModifierSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 5
      }
    }
  ]
}
```
</details>

<details>
<summary><b>Bluray-2160p (Sonarr)</b></summary>

```json
{
  "name": "Bluray-2160p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Bluray",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 6
      }
    },
    {
      "name": "2160p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 2160
      }
    },
    {
      "name": "Not REMUX",
      "implementation": "SourceSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 7
      }
    }
  ]
}
```
</details>

<details>
<summary><b>Bluray-720p (Radarr)</b></summary>

```json
{
  "name": "Bluray-720p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Bluray",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 9
      }
    },
    {
      "name": "720p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 720
      }
    },
    {
      "name": "Not REMUX",
      "implementation": "QualityModifierSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 5
      }
    }
  ]
}
```
</details>

<details>
<summary><b>Bluray-720p (Sonarr)</b></summary>

```json
{
  "name": "Bluray-720p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Bluray",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 6
      }
    },
    {
      "name": "720p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 720
      }
    },
    {
      "name": "Not REMUX",
      "implementation": "SourceSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 7
      }
    }
  ]
}
```
</details>

<details>
<summary><b>WEBDL-1080p (Radarr)</b></summary>

```json
{
  "name": "WEBDL-1080p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {


      "name": "WEBDL",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 7
      }
    },
    {
      "name": "1080p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1080
      }
    }
  ]
}
```
</details>

<details>
<summary><b>WEBDL-1080p (Sonarr)</b></summary>

```json
{
  "name": "WEBDL-1080p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {


      "name": "WEBDL",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 3
      }
    },
    {
      "name": "1080p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1080
      }
    }
  ]
}
```
</details>

<details>
<summary><b>WEBDL-2160p (Radarr)</b></summary>

```json
{
  "name": "WEBDL-2160p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "WEBDL",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 7
      }
    },
    {
      "name": "2160p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 2160
      }
    }
  ]
}
```
</details>


<details>
<summary><b>WEBDL-2160p (Sonarr)</b></summary>

```json
{
  "name": "WEBDL-2160p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "WEBDL",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 3
      }
    },
    {
      "name": "2160p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 2160
      }
    }
  ]
}
```
</details>

<details>
<summary><b>WEBDL-720p (Radarr)</b></summary>

```json
{
  "name": "WEBDL-720p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "WEBDL",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 7
      }
    },
    {
      "name": "720p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 720
      }
    }
  ]
}
```
</details>

<details>
<summary><b>WEBDL-720p (Sonarr)</b></summary>

```json
{
  "name": "WEBDL-720p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "WEBDL",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 3
      }
    },
    {
      "name": "720p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 720
      }
    }
  ]
}
```
</details>

<details>
<summary><b>WebRip-1080p (Radarr)</b></summary>

```json
{
  "name": "WebRip-1080p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "WebRip",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 8
      }
    },
    {
      "name": "1080p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1080
      }
    }
  ]
}
```
</details>

<details>
<summary><b>WebRip-1080p (Sonarr)</b></summary>

```json
{
  "name": "WebRip-1080p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "WebRip",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 4
      }
    },
    {
      "name": "1080p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1080
      }
    }
  ]
}
```
</details>

<details>
<summary><b>WebRip-720p (Radarr)</b></summary>

```json
{
  "name": "WebRip-720p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "WebRip",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 8
      }
    },
    {
      "name": "720p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 720
      }
    }
  ]
}
```
</details>

<details>
<summary><b>WebRip-720p (Sonarr)</b></summary>

```json
{
  "name": "WebRip-720p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "WebRip",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 4
      }
    },
    {
      "name": "720p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 720
      }
    }
  ]
}
```
</details>

<details>
<summary><b>Remux-1080p (Radarr)</b></summary>

```json
{
  "name": "Remux-1080p",
  "includeCustomFormatWhenRenaming":

 false,
  "specifications": [
    {
      "name": "1080p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1080
      }
    },
    {
      "name": "REMUX",
      "implementation": "QualityModifierSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 5
      }
    }
  ]
}
```
</details>

<details>
<summary><b>Remux-1080p (Sonarr)</b></summary>

```json
{
  "name": "Remux-1080p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Remux",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 7
      }
    },
    {
      "name": "1080p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 1080
      }
    },
    {
      "name": "Not BluRay",
      "implementation": "SourceSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 6
      }
    }
  ]
}
```
</details>

<details>
<summary><b>Remux-2160p (Radarr)</b></summary>

```json
{
  "name": "Remux-2160p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "2160p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 2160
      }
    },
    {
      "name": "REMUX",
      "implementation": "QualityModifierSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 5
      }
    }
  ]
}
```
</details>

<details>
<summary><b>Remux-2160p (Sonarr)</b></summary>

```json
{
  "name": "Remux-2160p",
  "includeCustomFormatWhenRenaming": false,
  "specifications": [
    {
      "name": "Remux",
      "implementation": "SourceSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 7
      }
    },
    {
      "name": "2160p",
      "implementation": "ResolutionSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": 2160
      }
    },
    {
      "name": "Not BluRay",
      "implementation": "SourceSpecification",
      "negate": true,
      "required": true,
      "fields": {
        "value": 6
      }
    }
  ]
}
```
</details>


> Hinweis: Wenn du ein Custom Format für eine andere Qualität benötigst, importiere eines der obigen Beispiele und ändere die Bedingungen entsprechend. Das sollte relativ selbsterklärend sein, aber falls du Hilfe brauchst, frag gerne.

Im [vorherigen Beispiel](https://raw.githubusercontent.com/PCJones/radarr-sonarr-german-dual-language/main/img/merge_qualities_2.png), in dem wir nach Remux-1080p, Bluray-1080p, WEBDL-1080p und Bluray-720p suchen, müssten wir für jede dieser Qualitäten ein Custom Format hinzufügen.

Ordne diesen Custom Formats anschließend Punkte in deinem Quality Profile zu. Setze `0` für die niedrigste Qualität, `2000` für die zweitniedrigste, `4000` für die nächste, usw. - erhöhe also die Punktzahl jedes Mal um `2000`. Mit diesem Punktesystem wird eine Priorisierung und ein Upgrade auf höherwertige Formate ermöglicht, wenn sie verfügbar werden.

Hier ist ein Beispiel dafür, wie die Punktevergabe aussehen sollte:

![Custom Format Quality](https://github.com/PCJones/radarr-sonarr-german-dual-language/blob/main/img/custom_format_quality.png?raw=true)

Jetzt werden deine Filme und Serien immer dann auf eine höhere Qualität upgraden, sobald ein German DL Release in höherer Qualität verfügbar wird.

### 10. Optional: Mic Dubbed Tonspuren vermeiden
Mic Dubbed ("MD") Tonspuren werden z.B. im Kino aufgenommen und haben eine schlechte Soundqualität. Außerdem können sie Geräusche von Zuschauern enthalten. Der erste German DL Release hat oft eine einwandfreie Englische Tonspur, die Deutsche ist aber Mic Dubbed. Falls du nicht total ungeduldig bist empfehle ich sehr, diese zu vermeiden. Da es hier nur um Filme geht ist das Ganze nur für Radarr relevant.

Wenn du Mic Dubbed releases haben möchtest, empfehle ich trotzdem das Custom Format zu importieren und einen Score von `-100` zu vergeben, damit Radarr den Film upgraded, sobald er mit einer besseren Tonspur released wird.

Importiere das MIC DUB Custom Format:

<details>
<summary><b>MIC DUB Custom Format</b></summary>

```json
{
  "name": "MIC DUB",
  "includeCustomFormatWhenRenaming": true,
  "specifications": [
    {
      "name": "MD",
      "implementation": "ReleaseTitleSpecification",
      "negate": false,
      "required": true,
      "fields": {
        "value": "(?i)((19|20|21)[0-9]{2}).*?(?<=^|[ .-])GERMAN(?=[ .-]|$).*?(?<=^|[ .-])(?:MD|MIC[ .-]?Dubbed)(?=[ .-]|$)|((19|20|21)[0-9]{2}).*?(?<=^|[ .-])(?:MD|MIC[ .-]?Dubbed)(?=[ .-]|$).*?(?<=^|[ .-])GERMAN(?=[ .-]|$)"
      }
    }
  ]
}
```
</details>

In den Einstellungen des Quality Profiles setze die Punktzahl für das Custom Format wie folgt:

| Custom Format         | Score  |
|-----------------------|--------|
| MIC DUB             | -35000  |

## Kontakt & Support
- Öffne gerne ein Issue auf GitHub falls du Unterstützung benötigst.
- [Telegram](https://t.me/pc_jones)
- Discord: pcjones1 - oder komm in den UsenetDE Discord Server: [https://discord.gg/pZrrMcJMQM](https://discord.gg/pZrrMcJMQM)

## Spenden
Über eine Spende freue ich mich natürlich immer :D
PayPal: https://paypal.me/pcjones1

Für andere Spendenmöglichkeiten gerne auf Discord oder Telegram melden - danke!
