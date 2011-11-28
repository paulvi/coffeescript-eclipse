
* Display parse tree for debugging purposes
	* Trying [Implement toString with Xtext's Serializer][1].
	  The parse tree I get is flat without indentation when not doing the `toString` replacement magic,
	    just overriding `AdditionImpl.toString()`.
	  So displaying the parse tree of `1+2` just gives `1+2`.
	  (I had to use the built-in lexer, it doesn't seem to work with my external beaver-based lexer.)
* Display parse tree with structure
	* I'll have to implement manually an AST displayer.
	  The serializer approach doesn't work, because [it's supposed to output a parseable string][2]
	* Done.  See `csep.parser.Helper.stringify`.

* Overriding `nextToken()` in Lexer is not enough.
  The parser seems to use only the first token when building the AST.
	* There are some differences between the token returned by the internal lexer and the token by the aptana lexer.
	  The differences include: whitespace handling; EOF; type/id of operators.
	* The difference of whitespace and EOF handling seem to cause no problem.
* Why is the type of operators different in BeaverToken?
	* A literal '+' within a rule is not the same as using PLUS within the same rule.
	  (Note: the aptana lexer returns a MATH token both for '*' and for '/')

* How to vizualize the LALR grammar of coffeescript?
	* I asked about [visualizing LALR grammar] on Stackoverflow
	* I found [GOLD parser][4] which has an IDE, but runs only on Windows.
  	  But I may check it out.

* Learning ANTLR
  * Tried to use the generated python target, but it seemed difficult on
   Ubuntu: there is no python binding for antlr3, the java version
   doesn't match with the downloadable python bundles.
   So I abandon this track, and go back to painful old java.
 * The difficulty seems to be left-factoring expressions.
   I asked for [help with left-factoring expressions][5] at Stackoverflow.
 * I got an answer to my question which doesn't fix the problem but
   helped me understand it.
   The issue boils down to this: write a simple expression language with
   function calls.
   An expression can be an operation or a function call.
   The operand in an operation can be any expression, including a
   function call.
 * The lesson learned is this: if I have `expression: operation | whatever`,
   and `whatever` can be reached via `operation`, then omit it from the
   right-hand side of `expression`.
 * I'm not sure how close I can stay to the original LR grammar.

* [Convert Antlr grammar to Xtext][6]
 * Terminals should be prefixed with `terminal`
 * The first rule should be the root, it cannot be a terminal
 * Fix the error `Cannot create datatype X` by including
   `import "http://www.eclipse.org/emf/2002/Ecore" as ecore` right after
   the first line
 * Fix the error `Generated package X may not be empty` by including a
  feature in the root rule.
  If you have `Root: Foo`, replace it with `Root: bar=Foo`.
 * Rules are case-insensitive, you cannot have a terminal ID and a rule
  Id in the same file.
 * Optionally you may want to capitalize the rule names to follow the
  java convention.

* Running headless with make
  * I'm going to abandod this because eclipse packages are versioned with a build date too which is difficult to handle in a Makefile.
  * Possible (and time-consuming) solutions are
    * use some dependency management tool, such as ivy
    * do some bash scripting magic to find foo-[version]-v[build-date].jar and copy it as foo-[version].jar

  [1]: http://jevopisdeveloperblog.blogspot.com/2011/03/implement-tostring-with-xtexts.html
  [2]: http://www.eclipse.org/Xtext/documentation/2_1_0/100-serialization.php#serializationcontract 
  [3]: http://stackoverflow.com/questions/8154790/visualize-lalr-grammar
  [4]: http://goldparser.org/
  [5]: http://stackoverflow.com/questions/8263772/left-factoring-grammar-of-coffeescript-expressions
  [6]: http://stackoverflow.com/questions/8279790/convert-simple-antlr-grammar-to-xtext