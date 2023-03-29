## ¿Qué es RuboCop?

RuboCop es un analizador de código estático para Ruby (también conocido como _linter_) y _formatter_ de código. Fuera de la caja, hará cumplir muchas de las pautas descritas en la Guía de estilo de Ruby de la comunidad. Además de informar los problemas descubiertos en su código, RuboCop también puede solucionar automáticamente muchos de ellos.

RuboCop es extremadamente flexible y la mayoría de los aspectos de su comportamiento se pueden modificar mediante varias opciones de configuración.

## ¿Cómo integrarlo en su proyecto?

Primero que todo es necesario instalarlo. Para esto agreguen la siguiente línea en su _Gemfile_:
```ruby
gem 'rubocop', require: false
```
y luego ejecuten:
```bash
bundle install
```

Otra alternativa es simplemente ejecutar:
```sh
gem install rubocop
```

Por último, deben crear, en la raiz de su proyecto, un [archivo de configuración](https://docs.rubocop.org/rubocop/1.20/configuration.html) donde se definan las reglas a utilizar.

Si no saben cómo hacerlo, no se preocupen, acá les dejamos una configuración sugerida para el proyecto. Recomendamos investigar de todas formas configuraciones para que vean cuales se ajustan a su proyecto:

### Configuración sugerida

En la raiz del proyecto deberán crear un archivo llamado `.rubocop.yml` y agregar lo siguiente:

```yml
#  Este es un archivo de rubocop simple donde se deshabilitan
# muchas de las reglas que suele checkear rubocop, para dejar
# una base simple y se tengan que preocupar solo de ciertos "cops"

#  Al final de este archivo, si encuentras que te ayudaría a tener
# un código mas ordenado, puedes añadir todos los cops que creas convenientes.

AllCops:
  DisabledByDefault: true

  NewCops: enable
  Exclude:
    - 'vendor/**/*'
    - 'test/fixtures/**/*'
    - 'db/**/*'
    - 'bin/**/*'
    - 'log/**/*'
    - 'tmp/**/*'
    - 'app/views/**/*'
    - 'config/environments/*'
    - 'node_modules/**/*'

# Layout
Layout/LineLength:
  Max: 120
  Enabled: true
Layout/AssignmentIndentation:
  Enabled: true
Layout/ClosingParenthesisIndentation:
  Enabled: true
Layout/IndentationStyle:
  Enabled: true
Layout/IndentationWidth:
  Enabled: true
  Width: 2
Layout/MultilineMethodCallIndentation:
  Enabled: true
Layout/ArgumentAlignment:
  Enabled: true
Layout/EmptyLinesAroundMethodBody:
  Enabled: true
Layout/ArrayAlignment:
  Enabled: true

# Naming
Naming/ConstantName:
  Enabled: true
Naming/FileName:
  Enabled: true
Naming/MethodName:
  Enabled: true
Naming/AsciiIdentifiers:
  Enabled: true
Naming/BlockParameterName:
  Enabled: true
  MinNameLength: 2
Naming/VariableName:
  Enabled: true

# Style 
Style/Next:
  Enabled: true
Style/RedundantArgument:
  Enabled: true
Style/RedundantAssignment:
  Enabled: true
Style/RedundantParentheses:
  Enabled: true
Style/ReturnNil:
  Enabled: true
Style/SelfAssignment:
  Enabled: true
Style/CombinableLoops:
  Enabled: true
Style/DoubleNegation:
  Enabled: true
Style/EmptyElse:
  Enabled: true

# Lint
Lint/AmbiguousAssignment:
  Enabled: true
Lint/AmbiguousOperator:
  Enabled: true
Lint/DuplicateMethods:
  Enabled: true
Lint/RedundantWithIndex:
  Enabled: true
Lint/SelfAssignment:
  Enabled: true
Lint/UnreachableCode:
  Enabled: true
Lint/UnreachableLoop:
  Enabled: true
Lint/UnusedMethodArgument:
  Enabled: true
Lint/UselessAssignment:
  Enabled: true
Lint/EmptyBlock:
  Enabled: true
Lint/AssignmentInCondition:
  Enabled: true
Lint/EmptyExpression:
  Enabled: true
Lint/ShadowedArgument:
  Enabled: true

### Añade aquí todos los Cops que quieras:
```

[Aquí](https://nextlinklabs.com/insights/our-rubocop-configuration-for-rails-development-projects) una buena guía de Rubocop si quieren seguir investigando 😄 

## ¿Cómo ejecutar RuboCop?

La ejecución de RuboCop sin argumentos comprobará todos los archivos de Ruby en el directorio actual:

```bash
rubocop
```

También pueden ejecutar RuboCop en un modo de autocorrección, donde intentará solucionar automáticamente los problemas que encontró en su código:

```bash
rubocop -a
# o
rubocop --auto-correct
```