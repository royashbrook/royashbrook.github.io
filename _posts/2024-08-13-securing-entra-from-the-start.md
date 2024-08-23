---
layout: post
published: false
title: securing entra from the start
date: '2024-08-13'
---

i recently switched my domain over to a new microsoft tenant and began getting emails from entra for things i need to secure. i thought, as i just have a single account with many aliases, i would look at all of the recommendations that were made out of the gate. first, a little background...

# but why tho?

i have been using ms365 for some time for ashbrook.io. however, recently, i decided to switch from having a tenant directly with microsoft to securing this domain via a license with godaddy. this simplified some things for me as the tooling on godaddy was fine for my needs. i'm not really an email reading person for my personal emails, usually i am only going in and looking for something that i will actually need. i noticed the other day when i was resetting up this email in apple mail, i had a lot of messages to download:

<img width="189" alt="image" src="https://github.com/user-attachments/assets/5c42f545-03dd-40b4-a600-925ebdf10955">

that's a lot of emails, but is multiple accounts and some of these email accounts go back to the mid 90s, so i guess i don't empty my trash too often. =)

anyway, because i was setting up email, i happened to be looking at my inbox, and i noticed i had these emails from entra to improve security

<img width="232" alt="image" src="https://github.com/user-attachments/assets/801c1367-d47b-4ba8-8ae2-c85d9a1f1386">

as i have the most basic of setups using a built-in email only 365 license via godaddy, i thought 'hey, let's check out the recommendations and fix it up' after all, how hard could it be =)

# but what to do?

so, here are my recommendations...

<img width="1378" alt="image" src="https://github.com/user-attachments/assets/a54153e0-5259-45e6-982f-665af71df9fe">

what even are these things and why do i even care? and what is this score?

first of all, i checked the score. i just clicked on the view score and now i get even more info!

<img width="1311" alt="image" src="https://github.com/user-attachments/assets/ef0514a8-0106-44be-8bc3-de387b65de36">

ok, so all of the items on this page say they are identity risks. why are these numbers different? I'm not sure. let's go ahead and start addressing some things.

so.... for some reason, in microsoft defender, when i click on the recommendations to see them all, the page just spins and spins. who knows why. i'm not going to submit a ticket on it yet, let's head back to entra and see if we can fix it there.

# fix the score

i'm going to start with the items that have the highest score first and work my way down. i also see on eitem seems to need a p2 license. i'm not sure if i can do that one, but will give it a try after i do all of the 'free' licenses ones.

## mfa... where we're going we don't need mfa!

<img width="1374" alt="image" src="https://github.com/user-attachments/assets/83bd47ad-af48-4c0d-b1f7-9234ae2a5251">

actually... we do want mfa. and what's more, we *do* have mfa as i already have microsoft authenticator enabled for my logins here... odd. so i click on the mfa wizard link, and unfortunately this just takes me to godaddy's admin panel. so, i try going to admin>security settings and this mostly brings me to just more help article links. i guess let's go down the rabbit hole some more.

<img width="1031" alt="image" src="https://github.com/user-attachments/assets/d8f3de52-7f84-4c3b-91f6-533fadb42b73">

i just click on 'manage' and i get this window

<img width="735" alt="image" src="https://github.com/user-attachments/assets/b64d7c0c-d68b-4ea6-a309-11a738cefbb3">

and now.... it's done? i guess.

<img width="732" alt="image" src="https://github.com/user-attachments/assets/52d47d07-321e-4be5-8223-6324c540e91e">

i can't see that anything is done and i already had mfa enabled so... maybe this is just a suggestion list? i guess let's move onto the next one.

## no more legacy

<img width="1372" alt="image" src="https://github.com/user-attachments/assets/bc64d0eb-7e95-46b5-885d-53ba9ffdb4a1">

so next we are blocking legacy auth. i followed some of the links and they just seemed to go to the same place. fine, let's maybe revisit that security page on godaddy. i'll try this link next.

<img width="1034" alt="image" src="https://github.com/user-attachments/assets/79f989bb-ea65-443d-bb06-c4482a0b57e9">

this took me to the following page, which _did_ actually take me to a page where i could enable mfa on a user!

https://www.godaddy.com/help/enable-or-disable-multi-factor-authentication-32039?isc=appemail40&utm_source=o365&utm_medium=applications&utm_campaign=en-us_corp_applications_base

i ended up on a page that looks kind of like this and was able to enable mfa on each account

<img width="237" alt="image" src="https://github.com/user-attachments/assets/60bbdfbb-62a2-4122-b122-5011af006bae">

i was able to select both, and there was a way to bulk enable

<img width="215" alt="image" src="https://github.com/user-attachments/assets/520ef2ea-c660-4205-b751-613ec8c68751">

so now mfa is on... again... i think? it shows enabled for the accounts anyway, so back to the legacy. i still can't find a 'setting' for this as i keep getting taken to godaddy pages, not the normal admin pages i expect and had when i had a full normal tenant. 

