# ProPresenter7-Proto

This is a set of ***unoffical and unsupported*** .proto files that define the messages (data structures) used in the ProPresenter 7 files that are encoded as Google Protocol Buffers.

## Reverse engineering of `Pro.SerializationInterop.dll` by reflection
```cs
using Google.Protobuf;
using Google.Protobuf.Collections;
using Google.Protobuf.Reflection;
using System.Diagnostics;
using System.Reflection;
using System.Text;

var libPath = Path.Join(Environment.GetFolderPath(Environment.SpecialFolder.ProgramFiles), "Renewed Vision", "ProPresenter");

var protobuf = Assembly.LoadFrom(Path.Join(libPath, "Google.Protobuf.dll"));
var fd = protobuf.GetType("Google.Protobuf.Reflection.FileDescriptor")!;
var bs = protobuf.GetType("Google.Protobuf.ByteString")!;
var n = fd.GetProperty("Name", typeof(string))!;
var sd = fd.GetProperty("SerializedData", bs)!;
var tba = bs.GetMethod("ToByteArray")!;

var interop = Assembly.LoadFrom(Path.Join(libPath, "Pro.SerializationInterop.dll"));
var fds = (
    from type in interop.GetExportedTypes()
    let descriptor = type.GetProperty("Descriptor", fd)
    where descriptor != null
    select (byte[])tba.Invoke(sd.GetValue(descriptor.GetValue(null)), null)!
).Select(d =>
{
    FileDescriptorProto p = new();
    {
        using CodedInputStream s = new(d);
        s.ReadRawMessage(p);
    }
    return p;
});
foreach (var p in fds)
{
    FileInfo fi = new(p.Name);
    fi.Directory?.Create();
    using var file = fi.Create();
    using var writer = new StreamWriter(file, Encoding.ASCII) { NewLine = "\n" };
    var isProto3 = false;
    if (p.HasSyntax)
    {
        if (p.Syntax == "proto3")
        {
            isProto3 = true;
        }
        writer.Write($"syntax = \"{p.Syntax}\";\n\n");
    }
    if (p.HasPackage)
    {
        writer.Write($"package {p.Package};\n\n");
    }
    foreach (var import in p.Dependency)
    {
        writer.Write($"import \"{import}\";\n");
    }
    writer.Write('\n');
    void WriteBoolOption(string name, bool value, int level = 0)
    {
        writer.Write($"{new string(' ', level * 2)}option {name} = {(value ? "true" : "false")};\n");
    }
    void WriteStringOption(string name, string value, int level = 0)
    {
        writer.Write($"{new string(' ', level * 2)}option {name} = \"{value}\";\n");
    }
    if (p.Options.HasCcEnableArenas)
    {
        WriteBoolOption("cc_enable_arenas", p.Options.CcEnableArenas);
    }
    if (p.Options.HasObjcClassPrefix)
    {
        WriteStringOption("object_class_prefix", p.Options.ObjcClassPrefix);
    }
    if (p.Options.HasCsharpNamespace)
    {
        WriteStringOption("csharp_namespace", p.Options.CsharpNamespace);
    }
    if (p.Options.HasSwiftPrefix)
    {
        WriteStringOption("swift_prefix", p.Options.SwiftPrefix);
    }
    if (p.Options.HasPhpClassPrefix)
    {
        WriteStringOption("php_class_prefix", p.Options.PhpClassPrefix);
    }
    if (p.Options.HasPhpNamespace)
    {
        WriteStringOption("php_namespace", p.Options.PhpNamespace);
    }
    if (p.Options.HasPhpMetadataNamespace)
    {
        WriteStringOption("php_metadata_namespace", p.Options.PhpMetadataNamespace);
    }
    if (p.Options.HasRubyPackage)
    {
        WriteStringOption("ruby_package", p.Options.RubyPackage);
    }
    void WriteMessageOptions(MessageOptions options, int level)
    {
        if (options.HasMessageSetWireFormat)
        {
            WriteBoolOption("message_set_wire_format", options.MessageSetWireFormat, level);
        }
        if (options.HasNoStandardDescriptorAccessor)
        {
            WriteBoolOption("no_standard_descriptor_accessor", options.NoStandardDescriptorAccessor, level);
        }
        if (options.HasDeprecated)
        {
            WriteBoolOption("deprecated", options.Deprecated, level);
        }
    }
    void WriteFields(IEnumerable<FieldDescriptorProto> fs, int level)
    {
        foreach (var field in fs)
        {
            writer.Write(new string(' ', level * 2));
            if (field is { Options: { HasDeprecated: true, Deprecated: true } })
            {
                writer.Write("deprecated ");
            }
            if (field.HasLabel)
            {
                if (field.Label switch
                {
                    FieldDescriptorProto.Types.Label.Optional => isProto3 ? null : "optional ",
                    FieldDescriptorProto.Types.Label.Required => "required ",
                    FieldDescriptorProto.Types.Label.Repeated => "repeated ",
                    _ => throw new UnreachableException()
                } is { } label)
                {
                    writer.Write(label);
                }
            }
            writer.Write(field.HasTypeName ? field.TypeName : field.Type switch
            {
                FieldDescriptorProto.Types.Type.Double => "double",
                FieldDescriptorProto.Types.Type.Float => "float",
                FieldDescriptorProto.Types.Type.Int64 => "int64",
                FieldDescriptorProto.Types.Type.Uint64 => "uint64",
                FieldDescriptorProto.Types.Type.Int32 => "int32",
                FieldDescriptorProto.Types.Type.Fixed64 => "fixed64",
                FieldDescriptorProto.Types.Type.Fixed32 => "fixed32",
                FieldDescriptorProto.Types.Type.Bool => "bool",
                FieldDescriptorProto.Types.Type.String => "string",
                FieldDescriptorProto.Types.Type.Group => "group",
                FieldDescriptorProto.Types.Type.Message => "message",
                FieldDescriptorProto.Types.Type.Bytes => "bytes",
                FieldDescriptorProto.Types.Type.Uint32 => "uint32",
                FieldDescriptorProto.Types.Type.Enum => "enum",
                FieldDescriptorProto.Types.Type.Sfixed32 => "sfixed32",
                FieldDescriptorProto.Types.Type.Sfixed64 => "sfixed64",
                FieldDescriptorProto.Types.Type.Sint32 => "sint32",
                FieldDescriptorProto.Types.Type.Sint64 => "sint64",
                _ => "unknown"
            });
            writer.Write(' ');
            if (field.HasName)
            {
                writer.Write(field.Name);
                writer.Write(' ');
            }
            if (field.HasNumber)
            {
                writer.Write("= ");
                writer.Write(field.Number);
            }
            // TODO: default value and JSON name
            writer.Write(";\n");
        }
    }
    void WriteMessage(RepeatedField<DescriptorProto> ds, int level = 0)
    {
        foreach (var message in ds)
        {
            writer.Write($"\n{new string(' ', level * 2)}message ");
            if (message.HasName)
            {
                writer.Write(message.Name);
                writer.Write(" ");
            }
            writer.Write("{\n");
            WriteFields(message.Field.Where(f => !f.HasOneofIndex), level + 1);
            // TODO: extension
            WriteMessage(message.NestedType, level + 1);
            foreach (var e in message.EnumType)
            {
                writer.Write($"\n{new string(' ', (level + 1) * 2)}enum ");
                if (e.HasName)
                {
                    writer.Write(e.Name);
                    writer.Write(' ');
                }
                writer.Write("{\n");
                foreach (var v in e.Value)
                {
                    writer.Write(new string(' ', (level + 2) * 2));
                    if (v.HasName)
                    {
                        writer.Write(v.Name);
                    }
                    if (v.HasNumber)
                    {
                        writer.Write($" = {v.Number};\n");
                    }
                }
                foreach (var r in e.ReservedRange)
                {
                    writer.Write($"{new string(' ', (level + 2) * 2)}reserved ");
                    if (r.HasStart)
                    {
                        writer.Write(r.Start);
                    }
                    if (r.HasStart && r.HasEnd)
                    {
                        writer.Write(" to ");
                    }
                    if (r.HasEnd)
                    {
                        writer.Write(r.End);
                    }
                    writer.Write(";\n");
                }
                foreach (var r in e.ReservedName)
                {
                    writer.Write($"{new string(' ', (level + 2) * 2)}reserved \"{r}\";\n");
                }
                writer.Write($"{new string(' ', (level + 1) * 2)}}}\n");
            }
            // TODO: extension range
            foreach (var p in message.OneofDecl
                .Select((o, i) => new KeyValuePair<string?, IEnumerable<FieldDescriptorProto>>(o.HasName ? o.Name : null, message.Field
                    .Where(f => f.HasOneofIndex && f.OneofIndex == i))))
            {
                writer.Write($"\n{new string(' ', (level + 1) * 2)}oneof ");
                if (p.Key is { } name)
                {
                    writer.Write(name);
                    writer.Write(' ');
                }
                writer.Write("{\n");
                WriteFields(p.Value, level + 2);
                writer.Write($"{new string(' ', (level + 1) * 2)}}}\n");
            }
            if (message.Options is { } options)
            {
                WriteMessageOptions(options, level + 1);
            }
            foreach (var r in message.ReservedRange)
            {
                writer.Write($"{new string(' ', (level + 1) * 2)}reserved ");
                if (r.HasStart)
                {
                    writer.Write(r.Start);
                }
                if (r.HasStart && r.HasEnd)
                {
                    writer.Write(" to ");
                }
                if (r.HasEnd)
                {
                    writer.Write(r.End);
                }
                writer.Write(";\n");
            }
            foreach (var r in message.ReservedName)
            {
                writer.Write($"{new string(' ', (level + 1) * 2)}reserved \"{r}\";\n");
            }
            writer.Write($"{new string(' ', level * 2)}}}\n");
        }
    }
    WriteMessage(p.MessageType);
}
```

