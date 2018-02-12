# Check if there is a network connection available or not

Sometimes network connection can be lost after loading our page. It may be useful to know this in order to adjust some content or stop trying to fetch new data or whatever. We can get this information as follows:

```javascript
function networkStatus (e) {
    // Option 1, check e.type ('offline' or 'online')
    document.title = e.type;
    
    // Option 2, check navigator.onLine (true or false)
    if (navigator.onLine) {
        console.log('Back online!');
    }
}
	
window.addEventListener('online', networkStatus);		
window.addEventListener('offline', networkStatus);
```
