---
layout: inner
title: Blog
permalink: /blog/
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



---
---

# Adding the current_user to an imported CSV in Rails 6
December 31, 2019


I worked through the GoRails videos on importing and exporting CSV and they were wonderful. If you're stuck I'd highly recommend checking them out. \

I wanted to customize my implementation a bit by adding a current user and their team to the csv import so they'd go to the right place. However, since the videos covered a more generic implementation, the rest was up to me. I got stuck on this part for a while so I'm posting it here just in case anyone else on the interweb runs into the same problem (hello, internet citizens!). 

My strategy that I landed up using was to create hidden fields in the form where the file is uploaded and then use those parameters when importing so I could add them then. Passing an instance variable like current_user does not work in the model. Now I know!



---

### In my controller:

Here's `items_controller.rb`:

    def import

    @import = Item::Import.new item_import_params
        if @import.save

    redirect_to root_path, notice: "Imported #{@import.imported_count} items"

    else

        @items = Item.where('team_id = ?', current_user.team_id)
        flash[:alert] = "There were #{@import.errors.count} errors with your csv file"

        render action: :index

    end


In `private` in the items controller:


    def item_import_params
         params.require(:item_import).permit(:file, :user_id, :team_id)
    end




In the `index` method of my controller:

`	@import = Item::Import.new`    



### In my view:

        <%= form_for @import, url: import_items_path, multipart: true do |f| %>
          <% if @import.errors.any? %>
            <div class="alert alert-error">
              <% @import.errors.full_messages.each do |msg| %>
                <div> <%= msg %></div>
              <% end %>
            </div>
          <% end %>
          <%= f.file_field :file %>
          <%= f.hidden_field(:user_id, :value => current_user.id) %>
          <%= f.hidden_field(:team_id, :value => @team) %>
          <%= f.submit "Upload" %>
        <% end %>


### In models/item/import/rb:



    class Item::Import
    include ActiveModel::Model

	attr_accessor :file, :imported_count, :user_id, :team_id

	# process csv file
	    def process!
		@imported_count = 0
		table = CSV.read(file.path, {headers: true, col_sep: ","})    
	        # Add another col, row by row:
	        table.each do |row|
	          row["user_id"] = user_id
	          row["team_id"] = team_id
	        end
	        # write to file
	        CSV.open(file.path, "w") do |f|
	          f << table.headers
	          table.each{|row| f << row}
	        end
    	CSV.foreach(file.path, headers: true, header_converters: :symbol) do |row|
     	 item = Item.assign_from_row(row)
	    	     if item.save
	    	      @imported_count += 1
	    	     else
	    	     	errors.add(:base, "Line #{$.} - #{item.errors.full_messages.join(",")}")
		     	#puts "#{item.name} - #{item.errors.full_messages.join(",")}" if item.errors.any?
	    	     end
    	end
	    end

	    def save
		process!
	     	errors.none?
	   end
    end



### In my items model:

    def self.assign_from_row(row)
       item = Item.where(name: row[:name]).first_or_initialize
       item.assign_attributes row.to_hash.slice(:board_id, :team_id, :user_id)
       item    
    end
