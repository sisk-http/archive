# Ini Reader Syntax Specification

Syntax specification for the Sisk.IniConfiguration package, version 1.0.

---

The Sisk ecosystem provides a very straightforward INI configuration reader: it's reader is fast, capable of reading configurations from streams, and does not throw errors for common syntax mistakes. INI files were designed to be human-readable and easy to edit; therefore, this library adheres to that philosophy.

INI files consist of configurations that are properties and values, which can include comments, separators ("sections"), and are text-based. They do not have typing, booleans, or arrays, only text. However, some design choices of the reader allow for interesting functionalities. This article addresses all of them with practical examples.

## Encodings, characters and inputs

The Sisk INI Reader is encoding-agnostic. However, it must encounter certain specific characters for the implementation to function properly. These characters are adapted according to the cultural variations of .NET, which it handles automatically and effectively. Therefore, an ASCII input will not have any issues being read as UTF-8, for example.

```cs
static class Token
{
    public const char SECTION_START = '[';
    public const char SECTION_END = ']';
    public const char COMMENT_1 = '#';
    public const char COMMENT_2 = ';';
    public const char STRING_QUOTE_1 = '\'';
    public const char STRING_QUOTE_2 = '\"';
    public const char PROPERTY_DELIMITER = '=';
    public const char NEW_LINE = '\n';
    public const char RETURN = '\r';
    public const char TAB = '\t';
    public const char SPACE = ' ';
}
```

All the aforementioned characters are identified using UTF-8. .NET handles the mapping of these characters to most other encodings, so the concern in this regard is minimal. An INI document (IniDocument) can currently be created from three different inputs:

- From a string (IniDocument.FromString), which reads a static string.
- From a file (IniDocument.FromFile), which creates a read stream from a file (defaulting to UTF-8) and reads the content of the file.
- From a read stream (IniDocument.FromStream), which reads a stream (using UTF-8 by default) from any source.

The reading process occurs character by character, whenever the Stream provides data to the reader. The speed of this process may be delayed if the provision of data is also slow, such as with a remote file, a network input, or a faulty drive. In-memory reads (such as FromString) are nearly instantaneous and do not have any reading latency.

The INI reader **does never throws any errors**. Regardless of the input, if the reader does not understand what something means, it will simply ignore that instruction until it encounters the next valid entry.

## Values, properties and sections

For sections and property names, the INI document is **case-insensitive**. Therefore, "foo" is equivalent to "FOO." This applies to both properties and sections. Properties can be declared multiple times with different values. It is also possible to have multiple sections with the same name. All properties defined within multiple sections, sharing the same name, are stored in the same sectoin array. The "Global" section is the section of the INI file that is not preceded by any defined section.

As mentioned, properties can be repeated. By default, an IniSection object has two methods for retrieving values based on property names: IniSection.GetOne and GetMany. The GetOne function retrieves the last value defined for the specified property or returns null if the property has not been defined. The GetMany function retrieves an array of string[] containing all the values defined for a property.

```ini
Name = John
Name = Jacob
Name = Joseph
Name = Faith
```

```cs
string? lastName = document.Global.GetOne("name");
string[] names = document.Global.GetMany("name");

// results:
// lastName = "Faith"
// names = ["John", "Jacob", "Joseph", "Faith"]
```

If GetMany does not find any values with the given property name, an empty array is returned.

Empty property names are also allowed, as are empty values. This can be useful for defining multiple values within a single section. It is important to note that the reader begins to read the value of a property whenever it encounters an "=" sign within it. Property names cannot be escaped.

When retrieving the properties of a section through IniSection.Keys, the values are, by default, deduplicated. This means that if you have three properties named "Name" within a section and retrieve the properties using IniSection.Keys, you will receive an array of strings containing a single item: "Name." This behavior does not apply to IniSection.Values.

Property names can contain any characters, but they are delimited by the characters '=', newline (`\n`), and carriage return (`\r`). When the name of a property encounters an '=', the parser immediately begins to read the value for that property until the next line feed. When it does not find an '=' but encounters the end of the line, the property is added to the session with an empty value.

```ini
[Names]
John
Jacob
Jacob
Jacob
Faith
```

```cs
string[] keys = document.GetSection("Names")?.Keys;
// = ["John", "Jacob", "Faith"]
```

However, when used with values, where each value is defined after an "=" sign:

```ini
[Names]
= John
= Jacob
= Jacob
= Jacob
= Faith
```

```cs
string[] values = document.GetSection("Names")?.Values;
// = ["John", "Jacob", "Jacob", "Jacob", "Faith"]
```

## Value quotes

Values can be encapsulated with double or single quotes. When encapsulated with quotes, all the content within the quotes is read exactly as it appears, and newline characters are preserved. It is important to note that it is not possible to escape quote characters within these literal values.

```ini
foo = "hello, world!"
property with spaces = "as you see, properties
    names also can have spaces. and here, i 'can
    have single quotes'."
```

Values that do not begin with quotes have whitespace characters trimmed from the beginning and the end.

# Revision 1.5

