# Hgl background

## Hgl background - a personal perspective

I have been architecting and designing FPGAs and silicon chips for over thirty years. The designs have ranged from simple FPGAs for demonstration purposes, through embedded processors for routers and printers, to high end network processors and PCIe/Ethernet switches. My design responsiblity has always covered architecture, specification, design implementation, and design verification (to a level suitable for tapeout of silicon).
I have not used VHDL, Verilog or SystemVerilog as my main hardware design language for the past twenty years; this section describes why.

## Purely Synthesizable

The serious design work that I have to do is always synthesizable. There has never been an absolute requirement for uninitialized registers, so no need for simulating with anything other than binary signal values (no X, no Z). Hence simulation with a two-state cycle simulator has always been ideal. At no time (in my non-asynchronous designs) have I had to use transparent latches in the main design. So having a design language that is built on the basic assumption that support for multistate values is needed, or asynchronous design is necessary, leads to an inability to enforce the required design practices through the type system or syntax of the language.
However, the FPGA and silicon design tools all depend on Verilog (or, more recently, SystemVerilog), so ‘compiling’ down to that in a manner that is traceable (i.e. the design can be debugged using simulation, emulation, and tools such as ChipScope) is critical. The ability to know exactly what the synthesis source code is, is needed also to close timing for clock 
cycles of higher than 1GHz.

## Alternative Design Languages

My first significant foray into a design languages was Cyclicity CDL, which is now over 20 years old. It is a strongly typed language with a C like syntax, support two kinds of ‘variable’ - clocked (with a specified clock and reset signal) or combinational. This has been used for the design of processor, network, memory controller, I/O, etc components, targeting FPGAs and tapeouts of multibillion transistor devices at various geometries including 14nm. CDL is actually fairly crude, and it still supports (in cycle simulation) bit vector widths of only up to 64 bits (it is painful to implement 100+ bit wide databuses!). CDL compiles to Verilog, keeping some of the documentation (in the CDL source) as comments in the Verilog;and it compiles to C source code that can be compiled and linked with a simulation harness for a clocked simulation, which ties in to Python as a unit and random regression simulation environment. Cdl is available on github at atthecodeface/cdl.
More recently I have had a requirement to design to produce SystemVerilog that matches somewhat to the OpenTitan coding standards, with that SystemVerilog code to be supportable entirely on its own; this required a different backend. For that I used a different language, in part to investigate a future for CDL using Rust-based simulation, but mainly to provide a clear map from its source to the required format SystemVerilog; this has been used for portions of a design that has taped out on 5nm.

## Software language development

The fundamental design of software languages has evolved considerably in the past thirty or forty years. Low level languages such as BCPL or C from the 1980s are really dinosaurs that have been replaced with the likes of Rust. Most coding is now done at a higher level in Python, C++ or Java, but even those languages have evolved, and are considered (from a language design perspective) to be heavily flawed. Fundamentally this is due to the increasing processing performance and resultant engineering complexity that can applied to the language compilers, and to more rigorous process in developing the language semantics; also, lessons have been learned from large application design and from other engineering disciplines, enabling efficient , tractable architecture and design of large complex systems.

## Engineering, not coding

One of the questions I ask the students that I supervise is: “Are you computer scientists or software engineers” (none are really hardware focused when I start to supervise them). Most of the students have not thought of any distinction between the two; but I doubt they see no distinction between coding for a safety critical system such as the control for a nuclear power plant, and coding a Flappy-bird mobile phone game; or even programming a particle simulation to investigate erosion of riverbeds during flood events. This is not to denigrate computer science (or to put down engineering either), but to spark the thought that how we engage in developing our understanding of a project correlates with the quality of the implementation. This operates not in the ‘absolute’ sense that some might apply to quality (although spending little time on understanding the project remit will almost certainly yield an unsatisfactory implementation) but in  the abstract sense of quality - a thoroughly theoretical approach to comprehending the problem can yield a fine theoretical solution, of no practical value. A high quality design for a project enables support-ability, testing, reuse, and project growth and enhancement. Components of such a design may be parameterized and flexible, while remaining verifiable or provably correct provided users conform to interface or design specifications, which themselves can be statically proven.

