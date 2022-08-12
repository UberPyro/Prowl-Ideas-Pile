# Modules
So far, we have covered most of Prowl's expression language, starting with the basic syntax and then moving towards the more unique features. At this point, we have to back up and introduce some more definitions, particularly ones that help define and manage modules, as many interesting features from this point on depend on them. 

Prowl's module system comes directly from its inspirations and can be clearly divided among them. The inspirations are the [MixML module system](https://people.mpi-sws.org/~rossberg/mixml/) (developed by Rossberg and Dreyer), [Modular Implicits](https://arxiv.org/pdf/1512.01895.pdf) (developed by OCaml researchers), and [Modular Datatypes](http://macqueenfest.cs.uchicago.edu/slides/harper.pdf) (by Harper, see p. 24). Our goal is to support a module system that is like a modern version of OCaml's module system, where we keep the core that works so well but polish the system and improve on its mistakes. 
- The ML module system is wonderful in its ability to describe and typecheck algebras of functions without the need for objects or a notion of self, and to give the programmer full control over how complex structures are build and parameterized, associating types with code, and supporting powerful abstractions. 
- The MixML system in particular takes this system and embeds it in a more general theory. It combines ML structures and signatures in a way that allows you to write modular code using simpler primitives in simpler ways, and additionally solves infamously hard problems like recursive modules and separate compilation. 
- Modular implicits bridge the gap between modules and typeclasses, allowing types to determine how implementations are retrieved for particular identifiers in particular type settings, which provides ad-hoc polymorphism, but in a way that is compatible with modular coherence. 
- Modular Datatypes allow for the definition of ADTs that are extenisble by riding on the back of module composition. This connection lets you manipulate data specifications in very expressive ways that are also well-controlled and encapsulated. 

## Structures and Signatures