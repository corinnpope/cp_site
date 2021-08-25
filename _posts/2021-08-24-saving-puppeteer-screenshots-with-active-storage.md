---
layout: post
title:  "Saving Puppeteer Screenshots To Active Record In Rails"
date:   2020-08-24 09:08:49 -0600
categories: development
---

I'm working on a side project right now. It lets website owners, copywriters, and designers run [free 5-second tests](https://isthisclear.com).

After posing a question about who is responsible for conversion rates on twitter, [@andrewculver](https://twitter.com/andrewculver/status/1427309562059628549?s=20) responded about how his whole team is and how a developer created essentially an open form that helped make it really easy for people to get started on a task on their site. 

I wondered if there was anything I could apply that same thing to on my own site. I have been having some trouble with getting traffic so I want to make sure that any traffic I do get will be able to get started with as little effort as possible. And when I say "get started", I really mean "create a test". 

**Entering a URL and taking a screenshot of it using puppeteer** was an idea [@excid3](https://twitter.com/excid3) had suggested a while back I've been meaning to implement. I had some time this weekend and thought I may as well give it a go.

But I ran into a lot of issues when getting it to work. First, since puppeteer is mostly javascript, I knew I'd probably want to use either a lot of stimulusJS or a gem. I don't want to write everything from the ground up. [Grover](https://github.com/Studiosity/grover) landed up fitting the bill. The only thing was that most of the tutorials on it were around PDFs and there wasn't any rails specific documentation in the docs on getting it set up with Active Storage. 

Grover gave a nice `.to_png` method that was super handy, but it didn't really give a great explanation of what you were getting with that. So first issue was trying to decode what that meant. Literally. 

Next issue I ran into was getting it to save. I tried what felt like everything. Most of my time was spent trying to get it into a TempFile, but trying to save that tempfile resulted in a weird authentication error. 

Eventually I realized I'd need ImageMagick to save it as an image successfully. And because it wasn't a direct upload, I'd need to use `io:`, `filename:`, and `content_type:` to send my image to Active Storage. 

Things I found helpful and pulled from along the way:
* [An Elastic Beanstalk, Rails, Grover, Puppeteer app](https://github.com/paulmwatson/elasticbeanstalk-rails-grover-puppeteer)
* [SO question on binary data into Active Storage compatible data](https://stackoverflow.com/questions/55787737/is-there-a-way-to-convert-binary-data-into-a-data-type-that-will-allow-activesto)

Here's how I landed up getting it working:

### The Setup

I have cloudinary setup as my Active Storage provider. You'll need to have your own setup as well. 

You'll also need the following in your gemfile:
```
gem 'puppeteer-ruby'
gem 'grover'
gem "mini_magick"
```

And run `bundle install`. 

You may always want to set up an initializer for grover in config/intializers. Mine looks like this:

```
Grover.configure do |config|
  config.use_png_middleware = true
  config.options = {
    viewport: {
      width: 1024,
      height: 768
    },
    prefer_css_page_size: true,
    emulate_media: 'screen',
    cache: true,
    timeout: 0,
    wait_until: 'domcontentloaded',
    full_page: false
  }
end
```

You'll also want to make sure you have active storage setup for the field you're using as your file field like so: `has_one_attached :screenshot`.

### The First Form

Alright. Setup is done. Next we gotta get our screenshot in somehow. I created this in a partial and rendered it in my homepage. It takes a url from the user. 

```
<div class="" data-turbolinks-animate-persist="true">
  <form>
    <input class="input" type="url" placeholder="Enter a URL" value="<%= @test_url %>" name="test_url" required autofocus>
      <input type="submit" value="get feedback" class="btn btn-large btn-primary"> 
  </form>
</div>
```

I then store the input of the form in a cookie, take them through the signup process, and spit them out on a create test page with their test url they entered already pre-filled. 

### The Create Form
We'll also need a "regular" create form. You could probably figure out how to combine these two, but this way worked best for the flow I wanted to take the users through. 

```
<%= form_with(model: @test) do |form| %>
  <p class="text-xs text-gray-700">Enter a URL (including the https:// part and we'll optimize it for a 5-second test</p>
  <div class="form-group">
    <%= form.text_field :test_url, placeholder: "https://yoursite.com", class: "form-control" %>
  </div>

  <!-- more form fields would go here, but skipping them for brevety's sake -->

  <%= form.button "Post Test", class: "btn btn-primary" %>
<% end %>
```

The form setup is pretty simple. Next up is saving what comes through that form.

### The Create Method
So it turns out that the `.to_png` outputs binary data. We then give that data to MiniMagick to read and give it a format. 

We'll need to store attach it as an io object next. The [Rails docs](https://edgeguides.rubyonrails.org/active_storage_overview.html#attaching-file-io-objects) tell us just how to do that.

```
  def create
    @test = Test.new(test_params)
    if @test.save
      test_url = ActionController::Base.helpers.sanitize(test_params[:test_url] || '', tags: [])
      decoded_data = Grover.new(test_url).to_png
      image = MiniMagick::Image.read(decoded_data)
      image.format("png")
      @test.screenshot.attach(
        io: StringIO.new(image.to_blob),
        filename: "#{@test.name}_screenshot.png",
        content_type: "image/png"
      )
      redirect_to test_path, notice: "Test was successfully created."
    else
      render :new, status: :unprocessable_entity
    end
  end
```

### Viewing the Newly Created Screenshot

If you want to view the screenshot from active storage, use the following:

```
<% if @test.screenshot_attached? && @test.screenshot.representable? %>
  <%= image_tag(@test.screenshot) %>
<% end %>
```

You can also forgo uploading it at all if all you want to do is view it. 

#### Just viewing it (controller)
```
@test_url = ActionController::Base.helpers.sanitize(cookies[:test_url] || '', tags: [])
@image = Rails.cache.fetch(Digest::MD5.hexdigest("grover_url_png_#{@test_url}")) do
  Base64.encode64(Grover.new(@test_url).to_png)
end
```

#### Just viewing it (in the view)
```
<% if @image %>
  <h4>Test screenshot preview</h4>
  <%= image_tag "data:image/png;base64,#{@image.html_safe}" %>
<% end %>
```


That's essentially it! I hope this saves you some time and trouble if you're trying to screenshots in your app.

P.S  After writing this article, it was pointed out to me that cloudinary offers a URL to PNG converter. (Check out the docs for that here.)[https://cloudinary.com/documentation/url2png_website_screenshots_addon].