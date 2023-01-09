```
let mapleader=" "

""" Plugins  --------------------------------
Plug 'easymotion/vim-easymotion'
Plug 'preservim/nerdtree`
set surround
set multiple-cursors
set commentary
set argtextobj
set easymotion
set textobj-entire
set ReplaceWithRegister

""" Plugin settings -------------------------
""" let g:EasyMotion_override_acejump = 0
let g:argtextobj_pairs="[:],(:),<:>"

""" Common settings -------------------------
set scrolloff=5
set clipboard+=unnamed
set clipboard+=idea-put
set scrolloff=5
set number relativenumber
set incsearch
set hls

""" Idea specific settings ------------------
set ideajoin
set ideastatusicon=gray
set idearefactormode=keep

" Don't use Ex mode, use Q for formatting.
map Q gq

""" Mappings --------------------------------
map <leader><leader> <Plug>(easymotion-s)
""" map <leader><leader>w <Plug>(easymotion-w)
""" map <leader><leader>b <Plug>(easymotion-b)
nnoremap <leader>nh :nohl<CR>
xnoremap p "_dP
noremap gb viw
nnoremap gh :action QuickJavaDoc<cr>
nnoremap <leader>r :action RenameElement<CR>
nmap <C-n> <Plug>NextWholeOccurrence
xmap <C-n> <Plug>NextWholeOccurrence
nnoremap <C-j> :action GotoNextError<CR>
nnoremap <C-k> :action GotoPreviousError<CR>
""" Switch windows
nnoremap <leader>h <c-w>h
nnoremap <leader>l <c-w>l
nnoremap <leader>j <c-w>j
nnoremap <leader>k <c-w>k
""" nnoremap <leader>e :action ActivateProjectToolWindow<CR>
nnoremap <leader>e :NERDTreeToggle<CR>
nnoremap ;r :action FindInPath<CR>
nnoremap <C-p> :action GotoFile<CR>
""" Switch tabs
nmap <C-i> :action NextTab<CR>
nmap <C-o> :action PreviousTab<CR>
""" nmap gh :action ShowErrorDescription<CR>

"" -- Map IDE actions to IdeaVim -- https://jb.gg/abva4t
"" Map \r to the Reformat Code action
"map \r <Action>(ReformatCode)

"" Map <leader>d to start debug
"map <leader>d <Action>(Debug)

"" Map \b to toggle the breakpoint on the current line
"map \b <Action>(ToggleLineBreakpoint)


" Find more examples here: https://jb.gg/share-ideavimrc
```