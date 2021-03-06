*command-t.txt* Command-T plug-in for Vim         *command-t*

CONTENTS                                        *command-t-contents*

 1. Introduction            |command-t-intro|
 2. Requirements            |command-t-requirements|
 3. Installation            |command-t-installation|
 3. Managing using Pathogen |command-t-pathogen|
 4. Trouble-shooting        |command-t-trouble-shooting|
 5. Usage                   |command-t-usage|
 6. Commands                |command-t-commands|
 7. Mappings                |command-t-mappings|
 8. Options                 |command-t-options|
 9. Tips                    |command-t-tips|
10. Authors                 |command-t-authors|
11. Development             |command-t-development|
12. Website                 |command-t-website|
13. Donations               |command-t-donations|
14. License                 |command-t-license|
15. History                 |command-t-history|


INTRODUCTION                                    *command-t-intro*

The Command-T plug-in provides an extremely fast, intuitive mechanism for
opening files and buffers with a minimal number of keystrokes. It's named
"Command-T" because it is inspired by the "Go to File" window bound to
Command-T in TextMate.

Files are selected by typing characters that appear in their paths, and are
ordered by an algorithm which knows that characters that appear in certain
locations (for example, immediately after a path separator) should be given
more weight.

To search efficiently, especially in large projects, you should adopt a
"path-centric" rather than a "filename-centric" mentality. That is you should
think more about where the desired file is found rather than what it is
called. This means narrowing your search down by including some characters
from the upper path components rather than just entering characters from the
filename itself.

Screencasts demonstrating the plug-in can be viewed at:

  https://wincent.com/products/command-t


REQUIREMENTS                                    *command-t-requirements*

The plug-in requires Vim compiled with Ruby support, a compatible Ruby
installation at the operating system level, and a C compiler to build
the Ruby extension.


1. Vim compiled with Ruby support ~

You can check for Ruby support by launching Vim with the --version switch:

  vim --version

If "+ruby" appears in the version information then your version of Vim has
Ruby support.

Another way to check is to simply try using the :ruby command from within Vim
itself:

  :ruby 1

If your Vim lacks support you'll see an error message like this:

  E319: Sorry, the command is not available in this version

The version of Vim distributed with OS X may not include Ruby support (for
exmaple, Snow Leopard, which was the current version of OS X when Command-T
was first released, did not support Ruby in the system Vim, but the current
version of OS X at the time of writing, Mavericks, does). All recent versions
of MacVim come with Ruby support; it is available from:

  http://github.com/b4winckler/macvim/downloads

For Windows users, the Vim 7.2 executable available from www.vim.org does
include Ruby support, and is recommended over version 7.3 (which links against
Ruby 1.9, but apparently has some bugs that need to be resolved).


2. Ruby ~

In addition to having Ruby support in Vim, your system itself must have a
compatible Ruby install. "Compatible" means the same version as Vim itself
links against. If you use a different version then Command-T is unlikely
to work (see TROUBLE-SHOOTING below).

On OS X Snow Leopard, Lion and Mountain Lion, the system comes with Ruby 1.8.7
and all recent versions of MacVim (the 7.2 snapshots and 7.3) are linked
against it.

On OS X Mavericks, the default system ruby is 2.0, but MacVim continues to
link against 1.8.7, which is also present on the system at:

  /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby

On Linux and similar platforms, the linked version of Ruby will depend on
your distribution. You can usually find this out by examining the
compilation and linking flags displayed by the |:version| command in Vim, and
by looking at the output of:

  :ruby puts RUBY_VERSION

A suitable Ruby environment for Windows can be installed using the Ruby
1.8.7-p299 RubyInstaller available at:

  http://rubyinstaller.org/downloads/archives

If using RubyInstaller be sure to download the installer executable, not the
7-zip archive. When installing mark the checkbox "Add Ruby executables to your
PATH" so that Vim can find them.


3. C compiler ~

Part of Command-T is implemented in C as a Ruby extension for speed, allowing
it to work responsively even on directory hierarchies containing enormous
numbers of files. As such, a C compiler is required in order to build the
extension and complete the installation.

On OS X, this can be obtained by installing the Xcode Tools from the App
Store.

On Windows, the RubyInstaller Development Kit can be used to conveniently
install the necessary tool chain:

  http://rubyinstaller.org/downloads/archives

At the time of writing, the appropriate development kit for use with Ruby
1.8.7 is DevKit-3.4.5r3-20091110.

To use the Development Kit extract the archive contents to your C:\Ruby
folder.


INSTALLATION                                    *command-t-installation*

You can install Command-T by obtaining the source files and building the C
extension. The recommended way to get the source is by using a plug-in
management system such as Pathogen (see |command-t-pathogen|).

Command-T is also distributed as a "vimball" which means that it can be
installed by opening it in Vim and then sourcing it:

  :e command-t.vba
  :so %

The files will be installed in your |'runtimepath'|. To check where this is
you can issue:

  :echo &rtp

