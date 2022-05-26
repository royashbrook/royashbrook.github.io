---
layout: post
published: true
title: Migrate users from ASP.NET Forms-based Auth to ASP.NET Core Identity
subtitle: How old is this app again?
date: '2020-06-30'
image: /img/mummy_head.jpg
---
Do you have a very old ASP forms based app? Do you want to run `dotnet new webapp` and migrate your pages, but you don't know how you'll simply move the users over to the new world? Well, let me give you a solution *if* you are moving over to ASP.Net Core Identity. If you don't know what that is, think about it like new forms auth, I suppose.

We're going from [this](https://support.microsoft.com/en-us/help/308157/how-to-implement-forms-based-authentication-in-your-asp-net-applicatio) to [this](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-3.1&tabs=netcore-cli).

At least that's what I'm going to cover. Basically the idea of having a brand new site, but I add all of my existing users to the backend. No re-registration, no changes for the users if you can change the app behavior to match. Just easy.

You can check the second link up above to see what the end table looks like, but for now I'm going to just talk about the rest of this situation like you have an old app and plain text username/password pairs.

## Let's begin

Short version of this story is I wrote an app to generate the hashes I needed (based on the source from dotnetcore on github) and then just injected guids into the other fields that needed information. Probably not perfect, but it seems to work fine.

Here are the sources for the relevant code from dotnet core identity:
- https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs
- https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasherOptions.cs

I am going to tweak these a bit for our purposes. First of all, I want to skip the options, secondly I don't want to have to pass in a user, I just want to put in a password and get the pass.

So let's make an app that does that. I'll creatively call it 'h' for hasher. You can call it whatever you want though, just change 'h' below to something else. =)

{% gist e708a7969084345069df4a43b3229567 InitialProjectSetup.ps1 %}

Now we need to make some tweaks to the files. First of all, we'll just copy, paste the following code into the program.cs. Again, replace the namespace with h if you named it something else. You can also tweak this if you want to some other structure, but this is what I went with.

{% gist e708a7969084345069df4a43b3229567 Program.cs %}

Now we need to update the PasswordHasher.cs file to remove the need for a user to be pushed in. This only requires a few minor changes:

{% gist e708a7969084345069df4a43b3229567 PasswordHasherChanges.cs %}

That's it. Basically we are just avoiding the use of the options class and removing dependency on the user object. I believe in my reading I found that there was a convention/reason to keep that in the interface, but it's not actually used inside this class at all, so simple to remove so we can use the same hashing mechanism.

Now we can build and run it. Here's some powershell CLI output for this:

{% gist e708a7969084345069df4a43b3229567 Output.txt %}

