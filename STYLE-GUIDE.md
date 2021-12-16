# Style Guide

This document defines some general styles we adhere to in the docs.

## Use Inclusive Language

Words are important.  Pulumi strives to use language that is clear, harmonious, and friendly to all readers.  With these goals in mind, we use the following guidelines:

* Avoid ableist language:
  * Instead of _crazy_ try _wild_.
  * Instead of _click_ use _select_.
  * Instead of _dummy_ use _placeholder_.
* Avoid unnecessarily gendered language: Instead of _guys_ try _folks_, _yall_, or _everyone_.
* Avoid using violent language (e.g., _kill_)
* Avoid pop-culture references as such references may not be familiar to all readers.

## Headings

* Ensure that readers are able to scan the headings of a page and get an effective overview of the page's content.
* Every page should have exactly one `h1`.
* Headings levels should only increment one level at a time.  E.g., if your previous heading level was an `h2`, the next heading must be an `h2` or an `h3`, but not, e.g., an `h4` or `h5`.

## Links

* Link text should be descriptive and have meaning outside of the surrounding context: Avoid link text like _here_, _click here_, _see here_ and instead link to the title of the linked page, e.g. "see [Pulumi Pricing](https://www.pulumi.com/pricing/)".  (While this practice beneftis all readers, it is of particular important for visually impared users who use screen readers and often jump through the links of a document.)

## Notes and Warnings

Our docs currently support two kinds of note: `info`-level and `warning`-level.

* Use notes in general to communicate important information.
* Try to limit the number of notes within a single page.
* Use `info`-level notes to convey general information.
* Use `warning`-level notes for information that, if missed, could lead to negative or unexpected consequences.

### Examples

```go
{{% notes type="info" %}}
This bit of info is important enough to call out, but not critical.
{{% /notes %}}

{{% notes type="warning" %}}
This bit of info is serious. If you missed it, bad things could happen.
{{% /notes %}}
```

## Blockquotes

* Use blockquotes only when directly quoting content from another source.

### Example

> This is something a person said.

## Additional Resources

* Markdownlint will help enforce syntactically valid Markdown and is available as a [CLI tool](https://github.com/igorshubovych/markdownlint-cli#installation) or as a [Visual Studio Code plugin](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint).
* [Writing inclusive documentation](https://developers.google.com/style/inclusive-documentation) (Google).