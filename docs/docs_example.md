# Reference

Material for MkDocs is packed with many great features that make technical
writing a joyful activity. This section of the documentation explains how to set up
a page, and showcases all available specimen that can be used directly from
within Markdown files.

## Configuration

## Usage

### Setting the page `title`

Each page has a designated title, which is used in the navigation sidebar, for
[social cards] and in other places. While MkDocs attempts to automatically
determine the title of a page in a [four step process], the title can also be
explicitly set with the front matter `title` property:

``` yaml
---
title: Lorem ipsum dolor sit amet # (1)!
---

# Page title
...
```

1.  This line sets the [`title`][title] inside the HTML document's
    [`head`][head] for the generated page to the given value. Note that the
    site title, which is set via [`site_name`][site_name], is appended with a
    dash.

  [social cards]: ../setup/setting-up-social-cards.md
  [four step process]: https://www.mkdocs.org/user-guide/writing-your-docs/#meta-data
  [title]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/title
  [head]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/head
  [site_name]: https://www.mkdocs.org/user-guide/configuration/#site_name

### Setting the page `description`

A Markdown file can include a description that is added to the `meta` tags of
a page, and is also used for [social cards]. It's a good idea to set a
[`site_description`][site_description] in `mkdocs.yml` as a fallback value if
the author does not explicitly define a description for a Markdown file:

``` yaml
---
description: Nullam urna elit, malesuada eget finibus ut, ac tortor. # (1)!
---

# Page title