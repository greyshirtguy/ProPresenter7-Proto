# ProPresenter7-Proto

This is a set of ***unoffical and unsupported*** .proto files that define the messages (data structures) used in the ProPresenter 7 files that are encoded as Google Protocol Buffers.

## Background
One of the things I really loved about ProPresenter 5 and 6 was how easy it was to read and understand the documents and configuration files - both of which are XML. I loved being able to make scripts and programs that could help improve and automate our workflow by working directly with Pro6 documents and also perform my own troubleshooting.

When ProPresenter7 first came out I was a bit surprised to find that the files were no longer easy-to-understand, human readable XML files.  
Instead, ProPresenter 7 uses Google "Protocol Buffers" to encode data stored in documents and various configuration files.  
(See https://developers.google.com/protocol-buffers for more information.)

Even though the new files encoded with protocol buffers are not human readable, one of the nice things about using protocol buffers is how easy it is to auto-generate code that to work with the files once you have the data structure (messages) defined in human-readable .proto files.  If you have the .proto files, you have everything you need to create, read and update the data files.

That's what you will find in this repository - a set of unofficial .proto files, *that I have reverse-engineered*, which you can use to *auto-generate code* that will enable you to read, create and modify ProPresenter 7 data files including:  

  * ProPresenter7 Documents
  * ProPresenter7 PlayLists
  * (TODO: Other protobuf definitions like workspace, stage, CCLI etc etc)
  
  
## WARNING: These are unofficial, reverse-engineered .proto files created by myself as a user of ProPresenter.  
__They are NOT created, endorsed or supported by the makers of ProPresenter: Renewed Vision.__ 

In addition to the "usual disclaimers" that should obviously apply, please note the following:
* _If you don't understand what you are doing with these files or you don't have a proven backup and recovery process, you may end up destroying your ProPresenter documents and configuration with no way to recover what you have lost._
* __Do NOT contact Renewed Vision for support!__
* _You might also destroy the computer itself, and all other computers on the your network (including the Pastor's Macbook)._
* __Do NOT contact Renewed Vision for support!__
* _It's possible you might even be responsible for the destruction of millions of other computers around the world and cause the building you are in to burn down._  
* __Do NOT contact Renewed Vision for support!__
  
  
### Well that should cover it.  
Hopefully you understand what you are doing with these file, and have __multiple, good backups__ of your entire Pro7 configuration and documents and are ready to take full responsibility for your actions/experiments as you now have the power to auto-generate code that can read, write and modify ProPresenter 7 data files!
