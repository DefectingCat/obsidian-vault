https://vim.fandom.com/wiki/Search_and_replace_in_a_visual_selection#Visual_selection_basics

For example, put the cursor on this line:

```
music amuse fuse refuse
```

In normal mode, type `^wvee` to visually select "amuse fuse" (`^` goes to first nonblank character, `w` moves forward a word, `v` enters visual mode, `e` moves forward to end of next word). Then press Escape and enter the following command to change all "us" to "az" in the last-selected area within the current line:

```
:s/\%Vus/az/g
```

The result is:

```
music amaze faze refuse
```