# Tell whether the page is currently visible or not

In some cases it can be really handy to know if our page is visible on screen or not (maybe to pause something or to stop fetching new data or whatever). This can be accomplished as easily as this:

```javascript
document.addEventListener('visibilitychange', () => {
    switch(document.visibilityState) {
    case 'prerender':
        document.title = 'Tab pre-rendering';
        break;
    case 'hidden':
        document.title = 'Tab not visible';
        break;
    case 'visible':
        document.title = 'Tab visible';
        break;
    }
});
```
