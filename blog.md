---
layout: inner
title: Blog
permalink: /blog/
---

# Adding the current_user to an imported CSV in Rails 6


I worked through the GoRails videos on importing and exporting CSV and they were wonderful. If you're stuck I'd highly recommend checking them out. \

I wanted to customize my implementation a bit by adding a current user and their team to the csv import so they'd go to the right place. However, since the videos covered a more generic implementation, the rest was up to me. I got stuck on this part for a while so I'm posting it here just in case anyone else on the interweb runs into the same problem (hello, internet citizens!). \

My strategy that I landed up using was to create hidden fields in the form where the file is uploaded and then use those parameters when importing so I could add them then. Passing an instance variable like current_user does not work in the model. Now I know!



---

### In my controller:

`items_controller.rb`

`  def import
    @import = Item::Import.new item_import_params
    if @import.save
      redirect_to root_path, notice: "Imported #{@import.imported_count} items"
    else
      @items = Item.where('team_id = ?', current_user.team_id)
      flash[:alert] = "There were #{@import.errors.count} errors with your csv file"
      render action: :index
    end`

In `private` in the items controller

`    def item_import_params
      params.require(:item_import).permit(:file, :user_id, :team_id)
    end`

In the `index` method of my controller:

`	@import = Item::Import.new`    



### In my view:

`        <%= form_for @import, url: import_items_path, multipart: true do |f| %>
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
        <% end %>`

### In models/item/import/rb

`class Item::Import
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
end`

### In my items model

`  def self.assign_from_row(row)
      item = Item.where(name: row[:name]).first_or_initialize
      item.assign_attributes row.to_hash.slice(:board_id, :team_id, :user_id)
      item
  end`

