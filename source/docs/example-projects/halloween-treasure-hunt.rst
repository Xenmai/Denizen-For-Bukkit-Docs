==========================
1. Halloween Treasure Hunt
==========================

.. contents:: Table of Contents
    :local:

Introduction
============

In this first example project, we'll start with the basics. The following topics will be covered:

* Denizen objects and general syntax.
* Browsing through the documentation.
* Reloading scripts and debug modes.
* World script containers, events and context tags.
* Common script commands and arguments.
* Constructing tags with simple bases and modifiers.
* Basic usage of foreach loops.

If you are not familiar with any of the concepts listed, we suggest revisiting the following tutorial documents:

* :doc:`Section 1.3 - dScript Format </docs/getting-started/dscript-format>`
* :doc:`Section 2.1 - An Introduction to Tags </docs/basics-of-scripting/an-introduction-to-tags>`
* :doc:`Section 2.2 - The Basics of Denizen Commands </docs/basics-of-scripting/basics-of-denizen-commands>`
* :doc:`Section 2.7 - Your First World Script </docs/basics-of-scripting/your-first-world-script>`
* **[TODO: Add FOREACH section reference]**

This will help you make a better use of this example project, and follow its contents successfully.

.. note::
    It is also recommended to have the `meta documentation`__ open in another tab while scripting this project, as 
    we'll need to check the syntax of events, commands and tags very often.

.. __: https://one.denizenscript.com/denizen/logs
    
1. First Steps
==============

Now let's jump (or slowly crawl) into the scripting process. In our case, we want to make a **Halloween Treasure 
Hunt** event in our *Hub world*. We plan on manually placing some hidden pumpkins ourselves, and reward players for 
finding (and left clicking) them. First of all, we'll need to *create a new script file* (for example 
``halloween-treasure-hunt.yml``) and build a world  script container which, as we know, will listen to events 
happening within our server.

Our file with the script container looks like this:

.. code-block:: dscript
    :name: figureDIY_1_1
    :linenos:

    halloween_treasure_hunt:
    type: world
    events:

.. rst-class:: figurecaption

**Figure 1.1** Our starting world script container

Now we need to find an event that fits our case. Looking through the event list documentation, `on player clicks 
block`__ looks like the best match. We just have to adjust it for our specific use case by adding optional arguments. 
According to the event syntax, it accepts a click type, a material and an area switch, which will just be ``left``, 
``carved_pumpkin`` and ``in:Hub`` respectively.

.. __: https://one.denizenscript.com/denizen/evts/clicks%20block

.. note::
    This tutorial assumes our *Hub world* is in fact called ``Hub``.

We'll just make sure it works for now, so we add the event to our world script. Inside this event we can place our 
first command. A good option for testing purposes is just to show a word in chat, as well as checking out the console 
for debug information. The command for this is :guilabel:`narrate`. Its syntax is very simple, only requiring a 
message argument. We feel the hype, so we'll go with a ``- narrate "yay"``.

Our script should be the following at this point:

.. code-block:: dscript
    :name: figureDIY_1_2
    :linenos:
    :emphasize-lines: 4,5

    halloween_treasure_hunt:
    type: world
    events:
      on player left clicks carved_pumpkin in:Hub:
      - narrate "yay"

.. rst-class:: figurecaption

**Figure 1.2** Our world script with a specific event

It's time to save the script file, reload scripts ingame with ``/denizen reload scripts`` and trigger the event by 
left clicking a carved pumpkin block in our Hub world. We should now be able to see a cute little ``yay`` in chat, 
along with some debug information in the console, just as we expected. That's great, but you should also test and make 
sure the event is not being triggered when clicking other types of blocks, when right clicking, or when clicking in 
another world.

2. Core Functionality
=====================

We're ready to move further ahead and actually give a reward to the player clicking the block. Since we're nice server 
owners, the prize will be a free diamond. This is where the :guilabel:`give` command comes in handy. Its syntax 
specifies a single required argument: ``[money/xp/<item>|...]``. In our case, what we want to give the player is a 
diamond item, so we can ignore the *money* and *xp* options.

.. note::
    If you need help with reading the command documentation, refer to this link: `command syntax`__.

.. __: https://one.denizenscript.com/denizen/lngs/syntax".    
    
Let's go ahead and specify ``diamond`` as the first argument of our give command. We don't have to worry about who to 
give the diamond to, as the command will target the linked player by default. That is, the player that triggered the 
event. The full command line will then be ``- give diamond``.

Now it's time to make sure it works. After saving and reloading scripts again, it should be giving us a diamond *every 
time we click the carved pumpkin*. While players will totally love this, we should probably avoid giving out unlimited 
diamonds. That's easy to fix though, we just have to *remove the carved pumpkin once it's clicked*. If we do it before 
even giving out the reward, we'll make sure it won't be clicked twice. 

