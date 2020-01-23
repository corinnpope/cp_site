---
layout: post
title:  "Creating a Modal with Vue, Rails 6, and Tailwind"
date:   2020-01-03 09:08:49 -0600
categories: development
---

# Creating a Modal with Vue, Rails 6, and Tailwind
January 3, 2020

The latest problem I was stuck on was creating a modal for a rails app. I wanted to use Vue so I could reuse the component over and over again. I also wanted to use TailwindCSS because I like the control I have over styling elements. 

I'm using the Vue setup outlined by Chris Oliver in ["Vue.js Components in Rails Views"](https://gorails.com/episodes/vuejs-components-in-rails-views?autoplay=1) that wraps everything in a handy vue wrapper. 

This is not the most elegant implementation. In a few months I'll probably look back on this and cringe, but for now I'm putting this out there in case it may help anyone else get a little less stuck. 




### Files used

* app/javascript/components/modal.vue
* app/javascript/packs/application.js
* app/views/layouts/application.html.erb
* app/views/home.html.erb



### in `application.js` 

    import Modal from '../components/modal'
    Vue.component('modal', Modal)


### in `application.html.erb`

    <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>`

### in `modal.vue`

    <template>
      <div>
        <div role="button" class="select-none" @click.prevent="open = !open">
          <slot name="link"></slot>
        </div>

        <div v-show="open" class="bg-neutral-9 absolute top-0 left-0 h-screen w-full flex items-center justify-center">
          <div class="bg-white p-4 rounded w-1/3">
            <slot name="header"></slot>
            <p>Some filler text blah blah blah blah loreim ipsum dolor sit something or other. </p>
            <button v-on:click="toggle" class="mt-4 bg-yellow-5 rounded p-2 font-semibold">Close</button>
          </div>
        </div>
      </div>
    </template>


    <script>
    export default {
        data() {
          return {
            open: false,
          }
        },
        methods: {
          toggle() {
            this.open = !this.open
          },
        },
        created() {
          document.addEventListener('keydown', (e) => {
              if (this.open && e.keyCode == 27) {
                this.open = false
              }
          })
        }
    }
    </script>


### Time to show the modal in my view file


    <modal>
        <a slot="link" href="#" class="text-brandyellow hover:text-brandyellowdark hover:bg-white">OPEN MY MODAL</a>
        <div slot="header">***My SlOt HeAdEr***"</div>
    </modal>