Software engineering languages that enforce this level of quality are not easy to code for, as they require the programmer to consider many more detailed aspects of potential use than a language that permits the component designer to present less rigorous interfaces to their users. One simple example is that of memory ownership: does the language permit invoking a function call with some reference to an object without indicating that ownership is passed to the function, or lent to the function for its duration of execution, or providing an indication that the object may be thread by other threads simultaneously? Perhaps the language uses garbage collection in some form - which is a fine approach - but does it then provide a provable mechansim to show that data has been dropped (is no longer reachable) so that memory ‘leaks’ do not occur, and so that the memory footpring does not grow arbitrarily over time? Another aspect is immutability - does the language explicitly and safely manage the ability of code to modify data, to ensure that one portion of code is not relying on the state of a data structure while another portion of code mutates it? Does it provide guaranteed encapsulation boundaries, to guarantee that data structures maintain internal consistency and any validity constraints, and to ensure that accesses to the structure can only occur through a specific API? Furthermore, are all of these aspects managed in a manner that enables a high level design (even something as simple as a list) to work with multiple different low level structures that conform to a specification, so that one high level design implementation suffices, with one verification suite, rather than requiring multiple implementations or verification suites.

When I talk to the students about programming in Rust, I point out that this can be difficult; it does require the programmer to be critically aware of what data structures are shared at what times, and what structures are uniquely accessible and hence can be mutable. This is difficult, and seems to be the cause of most programmers struggling with Rust; but it is required to engineer a program correctly. The upshot of this up-front thought and analysis is that, when the program compiles, it works. It is like building an iron bridge; if engineered properly then, once constructed, the bridge stays up for its design life and carries its loads from one side to another. “Debugging” a bridge, or running a “memory sanitizer” of some form to find overly-stressed design members that then require reinforcement as an afterthought would be madness. Throwing a bridge together with bits that are taken from an ACME box-of-struts is fine for a cartoon bridge; cartoon cars don’t actually weigh very much. It is not an engineering process. Engineering requires specification, architecture, more specification, detailed design, and construction with verification (Xrays, sample testing, etc) throughout.

The output of the hardware design process is a manufacturable entity. This requires a proper engineering process, just like with a bridge; a silicon chip design takes as long as a bridge or a commercial building. On a leading edge process technology  the manufacturing cost of the first chip is many tens of millions of dollars - after all the design work has been paid for, and it will consist of tens of billions of components that all have to work perfectly together - not once, but a billion times every second (to be honest, any expectation that a silicon chip should work flawlessly is simply unreasonable…)

Using the tools that are standard today, a silicon chip design, once coded, will usually have to be lint-free with hundreds of language-permitted constructs actually being rejected, to help ensure its manufacturability. Coding in such HDLs can seem to be somewhat like programming in a C-like language which, once compiled, you then have to check the resultant assembly for illegal instruction pairings. Modern software engineers have long ago moved on from this; fortunately many engineering teams that design silicon have implemented their own higher level construction systems, but this is not pervasive.

## Hardware language development

The design languages for hardware are not all stuck in the 1980s. The basic design languages are VHDL and Verilog - very few people use VHDL any more (it is perceived to be the “Ada” compared to Verilog’s “C”). In the same way that “C” from the 1980s has been improved over time (the 1998 standard, for example), Verilog has improved - much of which is actually through SystemVerilog. The ‘modernity’ of this  an reasonably be asserted to be roughly matching that of C from 1998.

Other hardware design languages have grown out of more modern software programming languages, and in general from functional programming languages. Bluespec is a hardware development using Haskell; Chisel similarly uses Scheme.

The functional programming model matches that of the operation of hardware.

## Synthesizable Logic Design

Despite its apparently super-rational complexity, a multibillion gate silicon chip mostly (99.99+%) consists of only two basic block classes: state storage, and next-state function (NSF) logic. Synthesizable logic design is explicitly this: a description of the state, and a description of the NSF.

As an example, consider a loadable up-down counter, of 8 bits. This will have storage state of, well, 8 bits: call that ‘count’. The logic for the counter will depend on a control (in software this might be an enumeration of Hold, Load, Increment, Decrement) and some input data ‘in_data’. The next state function is then simply:

  let next_count = {
    match control {
    ctrl::Hold => count,
    ctrl::Load => in_data,
    ctrl::Increment => (count+1),
    ctrl::Decrement => (count-1),
  }};

The logic for a NSF does not have arbitrary complexity; it must, at synthesis time, describe a directed acyclic graph (DAG) where nodes are basic logic operations and links flow from state and inputs toward next state values and outputs.

The state description itself has slightly more complexity than a software data structure: it certainly had a type (8 bit vector, here); it has an associated clock, upon whose (rising) edge it captures the NSF value; it has (possibly optionally) a reset value and associated reset criteria (reset signal and whether asynchronous or synchronous assertion) to which it is set when reset is applied.

