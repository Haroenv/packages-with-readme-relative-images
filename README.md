# Publishing a README using relative image paths from GitHub to NPM!

---

**SOLVED:** A previous solution that made it possible to render SVG files on
GitHub (using relative URLs) seems to work on raw.githubusercontent.com by
making it correctly set the Content-Type. So, in order to render SVG images that
use relative paths in markdown and have them render properly on NPM & Yarn, you
simply have to append this query to your relative path: `?sanitize=true`.

---

**UPDATE:** *Images have been updated to show the difference between unsanitized and
sanitized (adding `?sanitize=true`) for svg images with relative & absolute URLs
served from GitHub.*

---

## **Chapter 1:** Authoring

In GitHub-Flavoured Markdown (GFM), when you want to include an image using its
relative path you do either this:

> An *inlined* image:
>
>     ![description of the image](relative/path/to/image.ext)
>

or this:

> A *referenced* image with a relative path:
>
>     ![description of the image][asset-1]
>
> Where somewhere in the file (not sure if the order is a factor):
>
>     [asset-1]: relative/path/to/image.ext
>

Both GitHub and VSCode will resolve those paths relative to the markdown source
path. So if you've been down this path, you, myself included, might have
concluded that this is the behaviour to be expected when using relative paths
in GFM.

But then you try to figure out how to reference a particular branch or commit
for your assets, and things start going insane, and you might have decided to
cut your losses just to keep things simple, as I did. Sparing details, you are
essentially choosing between hard-linked githubusercontent or rawgit URLs which
don't fork or relative paths and maybe subtrees and those fork ugly but fork.

So between relative and absolute, you would naturally opt for relative and you
repeat this to yourself a few times so that it sticks, before moving on:

> I promise to only use relative paths from now on!

## **Chapter 2:** Publishing

Now you have your package all cooked up and ready to go and fire this:

    npm publish

or that:

    yarn publish

Sweet, right… not when you get something like:

> you are awesome but does your package really work if you can't even get the
damn thing to render correctly on npm and yarn! *maybe you should stick to
your day job* and **I will go ahead and use that other one that looks right**

Yes, that is what you will eventually be thinking that people are thinking when
they look at your broken README.md on both Yarn & NPM (as of this moment),
because not all images will render like this and we will see why in a bit.

But you figure it has to be that relative paths are being resolved relative to
yarnpkg.com or npm.com and that is an okay assumption, dead-wrong but okay, but
honestly, those guys do a lot of work trying to get your relative stuff to be
as relative as if they were on GitHub's own rendering. Sadly, this last portion
takes a lot of investigating to appreciate, and like me, I think you just want
to keep some of your remaining sanity rid yourself of relative paths and opt for
absolute paths (what could go wrong there aside from the obvious).

So you if you opt to remain relativly sane-minded like myself most of the time,
you repeat this to yourself a few times, before moving on:

> I promise to only use absolute paths from now on!

## **Chapter 3:** Republishing

Wow, it haz downloadz for a few days in a row, and you even start getting that
awesome feeling when someone files their first issue:

> *User:*
> > It is broken, and I hate you.
>
> *You:*
> > I love you so much, how can I make you happier.
>
> *User:*
> > Fix that shim or polyfill it up your stack
>
> *You:*
> > I see. And how does that make you feel? Did you try this …
>
> *User:*
> > Mother-bugger, people need to know that, here is a patch (aka pull-request).
>
> *You:*
> > Oh, I see, you are healing by helping others, I will follow that. Merged!
>
> *User:*
> > Mother-bugger, what happened to my figures?
>
> *You:*
> > Mother-bugger, I've seen that before, let's stick to absolute paths (again).

So you decide to learn from this, and you repeat this to yourself a few times,
before moving on:

> I promise to only use text from now on, maybe html-served links, but my docs
will be as bland and possible and if anyone touches my assets I will have them
arrested.

## **Reflection:**


*Quick Comparison: *

