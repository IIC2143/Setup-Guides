# Actualizar Rails de 8.1.2 a 8.1.3

Esta guía asume que tienes Ruby 4.0.1 y Rails 8.1.2, como indican las guías de instalación, y muestra cómo actualizar Rails a la versión 8.1.3.

Todos los comandos deben ejecutarse en la terminal.

## Verificar Ruby

```bash
ruby -v
```

El comando debe mostrar Ruby 4.0.1. Si tienes otra versión, sigue la guía de instalación correspondiente a tu sistema operativo antes de continuar:

- [Ubuntu y WSL](ubuntu.md)
- [macOS con RVM](macos-rvm.md)
- [macOS con rbenv](macos-rbenv.md)

## Actualizar Rails en el sistema

Instala Rails 8.1.3:

```bash
gem install rails -v 8.1.3
```

Comprueba la instalación:

```bash
rails _8.1.3_ -v
```

El comando debe mostrar `Rails 8.1.3`. No es necesario desinstalar Rails 8.1.2: RubyGems permite mantener ambas versiones.

## Actualizar un proyecto existente

Antes de empezar, guarda tus cambios y comprueba que el repositorio esté limpio:

```bash
git status
```

En el `Gemfile` del proyecto, cambia la versión de Rails:

```ruby
gem "rails", "8.1.3"
```

Luego actualiza Rails y sus dependencias:

```bash
bundle update rails
```

Comprueba la versión usada por el proyecto:

```bash
bin/rails -v
```

El comando debe mostrar `Rails 8.1.3`. Finalmente, ejecuta los tests del proyecto y revisa el cambio en `Gemfile.lock` antes de continuar trabajando.
