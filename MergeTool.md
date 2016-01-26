The command `git mergetool` is a wrapper call that will do a diff in
the context of your git repository, then display the results in the
diff visualization tool of your choice. You can set the tool you wish
to use with:

```shell
# Utilize a particular tool just this once:
git mergetool --tool=myFavoriteTool
# Or modify your .gitconfig:
git config --global merge.tool myFavoriteTool
# May also want to prevent git from always asking if it is ok to fire up the tool:
git config --global mergetool.prompt 0
```

`git difftool` can optionally utilize a different tool (via
`diff.tool`) but will apparently default to the value set by
merge.tool.

The following tools were listed as recognized at time of writing:

### Open Source or Free

Four of these tools (5 if you count vim) are available in Debian-based
package repositories:

`sudo apt-get install diffuse kdiff3 meld tkdiff`

* [diffuse][diffuse]
  * `sudo apt-get install diffuse`
* [kdiff3][kdiff3] - KDE-based with broad support
  * [Screenshots][kdiff3screen]
  * `sudo apt-get install kdiff3` - 162MB dependencies
* [meld][meld]
  * `sudo apt-get install meld`
* [opendiff][opendiff] - Mac built-in tool?
* [tkdiff][tkdiff]
  * `sudo apt-get install tkdiff`
* [tortoisemerge][tortoisemerge]
  * Win only?
* [vimdiff][vimdiff] - diff mode for Vim
  * `gvimdiff` - GTK version?
  * `vimdiff2` / `gvimdiff2`- I assume this is a varient but did not find more
* [xxdiff][xxdiff]
  * [Screenshots][xxdiffscreen]
  * Pretty basic, very 1990's X-windows

### Commercial

* [bc3][bc3] - Beyond Compare
* [codecompare][codecompare]
* [deltawalker][deltawalker] - Mac only
* [diffmerge][diffmerge]
* [ecmerge][ecmerge]
* [p4merge][p4merge]

### Comment threads

* [What's the best visual merge tool for Git?][SObestdiff]

[bc3]: http://www.scootersoftware.com/features.php
[bc3screen]: http://www.scootersoftware.com/features.php?zz=gallery
[codecompare]: https://www.devart.com/codecompare/
[deltawalker]: http://www.deltawalker.com/
[diffmerge]: https://www.sourcegear.com/diffmerge/
[diffuse]: http://diffuse.sourceforge.net/
[ecmerge]: http://www.elliecomputing.com/en/Home/default.asp
[gvimdiff]: http://vimdoc.sourceforge.net/htmldoc/diff.html
[kdiff3]: http://kdiff3.sourceforge.net/
[kdiff3screen]: http://kdiff3.sourceforge.net/doc/screenshots.html
[meld]: http://meldmerge.org/
[opendiff]: https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/opendiff.1.html
[p4merge]: https://www.perforce.com/product/components/perforce-visual-merge-and-diff-tools
[tkdiff]: http://sourceforge.net/projects/tkdiff/
[tortoisemerge]: https://tortoisesvn.net/docs/nightly/TortoiseMerge_en/
[vimdiff]: http://vimdoc.sourceforge.net/htmldoc/diff.html
[xxdiff]: http://furius.ca/xxdiff/
[xxdiffscreen]: http://furius.ca/xxdiff/doc/screenshots/gallery/index.html
[SObestdiff]: https://stackoverflow.com/questions/137102/whats-the-best-visual-merge-tool-for-git
