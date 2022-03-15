# Sulu block bundle

Un bundle qui permet de gérer les blocs de contenu pour le CMS SULU.

C'est un fork du projet https://github.com/Harborn-digital/sulu-block-bundle

## 1. Installation
### Composer
```bash
composer require pixeldev/sulu-block-bundle
```

## 2. Usage
### Template
Modification d'un template de page (penser à inclure cet élément xmlns:xi="http://www.w3.org/2001/XInclude")
```xml
<!-- app/Resources/templates/pages/default.xml -->
<?xml version="1.0" ?>
<template xmlns="http://schemas.sulu.io/template/template"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:xi="http://www.w3.org/2001/XInclude"
          xsi:schemaLocation="http://schemas.sulu.io/template/template http://schemas.sulu.io/template/template-1.0.xsd">

    <key>default</key>

    <view>templates/default</view>
    <controller>SuluWebsiteBundle:Default:index</controller>
    <cacheLifetime>2400</cacheLifetime>

    <meta>
        <title lang="en">Default</title>
        <title lang="nl">Standaard</title>
    </meta>

    <properties>
        <!--
        <section name="highlight">
            <properties>
                <property name="title" type="text_line" mandatory="true">
                    <meta>
                        <title lang="en">Title</title>
                        <title lang="nl">Titel</title>
                    </meta>
                    <params>
                        <param name="headline" value="true"/>
                    </params>

                    <tag name="sulu.rlp.part"/>
                </property>

                <property name="url" type="resource_locator" mandatory="true">
                    <meta>
                        <title lang="en">Resourcelocator</title>
                        <title lang="nl">Adres</title>
                    </meta>

                    <tag name="sulu.rlp"/>
                </property>
            </properties>
        </section>

        <property name="article" type="text_editor">
            <meta>
                <title lang="en">Article</title>
                <title lang="de">Artikel</title>
            </meta>
        </property>-->

        <!-- Choose the same name as using in twig (see next paragraph) -->
        <block name="blocks" default-type="text" minOccurs="0">
            <meta>
                <title lang="en">Content</title>
                <title lang="nl">Inhoud</title>
            </meta>
            <types>
                <xi:include href="sulu-block-bundle://blocks/text.xml"/>
                <xi:include href="sulu-block-bundle://blocks/youtube.xml"/>
            </types>
        </block>

        <!-- Choose the same name as using in twig (see next paragraph) -->
        <block name="banners" default-type="text" minOccurs="0">
            <meta>
                <title lang="en">Banners</title>
            </meta>
            <types>
                <xi:include href="sulu-block-bundle://blocks/text.xml"/>
                <xi:include href="sulu-block-bundle://blocks/youtube.xml"/>
            </types>
        </block>
    </properties>
</template>
```
### Twig
Ajouter les blocs au niveau de Twig
```twig
{% block content %}
    <div vocab='http://schema.org/' typeof='Content'>
        <h1 property='title'>{{ content.title }}</h1>

        <div property='article'>
            {{ content.article|raw }}
        </div>

        {% include '@Block/html5-blocks.html.twig' %}
        {% include '@Block/html5-blocks.html.twig' with {element: 'aside', collection: 'banners'} %}
    </div>
{% endblock %}
```
#### Surcharger les templates Twig
Créer la structure suivante pour surcharger les blocs via Twig `templates/bundles/PixelBlockBundle`.
Si vous souhaitez surcharger le bloc suivant `Resources/views/html5/parts/images.html.twig` vous devez créer le fichier suivant `templates/bundles/PixelBlockBundle/html5/parts/images.html.twig`.

Et si vous souhaitez uniquement remplacer certains blocs des modèles de ce bundle, vous pouvez étendre le modèle de base en utilisant le namespace `@!Block`.

Par exemple
```twig
{# templates/bundles/PixelBlockBundle/html5/parts/images.html.twig #}
{% extends "@!Block/html5/parts/images.html.twig" %}

{% block image_source %}{{ image.thumbnails['50x50'] }}{% endblock %}
```

