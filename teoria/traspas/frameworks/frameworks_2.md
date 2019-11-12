<!-- .slide: class="titulo" -->

# Tema 3: *Frameworks* JS en el cliente
## Parte 2: Funcionalidades

---

<!-- .slide: class="titulo" -->

## 3.4 
## Reactividad

---

Aunque **reactividad** es un término bastante amplio que tiene distintos significados en distintos contextos, en el contexto de los *frameworks* JS se suele entender como **que cambie la vista automáticamente cuando cambia el estado** del componente 

---

Se suelen distinguir dos tipos de reactividad:

- **Tipo *pull***: hay que llamar a un método para actualizar el estado, que a su vez "repinta" la vista. Por ejemplo: React, Angular (este último llama al método automáticamente por ejemplo en manejadores de evento)
- **Tipo *push***: automáticamente al cambiar los datos se repinta la vista. Ejemplos: Vue, Knockout, MobX

---

## Reactividad tipo "pull" 

```javascript
var update, state

//función a la que hay que pasar qué hacer cuando cambie el estado
var onStateChanged = function(update_fn)  {
  update = update_fn
}

//función que sirve para cambiar el estado
var setState = function(newState) {
  state = newState
  update()
}

onStateChanged(function () {
  var view = render(state)
})
```

---

[Ejemplo de código](https://jsbin.com/cozigix/2/edit?html,js,console,output) 

Para probarlo escribir en la consola `setState({contador:0}`. A partir de ahí se puede usar el botón (que llama a`setState`) 


---

## *Pull* en una jerarquía de componentes

- Normalmente una *app* es un árbol de componentes
- El *framework* repintará todos los nodos por debajo de los que han cambiado el estado
- Para aumentar la eficiencia, se suele permitir a un nodo indicar si debería o no ser repintado (el desarrollador debe escribir código para eso, en React implementar `shouldComponentUpdate()`)

![](imag/rerendering.png)

[Fuente](https://calendar.perfplanet.com/2013/diff/) <!-- .element: class="caption" -->

---

## Reactividad tipo *push* 

Vue convierte las propiedades del estado de un componente en *getters* y *setters*

-  Cuando se llama a un *getter* Vue aprovecha para "anotarse" que el componente depende de un dato
- Cuando se llama a un *setter* Vue dispara el repintado de la vista

![](imag/reactivity.png)<!-- .element: class="stretch" -->

---

[Ejemplo de código de reactividad push](https://jsbin.com/hubecop/5/edit?html,js,output) 

(el código es un poco retorcido, pero podéis probar cómo funciona clicando en el botón o escribiendo en la consola algo como `state.contador = <nuevo_valor>`)

---

## *Push* en una jerarquía de componentes

Como el *framework* lleva la pista de las dependencias, solo repinta los nodos cuyas dependencias han cambiado

---

<!-- .slide: class="titulo" -->

## 3.5
## *Rendering*

---


El *framework* debe permitirnos especificar cómo será la vista en función de los datos

```
vista = f(datos)
```

Aunque podríamos hacer muchas más diferencias hay *frameworks* que usan un lenguaje de *templates* (p.ej Angular) y otros usan directamente Javascript (p.ej React)

---

## Lenguajes de templates

- Problema: nos vemos limitados a la expresividad del lenguaje
- Ventaja: Los *framework* suelen incluir un *compilador* de *templates* que puede optimizar la detección de cambios para el repintado

```html
<div id="content">
  <p class="text">Lorem impsum</p>
  <p class="text">Lorem impsum</p>
  <p class="text">{{mensaje}}</p>
  <p class="text">Lorem impsum</p>
  <p class="text">Lorem impsum</p>
</div>
```

En el ejemplo, solo puede cambiar el tercer `<p>`, así que **para repintar el componente *como mucho* solo hace falta repintar ese `<p>`**


---

## Javascript para definir *templates*

- En React: `React.createElement(<tag>, <atributos>, <hijos>)`
- Luego veremos por qué no se usan directamente las funciones del DOM del mismo nombre. Por ahora podemos suponer que hacen lo mismo

```javascript
//esto es como la "f" de vista=f(datos) 
function HelloReact(props) {
  return React.createElement("div", null, 
            React.createElement("h1", null, props.saludo), 
            React.createElement("p", null, "Welcome to React"));
}
//maquinaria necesaria para pintar el componente React en la página
const element = <HelloReact saludo="Ehh!!" />;
ReactDOM.render(
  element,
  //suponiendo que exista un tag con id="mountNode"
  document.getElementById('mountNode')
);
```
[Probar ejemplo](https://jscomplete.com/playground/s357745)

---

El ejemplo anterior puede parecer tedioso (¡y lo es!), pero usar JS para la función de *render* tiene la **ventaja** de que **podemos usar toda la expresividad de JS**

```javascript
function Ejemplo(props) {
    var c = React.createElement
    const children = []
    for(var i=0; i<5; i++) {
        children.push(c('p', {class:'text'},
                       i == 2 ? props.mensaje : 'Lorem ipsum'))
    }
    return c('div', {id:'content'}, children)
}

const element = <Ejemplo mensaje="Hola amigos" />;
ReactDOM.render(
  element,
  document.getElementById('mountNode')
);
```

[Probar ejemplo](https://jscomplete.com/playground/s357763)

---

**Problema**: el lenguaje es tan expresivo que el *framework* no puede analizar qué estamos haciendo y no puede optimizar tanto el proceso de repintado.

Como hemos visto en los ejemplos, el desarrollador lo programa como si se repintara todo el árbol (*como los gráficos de un juego que se repintan enteros n veces por segundo*) 

¿Cómo reducir entonces el coste del repintado?

---

## DOM virtual

- Idea introducida por React
- La función `React.createElement` no genera nodos del DOM real, sino nodos en memoria (en un "árbol DOM virtual"), con un API más rápido
- Se hace una especie de *diff* entre el DOM virtual actual y el anterior. Según la [documentación de React](https://reactjs.org/docs/reconciliation.html) el coste es O(número de nodos)
- **Solo se repintan en el DOM real los nodos que cambian**. 



---


## Referencias

- 🎥 [dotJS 2016 - Evan You - Reactivity in Frontend JavaScript Frameworks](https://www.youtube.com/watch?v=r4pNEdIt_l4)
- 🎥 [Evan You on Vue.js: Seeking the Balance in Framework Design | JSConf.Asia 2019](https://www.youtube.com/watch?v=ANtSWq-zI0s)