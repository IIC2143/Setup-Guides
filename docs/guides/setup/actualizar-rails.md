# Actualizar Rails a la versión 7.1

La versión 7.0.2 que estábamos utilizando tenía un error con la gema `concurrent-ruby`. Este problema está solucionado en la versión 7.1, así que realizaremos la migración. Aquí podrás encontrar cómo cambiar de la versión 7.0.4 a la nueva versión.

Todos los comandos deben ejecutarse en la terminal:

1. Desinstalamos la versión de Rails que instalamos anteriormente:

```
gem uninstall rails -v 7.0.2
```

2. Comprobamos que ya no existe la versión de Rails 7.0.2:

```
gem list rails
```
No debería aparecer ninguna versión o, si tienes alguna otra instalada, no debería aparecer la 7.0.2 en la lista de Rails.

3. Instalamos la nueva versión:

```
gem install rails -v 7.1
```

4. Comprobamos que se instaló correctamente:

```
rails -v
```

Este comando debería mostrar la versión 7.1.0.

## Actualizar un proyecto existente a Rails 7.1

1. Si tienen un proyecto ya creado y necesitan actualizar la versión de Rails, deben actualizar el archivo `Gemfile` y cambiar la línea que comienza con `gem "rails"` por `gem "rails", "= 7.1.1"`.
2. Correr `bundle` en la terminal, dentro del proyecto, para actualizar las gemas.

Importante: La tarea funciona con Rails `7.2.1`, pero no tendrán problemas ni incompatibilidades, así que no se preocupen.
