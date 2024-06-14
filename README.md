# ProPresenter7-Proto

This is a set of ***unoffical and unsupported*** .proto files that define the messages (data structures) used in the ProPresenter 7 files that are encoded as Google Protocol Buffers.

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

Update:
My original method of extracting the .protos was pretty clumsy - but I was happy that at least I found a way!  
_Now it's much easier to use https://github.com/arkadiyt/protodump on the ProPresenter binaries!_
