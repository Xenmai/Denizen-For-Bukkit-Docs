=======================
Halloween Treasure Hunt
=======================

.. contents:: Table of Contents
    :local:

Introduction
============

Welcome back to the **Denizen-for-Bukkit Docs**. This is our first example project, so we'll start with the basics. 
This guide will cover the following topics:

* Denizen objects and general syntax.
* Browsing through the documentation.
* Reloading scripts and debug modes.
* World script containers, events and context tags.
* Common script commands and arguments.
* Constructing tags with simple bases and modifiers.
* First contact with foreach loops.

If you are not familiar with any of the concepts listed, we suggest revisiting the following tutorial documents:

* :doc:`Section 1.3 - dScript Format </docs/getting-started/dscript-format>`
* :doc:`Section 2.1 - An Introduction to Tags </docs/basics-of-scripting/an-introduction-to-tags>`
* :doc:`Section 2.2 - The Basics of Denizen Commands </docs/basics-of-scripting/basics-of-denizen-commands>`
* :doc:`Section 2.7 - Your First World Script </docs/basics-of-scripting/your-first-world-script>`
* foreach loops here

This will help you make a better use of this example project, and follow its contents successfully.

.. note::
    It is also recommended to have the `meta documentation <https://one.denizenscript.com/denizen/logs>`_ open in another tab while scripting this project, as we'll 
    need to check the syntax of events, commands and tags very often.

First Steps
===========

Now let's jump (or slowly crawl) into the scripting process. In our case, we want to make a **Halloween Treasure 
Hunt** event in our *Hub world*. We plan on manually placing some hidden pumpkins ourselves, and reward players for 
finding (and left clicking) them.

First of all, we'll need to create a *new script file* (for example ``Halloween_Treasure_Hunt.yml``) and build a world 
script container which, as we know, will listen to events happening within our server. We just have to give it a name, 
like ``Halloween_Treasure_Hunt``, and set the ``type:`` key to ``world``. In addition to this, we'll also enable debug 
by setting the ``debug:`` key to ``true``. This will make our script print in the console everything it does and help 
us solve errors. It is not needed, so we can just disable it later on. We'll now add an ``events:`` subkey, which will 
in the end hold the executable code we're going to write.

Our file with the script container looks like this:

.. code-block:: dscript
    :name: figure_1_1
    :linenos:
    :emphasize-lines: 1-4

    Halloween_Treasure_Hunt:
    type: world
    debug: true
    events:

.. rst-class:: figurecaption

**Figure 1.1** Our starting world script container

It's worth noting that Denizen scripts follow **YAML**'s indentation rules, with either *2 or 4-space tabs*. Many text 
editors can be used for writing these scripts, but we personally prefer **Notepad++**. You just have to make sure the 
tabs are replaced with actual spaces. There's a settings option in that automatically does that for us, so no problem.

Now we need to find an event that fits our case. Looking through the *event list* documentation, ``on player clicks 
block`` looks like our best bet. We just have to adjust it for our specific use case by adding *optional* arguments. 
According to the event syntax, it accepts a click type, a material and an area, which will just be ``left``, 
``pumpkin`` and ``in Hub`` respectively.

.. note::
    This tutorial assumes our *Hub world* is in fact called ``Hub``.

We'll just make sure it works for now, so we add it under the events key and end the line with a ``:``. Inside this 
event we can place our first command. A good option for testing purposes is just to show a word in *chat*. The command 
for this is :guilabel:`narrate`. As the documentation explains, its syntax is very simple, only requiring a message 
argument. We feel the hype, so we'll go with a ``- narrate "yay"``.

Our script should be the following at this point:

.. code-block:: dscript
    :name: figure_1_2
    :linenos:
    :emphasize-lines: 5,6

    Halloween_Treasure_Hunt:
    type: world
    debug: true
    events:
      on player left clicks pumpkin in Hub:
      - narrate "yay"

.. rst-class:: figurecaption

**Figure 1.2** Our world script with a specific event

It's time to *save* the script file, *reload* scripts ingame with ``/denizen reload scripts`` and *trigger the event* 
by left clicking a pumpkin block in our Hub world. We should now be able to see a cute little ``yay`` in chat, along 
with some debug information in the console, just as we expected. That's great, but we also have to *test* and make 
sure the event is not being triggered when clicking other types of blocks, when right clicking, or when clicking in 
another world.

Core Functionality
==================

We're ready to move further ahead and actually give a *reward* to the player clicking the block. Since we're nice 
server owners, the prize will be a free *diamond*. This is where the :guilabel:`give` command comes in handy. Its 
*syntax* specifies a single required argument: ``[money/xp/<item>|...]``. In our case, what we want to give the player 
is a diamond item, so we can ignore the *money* and *xp* options.

.. note::
    When reading command documentation, It's important to keep in mind that anything inside ``< >`` is *not literal* 
    and needs to be replaced. Arguments enclosed in ``[ ]`` are *required*, while ``( )`` means an argument is 
    *optional*. The ``/`` symbol separates argument *options*, meaning you have to choose one of them.

Let's go ahead and specify ``diamond`` as the first argument of our give command. We don't have to worry about who to 
give the diamond to, as the command will target the linked player by default. That is, the player that triggered the 
event. The full command line will then be ``- give diamond``.

