Download Link: https://assignmentchef.com/product/solved-cs502-project-4-cmscala-to-cps-translation
<br>
In this assignment, we are asking you to implement the optimized CPS transformations you have seen in the course.

In previous assignments, we have focused on building up frontend of our compiler pipeline (i.e., our parser and semantic analyzer), and have made due with a relatively simple backend. As many students have noticed, this has resulted in suboptimal assembly code being generated. As such, we will now shift gears and focus more heavily on creating an optimized backend.

For this project, we have provided you with a more complex frontend than we’ve seen thus far. Some of the new pieces of syntactic sugar of which you should be aware are as follows:

e1 &amp;&amp; e2 ==&gt; if (e1) e2 else false e1 || e2 ==&gt; if (e1) true else e2

!e1 ==&gt; if (e1) false else true

“c1…cn” ==&gt; val s = new Array[Char](200); s(0) = ‘c1’;…;s

(n1: T1, …) =&gt; b1 ==&gt; def anon1(n1: T1, …) = b1; anon1

You can nd all code dealing with syntactic sugar in compiler/src/miniscala/MiniScalaParser.scala, though you will not need to make any modi cations there.

Similarly, the code for desugaring can be found in compiler/src/miniscala/CMScalaAnalyzer.scala. Again, no modi cations are necessary in this le.

As we will not be providing solutions for previous assignments, you may want to look over MiniScalaParser and CMScalaAnalyzer if you have any questions about how these functions are implemented. We are also happy to provide further clari cation on Piazza as needed.

In addition to these pieces of syntactic sugar, we also have some new types, with corresponding literals and operators. Below is a list of some of the types available in our language:

Integer: Now available in base 16 with 0x pre x.

Char: All character literals are supported (e.g., ‘c’, ‘s’, ‘3, ‘5’, ‘2’, ‘
’, etc.).

Boolean: As before, Boolean literals are true and false.

Unit: As before, the Unit literal is ().

String: Strings are now supported in our language, with string literals appearing as expected (e.g. “this is a string”, “help, I’m stuck in grad school”, “let me out”, “
\gradschool gives an error: ”unable to escape”
”, etc.).

And some corresponding operators:

+ – * &lt;&lt; &gt;&gt; &amp; | ^ / % : (Int, Int) =&gt; Int

&lt; &lt;= &gt; &gt;= : (Int, Int) =&gt; Boolean == !=: (T1, T2) =&gt; Boolean

id : T =&gt; T

toChar : Int =&gt; Char //Note: Int must be valid Unicode code-point toInt : Char =&gt; Int isInt isChar isBoolean isUnit : T =&gt; Boolean getchar : () =&gt; Int, between 0 and 255, or -1 putchar : Int =&gt; Unit, Int between 0 and 255

Other types include Array[T], function types, Pairs, and List[T]. Take a look at the les in library/ to see how to use them.

<h2>Step 1: Getting Started</h2>

The project has been designed and tested for Linux/Mac OS. If you only have Windows installed on your personal laptop, consider running Linux in a VM or using the lab machines for the project. Another alternative is to try Windows Terminal App, which offers a Ubuntu18 terminal interface.

If you use remote access to work on your project, please use one of the lab machines pod1-1 to pod1-20 with the suf x cs.purdue.edu (e.g. pod1-1.cs.purdue.edu)

Download the skeleton le <a href="https://www.cs.purdue.edu/homes/wang603/cs502/proj4.tar.gz">here</a>.

<h2>Step 2: Project Structure</h2>

<h3>Browse the Files</h3>

Now the project directory has changed a little bit. In the top dir, you can see compiler/ examples/ and library/. The src and test codes are in compiler/. The examples/ dir is like before: providing example programs. The library/ dir contains les that de ne standard libraries for our language. In compiler/src/miniscala/, all les with the pre x MiniScala are handling the parsing. All les with pre x CMScala are handling the semantic analyzer pass.

You only need to update the le <strong>CMScalaToCPSTranslator</strong>.

You will notice that Main.scala from the new project doesn’t run the CMScalaInterpreter anymore (it was the interpreter we were using before), but rst runs the CPS translator and then an interpreter for your CPS representation.

Also, you may notice that between translation and interpretation, there is an additional step called

SymbolicCPSTreeChecker. The purpose of this module is to go through your generated CPS tree, check that all names used are de ned exactly once, and emit useful warnings in case they are not. Take advantage of this output to track any bugs you may have in the translation. Notice that, since

SymbolicCPSTreeChecker.apply returns Unit, we have to pass it to the passThrough function to integrate it in the pipeline.

You will nd that methods nonTail, tail and cond in miniscala/CMScalaToCPSTranslator.scala directly correspond to translations that were explained during the lecture. Transformation for let is already given to you as an example. Your task is to complete these methods by adding the missing cases. <strong>Important: take the time to understand the helper methods that are provided, and use them as frequently as possible!</strong> It will save you time and help you avoid common errors.

Note that there are two different kinds of tests: Whitebox and Blackbox. Whitebox tests check that the entire output (i.e., the IR post-translation) is correct. Blackbox tests only ensure that the value generated is correct (e.g., def f(x: Int) = x*x; 0 will only check that the program returns 0).

<h3>Implementation Hint</h3>

Try implementing the non-optimized translations in nonTail (there are the rules shown in the beginning of the lecture). This represents 80% of the work. Once this is done, you can implement the optimized version using all three functions available (nonTail, tail, and cond