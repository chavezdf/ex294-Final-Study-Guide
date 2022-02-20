Consejos a implementar antes de comenzar a hacer Guias:
-------------------------------------------------------

**Configurar tu editor VIM:**
No hay consejo mas importante que este. Debido a que ansible ustiliza el formato YAML para el procesamiento de las guias tenemos que configurar su comportamiento para este tipo de archivos.
Para ello vamos a editar el archivo de configuracion .vimrc del perfil de trabajo.

Agregar al .vimrc

    autocmd FileType yaml setlocal ai ts=2 sw=2 et

> Tip: **a**uto**i**dent, **t**ab**s**top, **s**hift**w**idth, **e**xpand**t**ab

Otra cosa importante que considero importante es la definicion del esquema de colores, ya que, aveces no se logra ver bien el codigo que se escribe. Por ello para mi es importante.

Agregar al .vimrc

    colorschema desert

Tambien puede escoger el *colorscheme* como mas le plasca de la siguiente manera:
Al tener vim abierto, ejecute:

    :colorscheme[space][ctrl+D]
    
Alli saldran los *colorscheme* disponibles y podran escogerlos:

    blue       delek      evening    morning    peachpuff  slate
    darkblue   desert     industry   murphy     ron        torte
    default    elflord    koehler    pablo      shine      zellner
