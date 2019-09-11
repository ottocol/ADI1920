# Software necesario para ADI 19/20

Tienes dos opciones:

- Usar una máquina virtual de virtualbox con todo el software preinstalado, puedes descargarla [aquí](https://drive.google.com/open?id=1j6UPjdVxaqjSLKKjOCSwwF8KX9Nq400y) (unos 2 Gb comprimida) 
- Instalar el software de forma nativa como se explica a continuación. No es complicado, en los laboratorios usamos la máquina virtual solo por cuestiones técnicas.

> Todo lo que viene a continuación se aplica **solo si no usas la máquina virtual**
 
## Node

### Linux/MacOS

Probablemente lo más sencillo es usar `n`, un gestor de versiones de Node que nos permite tener varias versiones coexistiendo en nuestra máquina y no necesita permisos de superusuario para instalarlas.

Para instalar la última versión LTS de Node con `n`, simplemente tecleamos en una terminal:

```bash
curl -L https://git.io/n-install | bash
```
La instalación se hace en el directorio `$HOME/n`, para no requerir permisos de superusuario.

### Windows

Puedes [descargar un instalador](https://nodejs.org/es/download/) desde la web de NodeJS.

## Herramientas adicionales que necesitarás

- [Postman](https://www.getpostman.com/) o [HTTPie](https://httpie.org/) para hacer peticiones de prueba al API REST
- Algunos paquetes adicionales de node. Teniendo instalado ya node, en una terminal, sea windows, linux o Mac, escribe

```bash
npm install -g nodemon # para todas las prácticas
npm install -g @vue/cli # para la práctica 3
```
- Un navegador web cualquiera
- Un editor de código para Javascript/HTML/CSS. Usa el que quieras, en los laboratorios está el [Visual Studio Code](https://code.visualstudio.com/)