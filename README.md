gemoji
======

Emoji images and names. See the LICENSE for copyright information.


Installation
------------

Add `gemoji` to you Gemfile.

``` ruby
gem 'gemoji'
```

**Sync images**

Images can be copied to your public directory with `rake emoji` in your app. This is the recommended approach since the images will be available at a consistent location. This works best with cached formatted user content generated by tools like [html-pipeline](https://github.com/jch/html-pipeline).

``` ruby
# Rakefile
load 'tasks/emoji.rake'
```

```
$ rake emoji
```

**Assets Precompiling**

If you must, you can manually add all the images to your asset load path.

``` ruby
# config/application.rb
config.assets.paths << Emoji.images_path
```

Then have them compiled to public on deploy.

``` ruby
# config/application.rb
config.assets.precompile << "emoji/*.png"
```

**WARNING** Since there are a ton of images, just adding the path may slow down other lookups if you aren't using it. Compiling all the emojis on deploy will add overhead to your deploy if even the images haven't changed. Theres just so many more superfluous files to iterate over. Also, the urls will be fingerprinted which may not be ideal for referencing from cached content.


Example Rails Helper
--------------------

This would allow emojifying content such as: `it's raining :cats: and :dogs:!`

See the [Emoji cheat sheet](http://www.emoji-cheat-sheet.com) for more examples.

```ruby
module EmojiHelper
  def emojify(content)
    h(content).to_str.gsub(/:([a-z0-9\+\-_]+):/) do |match|
      if Emoji.names.include?($1)
        '<img alt="' + $1 + '" height="20" src="' + asset_path("emoji/#{$1}.png") + '" style="vertical-align:middle" width="20" />'
      else
        match
      end
    end.html_safe if content.present?
  end

  def reverse_emojify(content)
    Emoji.unicodes.each do |unicode|
      content.gsub!(unicode, ":#{Emoji.from_unicode(unicode)}:")
    end
    content
  end
end
```
