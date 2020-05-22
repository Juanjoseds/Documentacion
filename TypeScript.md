# TypeScript

Este documento tiene como objetivo almacenar código de interés para funciones específicas para TypeScript.

## Formas de recorrer un array

De forma común se usa ```foreach``` pero no se puede salir del bucle hasta que termina. Para ello existen dos formas alternativas:

- Array.every()

Para salir del bucle hay que retornar **false**.

```typescript
this.userCart.every((element, index) => {
    if(element.name === productName){
        return false;
    }else if (element.name !== productName && index === this.userCart.length-1){
        this.changeCart(productName);
    }
});
```

En dicho ejemplo, si el elemento pasado por parámetro y el recorrido en el array tienen el mismo contenido, *devuelve false para salir del bucle*.

- Array.some()

Similar al ejemplo anterior, pero hay que retornar **true** para salir del bucle.
