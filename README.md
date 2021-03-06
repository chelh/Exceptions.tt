# `Exceptions.tt`

This is a simple code generator that builds custom exception classes
in C# 3 and above. It lets you specify multiple custom exception
classes in a single file, each with custom properties, without
writing all the necessary serialization boilerplate code.

**I no longer maintain this project. I have released it into the public domain.**

## Features

`Exceptions.tt` generates exceptions that are fully compliant with
Microsoft design guidelines. The exceptions implement all exception
constructors, and are serializable if all their properties are
serializable.

`Exceptions.tt` adds no runtime dependencies to your project, and
does not require attribution if you distribute only in binary form.

## Using

Just copy `Exceptions.tt` to your project in Visual Studio, then
find the marked section of the file and start by writing calls to
`Exception(namespace, name)`. If needed, add properties to your
exception by chaining `.AddProperty(type, name)`. For each call to
`Exception(namespace, name)`, write the exception to the generated
file `Exceptions.cs` by chaining `.Emit()`.

For example, this code inserted within the `.tt` file:

```C#
Exception("MyNamespace", "SomethingHappenedException")
    .AddProperty("int", "Information1")
    .AddProperty("int", "Information2")
    .Emit();
```

Generates this custom exception class:

```C#
namespace MyNamespace
{
    [Serializable]
    public class SomethingHappenedException : Exception
    {
        public SomethingHappenedException()
        {
        }

        public SomethingHappenedException(string message) : base(message)
        {
        }

        public SomethingHappenedException(string message, Exception innerException) : base(message, innerException)
        {
        }

        protected SomethingHappenedException(SerializationInfo info, StreamingContext context) : base(info, context)
        {
            Information1 = (int)info.GetValue("Information1", typeof(int));
            Information2 = (int)info.GetValue("Information2", typeof(int));
        }

        public int Information1 { get; set; }
        public int Information2 { get; set; }

        [SecurityPermission(SecurityAction.Demand, SerializationFormatter = true)]
        public override void GetObjectData(SerializationInfo info, StreamingContext context)
        {
            base.GetObjectData(info, context);

            info.AddValue("Information1", Information1);
            info.AddValue("Information2", Information2);
        }
    }
}
```

## Public domain software

Created 2017 by Chelsea Hughes

I release all rights to this work. You may use it for any purpose, and alter
and redistribute it freely. If you use this in another product, credit would
be appreciated but is not required.

This software is provided “as-is,” without any express or implied warranty.
In no event will I or any other contributor be held liable for any damages
arising from the use of this software.