Now it's time to make sure it works. After *saving* and *reloading* scripts again, it should be giving us a *diamond* 
every time we click the *pumpkin*. While players will totally love this, we should probably avoid giving out unlimited 
diamonds. That's easy to fix though, we just have to *remove* the pumpkin once it's clicked. If we do it before even 
giving out the reward, we'll make sure it won't be clicked twice. 

For this, we'll use the :guilabel:`modifyblock` command, which lets us specify a *location* and a *material*. Now we 
only need to know which location was clicked by the player. Time to make use of *context* tags! This kind of tags are 
event specific and will let us retrieve useful information from said event. If we check again the event's 
documentation, we can see it has a ``<context.location>`` tag available, which is just what we needed for the first 
argument. The material, on the other hand, will be just ``air`` as we want to remove the original pumpkin. The full 
command line will then be ``- modifyblock <context.location> air``.

Our script with these new commands should look like this:

.. code-block:: dscript
    :name: figure_1_3
    :linenos:
    :emphasize-lines: 7,8

    Halloween_Treasure_Hunt:
    type: world
    debug: true
    events:
      on player left clicks pumpkin in Hub:
      - narrate "yay"
      - modifyblock <context.location> air
      - give diamond

.. rst-class:: figurecaption

**Figure 1.3** Our world script with core functionality

Rinse and repeat: save, reload scripts and do a quick test. Amazing! This deserves a "yay". Speaking of yays… we don't 
need to narrate ``yay`` for testing purposes anymore, so we better change it to something more informative. Something 
like ``- narrate "You've found a pumpkin! Here's your reward!"`` sounds like the way to go.

Topping It Off
==============

Let's make it even more fun. What if *jack o' lanterns* gave a diamond to *every online player*? Yeah, we can make 
that happen too! Let's start by making a copy of the event we already have and its contents. We should now change the 
``pumpkin`` material of said event to ``jack_o_lantern``, so it's only triggered when clicking jack o' lantern blocks.

.. note::
    There are other ways to achieve this event logic. For example, we could use a single general event that triggers 
    for every block clicked, and filter the needed blocks with logic, such as **if/else if/else** trees or **choose** 
    commands. In this guide though, two separate events will be used as we feel that can help keep it simple without 
    losing functionality.

Inside the event, we need to repeat the give command once per player. How to do that? You've guessed it, a loop! In 
our case, to wrap the :guilabel:`give` command with a :guilabel:`foreach` loop is all we need. This loop takes a 
*list* when it starts and executes some commands for *every object* on the list. We just need to feed it the list of 
online players, which can be accessed through ``<server.list_online_players>``.

Inside the :guilabel:`foreach` command block, we can retrieve the currently *looped object* with ``<def[value]>``. 
We'll use this player object to tell the give command who to target. This can easily be done by setting the linked 
player of said command, possible thanks to the ``player:`` argument. Feed this argument the tag we've just mentioned 
and we're ready to go.

Here's the complete second event:

.. code-block:: dscript
    :name: figure_1_4
    :linenos:
    :emphasize-lines: 10-14

    Halloween_Treasure_Hunt:
    type: world
    debug: true
    events:
      on player left clicks pumpkin in Hub:
      - narrate "You've found a pumpkin! Here's your reward!"
      - modifyblock <context.location> air
      - give diamond
     
      on player left clicks jack_o_lantern in Hub:
      - narrate "You've found a pumpkin! Here's your reward!"
      - modifyblock <context.location> air
      - foreach <server.list_online_players>:
        - give diamond player:<def[value]>

.. rst-class:: figurecaption

**Figure 1.4** Our world script with a second event

We also have to let all the players know who their new *hero* is, and instead of narrating to them one by one, we can 
just announce the message to the whole server. According to the :guilabel:`announce` command syntax, it only requires one 
argument: the message. We just want to know the *name* of the player who found the hidden block , but that's not a 
problem at all. As we already know, all events related to players let you access their linked player with the 
``<player>`` tag. In our case, we need their actual name, so we will just add ``.name`` to the tag.

.. note::
    Double quotes (``" "``) are used to group text so it's treated as a *single argument*. This is specially useful for 
    commands based on chat text, such as :guilabel:`narrate` and :guilabel:`announce`.

Our command would be as easy as ``- announce "<player.name> has found a jack o' lantern. Everybody gets a reward!"``. 
We only have to replace the old narrate command in the second event with our new announce. Now we just have to make 
sure it *works as intended* after reloading, and finally set the ``debug:`` key to ``false`` so only error messages 
are shown. No more console *spam*!

Finally, this is the full script that we've created:

.. code-block:: dscript
    :name: figure_1_5
    :linenos:
    :emphasize-lines: 11

    Halloween_Treasure_Hunt:
    type: world
    debug: false
    events:
      on player left clicks pumpkin in Hub:
      - narrate "You've found a pumpkin! Here's your reward!"
      - modifyblock <context.location> air
      - give diamond
     
      on player left clicks jack_o_lantern in Hub:
      - announce "<player.name> has found a jack o' lantern. Everybody gets a reward!"
      - modifyblock <context.location> air
      - foreach <server.list_online_players>:
        - give diamond player:<def[value]>

.. rst-class:: figurecaption

**Figure 1.5** Our world script, finally complete

This should be it for now. Enjoy your brand new **Halloween Treasure Hunt** event and *happy scripting*!