> |              | Relative SVG *fixed* | Relative PNG | Absolute SVG *fixed* | Absolute PNG |
> |:-------------|:------------:|:------------:|:------------:|:------------:|
> | *Referenced* | ![][rel-svg] | ![][rel-png] | ![][abs-svg] | ![][abs-png] |
> | *Inlined unsanitized*    | ![](image.svg) | ![](image.png) | ![](https://raw.githubusercontent.com/code-therapy/packages-with-readme-relative-images/HEAD/image.svg) | ![](https://raw.githubusercontent.com/code-therapy/packages-with-readme-relative-images/HEAD/image.png) |
> | *Inlined sanitized*    | ![](image.svg?sanitize=true) | ![](image.png) | ![](https://raw.githubusercontent.com/code-therapy/packages-with-readme-relative-images/HEAD/image.svg?sanitize=true) | ![](https://raw.githubusercontent.com/code-therapy/packages-with-readme-relative-images/HEAD/image.png) |
> |              |              |              |              |              |

Some might say that the problem is that you are using SVG, but that would be a
little embarrasing to say out loud!

Some people may eventually hate that last bit and decide to do some mindfulness
and reflection, and I happen to be one of those people.

Here is [what npm does (aka marky-markdown)](https://github.com/npm/marky-markdown/blob/3cf46914cfc3a8b20847b3c190862239dbe0ba45/lib/plugin/github.js#L8-L13):

```js
var DEFAULT_REF = 'HEAD'

function buildImageUrl (repository, url) {
  var prefix = 'https://raw.githubusercontent.com/'
  return prefix + path.join(repository.user, repository.repo, DEFAULT_REF, url.href)
}
```

Here is [what yarn does (aka yarnpkg/website)](https://github.com/yarnpkg/website/blob/e155b85428e5ebed5eec9302a5179e47486dc07c/js/src/lib/Details/Markdown.js#L7-L42):

```js
const GITHUB = {
    main: 'https://github.com',
    raw: 'https://raw.githubusercontent.com',
};

/* … then … */

const { user, project, path, head } = githubRepo
const prefix = (href, base) =>
    // prefixURL is a util function that does what it sounds like it does.
    prefixURL(href, { base, user, project, head: head ? head : 'master', path, });

/* … then … */

renderer.image = (href, title, text) =>
    `<img src="${prefix(href, GITHUB.raw)}" title="${title}" alt="${text}"/>`;
```

Remember that mindfulness is not about judging, only recognizing the strong
emotions that this had elicited and sitting with them knowing it is okay before
letting them go to move on. **Sure, enough of that, thanks, time for action.**

## **Conclusion:**

* **NPM & Yarn:** take your relative URLs and they have no clue which git reference
  they resolve from, whereas GitHub knows.*

* **Yarn:** uses algolia to get your package's raw README markdown in a more
  glorified `package.json`-like XHR response, then to head or not-to-head, all
  done client-side and very efficiently in my experience, which is nothing
  short of facinating.

  An advantage of having it on the client-side here is that you can actively
  participate in [debugging and resolving your own issue](yarn-example-issue)
  instead of waiting till someone can actually play around with the code-base
  in some test branch until they can reproduce the issue and isolate the bug
  going only what they can fathom from what you could deem important to them.

* **NPM:** uses own backend to prerender your README using marky-markdown, and
  to be honest, there is more to what markey does than I let on, including a
  plugin to use cdn when resolving relative images, but there is no way to tell
  if that plugin is used in production until I encounter such an image (but
  none so far for me).

  Server-side here can be a simple act of generousity and sense of community
  since it takes the load off client-side where caching can really be a benefit
  but there is also the fact that this is the actual registry, and one would
  expect that the registry itself should be the source of the one consistent
  truth on anything in the registry.

* **GitHub:** seems to not have a one-size strategy when serving files from
  `raw.githubusercontent.com`, treating [PNG as `image/png`](gh-png)
  and [SVG as 'text/plain'](gh-svg) as this should prevent potential
  malicious content from executing client-side, or so becomes the rhetoric.

  While there is an idea that GitHub simply wants to discourage people from
  overloading repos with production requests of live content, consider the fact
  that a PNG is most likely going to be larger than a well-formed SVG, and that
  its footprint grows exponentially with it's dimensions. You avoid this in
  production because GitHub is in the repo business, not a CDN.

* **RawGit:** if you accept the practical limitations on uptime that comes with
  the fact that this is an awesome free service that doing it's best effort,
  you can get properly MIME-typed files but to use them, you currently need
  absolute URLs to work across the board (until it doesn't).

  There is magic in how RawGit, a true CDN service, helps you split-hairs when
  deciding on the URL that best resolves to an asset across its history which
  can also be **permanently** cached (as it should) for production when served
  from `cdn.rawgit.com/…` versus `rawgit.com/…`. Those are functionally two
  very different services that cover most production and development scenarios.

  > |                       |      SVG     |      PNG     |
  > |:----------------------|:------------:|:------------:|
  > | raw.githubusercontent *unsanitized* | ![][gh-svg-1]  | ![][gh-png]  |
  > | raw.githubusercontent *sanitized* | ![][gh-svg]  | ![][gh-png]  |
  > | rawgit                | ![][raw-svg] | ![][raw-png] |
  > | cdn.rawgit            | ![][cdn-svg] | ![][cdn-png] |
  > |                       |              |              |

  **Note:** Just keep in mind that if you overtax them, you will be throttled or
  even banned (ie no more content served from repos under from your user or
  organization.

* **You:** Maybe file an issue, write a post, or pick absolute, or stick with
  relative paths… Nope, I just need a warm bath and maybe my next project will
  not even require a README :)


[rel-png]: image.png
[rel-svg]: image.svg?sanitize=true
[abs-png]: https://raw.githubusercontent.com/code-therapy/packages-with-readme-relative-images/HEAD/image.png
[abs-svg]: https://raw.githubusercontent.com/code-therapy/packages-with-readme-relative-images/HEAD/image.svg?sanitize=true
[gh-png]: https://raw.githubusercontent.com/code-therapy/packages-with-readme-relative-images/HEAD/image.png
[gh-svg-1]: https://raw.githubusercontent.com/code-therapy/packages-with-readme-relative-images/HEAD/image.svg
[gh-svg]: https://raw.githubusercontent.com/code-therapy/packages-with-readme-relative-images/HEAD/image.svg?sanitize=true
[raw-png]: https://rawgit.com/code-therapy/packages-with-readme-relative-images/HEAD/image.png
[raw-svg]: https://rawgit.com/code-therapy/packages-with-readme-relative-images/HEAD/image.svg
[cdn-png]: https://cdn.rawgit.com/code-therapy/packages-with-readme-relative-images/636b6b8616eed31d6333ce98aa414ccd9a47b477/image.png
[cdn-svg]: https://cdn.rawgit.com/code-therapy/packages-with-readme-relative-images/636b6b8616eed31d6333ce98aa414ccd9a47b477/image.svg
[yarn-example-issue]: https://github.com/yarnpkg/website/issues/685