## 3. Blocs disponibles
- Texte enrichi avec un titre (text)
- Images avec un titre (images)
- Images avec un titre et un texte enrichi (image_text)
- Image, titre avec sous-titre et citation (image_title_subtitle_quote)
- Vidéo Youtube (youtube)
- Lien (link)

## 4. Ajouter des propriétés
Lorsque vous utilisez un bloc et que vous souhaitez ajouter des propriétés supplémentaires, vous pouvez les configurer séparément dans `config/templates/PixelSuluBlockBundle/properties/{blockname}.xml`.

Par exemple, si vous souhaitez ajouter une légende au bloc d'images. Vous créeriez le fichier suivant dans votre application client :
```xml
<!-- config/templates/PixelSuluBlockBundle/properties/images.xml -->
<?xml version='1.0' ?>
<properties xmlns='http://schemas.sulu.io/template/template'
    xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
    xsi:schemaLocation='http://schemas.sulu.io/template/template http://schemas.sulu.io/template/template-1.0.xsd'
    >
    <property name='caption' type='text_line'>
        <meta>
            <title lang='en'>Caption</title>
        </meta>
    </property>
</properties>
```

## 5. Surcharge des paramètres des propriétés

### 5.1 Remplacer complètement tous les paramètres
Lorsque vous utilisez un bloc et que vous souhaitez choisir vous-même tous les paramètres des propriétés des blocs, vous pouvez les configurer séparément dans `config/templates/PixelSuluBlockBundle/params/{blockname}.xml`.
Par exemple, si vous souhaitez définir tous les paramètres de la propriété de l'éditeur de texte. Vous créeriez le fichier suivant dans votre application client :
```xml
<!-- config/templates/PixelSuluBlockBundle/params/text_editor.xml -->
<?xml version='1.0' ?>
<params xmlns='http://schemas.sulu.io/template/template'
    xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
    xsi:schemaLocation='http://schemas.sulu.io/template/template http://schemas.sulu.io/template/template-1.0.xsd'
    >
    <param name="link" value="true"/>
    <param name="paste_from_word" value="true"/>
    <param name="height" value="100"/>
    <param name="max_height" value="200"/>
</params>
```

### 5.2 Ajuster les paramètres
Lorsque vous utilisez un bloc et que vous souhaitez modifier les paramètres des propriétés des blocs, vous pouvez les configurer séparément dans `config/templates/PixelSuluBlockBundle/params/{blockname}_adjustments.xml`.

Par exemple, si vous souhaitez ajuster la hauteur et désactiver la fonctionnalité de tableau de la propriété text_editor. Vous créeriez le fichier suivant dans votre application client:
```xml
<!-- config/templates/PixelSuluBlockBundle/params/text_editor_adjustments.xml -->
<?xml version='1.0' ?>
<params xmlns='http://schemas.sulu.io/template/template'
    xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
    xsi:schemaLocation='http://schemas.sulu.io/template/template http://schemas.sulu.io/template/template-1.0.xsd'
    >
    <param name="height" value="200"/>
    <param name="table" value="false"/>
</params>
```

### 5.3 Ajouter des paramètres

Lorsque vous utilisez un bloc et que vous souhaitez ajouter des paramètres aux propriétés des blocs, vous pouvez les configurer séparément dans`config/templates/PixelSuluBlockBundle/params/{blockname}_additions.xml`.

Par exemple, si vous souhaitez ajouter le paramètre ui_color à la propriété text_editor. Vous créeriez le fichier suivant dans votre application client :
```xml
<!-- config/templates/PixelSuluBlockBundle/params/text_editor_additions.xml -->
<?xml version='1.0' ?>
<params xmlns='http://schemas.sulu.io/template/template'
    xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
    xsi:schemaLocation='http://schemas.sulu.io/template/template http://schemas.sulu.io/template/template-1.0.xsd'
    >
    <param name="ui_color" value="#ffcc00"/>
</params>
```