The C extension must then be built, which can be done from the shell. If you
use a typical |'runtimepath'| then the files were installed inside ~/.vim and
you can build the extension with:

  cd ~/.vim/ruby/command-t
  ruby extconf.rb
  make

Note: If you are an RVM or rbenv user, you must perform the build using the
same version of Ruby that Vim itself is linked against. This will often be the
system Ruby, which can be selected before issuing the "make" command with one
of the following commands:

  rvm use system
  rbenv local system

Note: If you are on OS X Mavericks and compiling against MacVim, the default
system Ruby is 2.0 but MacVim still links against the older 1.8.7 Ruby that is
also bundled with the system; in this case the build command becomes:

  cd ~/.vim/ruby/command-t
  /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby extconf.rb
  make

Note: Make sure you compile targeting the same architecture Vim was built for.
For instance, MacVim binaries are built for i386, but sometimes GCC compiles
for x86_64. First you have to check the platform Vim was built for:

  vim --version
  ...
  Compilation: gcc ... -arch i386 ...
  ...

and make sure you use the correct ARCHFLAGS during compilation:

  export ARCHFLAGS="-arch i386"
  make

Note: If you are on Fedora 17+, you can install Command-T from the system
repository with:

  su -c 'yum install vim-command-t'

MANAGING USING PATHOGEN                         *command-t-pathogen*

Pathogen is a plugin that allows you to maintain plugin installations in
separate, isolated subdirectories under the "bundle" directory in your
|'runtimepath'|. The following examples assume that you already have
Pathogen installed and configured, and that you are installing into
~/.vim/bundle. For more information about Pathogen, see:

  http://www.vim.org/scripts/script.php?script_id=2332

If you manage your entire ~/.vim folder using Git then you can add the
Command-T repository as a submodule:

  cd ~/.vim
  git submodule add git://git.wincent.com/command-t.git bundle/command-t
  git submodule init

Or if you just wish to do a simple clone instead of using submodules:

  cd ~/.vim
  git clone git://git.wincent.com/command-t.git bundle/command-t

Once you have a local copy of the repository you can update it at any time
with:

  cd ~/.vim/bundle/command-t
  git pull

Or you can switch to a specific release with:

  cd ~/.vim/bundle/command-t
  git checkout 0.8b

After installing or updating you must build the extension:

  cd ~/.vim/bundle/command-t/ruby/command-t
  ruby extconf.rb
  make

While the Vimball installation automatically generates the help tags, under
Pathogen it is necessary to do so explicitly from inside Vim:

  :call pathogen#helptags()


TROUBLE-SHOOTING                                *command-t-trouble-shooting*

Most installation problems are caused by a mismatch between the version of
Ruby on the host operating system, and the version of Ruby that Vim itself
linked against at compile time. For example, if one is 32-bit and the other is
64-bit, or one is from the Ruby 1.9 series and the other is from the 1.8
series, then the plug-in is not likely to work.

As such, on OS X, I recommend using the standard Ruby that comes with the
system (currently 1.8.7) along with the latest version of MacVim (currently
version 7.3). If you wish to use custom builds of Ruby or of MacVim (not
recommmended) then you will have to take extra care to ensure that the exact
same Ruby environment is in effect when building Ruby, Vim and the Command-T
extension.

For Windows, the following combination is known to work:

  - Vim 7.2 from http://www.vim.org/download.php:
      ftp://ftp.vim.org/pub/vim/pc/gvim72.exe
  - Ruby 1.8.7-p299 from http://rubyinstaller.org/downloads/archives:
      http://rubyforge.org/frs/download.php/71492/rubyinstaller-1.8.7-p299.exe
  - DevKit 3.4.5r3-20091110 from http://rubyinstaller.org/downloads/archives:
      http://rubyforge.org/frs/download.php/66888/devkit-3.4.5r3-20091110.7z

If a problem occurs the first thing you should do is inspect the output of:

  ruby extconf.rb
  make

During the installation, and:

  vim --version

And compare the compilation and linker flags that were passed to the
extension and to Vim itself when they were built. If the Ruby-related
flags or architecture flags are different then it is likely that something
has changed in your Ruby environment and the extension may not work until
you eliminate the discrepancy.


USAGE                                           *command-t-usage*

Bring up the Command-T file window by typing:

  <Leader>t

This mapping is set up automatically for you, provided you do not already have
a mapping for <Leader>t or |:CommandT|. You can also bring up the file window
by issuing the command:

  :CommandT

A prompt will appear at the bottom of the screen along with a file window
showing all of the files in the current directory (as returned by the
|:pwd| command).

For the most efficient file navigation within a project it's recommended that
you |:cd| into the root directory of your project when starting to work on it.
If you wish to open a file from outside of the project folder you can pass in
an optional path argument (relative or absolute) to |:CommandT|:

  :CommandT ../path/to/other/files

