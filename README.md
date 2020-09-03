# ProPresenter7-Proto

One of the things I really loved about ProPresenter 5 and 6 was how easy it was to read and understand the documents (and configuration) files. I loved being able to make scripts and programs that could help improve and automate our workflow by working directly with Pro6 documents.

When Pro7 came out I was a bit surprised to find that the files were no longer easy-to-understand, human readable files.  
It turns out that ProPresenter 7 uses Google Protocol Buffers to store document and various configuration files.  
See https://developers.google.com/protocol-buffers

Even though they are not human readable, one of the nice things about protocol-buffers is how easy it is to auto-generate code that can read/write/modify the files once you have the data structure (messages) defined in human-readable .proto files.  If you have the .proto files, you have everything you need to fully work with the files.

This is a set of ***unsupported, reverse-engineered*** .proto files that define the messages (data structures) used in the ProPresenter 7 files that are stored as Google Protocol Buffers.

Using these .proto files it is very simple to auto-generate code which you can add to your applications and/or scripts that will enable you to read, create and modify ProPresenter 7 data files including:
  * Documents
  * PlayLists
  * (TODO: all other protobuf definitions like workspace, stage, CCLI etc etc)
  
## WARNING: These are unsupported, reverse-engineered files created by a fan user.  
__They are NOT created, endorsed or supported by the makers of ProPresenter: RenewedVision.__ 

In addition to the "usual disclaimers" that should obviously apply, please note the following:
* __Do NOT contact Renewed Vision for support!__
* _If you don't understand what you are doing and/or you don't have a brilliant backup and disaster-recovery processes, you may end up destroying your ProPresenter documents and configuration. Do not even try to use these proto files - if you don't know what you are doing_
* __Do NOT contact Renewed Vision for support!__
* _You might also destroy the computer itself, and all other computers on the your network (including the Pastors Macbook)._
* __Do NOT contact Renewed Vision for support!__
* _It's possible you might even be responsible for the destruction of millions of other computers around the world if your network is connected to the Internet._  
* __Do NOT contact Renewed Vision for support!__
  
  
### Well that should cover it.  
If you understand what you are doing, and have __multiple, good backups__ of your entire Pro7 configuration and documents......  
You now have the power to auto-generate code that can read, write and modify ProPresenter 7 data files - go ahead and knock yourself out!
