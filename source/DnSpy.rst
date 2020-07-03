DnSpy
=====

A tool we use to look at the games assmebly is dnSpy_. This tool can be used to inspect the source of the game, and while it can be used it edit the assembly this will make your mod incompatible with other mods and require distribution of the entire :code:`assembly-csharp.dll` so instead this tool should only be used to find out how the game works internally.

How to Use
^^^^^^^^^^
1. Download the zip from the github release page.
2. Extract it somewhere
3. Run dnSpy.exe
4. On the left of the window there will be a :code:`Assembly Explorer`, drag the games :code:`assembly-csharp.dll` into the :code:`Assembly Explorer`
5. Click on the arrows to the left of items in the :code:`assembly-csharp.dll` to expand them
6. Inside :code:`Assembly-CSharp (0.0.0.0)` there is :code:`Assembly-CSharp.dll` and inside that there is :code:`{} -` and this is where a majority of the games code is.




.. _dnSpy: https://github.com/0xd4d/dnSpy