Type letters in the prompt to narrow down the selection, showing only the
files whose paths contain those letters in the specified order. Letters do not
need to appear consecutively in a path in order for it to be classified as a
match.

Once the desired file has been selected it can be opened by pressing <CR>.
(By default files are opened in the current window, but there are other
mappings that you can use to open in a vertical or horizontal split, or in
a new tab.) Note that if you have |'nohidden'| set and there are unsaved
changes in the current window when you press <CR> then opening in the current
window would fail; in this case Command-T will open the file in a new split.

The following mappings are active when the prompt has focus:

    <BS>        delete the character to the left of the cursor
    <Del>       delete the character at the cursor
    <Left>      move the cursor one character to the left
    <C-h>       move the cursor one character to the left
    <Right>     move the cursor one character to the right
    <C-l>       move the cursor one character to the right
    <C-a>       move the cursor to the start (left)
    <C-e>       move the cursor to the end (right)
    <C-u>       clear the contents of the prompt
    <Tab>       change focus to the file listing

The following mappings are active when the file listing has focus:

    <Tab>       change focus to the prompt

The following mappings are active when either the prompt or the file listing
has focus:

    <CR>        open the selected file
    <C-CR>      open the selected file in a new split window
    <C-s>       open the selected file in a new split window
    <C-v>       open the selected file in a new vertical split window
    <C-t>       open the selected file in a new tab
    <C-j>       select next file in the file listing
    <C-n>       select next file in the file listing
    <Down>      select next file in the file listing
    <C-k>       select previous file in the file listing
    <C-p>       select previous file in the file listing
    <Up>        select previous file in the file listing
    <C-f>       flush the cache (see |:CommandTFlush| for details)
    <C-q>       place the current matches in the quickfix window
    <C-c>       cancel (dismisses file listing)

The following is also available on terminals which support it:

    <Esc>       cancel (dismisses file listing)

Note that the default mappings can be overriden by setting options in your
~/.vimrc file (see the OPTIONS section for a full list of available options).

In addition, when the file listing has focus, typing a character will cause
the selection to jump to the first path which begins with that character.
Typing multiple characters consecutively can be used to distinguish between
paths which begin with the same prefix.


COMMANDS                                        *command-t-commands*

                                                *:CommandT*
|:CommandT|     Brings up the Command-T file window, starting in the
                current working directory as returned by the|:pwd|
                command.

                                                *:CommandTBuffer*
|:CommandTBuffer|Brings up the Command-T buffer window.
                This works exactly like the standard file window,
                except that the selection is limited to files that
                you already have open in buffers.

                                                *:CommandTJumps*
|:CommandTJump| Brings up the Command-T jumplist window.
                This works exactly like the standard file window,
                except that the selection is limited to files that
                you already have in the jumplist. Note that jumps
                can persist across Vim sessions (see Vim's |jumplist|
                documentation for more info).

                                                *:CommandTTag*
|:CommandTTag| Brings up the Command-T window tags window, which can
                be used to select from the tags, if any, returned by
                Vim's |taglist()| function. See Vim's |tag| documentation
                for general info on tags.

                                                *:CommandTFlush*
|:CommandTFlush|Instructs the plug-in to flush its path cache, causing
                the directory to be rescanned for new or deleted paths
                the next time the file window is shown (pressing <C-f> when
                a match listing is visible flushes the cache immediately; this
                mapping is configurable via the |g:CommandTRefreshMap|
                setting). In addition, all configuration settings are
                re-evaluated, causing any changes made to settings via the
                |:let| command to be picked up.


MAPPINGS                                        *command-t-mappings*

By default Command-T comes with only two mappings:

  <Leader>t     bring up the Command-T file window
  <Leader>b     bring up the Command-T buffer window

However, Command-T won't overwrite a pre-existing mapping so if you prefer
to define different mappings use lines like these in your ~/.vimrc:

  nnoremap <silent> <Leader>t :CommandT<CR>
  nnoremap <silent> <Leader>b :CommandTBuffer<CR>

Replacing "<Leader>t" or "<Leader>b" with your mapping of choice.

Note that in the case of MacVim you actually can map to Command-T (written
as <D-t> in Vim) in your ~/.gvimrc file if you first unmap the existing menu
binding of Command-T to "New Tab":

  if has("gui_macvim")
    macmenu &File.New\ Tab key=<nop>
    map <D-t> :CommandT<CR>
  endif

When the Command-T window is active a number of other additional mappings
become available for doing things like moving between and selecting matches.
These are fully described above in the USAGE section, and settings for
overriding the mappings are listed below under OPTIONS.


OPTIONS                                         *command-t-options*

A number of options may be set in your ~/.vimrc to influence the behaviour of
the plug-in. To set an option, you include a line like this in your ~/.vimrc:

    let g:CommandTMaxFiles=20000

To have Command-T pick up new settings immediately (that is, without having
to restart Vim) you can issue the |:CommandTFlush| command after making
changes via |:let|.

