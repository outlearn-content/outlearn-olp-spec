<!--
{
"name" : "outlearn-package-specification",
"version" : "0.5.0",
"freshnessDate": 2015-06-15,
"title" : "Outlearn Package (OLP) Specification",
"description": "Everything you need to know to create content using OLP (Outlearn Package) directory format.",
"homepage" : "https://github.com/outlearn-content/outlearn-olp-spec",
"author" : "Will Koffel",
"license" : "CC BY",
"contact" : { "email" : "will@outlearn.com" }
}
-->

<!-- @section -->

# Outlearn Package Specification

> THIS IS A DRAFT DOCUMENT AND REFERS TO OLP v0.5- WE WELCOME FEEDBACK AS THIS FORMAT EVOLVES - CURRENT AS OF JUNE 15, 2015

An Outlearn Package, or *OLP* is a directory containing an `outlearn.json` manifest file defining Paths, Modules, and Assets for import into the Outlearn learning catalog.

The OLP format aims to:

1. Make it easy to author developer learning materials in your native tools, and confidently import them to Outlearn where they can be discovered and consumed in a great learning environment.
2. Provide a simple format into which existing content can be programmatically transformed, making it convenient to migrate existing learning materials from diverse sources into Outlearn-compatible Learning Modules.

## Basic OLP Structure

An OLP directory must contain an `outlearn.json` file at its root.  This manifest file may contain four top-level keys:

* **"olpVersion"** - a [semantic version number](http://semver.org/) representing which version of the OLP format is being used here.  It is optional, but *encouraged*, and will default to the latest version.
* **"paths"** - an array of Path specs imported by this package.
* **"modules"** - an array of Learning Module specs imported by this package.
* **"assets"** - an array of Assets available to Modules imported by this package.

Here's a very simple `outlearn.json` file:

```json
{
  "olpVersion" : "0.5",

  "paths" : [
    {
      "name" : "learning-to-tango",
      "freshnessDate" : 2015-05-25,
      "title" : "Learning to Dance the Tango",
      "description" : "A learning path for those new to the marvelous tango dance, we'll go through all the basics.",
      "privacy" : "public",
      "pages" : [
        {"page" : "./pages/introduction.md"},
        {"module" : "dancer-doreen/history-of-dance"},
        {"page" : "./pages/why-tango.md"},
        {"module" : "tango-embrace-basics"},
        {"module" : "learning-the-tango-steps"},
        {"page" : "./pages/resources.md"}
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
      "_comment" : "Video upload is not a self-service feature yet.  Please contact support@outlearn.com for help with this feature.",
      "name" : "steps-video",
      "contentType" : "video/mp4",
      "location" : "assets/tango-steps.mp4"
    }
  ]
}
```

In this example, we specify a package which:

1. Uploads and hosts a video asset referenced as "*steps-video*" to locally imported Modules.
2. Creates two Learning Modules based on enriched Markdown files (Outlearn Markdown, aka *OLM*)
3. Assembles a single Path called "*learning-to-tango*", out of those two created Modules, one Module from another source in the Outlearn catalog, and three Context Pages with content from local Markdown files.

All the details of specifying Paths, Modules, and Assets are described below.

**Note**: an Outlearn Package doesn't have to specify Paths, Modules, *or* Assets.  Each is optional.  OLP may be used to import a Path and all its Modules, or maybe just the independent Modules for a single author, with Paths assembled separately in another OLP import.

<!-- @section -->

## Assembling Paths

The `paths` array defines specifications for one or more Paths to be imported by this package.

### Path Metadata

Each Path specification must have the following required attributes:

| attribute | sample | notes |
|-----------|--------|-------|
| name | learning-to-tango | unique within an Outlearn user or organization, alphanumeric+dashes |
| title | Learning to Dance the Tango | A human-readable title for your Path. |
| description | A great way to learn everyone's favorite dance, step-by-step. | A short description of your Path, to be used when viewing in the Outlearn catalog. We recommend less than around 130 characters so that the description fits on the Path card in the catalog.|
| freshnessDate | 2015-05-25 | the date when you last confirmed that the content is still valid and up-to-date |
| privacy | public | For now, may be "public" or "private".  Private paths will not show up in search or in the public catalog, but are available to the owner, and to any members of the owning organization. |
| pages | [ { page_spec }+ ] | An array of at least one Context Page or Learning Module, constituting the Path content itself. |

There are two types of page in a path, *Context Pages* and *Learning Modules*.  Both play an important role as described below.

### Context Pages

Context Pages (aka "Path Pages", or just "Pages") are simple rendered Markdown pages, specific to the Path, which act as interstitials and contextualize the surrounding Modules for your audience.  Pages have varied uses, including:

* Introduction to a Path.  What should your learners expect to get out of this?  Why did you choose to assemble this particular set of Modules together?
* Important context before beginning a Module.  Maybe this next Module has some gotchas to look out for.  Maybe you want to point out in advance why this content is particularly relevant within your project or organization.
* Describe offline learning steps.  Pages are a way to put a "pause" in the Path, for example to instruct your learners to wait until their next offline manager meeting before continuing on.
* Specific concluding thoughts.  What else would you share with your audience as they are digesting this new material?  What final takeaways do you have to solidify the importance of the preceding Modules?

Context Pages are optional, but highly encouraged.  Without your voice helping to narrate a Path, it is little more than the sum of its Modules.

In the manifest `pages[]` array, Context Pages are defined as:

```json
{"page" : "./pages/introduction.md"}
```

The referenced Markdown file supports [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/) (but *not* Outlearn OLM enrichments).  A title will be extracted based on a Markdown header of any size at the top of the file (page markdown that does not start with a header will trigger an error on import).  The remainder of the file will be rendered to the body of the page.  Pages are, by convention, placed in a `./pages/` directory inside the OLP root.

### Path Modules

The Modules (see below for more on Modules) that make up a path are included by reference to their `owner/module-name` in the Outlearn catalog.  If an `owner` is not specified, it defaults to the owner doing the current import.

```json
{"module" : "dancer-doreen/history-of-dance"},
{"module" : "tango-embrace-basics"}
```

**Note**: the Modules in an OLP import get loaded first.  So referencing a Module loaded in the same import as the referencing Path will work fine, and will include the freshly-imported version.

<!-- @section -->

## Defining Modules

Learning Modules (or simply "Modules") are the core units of engaging content on Outlearn.  They are intended to be stand-alone units of learning so they can be re-used in many different Paths.  Modules may be public or private to an organization.

Modules contain one or more Sections, which play an important role.  Section titles are used to build the table of contents for a module, and displayed in various ways within the learning experience.  Sections are also used to track user progress (e.g., a checkbox to indicate a user has completed a section.)

The content for a Module is written in [Markdown](https://help.github.com/articles/github-flavored-markdown), and may by enriched using special HTML-comment annotations in the Markdown (this is called "OLM", or "Outlearn Markdown"), as defined by the [OLM Specification](https://github.com/outlearn-content/outlearn-olm-spec)

**Note**: Any HTML contained in Markdown will be sanitized according to the same [rules that Github uses](https://github.com/github/markup/tree/master#html-sanitization).

### Modules in OLP

An OLP `modules` array contains simple JSON objects, one for each module being imported by this Package.  These modules may or may not be used by Paths defined in the Package, but typically are.

```json
{
  "name" : "learning-the-tango-steps",
  "version" : "1.0",
  "title" : "Learning the Tango Steps",
  "description": "An introduction to the steps of the tango, including a video lesson.",
  "homepage" : "tango.outlearn.com",
  "freshnessDate" : 2015-05-25,
  "author" : "Dancing Doreen",
  "license" : "CC-BY",
  "organization" : "Outlearn Dance Studios",
  "sections" : [
    {
      "title": "Basic Steps",
      "location": "modules/learning-the-tango-steps/sections/basic-steps.md"
    },
    {
      "title": "Corté, Swivel, and Turn",
      "location": "modules/learning-the-tango-steps/sections/advanced-steps.md"
    },
    {
      "title": "Watch the Pros",
      "location" : "modules/learning-the-tango-steps/sections/video-lesson.md"
    }
  ]
}
```

Each element of the `sections` array specifies the Markdown content, which may include [OLM enrichments](https://github.com/outlearn-content/outlearn-olm-spec).

The available attributes for a Learning Module are:

| attribute | required | sample | notes |
|-----------|----------|--------|-------|
| name | ✓ | learning-the-tango-steps | unique within an Outlearn user or organization, alphanumeric+dashes |
| version | ✓ | 1.0 | a [semantic version number](http://semver.org/) |
| title | ✓ | Learning the Tango Steps | human-readable title for the Module |
| description | ✓ | An introduction to the steps of the tango. | short (less than 130 characters) description of this Module, to be used various places in the Outlearn content catalog |
| homepage |  | tango.outlearn.com | URL homepage for this author or original content source |
| freshnessDate | ✓ | 2015-05-25 | the date when you last confirmed that the content is still valid and up-to-date |
| author |  | Dancing Doreen | free-form field for original author credit |
| license |  | CC-BY | A free-form string specifying a license for this content |
| organization |  | Outlearn Dance Studios | free-form field specifying an organization related to this content |
| coverImage |  | ./modules/learning-the-tango-steps/cover.jpg | An image that will be stylized grayscale and used as a background for your module header.  Should be JPG or PNG, we recommend keeping the file size down for faster loading of your content, dimensions roughly 1255x440px.  Presentation may not be precise, so patterns and low-contrast images work best. |
| coverColor |  | #eecc25 | An HTML color for this path.  If none is selected, a pretty decent one will be chosen for you.  Warning: please resist the temptation to make your learning ugly. |
| sections | ✓ | [ { section_spec }+ ] | An array of at least one section that specifies a title and content location. |

### An easier way --- Modules in OLM

In many common cases, rather than list all the attributes and sections of a Module in `outlearn.json`, that information can be written right in the Markdown content file.  

For the example above, the `modules` array would instead include just:

```json
{ "olm" : "./modules/my-module-full.md"}
```

And the top of the module OLM file would include any catalog metadata attributes:

```xhtml
<!--
{
"name": "learning-the-tango-steps",
"version" : "1.0",
"freshnessDate" : 2015-05-25,
"title" : "Learning the Tango Steps",
"license" : "CC-BY",
"homepage" : "tango.outlearn.com",
"author" : "Dancing Doreen",
"organization" : "Outlearn Dance Studios",
"description": "An introduction to the steps of the tango, including a video lesson."
}
-->
```

See [the OLM specification](https://github.com/outlearn-content/outlearn-olm-spec) for more, including how to define *section* breaks, and how to enrich the content with learning features like *task* items, *multiple choice* exercises, and more.

<!-- @section -->

## Declaring Assets

Outlearn supports uploading and hosting images and videos.  This is useful for private content, and for some public content that may not be hosted elsewhere, making it hard to reference from regular Markdown.

> **Note**: Video and image uploads are not self-service features yet.  Please contact support@outlearn.com for help with these features.

Video assets that are remotely hosted, for example through YouTube or Vimeo, can be included directly from the OLM content.  For examples, see the [OLM Specification](http://www.github.com/outlearn-content/outlearn-olm-spec)

<!-- @section, "tracked" : false -->

## JSON comments

The OLP specification ensures that no supported keys will ever start with an underscore character (`_`), so such fields are safe to use for comments or other custom data in your files.

```json
{
  "olpVersion" : "0.5",

  "paths" : [
    {
      "name" : "learning-to-salsa",
      "_comment" : "This path is still under construction."
    }
  ]
}
```

<!-- @section, "tracked" : false -->

## Examples of OLP

For many examples of content in OLM and OLP format, please refer to the [Outlearn Content](http://www.github.com/outlearn-content) repo on Github.