then i noticed on the recommendation page it says i can just enable security defaults as an action plan.... well i did that earlier in the goaddy portal, maybe i can follow this link?

<img width="1369" alt="image" src="https://github.com/user-attachments/assets/1034663b-a7ca-4840-8fc2-e97ec14202e8">

this link takes me to an ms page here:

https://learn.microsoft.com/en-us/entra/fundamentals/security-defaults#enabling-security-defaults

looks like this is just a setting in the entra settings.

sure enough, if i go to properties, i do not have security defaults enabled.

<img width="401" alt="image" src="https://github.com/user-attachments/assets/4ce0098e-7fc4-4451-b058-dd0067b9e2be">

so i click on the link to manage and......

<img width="418" alt="image" src="https://github.com/user-attachments/assets/89ffbac0-0515-46df-b6d2-b28028b1b693">

ok... now i *did* enable security defaults in the godaddy management console. maybe i need to let the security thing have 24 hours to update? maybe there is a way to force the security to audit again? let's ask chatgpt...

<img width="817" alt="image" src="https://github.com/user-attachments/assets/76bbb3f5-5d9a-4da5-99cd-68b3bf98ad23">

seems straightforward. let's give it a whirl.

... well unfortunately, this seems bogus as i don't have this option. after some research i found multiple articles like this:

https://learn.microsoft.com/en-us/answers/questions/1119107/re-evaluate-security-recommendations

where people complained about this same issue. i decided to just look through all of the recommendations to see if there was a fix button for any of these, or if they all just said something like enable security defaults. unfortunately, to my surprise, now when i check the recommendation page on entra i see this:

<img width="483" alt="image" src="https://github.com/user-attachments/assets/42b8ce2b-423b-4ced-8476-04795a386fbc">

hmmm... seems i have protected the system from myself, lol. after logging out and in a few times and trying different paths to reopen (probably should have just closed the browser or something) i finally got the recommendations to pop back up. and even the org shows it is protected by security defaults now

<img width="353" alt="image" src="https://github.com/user-attachments/assets/c265dadf-6203-4200-b0a6-17930a061ef0">

fabulous. i 'think' maybe legacy was fixed with the security defaults. let's move on to some other ones.

# what could i fix?

- Protect all users with a user risk policy - p2 only, dismiss
- Protect all users with a sign-in risk policy - p2 only, dismiss
- Use least privileged administrative roles - it says i have 0 users that fit this, dismiss
- Designate more than one global admin - already done, see below...
- Enable password hash sync if hybrid - i'm not hybrid, dismiss
- Ensure all users can complete multifactor authentication - fixed as noted above
- Do not allow users to grant consent to unreliable applications - I just set this to admin only, fixed
- Enable policy to block legacy authentication - fixed as noted above

so.. one fun thing was i was seemingly unable to 'dismiss' anything. so above where i noted dismissed, i should note that every dismissal was greeted with this status message.

<img width="348" alt="image" src="https://github.com/user-attachments/assets/e879dfac-518f-46af-80bd-24b56fe5d3a3">

i was also not able to 'postpone' a recommendation and got the same thing. some of these required a p2 license so i can't do them, but seems there is no way to clear them.

for 'more than one admin' this one was recommended, but was already done. for some reason i was able to dismiss this one and just put 'other' and note it's already complete and it did dismiss... i'm not sure i'm clear on why these dismissals are and aren't working. i also can't seem to be able to 'complete' things but maybe they will auto complete over time? 

i think i'll pause here and maybe check back in 24 hours to see if anything has changed once the security audit can check again...

<img width="238" alt="image" src="https://github.com/user-attachments/assets/6cf4ce7d-933f-4b8b-9d32-1245b75c4b4f">

# the next day...

so i came back the next day and it was not changed, so i thought maybe i'll give it a bit longer and take a look...

i actually ended up waiting about a week to come back as the security center also was laggy for some reason too. i *still* was not able to actually 'resolve' these items in entra, but the security center gave me a way to resolve some of these items and accept some of the risks. in this case, i only have one global admin because i only have one account, so, for example, i accepted this risk:

<img width="1400" alt="image" src="https://github.com/user-attachments/assets/eb9e53d2-187b-4128-b005-11c3c81fd568">

since the security center seems to be responsive this time around, it seems this is actually easier to handle in there. here is how i finished off this lates round.

<img width="904" alt="image" src="https://github.com/user-attachments/assets/4b1a93cf-9e2f-47b8-a330-32dbe06cee49">

since I am using godaddy, it seems like some of the stuff from their control center doesn't make it's way back to the ms area. but when you go to do some things like edit mfa, it kicks you to their control center. i have mfa enabled, but it doesn't see it, along with security defaults enabled. so i marked those as resolved by a third party. i accepted a few risks. and you can see one is still not resolved. this admin consent one. i just marked everything as admin consent required, but that seemed to never resolve it, so i set it to a limited view own profile with user consent and will give it a day to see if that works.