## Background
One of the things I really loved about ProPresenter 5 and 6 was how easy it was to read and understand the documents and configuration files - both of which are XML. I loved being able to make scripts and programs that could help improve and automate our workflow by working directly with Pro6 documents and also perform my own troubleshooting.

When ProPresenter7 first came out I was a bit surprised to find that the files were no longer easy-to-understand, human readable XML files.  
Instead, ProPresenter 7 uses Google "Protocol Buffers" to encode data stored in documents and various configuration files.  
(See https://developers.google.com/protocol-buffers for more information.)

Even though the new files encoded with protocol buffers are not human readable, one of the nice things about using protocol buffers is how easy it is to auto-generate code that to work with the files once you have the data structure (messages) defined in human-readable .proto files.  

**✅ If you have the .proto files, you have everything you need to create, read and update the data files.**

That's what you will find in this repository - a set of unofficial .proto files, *that I have reverse-engineered*, which you can use to *auto-generate code* that will enable you to read, create and modify ProPresenter 7 data files including:  

  * Documents
  * PlayLists
  * Workspace
  * Screens
  * Calendar
  * Masks
  * Props
  * Live Video
  * Looks
  * Recordings
  
  
## WARNING: These are unofficial, reverse-engineered .proto files created by myself as a user of ProPresenter.  
__They are NOT created, endorsed or supported by the makers of ProPresenter: Renewed Vision.__ 

In addition to the "usual disclaimers" that should obviously apply, please note the following:
* _If you don't understand what you are doing with these files or you don't have a proven backup and recovery process, you may end up destroying your ProPresenter documents and configuration with no way to recover what you have lost._
* __⚠️ Do NOT contact Renewed Vision for support!__
* _Who knows what else could go wrong if you don't understand how to correctly use these files - You might destroy the computer itself, and all other computers on your network (maybe even the Pastor's Macbook). It's possible you might even be responsible for the destruction of millions of other computers around the world and cause the building you are in to burn down. All kidding aside - be careful when changing ProPresenter files - mistakes can crash/break the application_  
* __⚠️ Do NOT contact Renewed Vision for support!__
  
  
### Well that should cover it.  
Hopefully you understand what you are doing with these file, and have __multiple, good backups__ of your entire Pro7 configuration and documents and are ready to take full responsibility for your actions/experiments as you now have the power to auto-generate code that can read, write and modify ProPresenter 7 data files!


#### Example of using with protoc to decode ProPresenter data files.
In addition to using protoc to auto-generate code for handling ProPresenter data files you can _also_ use protoc to decode data files and output a (JSON-like) text representation of the files:

1. Download protoc command for your system.
2. Download these .proto files to a folder
3. Open terminal/console and change to the folder containing the .proto files
4. To decode a ProPresenter presentation document run:  
`protoc --decode rv.data.Presentation ./propresenter.proto < /Path/To/ProPresenter/PresentationFile`
5. To decode a playlist file, run:  
`protoc --decode rv.data.PlaylistDocument ./propresenter.proto < /Path/To/ProPresenter/PlaylistFile`

You can find more info/example at my blog:
https://greyshirtguy.com/blog/propresenter-7-file-format-part-2/
