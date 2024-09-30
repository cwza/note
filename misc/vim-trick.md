# Vim Trick
## Shortcut
- Visual mode press U: Upper case, u: Lower case , Normal or Visual mode press ~: Toggle case
- V=: to select and indent
- Ctrl-E, Ctrl-Y: Scroll without move cursor
- Ctrl-a, Ctrl-x: increase decrease number
- f, F, t, T: find or to next character
    - ;: next ,: previous
- Ctrl-o, Ctrl-i: jump to previous
- u, Ctrl-R: undo, redo
- D: delete to the end, C: delete to the end and enter insert mode
- dd: delete the whole line, cc: delete the whole line and enter insert mode
- *: search current word
- .: repeat previous
- gUw: upper case a word, guw: lower case a word
- vi(: select all words inside(),  va(: select all words inside() include (),  i is for inside, a is for around
- %: match parenthesis, bracket
- qa[]q: to make a macro to register a,  @a to do the macro in register a, you can use . to repeat
- Ctrl-o: press this in insert mode will let you back to normal mode once and return insert mode.
- Ctrl-w: press this in insert mode will delete the previous word.
- S: go to auto indent position
- visual mode to select multiple lines, then :norm I[] to add something to the beginning, also you can use :norm A[] to add something to the end
- visual mode to select multiple lines, then :! sort | uniq will run sort and uniq on what you select
- insert mode press C-r then "(or other register) to paste in insert mode
- :%s/xx/yy/g: replace all xx with yy,  :%s/xx/yy/gc: ask for confirm, %s/xx/yy/gi: ignore case

## Surround
- ys[iw]" to surround a word by "
- yss" to surround a line by "
- S" to surround the selected part by " in visual mode
- ds" to remove surround by "
- cs"( to replace " with ( surround

## Quickfix List
- :vim xxx * to search xxx under all files
    + or you can use telescope to search and press C-q to send it to quick fix list, this way you can use tab or shift-tab to select files
- :copen to open quick fix list
- :cdo s/xxx/ooo/c update 
    + this will replace all xxx to ooo for each entry in this quickfix list
    + c is for confirm
    + use :cfdo to do only once for each file