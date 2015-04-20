# Outlearn Package

> THIS IS A DRAFT DOCUMENT - WE WELCOME FEEDBACK AS THIS FORMAT EVOLVES - CURRENT AS OF April 17, 2015

An Outlearn Package, or *OLP* is a directory containing learning content and packaged assets (i.e. assets like video or images uploaded directly along with your package) for import into the Outlearn learning catalog.

At the top-level, an OLP directory must contain an `outlearn.json` file which describes a learning module, references the markdown files for the content of each section, and declares assets like video files, images, code snippets, markdown content, or other supported Outlearn asset types.

The OLP format aims to:

1. Make it easy to author developer learning content in your native tools, and confidently import to Outlearn where it can be discovered and consumed in an effective learning environment.
2. Provide a simple format into which existing content can be programmatically transformed, making it convenient to migrate existing learning materials from diverse sources into Outlearn-compatible modules.

### A note on OLM (Outlearn Markdown)

A lightweight, single-file alternative to OLP exists in the form of Outlearn Markdown (*OLM*).  For many short, simple modules of learning content, OLM may be a preferred authoring format. [Read the full OLM specification](https://github.com/outlearn-content/outlearn-olm-spec/blob/master/olm-spec.md)

## Basic OLP Structure

An OLP directory must contain an `outlearn.json` manifest file at its root.  The basic structure includes
* metadata about the module
* a `sections` array, describing the various sections of content, which creates the table of contents
* an `assets` array, which declares an identifier for each packaged asset, so it can be referenced in Markdown section content where those assets are to be displayed

A very simple `outlearn.json` file:

```json
{
  "name" : "olp-example",
  "version" : "0.0.1",
  "title" : "Outlearn Package Example",
  "description": "An OLP (Outlearn Package) is a directory containing an Outlearn learning module in a format compatible for import into the Outlearn learning catalog.  This example acts as a simple template for creating your own Outlearn modules.",
  "sections" : [
    {
      "title": "Getting Started",
      "location": "sections/getting-started.md"
    },
    {
      "title": "Advanced Features",
      "location": "sections/advanced-features.md"
    },
    {
      "title": "More Resources",
      "location": "sections/more-resources.md"
    },
  ],
  "assets" : [
    {
      "ref" : "screencast-video",
      "contentType" : "video/mp4",
      "location" : "assets/olp-screencast.mp4"
    }
  ]
}
```

In this example, we specify a few of the most common meta data fields, `name`, `version`, `title`, and `description`.  We show three `sections` of this learning module, each of them a markdown file containing the content.  And we declare one `asset` containing a screencast about the topic, which you can assume is referenced in one of the markdown files as described later in this document.

### Unknown JSON keys and JSON comments

The outlearn parser will ignore any unrecognized fields in the `outlearn.json` file.  And the specification ensures that no supported keys will ever start with an underscore character (`_`), so such fields are safe to use for comments or other custom data in your files.


## Metadata Fields

| Req |     Field    |                                             Example                                             |           Notes           |
|:---:|:------------:|:-----------------------------------------------------------------------------------------------:|:-------------------------:|
|     | olpVersion   | "olpVersion": "0.2"                                                                             |    For now, version 0.2, but will change as this spec evolves.                       |
|  X  | name         | "name" : "olp-example"                                                                          |       Must be unique within your outlearn account, can contain only letters, numbers, dashes, and underscores.                    |
|  X  | version      | "version" : "0.0.1"                                                                             |   Your own version of the module.  This is a semantic version string of your choosing.                        |
|  X  | title        | "title" : "Outlearn Package Example"                                                            |                           |
|  X  | description  | "description": "This example acts as a simple template for creating your own Outlearn modules." |                           |
|     | labels       |  "labels": ["outlearn", "learning", "documentation"]                                                                                               |                Tags you want to include when searching for this module.           |
|     | authors      | "authors": [{"name": "Will Koffel","email": "will@outlearn.com","twitter": "wkoffel"}]            |                           |
|     | organization |      "organization" : "Outlearn"                                                                                           |        Your company name.                   |
|     | license      |     "license": "CC-BY"                                                                                            |       May be any license you choose.                    |
|     | git          |    "git": "outlearn-content/outlearn-olp-spec"                                                                                             |       A github repository of your choosing.  Should be directly related to this learning content, such as the open-source project that is being taught here.                    |
|     | homepage     |   "homepage": "http://www.outlearn.com"                                                                                              |     A homepage for yourself, company, or project.                      |

## Sections Array

The `sections` of a module have special meaning.  They show up in the table of contents for your module, and are used by the system to track user progress (e.g., a checkbox that a user can use to mark off that they've completed a section.)  Outlearn expects to use sections in the future for more user-facing functionality.

A section may be one of two types:

1. A Markdown file with content and asset annotations
2. An Outlearn module, either hosted on Outlearn, or contained in the OLP directory.  **Note**: this type is rare, only used in limited situations where compositing modules without using a Path is appropriate.

### Content Sections

Sections are written as Markdown files.  To include content for a section, put it in a Markdown file (by convention inside a *sections* directory under the OLP root directory) and reference it in your JSON:

```json

{
  "title": "Getting Started",
  "location": "sections/getting-started.md"
}

```

Markdown files are processed as [Github-Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/).  After being processed, and asset annotations are inlined.

**Note**: Any HTML contained in Markdown will be sanitized according to the same [rules that Github uses](https://github.com/github/markup/tree/master#html-sanitization).


#### Referencing Assets

Using an `@asset` annotation in a Markdown content section, you can include a reference to an Outlearn-supported asset type

```markdown

### Screencast

The following screencast does a good job of

<!-- @asset, name: 'screencast-video' -->

In the next section, we'll go through the whole process in more detail, talk about error handling, and how to write tests for our code.
```

An `@asset` annotation must be the only thing in the comment, and for maximum compatibility, the comment should be on a line by itself.  The `ref` attribute is required, its value set to the named reference for the asset declared in `outlearn.json`  Additional attribute/value pairs may be applicable for certain asset types, see detailed documentation below on supported types and their options.

### Module Sections

> **Note**: module sections may be deprecated, including Packaged modules.  Forthcoming documentation describing `Paths`  will be the preferred way to composite learning modules into bigger content experiences.

One of the core tenets of the Outlearn system is that content is modular.  Often, a great useful piece of learning content will be relevant in a larger learning module you are creating.

```json

{
  "type" : "module",
  "location" : "outlearn://w3c/intro-to-html5",
  "version" : "1.2.0"
}

```

This will embed version 1.2.0 of the W3C organizations "Intro to HTML5" from the outlearn catalog as a section within your module.

**Note**: some modules may have many sections or other modules inside them.  With great power comes great responsibility.  It is up to you to maintain a sane learning experience for your readers.

#### Packaged Modules

You may also declare sub-modules in your package directly.  To embed them, reference the OLP json or the OLM markdown file location in the JSON section:

```json
{
  "type" : "module",
  "contentType" : "outlearn/olm"
  "location" : "modules/my-other-great-module.md"
}
```

When the Outlearn importer processes this `outlearn.json` file, it will recursively process any included modules found in the tree.

## Packaged Asset Types

As shown above, assets are referenced inside section Markdown.  Here, we describe all the supported asset types.

Options for an asset may be specified in the outlearn.json file.  Alternatively, they may be specified as comma-separated attribute/value pairs in the `@asset` annotation.  The annotation value takes precedence.

### Common Attributes

All assets have the following attributes in common:

* **ref** - string.  A reference name which will be used in `section` content to embed this asset.  Allowed characters are alphanumeric, dash, and underscore (`[a-zA-Z0-9_-]*`)
* **contentType** - string, default "url". By convention, Outlearn uses standard MIME types where possible.  But the `contentType` is not a true MIME type.  Many custom asset types do not map to standard MIME types.  If you are embedding or packaging an asset without a special type, you should include a standard MIME type in the `contentType` field, as Outlearn will continue to add support for more standards, and your content may benefit from future improvements in how assets are rendered to the learner.  If a contentType is declared that Outlearn does not recognize, it will default to providing a simple link to load the asset in a new browser tab.
* **location** - string.  Packaged assets must live in the directory tree under the root of the `outlearn.json` that declares them.  By convention, they live under the `./assets` directory, but any directory structure may be used.  For clarity in these examples, we include a `./` prefix to indicate the current directory, but a simple `assets/asset_file` will work just as well.


### Plain Text

```json
{
  "ref" : "my-text",
  "contentType" : "text/plain",
  "location" : "./assets/my-text.txt",
  "pre": true
}
```

```markdown
<!-- @asset, ref: 'my-text', pre: true -->
```

#### Options

* **pre** - `true` will render this text asset inside a `<pre>`formatted block.

#### Notes

If conservation of paragraph formatting is desired within text content, the `text/markdown` asset type should be used.


### Markdown Text

```json
{
  "ref" : "my-markdown",
  "contentType" : "text/markdown",
  "location" : "./assets/my-markdown.md",
}
```

```markdown
<!-- @asset, ref: 'my-markdown' -->
```

#### Options

(none)

#### Notes

Since sections are already written natively in Markdown, we encourage content to be written inline when possible.  The Markdown asset type allows you to use a pre-existing Markdown files.  The Markdown will be rendered as if they were inlined directly into the section.  The same sanitization and other github variant rules apply.


### Code Snippet

```json
{
  "ref" : "my-ruby-snippet",
  "contentType" : "text/code",
  "location" : "./assets/my-ruby-snippet.rb",
  "syntax" : "ruby",
  "highlighting" : true,
  "lineNumbers" : false
}
```

```markdown
<!-- @asset, ref: 'my-ruby-snippet', syntax: 'ruby', highlighting: true, lineNumbers: false -->
```

#### Options

* **syntax** - string.  One of the supported syntax highlighting options from (whichever library we use).  If none is specified the code formatter will try to make a relevant guess
* **highlighting** - boolean, default true. Whether to use syntax highlighting on the snippet
* **lineNumers** - boolean, default false.  Whether to show line numbers in a gutter next to the code

#### Notes

Markdown provides native support for code blocks.  If found, these code blocks will also get rendered by our syntax-highlighting library.  Code blocks using triple back-tick (```lang) are supported just like on Github.  If more control is desired, or if you need to reference code samples that are already self contained in separate files, a Code Snippet asset may be more convenient.

### Video

```json

{
  "name" : "my-video",
  "contentType" : "video/mp4",
  "location" : "./assets/my-video.mp4",
  "allowFullScreen" : true,
  "transcriptTimings" : "./assets/my-video-transcript.txt",
  "subtitles" : "./assets/my-video.srt",
  "startTime" : 15
}

```

```markdown

<!-- @asset, ref: 'my-video', startTime: '321', endTime: '381' -->

```

#### Options

* **allowFullScreen** - boolean, default true.  Whether to include a full-screen button in the player

#### Notes

Markdown provides native support for code blocks.  If found, these code blocks will also get rendered by our syntax-highlighting library.  Code blocks using triple back-tick (```lang) are supported just like on Github.  If more control is desired, or if you need to reference code samples that are already self contained in separate files, a Code Snippet asset may be more convenient.

-------------------------------------

### More Types Coming Soon

* URL - url (special behaviors for various kinds of URLs, some documented here)
* Image - image/jpeg
* Audio - audio/mp3


## Remote Asset Types

In addition to assets that you create and include in your Outlearn Package directory structure, Sections can reference assets that are hosted elsewhere.  For examples, see the [OLM Specification](http://www.github.com/outlearn-content/outlearn-olm-spec)

**Note**: Remote Assets are not declared in the outlearn.json file.  They are parsed directly from the `@asset` annotation syntax via their URL instead of an OLP reference name.


### Future asset types

Outlearn is considering support for future asset types; in particular more rich interactive options for encouraging hands-on learning, including:

* SSH Terminal (to connect to a particular machine and run commands)
* REPL (with support for various languages)
* Docker image (with links to launch on various platforms depending on team integrations)

# Importing OLP

To import an OLP directory to Outlearn, put it in a Github repo that is public or that you are authorized to access.  Log in to Outlearn with your Github credentials (or link your Github account under *Settings*), and navigate to *Import Module*, where you can specify the Github location for your module which will then be imported into your Outlearn account.

Many more details on import are coming, including information about a command-line API for importing, and imports from additional web-based sources.

# Example OLP

For many examples of content in OLM and OLP format, please refer to the [Outlearn Content](http://www.github.com/outlearn-content) repo on Github.
