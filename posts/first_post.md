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
Row 1 Cell 1 | Row 1 Cell 2
Row 2 Cell 1 | Row 2 Cell 2

So, what do you think?
