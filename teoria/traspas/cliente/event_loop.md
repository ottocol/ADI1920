<!-- .slide: class="titulo" -->

# Tema 2: Desarrollo en el cliente con Javascript estándar 
## Parte 3: El *event loop* en Javascript

---

<!-- .slide: class="titulo" -->

# 1.
# Introducción


---


```javascript
setTimeout(function(){
  console.log("Esto debería aparecer primero");
}, 0);
console.log("Sorpresa!");
```
[https://jsbin.com/bibifan/edit?js,console](https://jsbin.com/bibifan/edit?js,console)

---


> Javascript utiliza un modelo asíncrono y no bloqueante, con un *loop* de eventos implementado con un único *thread* para sus interfaces de entrada/salida

[https://lemoncode.net/lemoncode-blog/2018/1/29/javascript-asincrono](https://lemoncode.net/lemoncode-blog/2018/1/29/javascript-asincrono) <!-- .element class="caption"-->


---

## El *runtime* de Javascript

Los motores JS como V8 (Node, Chrome,...)/Spidermonkey(Firefox) implementan lo "más básico": el *heap* y el *stack*

![](images_event_loop/heap_stack.gif) <!-- .element class="stretch"-->

[https://itnext.io/how-javascript-works-in-browser-and-node-ab7d0d09ac2f](https://itnext.io/how-javascript-works-in-browser-and-node-ab7d0d09ac2f) <!-- .element class="caption"-->

---

<!-- .slide: class="titulo" -->

# 2.
# El *loop* de eventos

---


![](images_event_loop/event_loop_steps.png) 
<!-- .element class="stretch"-->
[https://lemoncode.net/lemoncode-blog/2018/1/29/javascript-asincrono](https://lemoncode.net/lemoncode-blog/2018/1/29/javascript-asincrono) <!-- .element class="caption"-->


---

El *loop* de eventos no se procesa hasta que el *call stack* se queda vacío, lo que explica el ejemplo que vimos al principio

```javascript
setTimeout(function(){
  console.log("Esto debería aparecer primero");
}, 0);
console.log("Sorpresa!");
```
[Loupe, una herramienta para visualizar el *event loop*](http://latentflip.com/loupe/?code=c2V0VGltZW91dChmdW5jdGlvbiB0aW1lcigpewogIGNvbnNvbGUubG9nKCJFc3RvIGRlYmVy7WEgYXBhcmVjZXIgcHJpbWVybyIpOwp9LCAwKTsKY29uc29sZS5sb2coIlNvcnByZXNhISIpOw%3D%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

---

Os podéis imaginar el *loop* de eventos como "el bucle principal" del *runtime* (el navegador o node)

```javascript
while (true) {
    task = taskQueue.pop()
    execute(task)
}
```
[Further Adventures of the Event Loop - Erin Zimmer - JSConf EU 2018](https://www.youtube.com/watch?v=u1kqx6AenYw) <!-- .element class="caption" -->

- En la especificación se permite que haya más de una cola de tareas, por ejemplo Node usa una cola separada para los *timers*
- En navegadores, estamos ignorando otra cola de tareas que se ocupa del *rendering*

---

## Microtareas

- Típicamente asociadas a la resolución/rechazo de una promesa
- Se ejecutan inmediatamente después de la tarea actual, hasta que no terminan todas no se sigue con las "macro"tareas

```javascript
while (true) {
    queue = getNextQueue()
    task = queue.pop()
    execute(task)
    while(!microtaskQueue.empty()) {
        doMicrotask()
    }
}
```

---

![](images_event_loop/quiz.png) 
<!-- .element class="stretch"-->
[https://jsbin.com/lexujo/edit?js,console](https://jsbin.com/lexujo/edit?js,console) <!-- .element class="caption"-->


---

<!-- .slide: class="titulo" -->
# 3.
# Rendering

---


- Además de ejecutar los *scripts* y las microtareas, los navegadores deben repintar la página si hemos hecho algún cambio en el HTML/CSS. Esto es parte del *event loop*, razón por la que si tardamos demasiado en algún *script* el navegador no podrá repintar nada.
- El repintado se hace a intervalos regulares a 60fps

```javascript
while (true) {
    queue = getNextQueue()
    task = queue.pop()
    execute(task)
    while(!microtaskQueue.empty()) {
        doMicrotask()
    }
    if (isRepaintTime())
        repaint()
}
```

---

el *event loop* es la razón por lo que código como este, que intenta hacer una "animación" en la que el texto va aumentando de tamaño progresivamente no funcionará

```javascript
var titulo = document.getElementById('titulo')
for(i=1;i<=100;i++){
  titulo.style.fontSize = i + 'px'
}  
```
[https://jsbin.com/helaqic/edit?html,js,output](https://jsbin.com/helaqic/edit?html,js,output)

Pregunta: ¿Cómo lo arreglarías?

Notas: 

Posible solución:

```javascript
var titulo = document.getElementById('titulo')
function anim_frame(i) {
  titulo.style.fontSize = i + 'px'
  if (i<100)
    setTimeout(anim_frame, 100, i+1)  
}

setTimeout(anim_frame,100, 1)
```

---
`setInterval(callback, ms)` fija un temporizador cada x milisegundos que se ejecutará hasta que se pare con `clearInterval`

```javascript
var titulo = document.getElementById('titulo')
var timer
var tam = 1
function anim_frame() {
   if (tam<150) {
      tam = tam + 1
      titulo.style.fontSize = parseInt(tam) + 'px'
   }
   else
      clearInterval(timer)
}
timer = setInterval(anim_frame,50)
```

---

## requestAnimationFrame

Función especializada para las animaciones que nos asegura una sincronización precisa a 60FPS (ejemplo de [`setTimeout` vs `requestAnimationFrame](https://codepen.io/klugjo/pen/zBYLOX)
)

Desde dentro del *callback* de `requestAnimationFrame` hay que llamar a `requestAnimationFrame` igual que hacíamos con el `setTimeout`

```javascript
var titulo = document.getElementById('titulo')
var tam = 1
function anim_frame() {
   if (tam<150) {
      tam = tam + 1
      titulo.style.fontSize = parseInt(tam) + 'px'
      requestAnimationFrame(anim_frame)
   }
}
anim_frame()
```
[https://jsbin.com/vusekop/edit?html,js,output](https://jsbin.com/vusekop/edit?html,js,output) <!-- .element class="caption"-->

---

Referencias

- 📖 [Javascript Asíncrono: La guía definitiva](https://lemoncode.net/lemoncode-blog/2018/1/29/javascript-asincrono)
- 🖥 [What the heck is the event loop anyway? / Philip Roberts / JSConf EU 2014](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
- 🖥 [Further Adventures of the Event Loop / Erin Zimmer / JSConf EU 2018](https://www.youtube.com/watch?v=u1kqx6AenYw)