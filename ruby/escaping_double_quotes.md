Escaping Double Quotes Inside Single Quotes
====

I have a bit of JavaScript that updates a hidden field with some JSON data.  I was getting a test failure because the form was getting submitted before the JavaScript could run.  The way to fix this in Capybara is to use something like `page.find` or `page.has_selector?`
which will retry for up to a second until the thing you are looking for exists.  So I had some test code like:

```ruby
assert(page.has_selector('input.hidden-field[value="{\"foo\":2}"]',
                         visible: false)
```

I was concerned that this may not be the correct number of slashes to escape the double quotes but it seems to work.  Presumably this means that the ruby compiler notices that we are in a single quoted string and this that the double quotes don't need escaping and therefore it can safely interpret the `\`s as literally characters which then allows capybary to correctly escape the double quotes.  I thought this was neat.  I was also glad to see that capybara supports this correctly.