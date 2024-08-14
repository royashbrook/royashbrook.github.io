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
