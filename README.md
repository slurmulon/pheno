# pheno

> :snowflake: A collection of micro-standards and conventions for supporting l10n and i18n

---

As SaaS continues to grow on the web, the needs of these applications in
terms of locationalization (l10n) and internationalization (i18n) have grown
beyond the capabilities of `gettext`, the defacto industry standard.

`gettext` is a simple specification for supporting translations and alternative
versions of text, and it does a very good job at supporting this. There are also
_many_ libraries and client consumer tools for `gettext` across many languages
and platforms.

This is not a call to replace `gettext`, but rather an attempt to assist it
where it falls short and to help developers achieve l10n/i18n zen.

---

## Design

Supporting internationalization and localization in an application is manageable
with the right architecture, and it is always easier to integrate sooner than later.

The majority of open-source efforts towards l10n/i8n have more or less converged into
`gettext`, and thus the focus has generally been on **text and content** rather than
**context, functionality, behavior, and architecture**.

The following contains a categorized summarization of architectual and design considerations 
to make when developing l10n/18n applications, many of which `gettext` cannot address on its own:

### Encapsulation

Separating localizable elements from source code or content so that the elements may be alternatively localized based on the user's internationalization settings.

### Modularization

Business logic, modules and configuration related to internationalization or localization should be as decoupled from the implementation as much as possible.

### Reusability

Localized components should be able to inherit or compose re-usable units of common logic.

### Transparency

The use and effects of localized entities in code should be as transparent as possible, that is generally agreed on.
Technical and low-level considerations, such as establishing URL conventions or deciding how to structure your CDN / buckets, are less trivial and are sometimes approached in an adhoc manner.

In general, an excessive amount of transparency can make essential data impossible or difficult to acquire, while too little will sacrifice design sustainability and eventually lead to culture rot (i.e. resentful engineers). The greatest risk is running into a technical wall and being forced to perform a large refactor or re-write the related code entirely.

## Deployment

There are several considerations to make when designing a deployment architecture for an l10n/i18n application.

 * Role of the build step
 * Packaging technique of localized/organization-specific builds
 * Efficient deploy times (what **shouldn't/don't** we have to re-build on deploy?)
 * Are updates to translation files deployed with code or separately?

## Features

Certain features often apply in one localized context but not another. Minimizing the invasiveness of l10n/i18 related code (and thus increasing modularization)
is often a difficult task, particularly for those concerned with keeping code clean and free of awkward control flow structures.

## Presentation

`gettext` does an excellent job of helping to support plural pronounciations, right-to-left languages, and of course localized content based on a user's internationalization profile. However, it rightfully doesn't specify anything regarding the more _stylistic_ aspects of the content.

It is not uncommon for locales to correlate with another company branch or partner organization. SaaS companies have begun to offer "home-cooked" versions of their client application for customers, and the more gracefully an application can adapt to the individual requirements of each customer or partner organization the better.

---

## Conventions

### Build Step

Pre-processing code allows you to work in an abstracted layer of transparency that favors environment-specific or partner-specific logic. Modern applications using `gettext` often incorporate some sort of build step that packages/compiles translations or exports a subset of translations for a certain deployment.

In dynamic ecosystems such as JavaScript, the build step can be immensely powerful for customizing run-time functionality that applies only a specific partner, organization, locale, etc. We shall call this *-specific functionality. The primary benefit is that your code can be remain as agnostic to meta-information as possible, which leads to fewer concerns, greater encapsulation and less complexity.

An effective but admittedly non-DRY solution for *-specific functionality is using a simple directory convention that outlines inheritance:

```
.
└── src
    ├── feature-a
    ├── feature-b
    │   └── partner-b
    ├── index.js
    └── partner-a
        ├── feature-a
        └── index.js
```

The names of each directory are pretty arbitrary, but the pattern that they follow is important.

The default implementation of any script or feature lives wherever you would normally define it,
in something like `src` or `src/feature-a`.

Alternative implementations of scripts or features are defined as a sub-directory in the module
or directory that contains the default implementation. Take for example `partner-a`, an organization
which requires unique implementations of both the base `src/index.js` file and `src/feature-a`. Also, `partner-b` requires a custom implementation of `feature-b`.

So how does it come together?

Quite simply. The build step is responsible for performing a unification of the code through the following process:

1. Transpile or duplicate default implementation scripts to an output directory such as `dist` for each partner or organization
2. Transpile or duplicate partner-specific implementation scripts to the **same** output directory, but flattening the output paths such that the default implementation scripts are **completely overwritten** with the partner-specific scripts

Tools such as `glob` eliminate the need for complex recursive searches. Here is a potential mapping of source to destination patterns:

| Source                   | Destination  |
|--------------------------|--------------|
| src/**/!(partner_a)/*.js | dist/**/*.js |
| src/**/partner_a/*.js    | dist/**/*.js |

This convention ultimately requires a fine level of modularization and encapsulation in order to be successful and minimize duplication.

## Specifications

TODO 

 - [ ] JSON Schemas
 - [ ] Context
 - [ ] Configuration
