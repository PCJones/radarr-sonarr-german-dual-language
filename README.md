# Radarr & Sonarr German Dual Language Custom Format

This guide outlines how to configure Radarr and Sonarr to prefer German + English dual releases.

## Table of Contents
1. [Contributing](#contributing)
2. [Important Note](#important-note)
3. [General Information](#general-information)
4. [Guide](#guide)
   * [1. Create a Quality Profile](#1-create-a-quality-profile)
   * [2. Set the Language Profile](#2-set-the-language-profile)
   * [3. Import the German DL Custom Format](#3-import-the-german-dl-custom-format)
   * [4. Set Score for German DL](#4-set-score-for-german-dl)
5. [Optional Steps](#optional-steps)
   * [Prefer German over English if there is no DL Release](#prefer-german-over-english-if-there-is-no-dl-release)
   * [Prefer English over German if there is no DL Release](#prefer-english-over-german-if-there-is-no-dl-release)
6. [Contact & Support](#contact-&-support)

## Contributing
If you have questions, problems or improvements don't hesitate to create an Issue or Pull Request!

## Important Note
This setup requires Sonarr v4 Beta. Sonarr v3 will not work for this setup. However, Sonarr v4 Beta is quite stable and should not present issues.

## General Information
To reliably find German + English Dual Language releases (from now on referred to as "German DL" as the German Scene refers to them), it's best to have an indexer that specializes in these types of releases.

## Guide

### 1. Create a Quality Profile
Create a Quality Profile based on the Trash Guides for [Radarr](https://trash-guides.info/Radarr/radarr-setup-quality-profiles/#trash-quality-profiles) and [Sonarr](https://trash-guides.info/Sonarr/sonarr-setup-quality-profiles/). If you're already familiar with setting up quality profiles, you may not need to follow these guides exactly, but they're recommended for those new to the process.

### 2. Set the Language Profile
The language profile must be set to `Any`.

### 3. Import the German DL Custom Format
Import the Custom Format "German DL":

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
This custom format matches all possible combinations of "German DL" (without being falsely triggered by WEB-DL). It also matches combinations of [ger,eng] and [DE+EN] which can be found on some torrents.

### 4. Set Score for German DL
In the Quality Profile settings, set the score for the German DL custom format to `20000`.

## Optional Steps

### Prefer German over English if there is no DL Release
This will result in this priority:
1. German + English language
2. German language
3. English language

Import the Custom Format "Language: German Only":

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
        "value": "(?i)german\\s*\\.?dl|(?<=\\bGerman\\b.*)(?<!\\bWEB[-_. ])\\bDL\\b|\\[DE\\+[a-z]{2}\\]|\\[[a-z]{2}\\+DE\\]|ger,\\s*[a-z]{3}\\]|\\[[a-z]{3}\\s

*,\\s*ger\\]"
      }
    }
  ]
}
```
Set the custom score to `15000`.

### Prefer English over German if there is no DL Release
This will result in this priority:
1. German + English language
2. English language
3. German language

Import the Custom Format "Language: English Only":

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
    }
  ]
}
```
Set the custom score to `15000`.

## Contact & Support
- Feel free to create an Issue if you need support. 
- [Telegram](https://t.me/pc_jones)
- Discord: pcjones1
