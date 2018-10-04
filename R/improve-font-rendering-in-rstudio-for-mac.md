# Improve font rendering in RStudio for Mac

I like to work with thinner fonts than most text editors use. In RStudio it's possible to change that setting using this little hack.

First of all, we need to open RStudio, right-click on the main text editor and select Inspect Element.

Then, scroll down until we find a `link` line like this one:

```html
<link type="text/css" rel="stylesheet" id="rstudio-acethemes-linkelement" 
      href="http://127.0.0.1:43322/rstudio/3AAAF56BE4B500705B77183473800771.cache.css">
```

Finally, we go to where that CSS file is and change some style. In my case, I need to go to `/Applications/RStudio.app/Contents/Resources/www/rstudio`. And there I just need to add this at the top:

```css
div {
  -webkit-font-smoothing: antialiased;
}
```

With that my RStudio goes from this:

![Before -webkit-font-smoothing: antialiased][1]

To this:

![After -webkit-font-smoothing: antialiased][2]

[1]: improve-font-rendering-in-rstudio-for-mac-1.png
[2]: improve-font-rendering-in-rstudio-for-mac-2.png
