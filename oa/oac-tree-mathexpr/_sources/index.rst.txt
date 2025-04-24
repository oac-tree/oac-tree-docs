Welcome to oac-tree-plugin-mathexpr's documentation!
=====================================================

   .. toctree::
      :maxdepth: 2
      :caption: Contents:

   `oac-tree-plugin-mathexpr` is a oac-tree plugin that provides functionality for processing mathematical expressions using the exprtk library.

Functionalities
---------------
oac-tree-plugin-mathexpr provides the following functionalities for processing mathematical expressions:

1. Supports variables of any numeric type supported by AnyValue
2. Supports arrays and scalars as variables
3. Automatically converts variables to doubles before processing the expression
4. Automatically converts the result back to the input type after processing the expression

Usage
-----

To use this plugin it needs to be compiled and installed in the system. The following line has to be added to the xml:

.. code-block:: xml

   <Plugin>liboac-tree-mathexpr.so</Plugin>

The most basic use for this plugin is to assign a mathematical expression to a variable:

.. code-block:: xml

    <Sequence>
        <MathExpression expression="z:=x+y-1"/>
    </Sequence>
    <Workspace>
        <Local name="x" type='{"type":"uint8"}' value='1' />
        <Local name="y" type='{"type":"uint8"}' value='1' />
        <Local name="z" type='{"type":"uint8"}' value='0' />
    </Workspace>


In this example the output of 'x+y-1' is saved in the variable "z". In assignments, the type of the output is always respected, so if in the example above the result of the expression is negative or above the maximum value possible for an uint8, the oac-tree will return an executionstatus of failure.

Expressions without assignments are evaluated as a boolean and cause the instruction to return an executionstatus of success or failure accordingly.

For example:

.. code-block:: xml

    <Sequence>
        <MathExpression expression="x > y^2"/>
    </Sequence>
    <Workspace>
        <Local name="x" type='{"type":"int8"}' value='10' />
        <Local name="y" type='{"type":"int8"}' value='1' />
    </Workspace>


In this example, the instruction will succeed, since 'x' is bigger than 'y+2'.


Arrays can also be used:

.. code-block:: xml

    <Sequence>
        <MathExpression expression="z:=x+y*y-2"/>
        <Equals leftVar="z" rightVar="a"/>
    </Sequence>
    <Workspace>
        <Local name="x" type='{"type":"uint32_arr","element":{"type":"uint32"}}' value="[2,4,6]"/>
        <Local name="y" type='{"type":"uint32_arr","element":{"type":"uint32"}}' value="[1,1,1]"/>
        <Local name="z" type='{"type":"uint32_arr","element":{"type":"uint32"}}' value="[0,0,0]"/>
        <Local name="a" type='{"type":"uint32_arr","element":{"type":"uint32"}}' value="[1,3,5]"/>
    </Workspace>

Multiple assignments, even if chained, can be done in a single call:

.. code-block:: xml

    <Sequence>
        <MathExpression expression="y:=x+1; z:=y+2"/>
        <Equals leftVar="y" rightVar="a"/>
        <Equals leftVar="z" rightVar="b"/>
    </Sequence>
    <Workspace>
        <Local name="x" type='{"type":"float32"}' value='158'/>
        <Local name="y" type='{"type":"float32"}' value='0'/>
        <Local name="z" type='{"type":"float32"}' value='0'/>
        <Local name="a" type='{"type":"float32"}' value='159'/>
        <Local name="b" type='{"type":"float32"}' value='161'/>
    </Workspace>


An expression can be passed from a string variable:

.. code-block:: xml

    <Sequence>
        <MathExpression expression="@cond"/>
    </Sequence>
    <Workspace>
        <Local name="cond" type='{"type":"string"}' value='"x > y^2"' />
        <Local name="x" type='{"type":"int8"}' value='10' />
        <Local name="y" type='{"type":"int8"}' value='1' />
    </Workspace>

.. warning::

   All oac-tree plugins use an XML parser to process input functions. Please note that certain characters in your XML definitions may need to be escaped in order to avoid errors. These include:

   - & (ampersand) - use &amp;
   - < (less than) - use &lt;
   - > (greater than) - use &gt;
   - " (double quotation mark) - use &quot;
   - ' (apostrophe) - use &apos;

   From the above referenced characters, the greater than (>) character works as expected, but the less than (<) causes failures. To be sure no errors related to parsing this characters it is better to escape all of them.
