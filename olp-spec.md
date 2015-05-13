# Outlearn Package Specification

> THIS IS A DRAFT DOCUMENT AND REFERS TO OLP v0.5- WE WELCOME FEEDBACK AS THIS FORMAT EVOLVES - CURRENT AS OF MAY 14, 2015

An Outlearn Package, or *OLP* is a directory containing an `outlearn.json` manifest file defining Paths, Modules, and Assets for import into the Outlearn learning catalog.

The OLP format aims to:

1. Make it easy to author developer learning materials in your native tools, and confidently import them to Outlearn where they can be discovered and consumed in a great learning environment.
2. Provide a simple format into which existing content can be programmatically transformed, making it convenient to migrate existing learning materials from diverse sources into Outlearn-compatible modules.

## Basic OLP Structure

An OLP directory must contain an `outlearn.json` file at its root.  This manifest file may contain four top-level keys:

* **"olpVersion"** - a semantic version number representing which version of the OLP format is being used here.  It is encouraged, but *optional*, and will default to the latest version.
* **"paths"** - an array of path specs imported by this Package.
* **"modules"** - an array of Learning Module specs imported by this Package.
* **"assets"** - an array of Assets available to Modules imported by this Package.

Here's a very simple `outlearn.json` file:

```json
{
  "olpVersion" : "0.5",

  "paths" : [
    {
      "name" : "learning-to-tango",
      "title" : "Learning to Dance the Tango",
      "description" : "A learning path for those new to the marvelous tango dance, we'll go through all the basics, and all that's left for you to do is practice until your feet are sore!",
      "privacy" : "public",
      "pages" : [
        {"title" : "Introduction", "content" : "./pages/introduction.md"},
        {"module" : "outlearn://dancer-doreen/history-of-dance"},
        {"title" : "Why Tango?", "content" : "./pages/why-tango.md"},
        {"module" : "tango-embrace-basics"},
        {"module" : "learning-the-tango-steps"},
        {"title" : "Studio and Practice Resources", "./pages/resources.md"}
      ]
    }
  ],

  "modules" : [
    {
      "olm" : "./modules/tango-embrace-basics.olm"
    },
    {
      "olm" : "./modules/learning-the-tango-steps.olm"
    }
  ],

  "assets" : [
    {
      "name" : "steps-video",
      "contentType" : "video/mp4",
      "location" : "assets/tango-steps.mp4"
    }
  ]
}
```

In this example, we specify a Package which:

1. Uploads and hosts a video asset referenced as `steps-video` to locally imported Modules.
2. Creates two Learning Modules based on enriched Markdown files (Outlearn Markdown, aka *OLM*)
3. Assembles a single Path called `learning-to-tango`, out of those two created Modules, one Module from another source in the Outlearn catalog, and three Path Pages with content from local Markdown files.

All the details of specifying Paths, Modules, and Assets are described below.

**Note**: an Outlearn Package doesn't have to specify Paths, Modules, *or* Assets.  Each is optional.  OLP may be used to import a Path and all its Modules.  Or perhaps all the independent Modules for a single author, with Paths assembled separately in another OLP import.  The only requirement is that referenced Assets for a Module import must be declared in the same OLP as the Module that uses them.

## Assembling Paths

### Path Metadata

Paths have the following required attributes:

| attribute   | sample                                                       | notes                                                                                                                                      |
|-------------|---------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name        | learning-to-tango                                             | A name unique within an Outlearn user or organization, alphanumeric+dashes.                                                                                                               |
| title       | Learning to Dance the Tango                                   | A human-readable title for your Path.                                                                                                                                                     |
| description | A great way to learn everyone's favorite dance, step-by-step. | A short description of your Path, to be used when viewing in the Outlearn catalog.                                                                                                        |
| privacy     | public                                                        | For now, may be "public" or "private".  Private paths will not show up in search or in the public catalog, but are available to the owner, and to any members of the owning organization. |
| pages       | [ { page_spec }+ ]                                            | An array of at least one Path Page or Learning Module, constituting the Path content itself.                                                                                              |

There are two types of page in a path, *Context Pages* and *Learning Modules*.  Both play an important role as described below.

### Path Pages

Context Pages (or just "Pages") are simple rendered Markdown pages, unique to the Path, which act as interstitials and contextualize the surrounding Modules for your audience.  Pages have varied uses, including:

* Introduction to a Path.  What should your learners expect to get out of this?  Why did you choose to assemble this particular set of Modules together?
* Important context before beginning a Module.  Maybe this next Module has some gotchas to look out for.  Maybe you want to point out in advance why this content is particularly relevant within your project or organization.
* Describe offline learning steps.  Pages are a way to put a "pause" in the Path, for example to instruct your learners to wait until their next offline manager meeting before continuing on.
* Specific concluding thoughts.  What else would you share with your audience as they are digesting this new material?  What final takeaways do you have to solidify the importance of the preceding Modules?

Pages are optional, but highly encouraged.  Without your voice helping to narrate a Path, it is little more than the sum of its Modules.

In the manifest `pages[]` array, Path Pages are defined as:

```json
{"title" : "Introduction", "content" : "./pages/introduction.md"}
```

The `title` shows automatically at the top of the Page.  The `content` is rendered from the locally referenced Markdown file, the only currently supported Page content type.  Pages are by convention placed in a `./pages/` directory inside the OLP root.

### Path Modules

The Modules (see below for more on Modules) that make up a path may be listed one of two ways.

A Module that has been independently published in the Outlearn catalog can be assembled directly into a Path.

```json
{"module" : "outlearn://dancer-doreen/history-of-dance"}
```

If the Module in question is authored inside this Package, it should be referenced locally:

```json
{"module" : "tango-embrace-basics"}
```

QUESTION: do we need both?  TBD

## Defining Modules

Learning Modules (or simply "Modules") are the core units of engaging content on Outlearn.  They are intended to be stand-alone units of learning so they can be re-used in many different Paths.  Modules may be public or private to an organization.

An olp `modules` array contains simple JSON objects, one for each module being imported by this Package.  These modules may or may not be used by Paths defined in the Package, but typically are.



## Declaring Assets




## Unknown JSON keys and JSON comments

The outlearn parser will ignore any unrecognized fields in the `outlearn.json` file.  And the specification ensures that no supported keys will ever start with an underscore character (`_`), so such fields are safe to use for comments or other custom data in your files.



# TODO: PULL THIS STUFF OUT INTO OLM DOCS

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


#### Remote Assets

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

Module sections can have `title` attributes that override the title defined for the module.

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
  "name" : "my-text",
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
  "name" : "my-markdown",
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
  "name" : "my-ruby-snippet",
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

**Note**: Remote Assets are not declared in the outlearn.json file.  They are parsed directly from the `@asset` annotation syntax via their URL instead of an OLP reference ID.

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
