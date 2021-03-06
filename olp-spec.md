<!--
{
"name" : "outlearn-package-specification",
"version" : "0.5.3",
"title" : "Outlearn Package (OLP) Specification",
"homepage" : "https://github.com/outlearn-content/outlearn-olp-spec",
"freshnessDate": 2016-03-23,
"author" : "Will Koffel",
"license" : "CC BY",
"contact" : { "email" : "will@outlearn.com" }
}
-->

<!-- @section -->

# Outlearn Package Specification


> THIS IS A DRAFT DOCUMENT AND REFERS TO OLP v0.5.3 &mdash; WE WELCOME FEEDBACK AS THIS FORMAT EVOLVES - CURRENT AS OF MARCH 23, 2016


An Outlearn Package, or *OLP* is a directory containing an `outlearn.json` manifest file defining Paths and Modules for import into the Outlearn learning catalog.

The OLP format aims to:

1. Make it easy to author developer learning materials in your native tools, and confidently import them to Outlearn where they can be discovered and consumed in a great learning environment.
2. Provide a simple format into which existing content can be programmatically transformed, making it convenient to migrate existing learning materials from diverse sources into Outlearn-compatible Learning Modules.

## Basic OLP Structure

An OLP directory must contain an `outlearn.json` file at its root.  This manifest file may contain three top-level keys:

* **"olpVersion"** - a [semantic version number](http://semver.org/) representing which version of the OLP format is being used here.  It is optional, but *encouraged*, and will default to the latest version.
* **"paths"** - an array of Path specs imported by this package.
* **"modules"** - an array of Learning Module specs imported by this package.

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
    {"olm" : "./modules/tango-embrace-basics.olm"},
    {"olm" : "./modules/learning-the-tango-steps.olm"}
  ]
}
```

In this example, we specify a package which:

1. Creates two Learning Modules based on enriched Markdown files (Outlearn Markdown, aka *OLM*)
2. Assembles a single Path called "*learning-to-tango*", out of those two created Modules, one Module from another source in the Outlearn catalog, and three Guide Pages with content from local Markdown files.

All the details of specifying Paths and Modules are described below.

**Note**: an Outlearn Package doesn't have to specify Paths *or* Modules.  Each is optional.  OLP may be used to import a Path and all its Modules, or maybe just the independent Modules for a single author, with Paths assembled separately in another OLP import.

<!-- @section -->

## Assembling Paths

The `paths` array defines specifications for one or more Paths to be imported by this package.

### Path Metadata - Required Attributes

Each Path specification must have the following required attributes:

| attribute | sample | notes |
|-----------|--------|-------|
| name | learning-to-tango | Unique within an Outlearn user or organization, alphanumeric+dashes |
| title | Learning to Dance the Tango | Human-readable title for your Path |
| description | A great way to learn everyone's favorite dance, step-by-step. | Short description of your Path, to be used when viewing in the Outlearn catalog. We recommend less than around 130 characters so that the description fits on the Path card in the catalog|
| freshnessDate | 2015-05-25 | Date when you last confirmed that the content is still valid and up-to-date |
| pages | [ { page_spec }+ ] | An array of at least one Guide Page or Learning Module, constituting the Path content itself |

There are two types of page in a path, *Guide Pages* and *Learning Modules*.  Both play an important role as described later in this section.

### Path Metadata - Optional Attributes

The Path specification may also include one or more of the following optional attributes:

| attribute | sample | notes |
|-----------|--------|-------|
| privacy | public | For now, may be "public" or "private".  Private paths will not show up in search or in the public catalog, but are available to the owner, and to any members of the owning organization |
| coverImage | ./assets/cover.jpg | Image that will be stylized grayscale and used as a background for your path header.  Should be JPG or PNG, we recommend keeping the file size down for faster loading of your content, dimensions roughly 1255x440px.  Presentation may not be precise, so patterns and low-contrast images work best |
| coverColor | #eecc25 | An HTML color for this path.  If none is selected, a pretty decent one will be chosen for you.  Warning: please resist the temptation to make your learning ugly |
| collaborationChannel| [https://outlearn.slack.com/ messages/collaboration/](https://outlearn.slack.com/messages/collaboration/) | Link to a collaboration location such as Slack, HipChat, a discussion forum, etc. |

Including a collaboration channel link in your Path definition will create an icon at the bottom right corner as shown below.

![Collaboration Channel Icon](https://raw.githubusercontent.com/outlearn-content/outlearn-publishing/master/images/collaborate.png)

### Path Modules

The Modules (see below for more on Modules) that make up a path are included by reference to their `owner/module-name` in the Outlearn catalog.  If an `owner` is not specified, it defaults to the owner doing the current import.

```json
{"module" : "dancer-doreen/history-of-dance"},
{"module" : "tango-embrace-basics"}
```
**Note**: the Modules in an OLP import get loaded first.  So referencing a Module loaded in the same import as the referencing Path will work fine, and will include the freshly-imported version.

### Guide Pages and Module Prefaces

Guide Pages (aka "Path Pages", or just "Pages") are simple rendered Markdown pages, specific to the Path, which give structure to the path and contextualize the surrounding Modules for your audience.  Pages have varied uses, including:

* Introduction to a Path.  What should your learners expect to get out of this?  Why did you choose to assemble this particular set of Modules together?
* Important context before beginning a Module.  Maybe this next Module has some gotchas to look out for.  Maybe you want to point out in advance why this content is particularly relevant within your project or organization.
* Describe offline learning steps.  Pages are a way to put a "pause" in the Path, for example to instruct your learners to wait until their next offline manager meeting before continuing on.
* Specific concluding thoughts.  What else would you share with your audience as they are digesting this new material?  What final takeaways do you have to solidify the importance of the preceding Modules?

Guide Pages are optional, but highly encouraged.  Without your voice helping to narrate a Path, it is little more than the sum of its Modules.

In the manifest `pages[]` array, Guide Pages are defined as:

```json
{"page" : "./pages/introduction.md"}
```

The referenced Markdown file supports [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/) (but *not* Outlearn OLM enrichments).  A title will be extracted based on a Markdown header of any size at the top of the file (page markdown that does not start with a header will trigger an error on import).  The remainder of the file will be rendered to the body of the page.  Pages are, by convention, placed in a `./pages/` directory inside the OLP root.

Sometimes you want to write a short note about a particular module and adding a Guide Page seems like overkill. Maybe you are including in your Path a tutorial on JavaScript written by someone else and you want to make sure your learners pay attention to the second section. In such a case, adding a Preface to the Module is the perfect solution. Instead of appearing on a separate Page before the Module, a Preface appears at the top of the Module as shown below. Prefaces also show up on the home page of the Path to give an overview of what's included and why.

![Preface](https://raw.githubusercontent.com/outlearn-content/outlearn-publishing/master/images/preface.png)

To add a Preface to a Module in your path, use the key `preface` after specifying a Module to include.

```json
{"module" : "dancer-doreen/history-of-dance",
 "preface" : "Make sure to read the part about Finnish Tango."},
{"module" : "tango-embrace-basics",
 "preface" : "Even if you are experienced dancer, don't skip this because it will be super helpful later."}
