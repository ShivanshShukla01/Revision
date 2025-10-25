### Core Plugins
<u>Note Composer</u> : Merge two notes or split two into one
<u>Random Note</u> : opens a random note from the vault
<u>Slash Commands</u> : perform commands inside the editior using `/`
<u>Unique Note Creator </u> : create a unique note using time coded title

#### Backlink
You can open Backlinks from the command palette as well

#### Bookmarks
You can bookmark any files, folders, graph, searches, headings and blocks
can reorder the bookmarks by dragging them
You can add bookmarks using command palette
To know more about [Bookmarks!](https://help.obsidian.md/Plugins/Bookmarks)

#### Canvas
Create a Canvas from command palette or canvas icon from ribbon
you can drag files into canvas from obsidian itself or any other application. 
>[!example]- 
>Markdown Files, Images, Audios, PDFs, or even unrecognized file types

Add cards by either dragging or double clicking on the canvas
to convert the text card into file, `Right Click -> Convert to file`
>[!info]-
>text-only cards don't appear in backlinks, for that, convert to file is required

You can add multiple types of cards, web pages from the right click menu
you can delete, edit or swap cards
to select cards :-
- Make a selection by dragging the mouse
- add or remove by holding `Shift` and clicking on the cards
- arrange cards by dragging them
- resize cards by dragging the edges
- to connect cards, hover on edge and put the filled circle to the another card
- to disconnect cards, hold the arrow and remove it
- right click on the arrow to "Go to target" and "Go to  source"
- double click on the line to add a label and then either press `Escape` or click somewhere on the canvas
- you can also change the color of the cards
- to create a group of cards, right click on the selected cards and select create group
	- double click on the group to rename the group
- to zoom the canvas
	- `Space` or `Ctrl` with mouse wheel
	- zoom in and zoom out buttons in the canvas
	- `Ctrl + 1` `->` Zoom to fit
	- `Ctrl + 2` `->` Zoom to selection
- [More tips on Canvas](https://obsidian.md/canvas#protips)

#### Daily Notes
opens a note based on today's date or create if it does not exist
to open
- `open today's daily note` from ribbon
- from command palette
- you can also set a hotkey
the default date format is `YYYY-MM-DD`
if you want, you can set new file location so that new notes created go under that folder by default under plugin options to change where Obsidian creates a new note

you can also create a template for the daily notes. Make a note `<Daily Template>` and make a format as you wish. This is just a sample
```
# {{date:YYYY-MM-DD}}

## Tasks

	-[] 
```
and save the note, after saving the note, choose this note as the template note in plugin option. Obsidian will use this template to create a new note everyday

if there is date property in any note, and daily notes plugin is activated, Obsidian will attempt to generate a link to daily note on specific day

#### Graph View
right click on the nodes to enter the right click menu
zoom in using mouse wheel or use `+` or `-` key
move the graph around by dragging it with mouse cursor and speed the movement speed by holding the `Shift` key

for accessing the graph settings, click on the cog icon in the upper-left corner of the graph view

you can filters on the basis of Search files, Tags, Attachments, Existing files only, Orphans

you can create groups of different color to distinguish
	`create a new group -> enter query to add notes -> choose colors`

- Display section controls how to visualize notes and links
	- Arrows to show the direction of each link
	- Text fade threshold controls the text transparency
	- node size control the size of circles
	- link thickness controls the line widht
	- animate start a time-lapse animation
- Forces control forces that act on each note
	- <u>center force</u> control how compact graph is
	- <u>repel force</u> control how much a node pushes other nodes away
	- <u>link force</u> control the pull on each link. If it is a rubber band, controls how tight or loose the band is
	- <u>link distance</u> control the length of the lines of each note

#### Note Composer
merge two nodes and split a note into two
merging nodes adds a note and removes the first one. it will also update all the links to reference the first one

notes can be merger into by following methods :- ^note-composer-keys
- `Enter`: add the source note at the end of destination note
- `Shift + Enter`: add the source note at the start of destination note
- `Ctrl + Enter`: Creates a new note with content of source note

to merge file :-
- right - click on the note you want to merge
- click `merge enter file with...`
- select the destination note
- click merge 
	- or you can also use command palette for the same

notes can be extracted by selecting the text and [[04 Plugins#^note-composer-keys|following methods]] are :- 

to extract text :-
- select text
- right-click
- click `extract current selection...` 
- select the note you want to extract into
	- or you can also use command palette for the same

to know about the [template file](https://help.obsidian.md/Plugins/Note+composer#Template+file) for note composer

#### Search
Each word is matched independently in the search. To search for the exact phrase, surround it with `""`
and to [search something](https://help.obsidian.md/Plugins/Search#Search+terms) which is within the quote, you can use `\`
- `meeting work` - return files that contain both
- `meeting OR work` - return files that contain either of them
- `meeting work OR meetup task`
- `meeting (work OR meetup) task` - to give priorities of each expression
- `meeting -work` - to exclude a word, add `-`, can also do this with multiple words
- `meeting -(work meetup)` - return files that contain meeting but not work AND meetup

search operators
- `file:` find text in filename
- `path:` find text in file path
- `content:` find text in file content
- `match-case:`case-sensitive match
- `ignore-case:` case-insensitive match
- `tag:` find tag in file
- `line:` find matches on same line
- `block:` find matches in the same line
- `section`: find matches in same section(text between two headings)
- `task:` find matches in task
	- `task-todo:` uncompleted task
	- `task-done:` completed task

- use `[property]` to return files with that property
- use `[property:value]` to return files with that property and value
	- can do `[property:value1 OR value2]`

#### Slash Commands
to run commands inside the editor, type `/` at beginning of line or after any black space

#### Templates
to insert template :
	in the ribbon - click insert template - choose template

Template Variables
- `{{title}}`
- `{{date}}` default format is YYYY-MM-DD
- `{{time}}` default format is HH:mm 
	- you can change the format of above two using `:`