Following is a list of all available options:

                                               *g:CommandTMaxFiles*
  |g:CommandTMaxFiles|                           number (default 30000)

      The maximum number of files that will be considered when scanning the
      current directory. Upon reaching this number scanning stops. This
      limit applies only to file listings and is ignored for buffer
      listings.

                                               *g:CommandTMaxDepth*
  |g:CommandTMaxDepth|                           number (default 15)

      The maximum depth (levels of recursion) to be explored when scanning the
      current directory. Any directories at levels beyond this depth will be
      skipped.

                                               *g:CommandTMaxCachedDirectories*
  |g:CommandTMaxCachedDirectories|               number (default 1)

      The maximum number of directories whose contents should be cached when
      recursively scanning. With the default value of 1, each time you change
      directories the cache will be emptied and Command-T will have to
      rescan. Higher values will make Command-T hold more directories in the
      cache, bringing performance at the cost of memory usage. If set to 0,
      there is no limit on the number of cached directories.

                                               *g:CommandTMaxHeight*
  |g:CommandTMaxHeight|                          number (default: 0)

      The maximum height in lines the match window is allowed to expand to.
      If set to 0, the window will occupy as much of the available space as
      needed to show matching entries.

                                               *g:CommandTInputDebounce*
  |g:CommandTInputDebounce|                      number (default: 50)

      The number of milliseconds to wait before updating the match listing
      following a key-press. This can be used to avoid wasteful recomputation
      when making a rapid series of key-presses in a directory with many tens
      (or hundreds) of thousands of files.

                                               *g:CommandTFileScanner*
  |g:CommandTFileScanner|                        string (default: 'ruby')

      The underlying scanner implementation that should be used to explore the
      filesystem. Possible values are:

      - "ruby": uses built-in Ruby and should work everywhere, albeit slowly
        on large (many tens of thousands of files) hierarchies.

      - "find": uses the command-line tool of the same name, which can be much
        faster on large projects because it is written in pure C, but may not
        work on systems without the tool or with an incompatible version of
        the tool.

      - "watchman": uses Watchman (https://github.com/facebook/watchman) if
        available; otherwise falls back to "find". Note that this scanner is
        intended for use with very large hierarchies (hundreds of thousands of
        files) and so the task of deciding which files should be included is
        entirely delegated to Watchman; this means that settings which
        Command-T would usually consult, such as 'wildignore' and
        |g:CommandTScanDotDirectories| are ignored.

                                               *g:CommandTMinHeight*
  |g:CommandTMinHeight|                          number (default: 0)

      The minimum height in lines the match window is allowed to shrink to.
      If set to 0, will default to a single line. If set above the max height,
      will default to |g:CommandTMaxHeight|.

                                               *g:CommandTAlwaysShowDotFiles*
  |g:CommandTAlwaysShowDotFiles|                 boolean (default: 0)

      When showing the file listing Command-T will by default show dot-files
      only if the entered search string contains a dot that could cause a
      dot-file to match. When set to a non-zero value, this setting instructs
      Command-T to always include matching dot-files in the match list
      regardless of whether the search string contains a dot. See also
      |g:CommandTNeverShowDotFiles|. Note that this setting only influences
      the file listing; the buffer listing treats dot-files like any other
      file.

                                               *g:CommandTNeverShowDotFiles*
  |g:CommandTNeverShowDotFiles|                  boolean (default: 0)

      In the file listing, Command-T will by default show dot-files if the
      entered search string contains a dot that could cause a dot-file to
      match. When set to a non-zero value, this setting instructs Command-T to
      never show dot-files under any circumstances. Note that it is
      contradictory to set both this setting and
      |g:CommandTAlwaysShowDotFiles| to true, and if you do so Vim will suffer
      from headaches, nervous twitches, and sudden mood swings. This setting
      has no effect in buffer listings, where dot files are treated like any
      other file.

                                               *g:CommandTScanDotDirectories*
  |g:CommandTScanDotDirectories|                 boolean (default: 0)

      Normally Command-T will not recurse into "dot-directories" (directories
      whose names begin with a dot) while performing its initial scan. Set
      this setting to a non-zero value to override this behavior and recurse.
      Note that this setting is completely independent of the
      |g:CommandTAlwaysShowDotFiles| and |g:CommandTNeverShowDotFiles|
      settings; those apply only to the selection and display of matches
      (after scanning has been performed), whereas
      |g:CommandTScanDotDirectories| affects the behaviour at scan-time.

      Note also that even with this setting off you can still use Command-T to
      open files inside a "dot-directory" such as ~/.vim, but you have to use
      the |:cd| command to change into that directory first. For example:

        :cd ~/.vim
        :CommandT

                                               *g:CommandTMatchWindowAtTop*
  |g:CommandTMatchWindowAtTop|                   boolean (default: 0)

      When this setting is off (the default) the match window will appear at
      the bottom so as to keep it near to the prompt. Turning it on causes the
      match window to appear at the top instead. This may be preferable if you
      want the best match (usually the first one) to appear in a fixed location
      on the screen rather than moving as the number of matches changes during
      typing.

                                                *g:CommandTMatchWindowReverse*
  |g:CommandTMatchWindowReverse|                  boolean (default: 0)

      When this setting is off (the default) the matches will appear from
      top to bottom with the topmost being selected. Turning it on causes the
      matches to be reversed so the best match is at the bottom and the
      initially selected match is the bottom most. This may be preferable if
      you want the best match to appear in a fixed location on the screen
      but still be near the prompt at the bottom.

                                                *g:CommandTTagIncludeFilenames*
  |g:CommandTTagIncludeFilenames|                 boolean (default: 0)

      When this setting is off (the default) the matches in the |:CommandTTag|
      listing do not include filenames.

                                                *g:CommandTHighlightColor*
  |g:CommandTHighlightColor|                      string (default: 'PmenuSel')

      Specifies the highlight color that will be used to show the currently
      selected item in the match listing window.

                                                *g:CommandTWildIgnore*
  |g:CommandTWildIgnore|                          string (default: none)

      Optionally override Vim's global |'wildignore'| setting during Command-T
      searches. If you wish to supplement rather than replace the global
      setting, you can use a syntax like:

        let g:CommandTWildIgnore=&wildignore . ",**/bower_components/*"

      See also |command-t-wildignore|.

As well as the basic options listed above, there are a number of settings that
can be used to override the default key mappings used by Command-T. For
example, to set <C-x> as the mapping for cancelling (dismissing) the Command-T
window, you would add the following to your ~/.vimrc:

  let g:CommandTCancelMap='<C-x>'

Multiple, alternative mappings may be specified using list syntax:

  let g:CommandTCancelMap=['<C-x>', '<C-c>']

Following is a list of all map settings and their defaults:

                              Setting   Default mapping(s)

                                      *g:CommandTBackspaceMap*
              |g:CommandTBackspaceMap|  <BS>

                                      *g:CommandTDeleteMap*
                 |g:CommandTDeleteMap|  <Del>

                                      *g:CommandTAcceptSelectionMap*
        |g:CommandTAcceptSelectionMap|  <CR>

                                      *g:CommandTAcceptSelectionSplitMap*
   |g:CommandTAcceptSelectionSplitMap|  <C-CR>
                                      <C-s>

                                      *g:CommandTAcceptSelectionTabMap*
     |g:CommandTAcceptSelectionTabMap|  <C-t>

                                      *g:CommandTAcceptSelectionVSplitMap*
  |g:CommandTAcceptSelectionVSplitMap|  <C-v>

                                      *g:CommandTToggleFocusMap*
            |g:CommandTToggleFocusMap|  <Tab>

                                      *g:CommandTCancelMap*
                 |g:CommandTCancelMap|  <C-c>
                                      <Esc> (not on all terminals)

                                      *g:CommandTSelectNextMap*
             |g:CommandTSelectNextMap|  <C-n>
                                      <C-j>
                                      <Down>

                                      *g:CommandTSelectPrevMap*
             |g:CommandTSelectPrevMap|  <C-p>
                                      <C-k>
                                      <Up>

                                      *g:CommandTClearMap*
                  |g:CommandTClearMap|  <C-u>

                                      *g:CommandTRefreshMap*
                |g:CommandTRefreshMap|  <C-f>

                                      *g:CommandTQuickfixMap*
               |g:CommandTQuickfixMap|  <C-q>

                                      *g:CommandTCursorLeftMap*
             |g:CommandTCursorLeftMap|  <Left>
                                      <C-h>

                                      *g:CommandTCursorRightMap*
            |g:CommandTCursorRightMap|  <Right>
                                      <C-l>

                                      *g:CommandTCursorEndMap*
              |g:CommandTCursorEndMap|  <C-e>

                                      *g:CommandTCursorStartMap*
            |g:CommandTCursorStartMap|  <C-a>

In addition to the options provided by Command-T itself, some of Vim's own
settings can be used to control behavior:

                                               *command-t-wildignore*
  |'wildignore'|                                 string (default: '')

      Vim's |'wildignore'| setting is used to determine which files should be
      excluded from listings. This is a comma-separated list of glob patterns.
      It defaults to the empty string, but common settings include "*.o,*.obj"
      (to exclude object files) or ".git,.svn" (to exclude SCM metadata
      directories). For example:

        :set wildignore+=*.o,*.obj,.git

      A pattern such as "vendor/rails/**" would exclude all files and
      subdirectories inside the "vendor/rails" directory (relative to
      directory Command-T starts in).

      See the |'wildignore'| documentation for more information.

      If you want to influence Command-T's file exclusion behavior without
      changing your global |'wildignore'| setting, you can use the
      |g:CommandTWildIgnore| setting to apply an override that takes effect
      only during Command-T searches.


TIPS                                            *command-t-tips*

Working with very large repositories ~

One of the primary motivations for writing Command-T was to get fast, robust
high-quality matches even on large hierarchies. The larger the hierarchy, the
more important having good file navigation becomes. This is why Command-T's
performance-critical sections are written in C. This requires a compilation
step and makes Command-T harder to install than similar plug-ins which are
written in pure Vimscript, and can be a disincentive against use. This is a
conscious trade-off; the goal isn't to have as many users as possible, but
rather to provide the best performance at the highest quality.

The speed of the core is high enough that Command-T can afford to burn a bunch
of extra cycles -- using its recursive matching algorithm -- looking for a
higher-quality, more intuitive ranking of search results. Again, the larger
the hierarchy, the more important the quality of result ranking becomes.

Nevertheless, for extremely large hierarchies (of the order of 500,000 files)
some tuning is required in order to get useful and usable performance levels.
Here are some useful example settings:

    let g:CommandTMaxHeight = 30

You want the match listing window to be large enough that you can get useful
feedback about how your search query is going; in large hierarchies there may
be many, many matches for a given query. At the same time, you don't want Vim
wasting valuable cycles repainting a large portion of the screen area,
especially on a large display. Setting the limit to 30 or similar is a
reasonable compromise.

    let g:CommandTMaxFiles = 500000

The default limit of 30,000 files prevents Command-T from "seeing" many of the
files in a large directory hierarchy so you need to increase this limit.

    let g:CommandTInputDebounce = 200

Wait for 200ms of keyboard inactivity before computing search results. For
example, if you are enter "foobar" quickly (ie. within 1 second), there is
little sense in fetching the results for "f", "fo", "foo", "foob", "fooba" and
finally "foobar". Instead, we can just fetch the results for "foobar". This
setting trades off some immediate responsiveness at the micro level for a
better search experience overall.

    let g:CommandTFileScanner = 'watchman'

On a large hierarchy with of the order of 500,000 files, scanning a directory
tree with a tool like the `find` executable may take literally minutes with a
cold cache. Once the cache is warm, the same `find` run may take only a second
or two. Command-T provides a "find" scanner to leverage this performance, but
there is still massive overhead in passing the results through Vim internal
functions that apply 'wildignore' settings and such, so for truly immense
repos the "watchman" scanner is the tool of choice.

This scanner delegates the task of finding files to Facebook's `watchman` tool
(https://github.com/facebook/watchman), which can return results for a 500,000
file hierarchy within about a second.

Note that Watchman has a range of configuration options that can be applied by
files such as `/etc/watchman.json` or per-direcory `.watchmanconfig` files and
which may affect how Command-T works. For example, if your configuration has a
`root_restrict_files` setting that makes Watchman only work with roots that
look like Git or Mercurial repos, then Command-T will fall back to using the
"find" scanner any time you invoke it on a non-repo directory. For
simplicity's sake, it is probably a good idea to use Vim and Command-T
anchored at the root level of your repository in any case.

    let g:CommandTMaxCachedDirectories = 10

Command-T will internally cache up to 10 different directories, so even if you
|cd| repeatedly, it should only need to scan each directory once.

It's advisable to keep a long-running Vim instance in place and let it cache
the directory listings rather than repeatedly closing and re-opening Vim in
order to edit every file. On those occasions when you do need to flush the
cache (ie. with |CommandTFlush| or <C-f> in the match listing window), use of
the Watchman scanner should make the delay tolerable.

As noted in the introduction, Command-T works best when you adopt a
"path-centric" mentality. This is especially true on very large hierarchies.
For example, if you're looking for a file at:

  lib/third-party/adapters/restful-services/foobar/foobar-manager.js

you'll be able to narrow your search results down more narrowly if you search
with a query like "librestfoofooman" than "foobar-manager.js". This evidently
requires that you know where the file you're wanting to open exists, but
again, this is a concious design decision: Command-T is made to enable people
who know what they want to open and where it is to open it as quickly as
possible; other tools such as NERDTree exist for visually exploring an unknown
hierarchy.

Finally, it is important to be on a relatively recent version of Command-T to
fully benefit from the available performance enhancements:

- version 1.7 (February 2014) added the |g:CommandTInputDebounce| and
  |g:CommandTFileScanner| settings, along with support for the Watchman file
  scanner
- version 1.6 (December 2013) added parallelized search
- version 1.5 (September 2013) added memoization to the matching algorithm,
  improving general performance on large hierarchies, but delivering
  spectacular gains on hierarchies with "pathological" characteristics that
  lead the algorithm to exhibit degenerate behavior

AUTHORS                                         *command-t-authors*

Command-T is written and maintained by Wincent Colaiuta <win@wincent.com>.
Other contributors that have submitted patches include (in alphabetical
order):

  Andy Waite              Mike Lundy              Shlomi Fish
  Anthony Panozzo         Nadav Samet             Steven Moazami
  Daniel Hahler           Nate Kane               Sung Pae
  Felix Tjandrawibawa     Nicholas Alpi           Thomas Pelletier
  Gary Bernhardt          Noon Silk               Victor Hugo Borja
  Ivan Ukhov              Paul Jolly              V??t Ondruch
  Jeff Kreeftmeijer       Pavel Sergeev           Woody Peterson
  Lucas de Vries          Rainux Luo              Yan Pritzker
  Marcus Brito            Scott Bronson           Yiding Jia
  Marian Schubert         Seth Fowler             Zak Johnson
  Matthew Todd

As this was the first Vim plug-in I had ever written I was heavily influenced
by the design of the LustyExplorer plug-in by Stephen Bach, which I understand
is one of the largest Ruby-based Vim plug-ins to date.

While the Command-T codebase doesn't contain any code directly copied from
LustyExplorer, I did use it as a reference for answers to basic questions (like
"How do you do 'X' in a Ruby-based Vim plug-in?"), and also copied some basic
architectural decisions (like the division of the code into Prompt, Settings
and MatchWindow classes).

LustyExplorer is available from:

  http://www.vim.org/scripts/script.php?script_id=1890


DEVELOPMENT                                     *command-t-development*

Development in progress can be inspected via the project's Git web-based
repository browser at:

  https://wincent.com/repos/command-t

the clone URL for which is:

  git://git.wincent.com/command-t.git

Mirrors exist on GitHub and Gitorious; these are automatically updated once
per hour from the authoritative repository:

  https://github.com/wincent/command-t
  https://gitorious.org/command-t/command-t

Patches are welcome via the usual mechanisms (pull requests, email, posting to
the project issue tracker etc).

As many users choose to track Command-T using Pathogen, which often means
running a version later than the last official release, the intention is that
the "master" branch should be kept in a stable and reliable state as much as
possible.

Riskier changes are first cooked on the "next" branch for a period before
being merged into master. You can track this branch if you're feeling wild and
experimental, but note that the "next" branch may periodically be rewound
(force-updated) to keep it in sync with the "master" branch after each
official release.


WEBSITE                                         *command-t-website*

The official website for Command-T is:

  https://wincent.com/products/command-t

The latest release will always be available from there.

A copy of each release is also available from the official Vim scripts site
at:

  http://www.vim.org/scripts/script.php?script_id=3025

Bug reports should be submitted to the issue tracker at:

  https://wincent.com/issues


DONATIONS                                       *command-t-donations*

Command-T itself is free software released under the terms of the BSD license.
If you would like to support further development you can make a donation via
PayPal to win@wincent.com:

  https://wincent.com/products/command-t/donations


LICENSE                                         *command-t-license*

Copyright 2010-2014 Wincent Colaiuta. All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice,
   this list of conditions and the following disclaimer.
2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDERS OR CONTRIBUTORS BE
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
POSSIBILITY OF SUCH DAMAGE.

HISTORY                                         *command-t-history*

1.7 (9 March 2014)

- added |g:CommandTInputDebounce|, which can be used to improve responsiveness
  in large file hierarchies (based on patch from Yiding Jia)
- added a potentially faster file scanner which uses the `find` executable
  (based on patch from Yiding Jia)
- added a file scanner that knows how to talk to Watchman
  (https://github.com/facebook/watchman)
- added |g:CommandTFileScanner|, which can be used to switch file scanners
- fix processor count detection on some platforms (patch from Pavel Sergeev)

1.6.1 (22 December 2013)

- defer processor count detection until runtime (makes it possible to sensibly
  build Command-T on one machine and use it on another)

1.6 (16 December 2013)

- on systems with POSIX threads (such as OS X and Linux), Command-T will use
  threads to compute match results in parallel, resulting in a large speed
  boost that is especially noticeable when navigating large projects

1.5.1 (23 September 2013)

- exclude large benchmark fixture file from source exports (patch from V??t
  Ondruch)

1.5 (18 September 2013)

- don't scan "pathological" filesystem structures (ie. circular or
  self-referential symlinks; patch from Marcus Brito)
- gracefully handle files starting with "+" (patch from Ivan Ukhov)
- switch default selection highlight color for better readability (suggestion
  from Andr?? Arko), but make it possible to configure via the
  |g:CommandTHighlightColor| setting
- added a mapping to take the current matches and put then in the quickfix
  window
- performance improvements, particularly noticeable with large file
  hierarchies
- added |g:CommandTWildIgnore| setting (patch from Paul Jolly)

1.4 (20 June 2012)

- added |:CommandTTag| command (patches from Noon Silk)
- turn off |'colorcolumn'| and |'relativenumber'| in the match window (patch
  from Jeff Kreeftmeijer)
- documentation update (patch from Nicholas Alpi)
- added |:CommandTMinHeight| option (patch from Nate Kane)
- highlight (by underlining) matched characters in the match listing (requires
  Vim to have been compiled with the +conceal feature, which is available in
  Vim 7.3 or later; patch from Steven Moazami)
- added the ability to flush the cache while the match window is open using
  <C-f>

1.3.1 (18 December 2011)

- fix jumplist navigation under Ruby 1.9.x (patch from Woody Peterson)

1.3 (27 November 2011)

- added the option to maintain multiple caches when changing among
  directories; see the accompanying |g:CommandTMaxCachedDirectories| setting
- added the ability to navigate using the Vim jumplist (patch from Marian
  Schubert)

1.2.1 (30 April 2011)

- Remove duplicate copy of the documentation that was causing "Duplicate tag"
  errors
- Mitigate issue with distracting blinking cursor in non-GUI versions of Vim
  (patch from Steven Moazami)

1.2 (30 April 2011)

- added |g:CommandTMatchWindowReverse| option, to reverse the order of items
  in the match listing (patch from Steven Moazami)

1.1b2 (26 March 2011)

- fix a glitch in the release process; the plugin itself is unchanged since
  1.1b

1.1b (26 March 2011)

- add |:CommandTBuffer| command for quickly selecting among open buffers

1.0.1 (5 January 2011)

- work around bug when mapping |:CommandTFlush|, wherein the default mapping
  for |:CommandT| would not be set up
- clean up when leaving the Command-T buffer via unexpected means (such as
  with <C-W k> or similar)

1.0 (26 November 2010)

- make relative path simplification work on Windows

1.0b (5 November 2010)

- work around platform-specific Vim 7.3 bug seen by some users (wherein
  Vim always falsely reports to Ruby that the buffer numbers is 0)
- re-use the buffer that is used to show the match listing, rather than
  throwing it away and recreating it each time Command-T is shown; this
  stops the buffer numbers from creeping up needlessly

0.9 (8 October 2010)

- use relative paths when opening files inside the current working directory
  in order to keep buffer listings as brief as possible (patch from Matthew
  Todd)

0.8.1 (14 September 2010)

- fix mapping issues for users who have set |'notimeout'| (patch from Sung
  Pae)

0.8 (19 August 2010)

- overrides for the default mappings can now be lists of strings, allowing
  multiple mappings to be defined for any given action
- <Leader>t mapping only set up if no other map for |:CommandT| exists
  (patch from Scott Bronson)
- prevent folds from appearing in the match listing
- tweaks to avoid the likelihood of "Not enough room" errors when trying to
  open files
- watch out for "nil" windows when restoring window dimensions
- optimizations (avoid some repeated downcasing)
- move all Ruby files under the "command-t" subdirectory and avoid polluting
  the "Vim" module namespace

0.8b (11 July 2010)

- large overhaul of the scoring algorithm to make the ordering of returned
  results more intuitive; given the scope of the changes and room for
  optimization of the new algorithm, this release is labelled as "beta"

0.7 (10 June 2010)

- handle more |'wildignore'| patterns by delegating to Vim's own |expand()|
  function; with this change it is now viable to exclude patterns such as
  'vendor/rails/**' in addition to filename-only patterns like '*.o' and
  '.git' (patch from Mike Lundy)
- always sort results alphabetically for empty search strings; this eliminates
  filesystem-specific variations (patch from Mike Lundy)

0.6 (28 April 2010)

- |:CommandT| now accepts an optional parameter to specify the starting
  directory, temporarily overriding the usual default of Vim's |:pwd|
- fix truncated paths when operating from root directory

0.5.1 (11 April 2010)

- fix for Ruby 1.9 compatibility regression introduced in 0.5
- documentation enhancements, specifically targetted at Windows users

0.5 (3 April 2010)

- |:CommandTFlush| now re-evaluates settings, allowing changes made via |let|
  to be picked up without having to restart Vim
- fix premature abort when scanning very deep directory hierarchies
- remove broken |<Esc>| key mapping on vt100 and xterm terminals
- provide settings for overriding default mappings
- minor performance optimization

0.4 (27 March 2010)

- add |g:CommandTMatchWindowAtTop| setting (patch from Zak Johnson)
- documentation fixes and enhancements
- internal refactoring and simplification

0.3 (24 March 2010)

- add |g:CommandTMaxHeight| setting for controlling the maximum height of the
  match window (patch from Lucas de Vries)
- fix bug where |'list'| setting might be inappropriately set after dismissing
  Command-T
- compatibility fix for different behaviour of "autoload" under Ruby 1.9.1
- avoid "highlight group not found" warning when run under a version of Vim
  that does not have syntax highlighting support
- open in split when opening normally would fail due to |'hidden'| and
  |'modified'| values

0.2 (23 March 2010)

- compatibility fixes for compilation under Ruby 1.9 series
- compatibility fixes for compilation under Ruby 1.8.5
- compatibility fixes for Windows and other non-UNIX platforms
- suppress "mapping already exists" message if <Leader>t mapping is already
  defined when plug-in is loaded
- exclude paths based on |'wildignore'| setting rather than a hardcoded
  regular expression

0.1 (22 March 2010)

- initial public release

------------------------------------------------------------------------------
vim:tw=78:ft=help:
