## Process and Programs
A process is an instance of an executing program. 
A program is a file containing a rnage of information that describs how to construct a process at runtime. This information includes the following:
- *Binary format identification*: Each program file includes metainformation describing the format of the executable file. In modern Linux it is *Executable and Linking Format* (ELF)
- *Machine Language instructions*: This encodes the algorithm of the program
- *Program entry-point address*: This identifies the location of the instruction at which execution of the program should commence.
- *Data*: The program file contains values used to init variables and also literal constants used by the program.
- *Symble and relocation tables*: Describes the names and locations of functions and variables.
- *Standard-library and dynamic-linking information*: 3rd party libraries and dynamic links to locate those libraries.
- Other informations