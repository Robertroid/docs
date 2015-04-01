Una vistazo a CakePHP
###################

CakePHP ha sido diseñado para hacer las tareas más comunes del desarrollo web
de manera sencilla y fácil. Proveyendo una herramienta todo-en-uno para que pueda
partir, las distintas partes de CakePHP trabajan bien en conjunto o separadamente.

El objetivo de esta guía rápida es introducir los conceptos generales de CakePHP, y
darle un pequeño vistazo de cómo estos conceptos han sido implementados en CakePHP. Si
tiene ganas de comenzar un proyecto, puede :doc:`comenzar con el tutorial
</tutoriales-y-ejemplos/favoritos/intro>`, o bien :doc:`profundizar en la documentación
</topics>`.

Convención sobre configuración
==============================

CakePHP provee una estructura básica organizacional que cubre nombres de clases,
nombres de archivos, nombres de tablas de bases de datos y otras convenciones.
Mientras seguramente le tomará un poco de tiempo aprender las convenciones que CakePHP 
provee, al seguirlas usted puede ahorrarse configuraciones innecesarias y crear así una 
estructura uniforme de aplicación que hará que trabajar en varios proyectos sea sumamente sencillo.
El :doc:`capítulo de convenciones</intro/convenciones>` cubre varias de las convenciones
que CakePHP utiliza.


La capa Modelo
===============

La capa Modelo (Model) representa la parte de su aplicación que implementa la
lógica de negocio. Es responsable de recolectar los datos y convertirlos en los
conceptos primarios significativos de su aplicación. Esto incluye procesamiento,
validación, asociación u otras tareas relacionadas con el manejo de datos.

Tomando el ejemplo de una red social, la capa Modelo puede hacerse cargo de tareas
tales como guardar los datos del usuario, guardar las relaciones de amistad,
almacenar y recuperar las fotos del usuario, encontrar sugerencias para nuevos amigos,
etc. En este caso, los objetos de modelo pueden pensarse como "Amigo", "Usuario", "Comentario"
o "Foto". Si queremos cargar un poco de información de nuestra tabla ``usuarios``, haríamos 
algo como lo siguiente::

    use Cake\ORM\TableRegistry;

    $usuarios = TableRegistry::get('Usuarios');
    $query = $usuarios->find();
    foreach ($query as $fila) {
        echo $fila->nombre_usuario;
    }

Puede darse cuenta que no tuvimos que escribir ningún código antes que podamos
empezar a trabajar con nuestros datos. Usando convenciones, CakePHP usará clases
estándares para las clases de tablas y entidades que aún no han sido definidas.

Si queremos crear un usuario nuevo y guardarlo (validándolo antes), haríamos algo como::

    use Cake\ORM\TableRegistry;

    $usuarios = TableRegistry::get('Usuarios');
    $usuario = $usuarios->newEntity(['email' => 'mark@example.com']);
    $usuarios->save($usuario);

La capa Vista
==============

La capa Vista (View) entrega una presentación de los datos. Ya que está separada
de la capa Modelo, es responsable de usar la información que tenga disponible para producir
cualquier interfaz presentacional que su aplicación pueda necesitar.

Por ejemplo, la Vista puede usar los datos del modelo para entregar una página HTML
conteniendo estos datos, o bien una respuesta en XML para que otras fuentes puedan consumirla::

    // En un archivo de vista, dibujamos un 'elemento' para cada usuario.
    <?php foreach ($usuarios as $usuario): ?>
        <div class="usuario">
            <?= $this->element('usuario', ['usuario' => $usuario]) ?>
        </div>
    <?php endforeach; ?>

La capa Vista provee varios puntos de extensión, como :ref:`view-elements`
y :doc:`/views/cells` para que pueda fácilmente reusar su lógica de presentación.

Sin embargo, la capa Vista no está sólamente limitada a representaciones de HTML 
o texto, sino que puede ser usada para entregar cualquier tipo de formato de datos,
como JSON, XML, y a través de una arquitectura de plugins, cualquier otro formato que 
pueda necesitar.

La capa Controlador
====================

La capa Controlador (Controller) se hace cargo de las peticiones de los usuarios.
Es responsable de entregar una respuesta con la ayuda de tanto la capa de Modelo como
la capa de Vista.

Un controlador puede ser visto como un administrador que se asegura que todos los
recursos necesitados para completar una tarea puedan ser delegados a los trabajadores
correspondientes. Espera las peticiones de los clientes, verifica su validez de acuerdo según
las reglas de autenticación y autorización, delega la recolección de datos o procesamiento al
modelo, elije el tipo de presentación de datos que los clientes aceptarán, y finalmente delega el
proceso de rendereo a la capa Vista. Un ejemplo de un controlador de registro de usuario sería
el siguiente::

    public function agregar()
    {
        $usuario = $this->Usuarios->newEntity();
        if ($this->request->is('post')) {
            $usuario = $this->Usuarios->patchEntity($usuario, $this->request->data);
            if ($this->Usuarios->save($usuario, ['validate' => 'registro'])) {
                $this->Flash->success(__('Se ha registrado exitosamente.'));
            } else {
                $this->Flash->error(__('Ocurrió un error al registrarse.'));
            }
        }
        $this->set('usuario', $usuario);
    }

Podrá darse cuenta que nunca rendereamos explícitamente una vista. Las convenciones
de CakePHP se harán cargo de elegir la vista correcta y entregarla
con los datos que le hemos asignado con ``set()``.

.. _request-cycle:

Ciclo de Petición de CakePHP
=====================

Ahora que está familiarizado con las diferentes capas en CakePHP, revisemos cómo
un ciclo de petición funciona en CakePHP:

.. figure:: /_static/img/typical-cake-request.png
   :align: center
   :alt: Diagrama de flujo mostrando una petición típica en CakePHP

El ciclo de petición típico de CakePHP comienza con un usuario pidiendo una
página o recurso en su aplicación. En un nivel más alto cada petición va a través
de los siguientes pasos:

#. La petición primero se procesa por sus rutas (Routes).
#. Luego que la petición fue enrutada, el despachador (Dispatcher) seleccionará
   el objeto de controlador correcto para manejarla.
#. La acción del controlador es llamada y el controlador interactúa con los Modelos
   y Componentes (Components) requeridos.
#. El controlador delega la creación de la respuesta a la Vista para generar la salida
   resultante de los datos.

Sólo el comienzo
==============

Esperamos que esta guía rápida haya captado su interés. Otras geniales
características que puede encontrar en CakePHP son:

* Un framework de :doc:`caché </bibliotecas-principales/cacheo>` que se integra con
  Memcache, Redis y otras plataformas.
* Poderosas :doc:`herramientas de generación de código
  </bake/uso>` para que pueda comenzar ahora ya.
* :doc:`Un framework de testing integrado </desarrollo/testing>` para que se 
  asegure que su código funcione perfectamente.

Los siguientes pasos lógicos son :doc:`bajar CakePHP </instalacion>`, leer el
:doc:`tutorial</tutoriales-y-ejemplos/favoritos/intro>` y empezar a construir 
su genial aplicación.

Lectura adicional
==================

.. toctree::
    :maxdepth: 1

    /intro/donde-obtener-ayuda
    /intro/convenciones
    /intro/estructura-carpetas-cakephp

.. meta::
    :title lang=es: Comenzando
    :keywords lang=es: estructura de carpetas,nombres de tablas,petición inicial,tabla de base de datos,estructura organizacional,rst,nombres de archivo,convenciones,mvc,página web,sit
