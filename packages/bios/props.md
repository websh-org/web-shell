# bios: props

## Synopsis
```js
register("my-type",{
  extends: "props",
  props: {
    myProp: {
      public: false,
      writable: false,
      arg: false,
      set(value) { return value },
      get(value) { return value },
      changed(newValue, oldValue) {}
    }
  }
})
```
### Property definition fields

#### public
If set to `true`, this property will be readable on the component's public interface.

> **Inheritance:**
> Overrides the parent value.

#### writable
If both `public` and `writable` are set to `true`, this property will be writable on the component's public interface.

> **Inheritance:**
> Overrides the parent value.

#### arg
If set to `true`, this property will be copied from the argument supplied to `create`.

> **Inheritance:**
> Overrides the parent value.

#### set(value)
Must return the value that will be set as the internal value.
> **Inheritance:**
> Composed with the parent fanction.

#### get(value)
Must return the value that will be set as the internal value.
> **Inheritance:**
> Composed with the parent fanction.

#### changed(newValue,oldValue)
Must return the value that will be set as the internal value.
> **Inheritance:**
> Will run after the parent fanction.
