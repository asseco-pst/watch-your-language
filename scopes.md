# Scopes

```javascript
function() {
  console.log(foo); // undefined instead of "ReferenceError: foo is not defined"
  var foo = 'bar';
}
```