# BinaryFileStructExtractor v.0.1
 Simple tool to parse and extract data from binary files given their structure.
 Nesting of different structures and unknown structure sizes are supported.

Uses collections.OrderedDict, struct.calcsize, struct.unpack

# Step 1. Generate a parsing bin_struct object:

bin_struct is a an OrderedDict
constructed either by a single string argument that gets passed to struct.unpack
or multiple key=value pairs representing the struct(ure) of the data.

The keys will be the representative names amd can be anything you specify.

The values can be strings for struct.unpack or another initialized bin_struct type 
for nesting

## Examples

```
ushort = bin_struct("H")      # (*)
bin_float = bin_struct("f") 
bin_string = bin_struct("6c") # see also string_struct as a better alternative

vector = bin_struct("3f")
# or
vector = bin_struct(x=bin_float, y=bin_float, z=bin_float)

# (*) There are some initial types:
# Hardcoded to LITTLE ENDIAN!
# short = "<h"
# ushort = "<H"
# bfloat = "<f"
# uchar  = "<B"
# ulong  = "<L"
# uint   = "<I"
# bint   = "<i"
# char   = "<i"

```

# Step 2. Extract your data

After initializing a bin_struct object can be called to parse the given byte argument
The necessary byte size can be retrieved with bin_struct.size 
and updated with bin_struct.calcsize() if a changed was made to the object.

Out of a bytes object the data then can be retrieved by:
`xyz = vector(byte[:vector.size])`
Even if more bytes are provided only the onces according to size are evaluated.

`my_int = ushort(b'00')`
This returns a parsed_struct object also an OrderedDict.

The values can then be retrieved with their keywords
`xyz.x, xyz.y, xyz.z`
For single valued objects with the keyword *value*
`my_int.value`

NOTE: Unlike with struct.unpack single values are not in tuples
for example:
`ushort(b'00').value # -> 12366 and not (12366,)`

-----------------

# Special string support: string_struct 

For values that shall be converted to strings *'c', 's', 'p'*

There are two options. Use a parsed objects `parsed_struct().tostring(key="value")` or the parsed_struct class staticmethod `parsed_struct.get_string(bytes)`.

The default decode argument for 's' and 'p' are is set with the variable `DECODER = 'ASCII'`.

```
# Others are not tested. I doubt utf works.

parsed_string = bin_string(b'hello\x00') # See examples this was bin_struct("6c")
parsed_string.tostring(s) # -> "hello"

# or
parsed_struct.get_string(parsed_string.s) # -> "hello"

#Note: While typing this i'm not sure how correct this example is.

```
### Empty bytes

NOTE: Empty bytes *\x00* are removed by default.
    `tostring` and `get_string` have a `remove=iterable` (can also be False or None) parameter which let's you keep these or remove others.
    The default is set as a list: _default_remove = [b'\x00', 0]

easier to use is the string_struct

### Disclaimer
Like the license states I won't guarantee that everything works correctly and as intended, it is an early stage project extracted from another one which works as a standalone.
If you realize something is not correct feel free the open up issues.
