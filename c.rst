.. _c_standards:
.. highlight:: c
	:linenothreshold: 5

Nagios C Language Standards General Statement
====================

Nagios open source projects are largely written in the C programming language. Prior to this documentation all projects maintained individual standards. This leads to many difficulies, the largest being readability and maintainability. This documentation is a working attempt to merge all Nagios C projects into a consitent standard. These statements are currently not enforced as most projects need to be reformatted and moved towards this goal.

C Language Standard
====================
Nagios C projects currently do not define a C language standard for compilation. Going forward a goal of compilation against the C89 standard at a very least shall be implimented. All patches must be compatible with this standard.

Compilation Flags
====================
All projects going forward must adhere to passing compilation against -Wall and -Werror at the very least. -Wextra is suggested for best compatability. Not adhering to this only leads to undefined behavior and unreproducable bugs. There are no exceptions to this rule, once a project has passed the minimum requirements here. This should be a first priority for all new and existing projects that do not meet this specification already.

Language Semantics
====================

Overall Astyle format command
--------------------
.. code-block:: bash

	astyle --style=attach \
		--align-pointer=name --align-reference=name \
		--indent=force-tab --indent-cases \
		--indent-preproc-define \
		--pad-oper --pad-header --unpad-paren \
		--break-closing-brackets --keep-one-line-blocks \
		--add-comment-prefix \
		--preserve-date --linend=linux \
		[file]

Variable Conventions
--------------------
Short yet descriptive names should be used when possible. Full names are not required, however a reasonable expansion of the name should be possible. Variable names should be separated by underscores(_), not camel case(NagMon) or hyphens(-).

.. code-block:: c

	# Acceptable
	typedef struct {
		int fd;
		int events;
		int (*handler)(int, int, void *);
		void *arg;
	} iobroker_fd;

	# Unacceptable
	typedef struct {
		int f;
		int e;
		int (*h)(int, int, void *);
		void *a;
	} ib-fd;

Local counters and variables may use shorter or less descriptive names where logical and where readability is not impeded. No prefix is needed for local variables.

.. code-block:: c

	void acceptable (void) {
		int c, x = 10;
		for (c=0; c<x; c++, x--) {
			printf("c=%d\tx=%d\n", c, d);
		}
	}

Function arguments should always be descriptively named, with no exceptions. Function arguments follow the prefix of their intended scope.

.. code-block:: c

	void acceptable (char *string, int len);
	void unacceptable (char *c, int x);

Global variables should be prefixed with the ?? prefix.

.. code-block:: c

	TODO: add global prefix example

astyle flags: NA

Pointers and References
--------------------
Pointer and reference specifiers should always be attached to the left of a variable name. There are no exceptions to this going forward.

.. code-block:: c

	int *acceptabele = &variable;

	int * unacceptable = & variable;
	int* unacceptable = & variable;

astyle flags: --align-pointer=name --align-reference=name

Indentation
--------------------
Tab indentation should always be used, opposed to space indentation. The standard of 4 space tabs is generally adhered to, but may be defined separetly within your editor of choice. The adherance to indentation level is more important when working with Nagios projects. In general logical places for indenting should have a single indent applied. All blocks of code should be indented to the same level, with an increased indent level for each logical sub-block. Some special cases and clarification are defined below. Spaces should never be used to reach the same indent level as anothe line, tabs should be the only character used for intenting.

Code blocks within function declarations and language statements such as if() and for(), should be indented one intent from the declaration's indent level.

.. code-block:: c

	int acceptable (void) {
		if (do_something()) {
			return something;
		}
		return 0;
	}

	int unacceptable (void} {
			if (do_something()) {
					return something;
			}
			return 0;
	}

Function and other declarations with extended arguments that must be broken onto a new line, should be indented one indent from the declarations indent level.

.. code-block:: c

	void acceptable (int i, int x,
		char *separated,
		uint32_t counter);

	void unacceptable (int i, int x,
				char *separated,
				unint32_t counter);

Switches and case statements should be indented to the same level, with statement code blocks indented one indent from the switch and case indent level.

.. code-block:: c

	switch (acceptable) {
	case '\x13':
		break;
	default:
		continue;
	}

	switch (unacceptable) {
		case '\x13':
			break;
		default:
			continue;
	}

Closing braces should always be brought back to the indent level of the initial declaraton, not left at the indent level of the code block.

.. code-block:: c

	if (acceptable) {
		break;
	}
	else {
		acceptable = unacceptable;
		}

astyle flags: --indent=force-tab --indent-cases2

Spacing
--------------------
Spacing around conditionals, operators, braces, variables, and function declarations, is mandatory, with no spacing between a function call and arguments.

.. code-block:: c

	void acceptable (void) {
		if (acceptable()) continue;
		else {
			break;
		}
	}
	acceptable(void);

	void unacceptable(void){
		if(unacceptable ())continue;
		else{
			break;
		}
	}
	unacceptable (void);

astyle flags: --pad-oper --pad-header --unpad-paren

Brackets
--------------------
Braces should be used whenever possible to enclose code blocks. Single line statements are the only exception, and must be written on a non-broken line. As stated under "Indentation", closing braces should always be brought to the same indent level as the original declaration's indent level.

A multi-line block is defined as being any logical block of code not written on a single line. Such that if a statement has a single command executed when a condition is met, the line may only be broken when enclosed in braces.

.. code-block:: c

	if (acceptable) return true;
	
	if (acceptable) {
		return true;
	}

	if (unacceptable)
		return false;
	
	if (unacceptable) {
		return false;
		}

astyle flags: --break-closing-brackets --keep-one-line-blocks

Comment Structure
--------------------
Single line //, and single and multi-line /\* \*/ comment styles are accepted. All multi-line comments should have a standard comment prefix of " \*".

.. code-block:: c

	// acceptable comment
	/* Acceptable comment */
	/*
	 * Acceptable comment
	*/
	/*
	Unacceptable comment
	*/

astyle flags: --add-comment-prefix

Max Line Length
--------------------
Currently no max line length is enforced. A probably max length of 80-120 is currently being discussed. Patches need not adhere to this at present.

astyle flags: --break-after-logical --max-code-length=120 (suggested)

Pre-Processor Definitions
--------------------
C Pre-processor definitions should be set at the left most indentation possible. Macros and other multi-line definitions must follow all other standards as defined above.

astyle flags: --indent-preproc-define

Other astyle flags we use
--------------------
--preserve-date - Preserves modification date, leaving the last actual functionality change, not style modification date.
--linened=linux - Preserves linux line endings of \n opposed to Windows line endings of \n\r.
