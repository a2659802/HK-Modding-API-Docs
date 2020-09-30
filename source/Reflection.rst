Reflection
==============

Reflection is a powerful way to detect an entity's infomation. With reflection, 
you can retrieve a lot (such as methods or fields in a class) and filter them with BindingFlags.
**But if you just want to access a specific private field using a Reflection Helper is the better way.**
`To see more, you can read through Microsoft's tutorial. <https://docs.microsoft.com/en-us/dotnet/api/system.reflection?view=netframework-3.5>`_


Reflection Helper
^^^^^^^^^^^^^^^^^
The Modding API has it own reflection helper, but 56 made another reflection in Vasi which he named Mirror.
Mirror will be easier to use and more powerful, but I'll still introduce API's reflection helper,
you can skip this part if you like

Assume there are two class: one is a static class, other is a normal class

.. code-block:: C#

	public static class StaticClass
	{
		private static string _static_field = "a static field in a static class";
	}
	public class InstanceClass
	{
		private static string _static_field = "a static field in an instance class";
		private int _non_static_field = 59;
	}

I'll show you how to access the private field for the above classes with api and vasi,
you can try them for accessing classes in Assembly-CSharp

API Reflection Helper
~~~~~~~~~~~~~~~~~~~~~

GET
***

get :code:`StaticClass._static_field`

.. code-block:: C#

	FieldInfo fi = ReflectionHelper.GetField(typeof(StaticClass), "_static_field", false);
	Log($"StaticClass._static_field is {fi.GetValue(null)}");
	

get :code:`InstanceClass._static_field`

.. code-block:: c#
	
	fi = ReflectionHelper.GetField(typeof(InstanceClass), "_static_field",false);
	Log($"(First way)InstanceClass._static_field is {fi.GetValue(null)}");
	
.. note::
	To Get or Set an instance field, you must have the instance of this type.
	In this example, I use :code:`InstanceClass instance = new InstanceClass();` to create an instance for test

get :code:`InstanceClass._non_static_field`

.. code-block:: c#

	// first way
	fi = ReflectionHelper.GetField(typeof(InstanceClass), "_non_static_field");
	Log($"(First way)InstanceClass._non_static_field is {fi.GetValue(instance)}");

	// second way
	int nonstatic = ReflectionHelper.GetAttr<InstanceClass, int>(instance,"_non_static_field");
	Log($"(second way)InstanceClass._non_static_field is {nonstatic}");
	
SET
***

set :code:`StaticClass._static_field`

.. code-block:: c#

	FieldInfo fi = ReflectionHelper.GetField(typeof(StaticClass), "_static_field", false);
	fi.SetValue(null, "modify static field in static");
	Log($"StaticClass._static_field is {fi.GetValue(null)}"); // verify change
	
set :code:`InstanceClass._static_field`

.. code-block:: c#

	fi = ReflectionHelper.GetField(typeof(InstanceClass), "_static_field",false);
	fi.SetValue(null, "modify static field in instance");
	Log($"(First way)InstanceClass._static_field is {fi.GetValue(null)}"); // verify
	
set :code:`InstanceClass._non_static_field`

.. code-block:: c#

	// first way
	fi = ReflectionHelper.GetField(typeof(InstanceClass), "_non_static_field");
	fi.SetValue(instance, 1);
	Log($"(First way)InstanceClass._non_static_field is {fi.GetValue(instance)}");

	// second way
	ReflectionHelper.SetAttr<InstanceClass, int>(instance, "_non_static_field", 2);
	int nonstatic = ReflectionHelper.GetAttr<InstanceClass, int>(instance,"_non_static_field");
	Log($"(second way)InstanceClass._non_static_field is {nonstatic}");


Vasi Reflection Helper
~~~~~~~~~~~~~~~~~~~~~~

GET
***

get :code:`StaticClass._static_field`
	It seems to not be supported. 
	
get :code:`InstanceClass._static_field`

.. code-block:: c#

	string f1 = Mirror.GetField<InstanceClass, string>("_static_field");
	Log($"InstanceClass._static_field is {f1}");
	
get :code:`InstanceClass._non_static_field`

.. code-block:: c#

	int f2 = Mirror.GetField<InstanceClass, int>(instance,"_non_static_field");
	Log($"InstanceClass._non_static_field is {f2}");

SET
***

set :code:`StaticClass._static_field`
	It seems to not be supported.

set :code:`InstanceClass._static_field`

.. code-block:: c#

	// first way : use SetField
	Mirror.SetField<InstanceClass, string>("_static_field", "modify static in instance");
	string f1 = Mirror.GetField<InstanceClass, string>("_static_field");
	Log($"InstanceClass._static_field is {f1}");

	// second way : use ref
	ref string rf1 = ref Mirror.GetFieldRef<InstanceClass, string>("_static_field");
	rf1 = "(way2) modify static in instance";
	f1 = Mirror.GetField<InstanceClass, string>("_static_field");
	Log($"InstanceClass._static_field is {f1}");

set :code:`InstanceClass._non_static_field`

.. code-block:: c#

	// first way : use SetField
	Mirror.SetField<InstanceClass, int>(instance, "_non_static_field", 1);
	int f2 = Mirror.GetField<InstanceClass, int>(instance, "_non_static_field");
	Log($"InstanceClass._non_static_field is {f2}");

	// second way : use ref
	ref int rf2 = ref Mirror.GetFieldRef<InstanceClass, int>(instance, "_non_static_field");
	rf2 = 2;
	f2 = Mirror.GetField<InstanceClass, int>(instance, "_non_static_field");
	Log($"InstanceClass._non_static_field is {f2}");

Some usage for Reflection
^^^^^^^^^^^^^^^^^^^^^^^^^

Use Reflection to call private methods
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Assume there are two classes like the following:

.. code-block:: c#

    public static class StaticClass
    {
        private static int _static_method()
        {
            return 26;
        }
        private static string _static_method_with_arg(string name)
        {
            return $"Hello, {name}";
        }
    }
    public class InstanceClass
    {
        private static int _static_method()
        {
            return 80;
        }
        private string _non_static_method_with_arg(string name)
        {
            return $"Hello, {name}";
        }
    }

You may use typeof(T).GetMethod(methodName,BindingFlags) to get a method. 
Now suppose you want to call ``StaticClass._static_method()``
so you can use ``var method = typeof(StaticClass).GetMethod("_static_method", BindingFlags.NonPublic | BindingFlags.Static)`` to get this method, and then use ``m.Invoke(null, new object[] { });`` to call it.

I will give four examples for different method calling:

.. code-block:: c#

	BindingFlags private_static_flags = BindingFlags.NonPublic | BindingFlags.Static;
	BindingFlags private_instance_flags = BindingFlags.NonPublic | BindingFlags.Instance;
	MethodInfo m;
	string strReturn;
	int intReturn;
	InstanceClass instance = new InstanceClass();

	// Invoke a static method in a static class (without arguments)
	m = typeof(StaticClass).GetMethod("_static_method", private_static_flags);
	intReturn = (int) m.Invoke(null, new object[] { });
	Log("StaticClass._static_method() return:" + intReturn);

	// Invoke a static method in a static class (with arguments)
	m = typeof(StaticClass).GetMethod("_static_method_with_arg", private_static_flags);
	strReturn = (string) m.Invoke(null, new object[] {"sawyer" });
	Log("StaticClass._static_method_with_arg(\"sawyer\") return:" + strReturn);

	// Invoke a static method in a instance class (without arguments)
	m = typeof(InstanceClass).GetMethod("_static_method", private_static_flags);
	intReturn = (int) m.Invoke(null, new object[] { });
	Log("InstanceClass._static_method() return:" + intReturn);

	// Invoke a instance method in a instance class (with arguments)
	m = typeof(InstanceClass).GetMethod("_non_static_method_with_arg", private_instance_flags);
	strReturn = (string) m.Invoke(instance, new object[] { "hollowknight" });
	Log("instance._non_static_method_with_arg(\"hollowknight\") return:" + strReturn);
	

	
Use Reflection to create a series of classes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Sometimes we want to create an instance of a class, the code ``new XXX()`` would come into our mind
but if we need to create a lot of instances with different classes, reflection will be more convenient.

Assume there is an abstract shape defined like this:

.. code-block:: c#

    public abstract class AbstractShape
    {
        public Vector2 position;
        public abstract void Draw();
        public AbstractShape()
        {
            Modding.Logger.Log("An Concrete class has been instantiated");
            Draw();
        }
    }

And we still have its subclasses in a class like these:

.. code-block:: c#

    public class Shapes
    {
        public class RectShape : AbstractShape
        {
            float width;
            float height;
            public override void Draw()
            {
                Modding.Logger.Log("RectShape Drawn");
            }
        }
        public class CicrleShape : AbstractShape
        {
            float r;

            public override void Draw()
            {
                Modding.Logger.Log("CircleShape Drawn");
            }
        }
    }

Now we can create all the classes's instance in the Shapes using the following code:

.. code-block:: c#

	// this will get an array like this {typeof(RectShape),typeof(CicrleShape)}
	Type[] subclasses = typeof(Shapes).GetNestedTypes(BindingFlags.Public | BindingFlags.Instance);

	foreach(Type t in subclasses)
	{
		object shape = Activator.CreateInstance(t); //CreateInstance(Type) like new Type()
	}

Source Code 
^^^^^^^^^^^
	You can get ArticleExam_ in my github
	and also get a real mod (P5RandomMod_) in 56's github, it uses reflection to modify a private array.

.. _ArticleExam: https://github.com/a2659802/MyModdingToturial/tree/master/ReflectionExam
.. _P5RandomMod: https://github.com/fifty-six/HollowKnight.RandomizedPantheons/blob/master/RandomPantheons/RandomPantheons.cs