```



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
  "tags" : "dance, tango, intro",
  "homepage" : "http://tango.outlearn.com/",
  "canonicalSource" : "http://tango.outlearn.com/steps",
  "freshnessDate" : 2015-07-29,
  "author" : "Dancing Doreen",
  "license" : "CC BY",
  "organization" : "Outlearn Dance Studios",
  "sections" : [
    {"title": "Basic Steps",
    "location": "modules/learning-the-tango-steps/sections/basic-steps.md"},
    {"title": "Corté, Swivel, and Turn",
    "location": "modules/learning-the-tango-steps/sections/advanced-steps.md"},
    {"title": "Watch the Pros",
    "location" : "modules/learning-the-tango-steps/sections/video-lesson.md"}
  ]
}
```

Each element of the `sections` array specifies the Markdown content, which may include [OLM enrichments](https://github.com/outlearn-content/outlearn-olm-spec).

The available attributes for a Learning Module are:

| attribute | required | sample | notes |
|-----------|----------|--------|-------|
| name | ✓ | learning-the-tango-steps | Unique within an Outlearn user or organization, alphanumeric+dashes |
| version | ✓ | 1.0 | [Semantic version number](http://semver.org/) |
| title | ✓ | Learning the Tango Steps | Human-readable title for the Module |
| privacy | | public | For now, may be "public" or "private".  Private modules will not show up in search or in the public catalog, but are available to the owner, and to any members of the owning organization |
| tags |  | dance, tango, intro | Comma-separated string of suggested tags. We will match this against a whitelist of industry standard tags for developer topics |
| homepage |  | http://tango.outlearn.com/ | URL for author homepage or original content source, visible to learners |
| canonicalSource |  | http://tango.outlearn.com/steps | URL for original content source, not visible to learners, creates a [canonical ULR](https://support.google.com/webmasters/answer/139066?hl=en) link element that directs SEO benefits to the original post  |
| freshnessDate | ✓ | 2015-05-25 | Date when you last confirmed that the content is still valid and up-to-date |
| author |  | Dancing Doreen | Free-form string for original author credit |
| license |  | CC-BY | Free-form string specifying a license for this content |
| organization |  | Outlearn Dance Studios | Free-form string specifying an organization related to this content |
| coverColor |  | #eecc25 | HTML color for this path.  If none is selected, a pretty decent one will be chosen for you.  Warning: please resist the temptation to make your learning ugly |
| sections | ✓ | [ { section_spec }+ ] | Array of at least one section that specifies a title and content location |

### An easier way &mdash; Modules in OLM

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
"title" : "Learning the Tango Steps",
"tags" : "dance, tango, intro",
"freshnessDate" : 2015-05-25,
"license" : "CC-BY",
"homepage" : "tango.outlearn.com",
"author" : "Dancing Doreen",
"organization" : "Outlearn Dance Studios"
}
-->
```

See [the OLM specification](https://github.com/outlearn-content/outlearn-olm-spec) for more, including how to define *section* breaks, and how to enrich the content with learning features like *task* items, *multiple choice* exercises, and more.



<!-- @section -->

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
