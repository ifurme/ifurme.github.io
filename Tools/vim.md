c = y + i



### visual model

Add something in the beginning, for the example the word “HELLO”

#### manner 1

1. Click <kbd>v</kbd> to enter visual pattern

2. Type <kbd>j</kbd> or <kbd>k</kbd> to choose sone lines

3. Type <kbd>:</kbd> enter command line

4. Type normal IHELLO+<kbd>Enter</kbd>

   IHELLO : I to insert pattern; HELLO is plaintext.

#### manner 2

1. Type <kbd>Ctrl</kbd>+<kbd>v</kbd>to enter block visual pattern
2. Type <kbd>j</kbd> or <kbd>k</kbd> to choose sone lines
3. Type <kbd>I</kbd> to get ready for input something in the beginning
4. Type HELLO+<kbd>ESC</kbd>



# vimrc

syntax on

set number
set cursorline
set wrap

nmap J 3j
nmap K 3k

map Q :q!<CR>
map W :wq<CR>

set ignorecase
set hlsearch
set incsearch
exec "nohlsearch"

let mapleader=" "

noremap <LEADER>wq :wq<CR>

---

<span style="color:red">分屏</span>

映射普通模式下的sl为向右分屏并使光标进行右侧的分屏:

`map sl :set splitright<CR>:vsplit<CR>`

以此类推：

```
map sl :set splitright<CR>:vsplit<CR>
map sh :set nosplitright<CR>:vsplit<CR>
map sk :set nosplitbelow<CR>:split<CR>
map sj :set splitbelow<CR>:split<CR>
```

映射分屏之间的切换

映射 <kbd>Space</kbd>+<kbd>h</kbd>为将光标切换至左侧的分屏：

`map <LEADER>h <C-w>h`

以此类推：

```
let mapleader=" "
map <LEADER>h <C-w>h
map <LEADER>j <C-w>j
map <LEADER>k <C-w>k
map <LEADER>l <C-w>l
```



---



