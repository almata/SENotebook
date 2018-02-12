# Access battery information from a page

Although not common, in some specific scenarios it can be useful to know if the battery level is going too low. We can get this information this way:

```javascript
navigator.getBattery().then((battery) => {
    console.log(`${battery.level * 100}%`);
    
    battery.addEventListener('levelchange', () => {
        console.log(`${battery.level * 100}%`);					
    });
});
```

Sadly, at the time of this writing this only works in Chrome, Firefox and Opera.
