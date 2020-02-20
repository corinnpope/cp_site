---
layout: post
title:  "Getting Rails 6 ActionText and Tailwind to Play Nice"
date:   2020-01-23 09:08:49 -0600
categories: development
---

# I love Tailwind

I love tailwindcss. It makes styling everything so much easier and more intuitive. That's why I use it everywhere in my current project. 

However, my current project also called for a rich text field. 

ActionText and it's ability to "easily" add a rich text area was the obvious fit. So I followed the installation instructions on the [edge guides site](https://edgeguides.rubyonrails.org/action_text_overview.html).

Much to my dismay, after following all the instructions, my text field area looked like this: 

![That don't look right!](/assets/tailwind_rich_formatting_wrong.png) 

#### What I believe went wrong

I can't say this for certain with my limited programming knowledge, but I think having the trix styling code in actiontext.scss was messing everything up. I tried a bunch of different things, mostly by process of elimination, and eventually stumbled on something that worked. 

### How to modify the rails edge guides installation instructions for tailwind

_TL:DR; Move the content of actiontext.scss into application.scss._


##### Step One: Run `rails action_text:install`

That is, if you haven't already. This will create the migrations needed to allow actiontext to function.

You'll also need active storage set up as well but I'll let you take care of that one.

##### Step Two: Include Trix and ActionText in application.js

We still need trix and action text in our application.js pack.
```
// application.js
require("trix")
require("@rails/actiontext")
```
##### Step Three:  Cut out the code from actiontext.scss

Go to actiontext.scss and cut out all the code underneath all the comments. That'll be this ditty of code right here:

```
@import "trix/dist/trix";

.trix-content {
  .attachment-gallery {
    > action-text-attachment,
    > .attachment {
      flex: 1 0 33%;
      padding: 0 0.5em;
      max-width: 33%;
    }

    &.attachment-gallery--2,
    &.attachment-gallery--4 {
      > action-text-attachment,
      > .attachment {
        flex-basis: 50%;
        max-width: 50%;
      }
    }
  }

  action-text-attachment {
    .attachment {
      padding: 0 !important;
      max-width: 100% !important;
    }
  }
}
```

##### Step Four: Paste that code under your tailwind code in application.scss

Paste your actiontext.scss bit after your tailwind imports in application.scss.

 Your file will now look something like this:
```
@import "tailwindcss/base";

@import "tailwindcss/components";

@import "trix/dist/trix";

.trix-content {
  .attachment-gallery {
    > action-text-attachment,
    > .attachment {
      flex: 1 0 33%;
      padding: 0 0.5em;
      max-width: 33%;
    }

    &.attachment-gallery--2,
    &.attachment-gallery--4 {
      > action-text-attachment,
      > .attachment {
        flex-basis: 50%;
        max-width: 50%;
      }
    }
  }

  action-text-attachment {
    .attachment {
      padding: 0 !important;
      max-width: 100% !important;
    }
  }
}

```

##### Step Five: Refresh and enjoy your nicely formatted trix editor the way it was meant to

Ah, much better!

![That does look right](/assets/tailwind_actiontext_right.png) 


That's it! Hope this helps someone! :-) 