This program can now be [compiled into a single file](https://dotnetcoretutorials.com/2019/06/20/publishing-a-single-exe-file-in-net-core-3-0/) (if you want) using `dotnet publish -r win-x64 -c Release /p:PublishSingleFile=true` if you want. That's what I did. At a minimum, i'd recommend building it in release mode as that makes it run much faster.

I went ahead and put this code [here](https://github.com/royashbrook/CLI-PasswordHasher), but you can just follow the steps above to make it yourself.

## ok, so what now?

OK, well at this point, I have to assume a few things. I have to assume that:
- You already have a `dotnet new webapp` with the identity added
- You have a database setup to support that
- You know a little bit about powershell and editing files
- You know a little bit about SQL, at least to run some commands

I'm not going to go over setting up the webapp, but I already linked to the setup up top.

So!!!! Since we have a database setup that we want to inject our users into, we just need a list of users and passwords and a way to generate our sql commands. I'm just going to use powershell for this. So we will have a txt file called users.txt that looks something like this. I'm using a tab seperated file just to make things easy to ready, but you could use a regular csv or whatever.

 
{% gist e708a7969084345069df4a43b3229567 GenerateSql.ps1 %}


## Profit...?

That is the bulk of it. If you *are* using usernames and not emails, you will have to modify your login pages to get rid of the email checks. This amounts to removing/commenting the `asp-validation-for="Input.Email"` line in `Areas\Identity\Pages\Account\Login.cshtml` and commenting out the `[EmailAddress]` attribute in `Areas\Identity\Pages\Account\Login.cshtml.cs`. That will make sure it works, but you'll also want to change the label in the Login.cshtml page as well to say username or something that is not 'email' I would think.


## Final Thoughts

This is *really* only meant to be a very temporary solution to get over a hump. An old app that you can make work the same, but you don't have a great way to coordinate a release with the users. You can a/b test it, you can validate it all fully, and you have no reason to think anyone will notice or that the communication will cause more issues than it is worth. To me closing the security holes and other issues with a 15 year old code base is worth it, but this will be followed up with an initiative to migrate to a PWA and get into a real CI/CD cycle so this can be maintained easier moving forward. I don't recommend doing this and then sticking with this.

But if you do **need** to stick with this a bit, you can [scaffold](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/scaffold-identity?view=aspnetcore-3.1&tabs=netcore-cli) the identity pages, and customize items or follow [this article](https://andyp.dev/posts/disable-user-registrations-in-asp-net-core-3-identity) to disable user registration. As mentioned there, you'll need a way to register apps, but if you are stuck in between this place and the next place, at least you can generate the hashes using this method and simply insert new users or build your own internal registration app.

## References

There were many references I looked at while I was working on this. Here are some of the ones I found most useful:
- [https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-3.1&tabs=netcore-cli](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-3.1&tabs=netcore-cli)
- [https://docs.microsoft.com/en-us/aspnet/core/security/authentication/scaffold-identity?view=aspnetcore-3.1&tabs=netcore-cli](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/scaffold-identity?view=aspnetcore-3.1&tabs=netcore-cli)
- [https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs)
- [https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasherOptions.cs](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasherOptions.cs)
- [https://andrewlock.net/exploring-the-asp-net-core-identity-passwordhasher/](https://andrewlock.net/exploring-the-asp-net-core-identity-passwordhasher/)
- [https://andrewlock.net/migrating-passwords-in-asp-net-core-identity-with-a-custom-passwordhasher/](https://andrewlock.net/migrating-passwords-in-asp-net-core-identity-with-a-custom-passwordhasher/)
- [https://kenhaggerty.com/articles/article/aspnet-core-31-password-hasher](https://kenhaggerty.com/articles/article/aspnet-core-31-password-hasher)
- [https://stackoverflow.com/questions/20621950/asp-net-identitys-default-password-hasher-how-does-it-work-and-is-it-secure](https://stackoverflow.com/questions/20621950/asp-net-identitys-default-password-hasher-how-does-it-work-and-is-it-secure)
- [https://docs.microsoft.com/en-us/aspnet/core/security/authorization/simple?view=aspnetcore-3.1](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/simple?view=aspnetcore-3.1)
- [https://andyp.dev/posts/disable-user-registrations-in-asp-net-core-3-identity](https://andyp.dev/posts/disable-user-registrations-in-asp-net-core-3-identity)
- [https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity-configuration?view=aspnetcore-3.1#password](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity-configuration?view=aspnetcore-3.1#password)
- [https://code-maze.com/identity-asp-net-core-project/](https://code-maze.com/identity-asp-net-core-project/)
- [https://docs.microsoft.com/en-us/aspnet/web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset](https://docs.microsoft.com/en-us/aspnet/web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset)
- [https://support.microsoft.com/en-us/help/308157/how-to-implement-forms-based-authentication-in-your-asp-net-applicatio](https://support.microsoft.com/en-us/help/308157/how-to-implement-forms-based-authentication-in-your-asp-net-applicatio)
- [https://dotnetcoretutorials.com/2019/06/20/publishing-a-single-exe-file-in-net-core-3-0/](https://dotnetcoretutorials.com/2019/06/20/publishing-a-single-exe-file-in-net-core-3-0/)
- [https://community.spiceworks.com/how_to/153476-how-to-discover-types-classes-in-powershell](https://community.spiceworks.com/how_to/153476-how-to-discover-types-classes-in-powershell)
- [https://jasonwatmore.com/post/2019/10/21/aspnet-core-3-basic-authentication-tutorial-with-example-api](https://jasonwatmore.com/post/2019/10/21/aspnet-core-3-basic-authentication-tutorial-with-example-api)