## Hardware  Components

The unit of design in hardware is the component (e.g. Verilog module), which is a portion of design that has inputs, outputs, state, and next state functions. The inputs include the clock and reset signals, if any. Components may also include other components (this is called component instantiation).

## Hardware Design Language Complexity

For synthesizable designs, the job of a hardware description language is, then, simple. It must provide a mechanism to declare state, and it must provide a means to describe the NSF. In addition it must provide for components, and their instantiation.

The complexity of SystemVerilog is, though, comesin large part from the desire for it to be both a design and a verification language. This in turn stems from it being an event based simulation language, individual code blocks being separate threads of execution. The language is just not a match for engineering descriptions of hardware design.

# Hgl concept

Hgl is specifically a hardware generation language. It operates in three fundamental phases:

1. The hgl high level representation for a compilatipn unit is generated from an abstract syntax tree, and type inference and type checking is performed. Hgl code that is generic is not monomorphized; type checking operates on polymorphic code. This produces a type-annotated medium level representation.
 
2. A set of components are generated; this involves taking a set of medium level representation design units, performing some straightforwrd type trait validation, then monomorphization. This can produce a further set of the medium level representation. monomophization requires execution of const hgl code; this code is aimed to have a bounded execution time (hence is not Turing complete).
 
3. A monomorphized medium level representation can be code generated to produce a Rust source code simulation or synthesizable SystemVerilog.
 
A critical concept to HGL is its type system. There are four kinds of hgl entity:

1. Values. These are built from some fundamental value types: bit,  bit vector, unsigned int, signed int, (string). Struct value types consist of field names and value types; enumerations which can sint, uint, or bit vectors; named unions; fixed size arrays of value types; lists of value types. Some value types are Constructable - that is, they can be held in hardware state or buses; in general a type is Constructable if it is a Built-in constructable, if it is an array of Consctructable, or if it is a struct whose fields are all Constructable. Value types support associated functions, events, and methods and associated types.
 
2. Interfaces. These are effectively two separate structs of fields with value types, plus ? a clock signal; an interface type is generally Constructable if all of its. alue types are Constructable. Interface thpes support associated functions and events and associated types.
 
3. Components. These contain state and NSF, and define what in Verilog would be a hardware module. Components have a name, and ports; ports can be clocks and resets, named inputs with Constructable value types, named unclocked outputs with Constructable value tupes, named clocked outputs with Constrcuctable va;ue types, or named (clocked/unclocked) Constructable interface types (or reverse interfaces) (with assigned clcks and resets if required). Component types support associated functions and associated types.
 
4. Libraries. These contain value types, constant named values of value types, interface types, functions, component types, and components. Library types 

Fundamental to the operation of the type is the trait system. Each type above has an associated trait concept; this is the way encapsulation is supported to engineer designs in hgl.

## Const expressions and functions and the type system

Often in a polymorphic hardware generation language it is necessary to have value types that depend on other value types; for example, the value type for the selector for a multiplexer is bit[N] and the value type for the data input is bit[M], where M=1<<N.

## Concepts for const reduce functions

  impl uint {
  const reduce fn Succ { N < 2 } {N-1}
  const reduce fn Split2 { N < 2 } {N >> 1}
  const reduce fn BinSplit { N < 2 } { 1<<(sizeof(N)-1) }
  }

  fn parity{rec N:uint} (data:bv[N]) -> bit {
  match N{
  0 => 1b0,
  1 => bv[0],
  uint::Succ(M) => bv[M] ^ parity(bv[0..M]),
  }
  }

  fn parity{rec N:uint} (data:bv[N]) -> bit {
  match N{
  0 => 1b0,
  1 => bv[0],
  uint::Split2(M) => parity(bv[0:M] ^ parity(bv[M..N]),
  }
  }

  fn parity{rec N:uint, M:uint, M = Split2(N) } (data:bv[N]) -> bit {
  match N {
  0 => 1b0,
  1 => bv[0],
  _ => parity(bv[0..M] ^ parity(bv[M..N]),
  }
  } 

  fn bv_split{N, M1, M2} (data:bv[N]) -> (bv[M1], bv[M2]) 
  where
  M1 : uint,
  M2 : uint,
  N : uint = M1 + M2
  { 
  match N {
  0 => (
  1 => bv[0],
  half(M1, M2) => bv[M] ^ parity(bv[0..M]),
  }
  }

