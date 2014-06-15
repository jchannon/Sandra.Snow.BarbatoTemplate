---
layout: post
title: Nancy, ASP.Net vNext, VS2014 & Azure
category: nancyfx,oss,.net,community,owin
---
By now we know of Microsoft's plans for the next version of ASP.Net and they've turned it on its head and from the looks of it, its goooooood!

[Here][1] is a blog post from Scott Hanselman introducing ASP.Net vNext. There are introductory and deep dive videos available for your perusal which are also well worth a watch.

The TL;DR is ASP.Net vNext will take heavy influence from Node.js by using Owin to wire up all the app dependencies and middleware.  It will also remove *.csproj files and use a project.json file similar to Node's package.json and use NuGet to reference the application's dependencies.  It also takes inspiration from Node and Nancy's approach requiring you to opt-in to dependencies rather that traditionally having everything but the kitchen sink.  It also takes influence from Nancy via built in dependency injection and Mono support.  Microsoft announced they will run all their vNext tests against Mono builds ensuring all their code is compatible for cross platform deployments.

Here's a tweet direct from the horses mouth albeit with a typo .

![vNext influenced by Node/Nancy][2]

<!--excerpt-->

I also believe the guys at Microsoft are looking at developing an unmanaged code HTTP server to run on Mono to produce ridiculous performance which is great news!

###Azure
As you'll see from the videos vNext runs via the command prompt which is fine but obviously Microsoft would want to bake all the NuGet and other tooling features into Visual Studio.  A few weeks after the initial vNext details were made public Microsoft announced a pre-release of Visual Studio which had support for ASP.Net vNext.  They also [announced][3] that Azure had a virtual machine in its gallery that had this version of Visual Studio installed.

What this meant was that you could setup a VM in Azure, then use Remote Desktop to connect to the virtual machine and run Visual Studio and have a play with all the new shiny things (they also provided the ISO so you could download it and run it locally if you preferred, pretty cool huh!).

After you follow the [above steps][3] in the blog post announcing the Visual Studio release you'll see in your Azure dashboard that you can connect to the virtual machine. 

![Coonect to VM][4]

Clicking "connect" will download the information about your virtual machine and Remote Desktop can then use it to connect to it.  As a Mac user I was able to use the OSX Microsoft Remote Desktop app and connect to my Windows virtual machine in the cloud.  This was amazing.  Once connected, on your desktop you'll see a shortcut to Visual Studio.  When you click File - New Project you'll see the vNext project types.

![New vNext project][5]

As you can see there are similar options to what we've had in the past.  Creating a vNext Web Application will setup an application that has MVC and Entity Framework all wired up for you.  This is a blog post about Nancy and vNext so we'll choose `ASP.NET vNext Empty Web Application`.  The goood news is that choosing this option will actually give is an "empty" application whereas in the past this option still contained 17 references.

![Empty application][6]

Microsoft have also [produced][8] a "Getting Started with ASP.NET vNext and Visual Studio 14" guide if you want some more reading material.

###Nancy and vNext

As Nancy supported Owin from the very beginning unlike MVC there was a Nancy.Owin package that you could opt-in to your application and use Nancy in an Owin based application.  This also gave you the option to use Nancy in a non-Owin application.  Today you still have that option but as of June 10th 2014 the Nancy team decided to merge a pull request moving Owin as a separate package into the core code base.  This pull request also added extensions to be able to run Nancy in a ASP.Net vNext project.  You can still run Nancy in a non-Owin application but the decision was made to embrace Owin as a first class citizen.  These changes are currently in the master branch and not yet part of an official release but to save building the source and referencing the project we can use Nancy's [nightly builds][10]. 

In our `ASP.NET vNext Empty Web Application` we can open up the Package Manager Console and run the following commands

    Install-Package DiscoverPackageSources
    Discover-PackageSources -Url "https://www.myget.org/F/nancyfx/"
    
Now open up `project.json` and under the dependency node add the below and save the file.

    "Microsoft.AspNet.Owin": "0.1-alpha-*",
    "Nancy": "0.23-Pre1387"
    
You'll notice in the Solution Explorer under References that these two references have magically appeared.

Open `Startup.cs` and make it look like
    
    namespace WebApplication3
    {
        using Microsoft.AspNet.Builder;
        using Nancy.Owin;
        
        public class Startup
        {
            public void Configure(IBuilder app)
            {
                app.UseOwin(x => x.UseNancy());
            }
        }
    }

Now create a class called HomeModule and make it look like this:

namespace WebApplication3
{
    using Nancy;
    
    public class HomeModule : NancyModule
    {
	    public HomeModule()
	    {
            Get["/"] = _ => "Hello World from Nancy in ASP.Net vNext";
	    }
    }
}

Hit F5 and ****boom****

![Nancy running on vNext][7]

###Conclusion

ASP.Net vNext has some great changes in terms of solution architecture and developer productivity (see editing a file, pressing save, hitting F5 and the changes appearing not needing a re-compile).  Microsoft's embracing of Mono I hope will lead to a true cross platform development stack we can all work on.  ASP.Net vNext has been influenced by other major players in the OSS world and Microsoft has realised there are some great features that the community have provided for a long time now so Node, Go and Nancy should be flattered.  Its also great to see vNext on Github where poeple can see clearly the commits, issues and pull requests open for ASP.Net.  My personal belief and not an official Nancy statement is that Nancy still offers many great features that MVC and WebAPI (or I should call that MVC6 now that they've merged) doesn't but I think what Microsoft has produced in terms of vNext is awesome work and the features of MVC and new middleware can only add to the options available for .Net developers to choose from and having this choice is what its all about.  Good work Microsoft!

###One more thing!

The current release of Nancy is 0.23 and Nancy has been stable for a long time and is used in production for many users yet there are numerous requests for when Nancy will release a v1.0.  The response is usually "why will renaming 0.23 to v1.0 make any difference to the functionality within Nancy" mainly because its felt this is some psychological issue people have regarding version numbers.  I could do File - New Project and make it v1.0 and it could be completely unstable but does that give the perception that its more feature rich than something that is 0.23?  I think its an interesting topic how people perceive version  numbers but that discussion is for another day.  As of June 10th 2014 the Nancy team agreed that the next version of Nancy will not be 0.24 but in fact be v1.0.0.  There are more finer details about the v1.0 release which [TheCodeJunkie][9] will be blogging about soon but hopefully this release will attract more people to use Nancy that may have been put off by the version number in the past.

The future of .Net development is looking good. Go out and code up a storm =)

  [1]: http://www.hanselman.com/blog/IntroducingASPNETVNext.aspx
  [2]: http://i.imgur.com/XMmMDce.png
  [3]: http://blogs.msdn.com/b/visualstudioalm/archive/2014/06/04/visual-studio-14-ctp-now-available-in-the-virtual-machine-azure-gallery.aspx
  [4]: http://i.imgur.com/R6QFSjY.png
  [5]: http://i.imgur.com/9hINknn.png
  [6]: http://i.imgur.com/Npw77Ar.png
  [7]: http://i.imgur.com/dkf2HF0.png
  [8]: http://www.asp.net/vnext/overview/aspnet-vnext/getting-started-with-aspnet-vnext-and-visual-studio
  [9]: http://twitter.com/thecodejunkie
  [10]: https://www.myget.org/F/nancyfx/