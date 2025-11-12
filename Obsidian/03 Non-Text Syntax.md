---
tags:
  - syntax
  - obsidian
  - callouts
---
### Tags
to create a tag, enter using `#` , #syntax
you can also use tags property by putting `---` under the page title and choosing tags

to search via tag(s), use `tag: #tag_name` in search bar

to use nested tags use `/`, #obsidian/syntax

tags are case insensitive means , #syntax and #SYNTAX are same

tags cannot contain blanks, use `-` or `_`

### Attachments
Allowed attachments are Markdown files, Image, Audio, Video and PDF files

You can either:
- copy-paste the attachment
- drag -n-drop the attachment
- import to your vault(s)

to choose defaults locations for the new attachments in the vault :-
`settings => files & links => Default location for new attachments`

### Callouts

^e2c1fa

just add `![type-identifier]` at the start of the quote block, can change title there and can also omit the body of the callout to make the title only visible ^callouts

>[!info] Information
>'Information' can be changed into anything and [!info] tells what kind of call is this

>[!info] This only has title

>[!faq]- Can you make Foldable Callouts ?
>Yes, Just by adding `minus` after the type-identifier like this, [!info]-

>[!faq] Can you make nested Callouts ?
>>[!info] Yes just by increasing the number of `>` at the start
>>> [!tip] Number of `>` results in that position and layer in the Nested Callouts
>>> 
>>>>[!example] This has 4 `>` at the start

to customize callouts, use CSS snippets, Plugins or go down to [Customize Callouts](https://help.obsidian.md/Editing+and+formatting/Callouts#Customize+callouts)

##### Supported Callouts
>[!note]

>[!abstract] 
>This can also be achieved using `[!summary]` or `[!tldr]`

>[!info]

>[!todo]

>[!tip]

>[!success]
>This can also be achieved by `[!check]` or `[!done]`

>[!question]
>This can also be achieved by `[!help]` or `[!faq]`

>[!warning]
>This can also be achieved by `[!caution]` or `[!attention]`

>[!failure]
>This can also be achieved by `[!fail]` or `[!missing]`

>[!danger]
>This can also be achieved by `[!error]`

>[!bug]

>[!example]

>[!quote]
>This can also be achieved by `[!cite]`

### Multiple Cursors and Folding
folding can be done on indented lists and headings
can also create hotkeys for the folding

to fold and unfold all the heading and list, search of the same in `command palette`

to add multiple cursors, hold `Alt` key and click on the position where you want additional cursor
to select whole para while dragging, hold `Shift + Alt` and drag

### Properties
There are several ways to add property(s)
- `Ctrl + ;`
- `Add File Property` from Command Palette or More Actions Menu
- Type `---` in the beginning of the page, just below the title
after the above, you can type or choose name of a property, and its value(s)

there are several property types
- Text
- List
- Number
- Checkbox
- Date
- Date & Time
Values of the Property Name must be one of the above and you cannot have more than one `property_name`.Every name must be unique
The property type can be changed by right-clicking on the property name icon ^fd87b0

Text and links property type can contain URL within `[[Link]]` Syntax

The property can be searched using this way in the search tab - `Ctrl + Shift + F`:- 
- `[property_name]` or 
- `[property_name:property_value]` or 
- `[property_name:property_value1 AND/OR property_value2]`

You can change the view of property from `Settings -> Editor -> Properties in document` having the options of `Visible`, `Hidden` and `Source`

- `Ctrl + Shift + V` to paste without formatting

### Embedding
to embed a web page, just put this and it just work like normal HTML
```html
<iframe src="URL"></iframe>
```
<iframe src="https://www.wikipedia.org/"></iframe>

to embed a youtube video, just like embedding image `![](URL)`
![](https://youtu.be/P_Q6avJGoWI)

to embed a tweet/X, just like images `![](URL)`

==`!` used for embedding== and ==not using `!` is used for linking==
means `[[]]` for linking and `![[]]` or `![]()` for embedding
ways to embed different kinds of file ;-
- to embed a note `->` `![[internal link]]`
- to embed a heading or block `->` `[[internal link#^identifier]]`
- to embed and image 
	- `![[image.jpg]]` or 
	- `![[image.jpg|widthxheight]]` or
	- `![[image.jpg|only_width]]` or 
	- `![name.jpg](URL)` or
	- `![widthxheight](URL)` or
	- `![only_width](URL)` or
	- `![](URL)`
-  to embed any audio  `->` `![[file.ogg]]` or `![[file.mp3]]`
- to embed a pdf 
	- `![[file.pdf]]` or
	- `![[file.pdf#page=3]]` or
	- `![[file.pdf#height=400]]`
- to embed a list, first add a block identifier to the list and use that to link embed the block `->` `![[mynote#^identfier]]`
- to embed a search, add a query block
 ``
```write_only_query_here
embed OR search
```

### HTML Content
you can also add content using HTML language but Obsidian will [Sanitize](https://help.obsidian.md/Editing+and+formatting/HTML+content) the code for safety

#### Comments
`<!--- --->`
<!--- HTML Comment, you are seeing this means editor mode is ON  --->

#### Underline and Strikethrough
`<u> </u>` and `<s> </s>`
<u>Underline</u> <s>Strikethrough</s>

#### Span/Div
`<span style="font-family:cursive"> Content </span>`
<span style="font-family:cursive">The font of this Content is changed  </span>

#### All the other things you can do in HTML

### Linking
ways to create links while in editing mode :-
- Type `[[` and choose the file
- select text and type `[[`
- Command palette `->` Add Internal Link
linking to anything which is not markdown require to add extension at the end of file name as well

to link to headings, using `#` at the start of heading name after the link destination
- [[01 Basic Notes#Linking Notes]]
you can also add multiple `#` for sub-headings
- [[03 Non-Text Syntax#HTML Content#Span/Div]]

you can also link to block which is a para, quote block or even a list
you need to put the identifier, to create a human readable identifier, just add text after adding `^` after a space just like [[03 Non-Text Syntax#Callouts|Here]]
then use that identifier to link with a block after putting that identifier after the `#`
`[[03 Non-Text Syntax#^callouts]]` `->` [[03 Non-Text Syntax#^callouts]]
- even if you didn't created the human readable identifier, you can just type after `^` and it will suggest the block matching the input and choose the required and it will create a random identifier for it like this `->` [[03 Non-Text Syntax#^fd87b0]]

to change the display text, `[[link|display_text]]`

to preview the content of a link, just hold `Ctrl` and Hover over the link

#### Aliases
aliases are the other names you can give to a note. It is always in YAML format
Suppose there is a page called Programming Language, so
```YAML
---
aliases:
	-coding languages
	-computer languages
---
```

to link using alias, just type alias within `[[ ]]` `->` [[01 Basic Notes|Basics of Obsidian]]

## [[04 Plugins|Plugins]]

