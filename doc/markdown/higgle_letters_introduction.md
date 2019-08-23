# Introduction

This set of documents, collectively termed the 'Higgle letters', are the
rationale and thought process behind the design and development of 'Higgle',
a modern hardware generation language.

The reason for the development Higgle is to provide for efficient development of large scale silicon and FPGA designs. The design of Higgle draws from decades of silicon development and modern computer language definition.

The desire for Higgle comes from learnings from hardware design languages from the past thirty years, and the desire to provide for reasonable open hardware repositories. Current hardware description languages are very poor at managing libraries of design, and do not encourage a component-based design methodology. Many approaches are taken to improve upon this by using libraries in modern software languages to describe hardware (including, for example, Python); a domain specific language, though, should provide concise description (fundamental to clean hardware design) in addition to the power of a modern software language in its scope.

Computer language and compiler design has evolved considerably over time. The LLVM compiler infrastructure project has shown the power of a well described intermediate representation; Haskell, Ocaml and Rust 

## Higgle - a Hardware Generation Language

Higgle

### What must Higgle be?

Higgle must be a hardware generation language for developing synchronous digital designs, which can be targeted at ASIC cell library synthesis with standard test methodologies and tools, and also at FPGA synthesis.

Higgle must provide for simulation model generation, with a multithreadable simulation environment that may be optimised for highly parallel execution (tens of cores) to enable verification of designs.

Higgle must be statically strongly typed using type inference. Each library component must be sufficiently well described that, should it compile cleanly and be verifiable, that any use of the component which matches its interface will be a verified instance.

Higgle's library components must support polymorphism to enable instantiation of a component for a variety of flavors of type structures; this must include the ability to control the internal operational behavior of a type structure when it is utilized within a component (conceptually similar to class object methods).

### What must Higgle not be?

Higgle must not be an event based language; modern silicon and FPGA design is synchronous with explicit clocks, and explicit clock gating and multiplexing. Higgle must not attempt to intuit the clocking of a design; this must be explicit.

Higgle must not be unnecessarily verbose; hardware description requires considerable verification, and verbosity can obscure design intent.