For this, we'll use the :guilabel:`modifyblock` command, which lets us specify a *location* and a *material*. Now we 
only need to know which location was clicked by the player. Time to make use of *context tags*! If you aren't familiar 
with them already, you should make sure to revisit the sections where they are covered, 
**[TODO: Add CONTEXT TAGS section reference]**. In this case we'll use a simple ``<context.location>`` tag, which is 
just what we needed for the first argument. The material, on the other hand, will be just ``air`` as we want to remove 
the original carved pumpkin. The full command line will then be ``- modifyblock <context.location> air``.

Our script with these new commands should look like this:

.. code-block:: dscript
    :name: figureDIY_1_3
    :linenos:
    :emphasize-lines: 6,7

    halloween_treasure_hunt:
    type: world
    events:
      on player left clicks carved_pumpkin in:Hub:
      - narrate "yay"
      - modifyblock <context.location> air
      - give diamond

.. rst-class:: figurecaption

**Figure 1.3** Our world script with core functionality

Rinse and repeat: save, reload scripts and do a quick test. Amazing! This deserves a "yay". Speaking of yays… we don't 
need to narrate ``yay`` for testing purposes anymore, so we better change it to something more informative. Something 
like ``- narrate "You've found a carved pumpkin! Here's your reward!"`` sounds like the way to go.

3. Topping It Off
=================

Let's make it even more fun. What if *jack-o'-lanterns gave a diamond to every online player*? Yeah, we can make that 
happen too! Let's start by making a copy of the event we already have and its contents. We should now change the 
``carved_pumpkin`` material of said event to ``jack_o_lantern``, so it's only triggered when clicking jack-o'-lantern 
blocks.

.. note::
    There are other ways to achieve the same result. For example, a single general event that is triggered for both 
    carved pumpkin and jack-o'-lantern blocks being clicked could be used. This would mean filtering the needed blocks 
    with logic afterwards, usually with **if/else if/else** trees or **choose** commands. In this guide though, two 
    separate events will be used as that can help keep it simple without losing functionality.

Inside the event, we need to repeat the give command once per player. How to do that? You've guessed it, a loop! In 
our case, to wrap the :guilabel:`give` command with a :guilabel:`foreach` loop is all we need. We just need to feed it 
the list of online players, which can be accessed through ``<server.list_online_players>``.

Ad a reminder, we can retrieve the *currently looped object* inside the :guilabel:`foreach` command block with 
``<def[value]>``. We'll use this player object to tell the give command who to target. This can easily be done by 
setting the linked player of said command, possible thanks to the ``player:`` argument. Feed this argument the tag 
we've just mentioned and we're ready to go.

Here's the complete second event:

.. code-block:: dscript
    :name: figureDIY_1_4
    :linenos:
    :emphasize-lines: 9-13

    halloween_treasure_hunt:
    type: world
    events:
      on player left clicks carved_pumpkin in:Hub:
      - narrate "You've found a carved pumpkin! Here's your reward!"
      - modifyblock <context.location> air
      - give diamond
     
      on player left clicks jack_o_lantern in:Hub:
      - narrate "You've found a carved pumpkin! Here's your reward!"
      - modifyblock <context.location> air
      - foreach <server.list_online_players>:
        - give diamond player:<def[value]>

.. rst-class:: figurecaption

**Figure 1.4** Our world script with a second event

We also have to let all the players know who their new *hero* is, and instead of narrating to them one by one, we can 
just announce the message to the whole server. According to the :guilabel:`announce` command syntax, it only requires 
one argument: the message. We just want to know the *name* of the player who found the hidden block , but that's not a 
problem at all. As we already know, all events related to players let you access their linked player with the 
``<player>`` tag. In our case, we need their actual name, so we will just add ``.name`` to the tag.

.. note::
    Double quotes (``" "``) are used to group text so it's treated as a *single argument*. This is specially useful for 
    commands based on chat text, such as :guilabel:`narrate` and :guilabel:`announce`.

Our command would be as easy as ``- announce "<player.name> has found a jack-o'-lantern. Everybody gets a reward!"``. 
We only have to replace the old narrate command in the second event with our new announce. Now we just have to make 
sure it *works as intended* after reloading, and finally set the ``debug:`` key to ``false`` so only error messages 
are shown. No more console *spam*!

Finally, this is the full script that we've created:

.. code-block:: dscript
    :name: figureDIY_1_5
    :linenos:
    :emphasize-lines: 3,11

    halloween_treasure_hunt:
    type: world
    debug: false
    events:
      on player left clicks carved_pumpkin in:Hub:
      - narrate "You've found a carved pumpkin! Here's your reward!"
      - modifyblock <context.location> air
      - give diamond
     
      on player left clicks jack_o_lantern in:Hub:
      - announce "<player.name> has found a jack-o'-lantern. Everybody gets a reward!"
      - modifyblock <context.location> air
      - foreach <server.list_online_players>:
        - give diamond player:<def[value]>

.. rst-class:: figurecaption

**Figure 1.5** Our world script, finally complete

This should be it for now. Enjoy your brand new **Halloween Treasure Hunt** event and *happy scripting*!