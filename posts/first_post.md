# First blog post!

**This is the blog's first post**

This is some `code.snippet` that I want to show inline.
And below, we can check out some nice ruby code from this very project:

```ruby
get '/:page_name' do
  page = @content.page params[:page_name]
  erb(:index) { @presenter.present page }
end
```

And below we can see how tables are presented:

First Header | Second Header
-------------|--------------
Row 1 Cell 1 | Row 1 Cell 2 and some text
Row 2 Cell 1 | Row 2 Cell 2

> Block quote

> *Italic block quote*

> > Double block quote, **and in bold**

Now some lists:

1. These
2. are
3. ordered items
  * But these
  * are just plain old
  * bullet points

* More bullet points
* but at top level
  * and sub-level too
  * and code `snippets`

![Clean Architecture](/resource/clean-architecture.jpg)

So, what do you think?
