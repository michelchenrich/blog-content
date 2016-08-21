# First blog post!

**This is the blog's first post**

This is some `code.snippet` that I want to show inline.
And below, we can check out some nice ruby code from this very project:

```ruby
get '/' do
  erb :index, locals: {user: session_status, css: Rouge::Themes::Github.render} do
    Redcarpet::Markdown.new(HTML, {
      autolink: true,
      tables: true,
      fenced_code_blocks: true,
      disable_indented_code_blocks: true,
      underline: true
    }).render(File.read("#{__dir__}/../content/first_post.md").force_encoding("utf-8"))
  end
end
```

First Header | Second Header
-------------|--------------
Row 1 Cell 1 | Row 1 Cell 2
Row 2 Cell 1 | Row 2 Cell 2

So, what do you think?