TODO: AI
TODO: Implement penalty for being over core limit

what I need:
additional button to remove core
set title as core function
    - modifier for holding if game rule needs
    - count of core holdings
    - Keep as core on inheritance? - figure out how that works
check eligible title funtion
possibly tell AI how to use this feature

scripted value for limit
trigger for checking if title is eligible
    - trigger for checking if title is core
    - trigger for checking if character is over limit
value for flat prestige loss - here should remove core titles on inheritance (make them pay again)?
modifier for overtime prestige loss (scripted) - reference variable to multiply
variable on character for # core titles
scripted_list of core titles
on_actions  strip core on title changing hands https://forum.paradoxplaza.com/forum/threads/modding-quick-questions-and-answers.1414681/page-22#post-26910318
scripted value to calculate number of core titles on inheritance
Let AI use feature- would do through creating events with ai weights.


static values: variable - immutable?
scripted_values: calculated value based on formula
variables: value stored on a scope
modifiers: add modifier to scope - essentially adds modifier on value at scope - stack additively, but modifier can be applied multiplicitavely
scripted_modifiers: same as modifiers, but can be calculated?
triggers: Seem to be essentially formulas returning booleans to be used elsewhere (eg, evaluating if someone is eligible for something)
events: pop-up event with built in ui
opinions: Opinion modifier, presumably between characters -- looks to be only between characters
relations: relations between two things - like friend etc
on_actions: essentially pulses which can be used to trigger events - events are triggered in on_action?
scripted_gui: script that can be referenced in gui modding
scripted_list: generated list of scopes
scope: essentially game object
effect: something which does things
scripted_effect: essentially a generic function which does things
rules: ?

Communicate recommendations for settings
only counties and baronies count towards core limit
only counties and baronies have cost
Can't designate duchy where you don't own land as core. Might lead to it being lost even if not desired. Out of scope of this mod.
creating convention: don't use this for base scope of environment
!!!Possible problem if checking empire tier title-no dejure liege
Do not know how to get a list of dejure children of a title if it is even possible.

PRIMARY TITLE IS CONSIDERED CORE.
Make sure that primary title is never removed from cores.
****Need to handle primary title changing
    not sure if this will break if an event changes the primary title
I THINK I AM GETTING PRIMARY TITLE CONFUSED WITH CAPITAL
!!!!Need to consider what happens if one of the titles has a different title succession law
if capital and primary title are different counties, you get both.
If primary title is not dejure above capital, get capital dejure titles up to one tier below primary title.
PRIMARY TITLE ONLY EFFECTS CORE THINGS IF YOU ARE A COUNT WITH A DIFFERENT CAPITAL AND PRIMARY TITLE
add decision to show core titles?
has_primary_title triggers.log line 2180 scopes character, landed title
variable on character will be named core_count
primary title is the only one which breaks the rules of core territories.



core flag utilizes
effects.log:
    set_always_follows_primary_heir (986)*************

Need:
gui:
-    core and de-core gui and scripted gui
    notification for core title loss?
on_action:
-    on_action for losing core title
-    on_action when inheriting core titles
trigger:
-    trigger for eligible title
-    trigger for if title is core
-    trigger for eligibility requirements
-    trigger for over core title limit
-    trigger for higher tiers breaking
-    trigger for allowed to core title
-    trigger for allowed to de-core title
scripted_value:
-    scripted_value for core limit (core_limit)
-    scripted value for core costs and refunds - flat and scaled
value:
    any constants needed - can think of as parameters
scripted_modifier:
-    scripted_modifier for monthly prestige loss - will add or not depending on game rule
variable:
    character scope variable for number of cores (core_count) - Could instead build a list of core titles and recalculate each time. like this way better - more for me to keep track of, but less iteration over lists
scripted_effect:
-    scripted_effect to apply core flag to titles
-    scripted_effect to remove core flag from titles
-    scripted_effect to remove invalid core titles
-    scripted_effect to check wiping coreness and do it
-    scripted_effect to update character with given cores
-    scripted_effect to remove/refund all core titles
scripted_list:
-    scripted_list to retrieve core titles
decision:
-    decision to see core titles
AI stuff
    
    



Flow:
character actions\events:
    designate title
        check if title is eligible
            not core
            owned by character
            county, barony, or own one title below of tier -1
        check if limit is reached
            calculate limit for character
            retreive # of cores of character
            compare
        apply cost
            flat cost - apply cost from static value (or variable prestige cost provided)
            apply modifier (scripted from number of cores)
        designate title as core
            add core flag to title
            increment any variables stored on character
    de-designate title
        check if title is core
            is core
            owned by character
            higher tiers not invalidated by it's loss
        refund cost
            refund cost (flat or variable prestige cost provided)
            remove modifier or change things it is calculated from (possibly done automatically in the next step)
        de-desginate title as core
            remove core flag
            increment any variables stored on character
    lose core title
        update character losing
            send notification
            increment any variables stored on character
            remove invalid core titles
        wipe title of coreness
            remove core title flag
character dies
    titles inherited
        possibly wipe coreness
            remove core title flag depending on game rule
        update character cores if not wiped
            check titles still valid
            initialize and increment any variables stored on character
            initialize modifier (scripted from number of cores)
            possible remove core status from titles if over limit (potential bandaid - if over title limit refund full cost and remove all titles)

AI: Low priority
    check designate on timed pulse or on title situation change
    check designate core on timed pulse or on title situation change
    
    
    
How to check if a core list is consistent?
1. check that every title greater than county is dejure liege of another one - requires going through the list twice
have to go through list twice

It looks like there are no scaled prestige values. Removing option.
It is possible that inheriting core titles will not work. In this case automatically remove them on inheritance.
It is possible that primary titles and capital chain titles will not be caught by current check for cores. Fix this if that is the case.
Need to know if x = no is equivalent to NOT = {x}
Have to be careful with special titles which do always follow primary heir (especially if they are conquered)
changing to have a core variable on the core titles, and the coring and de-coring options take care of setting the flag
don't know if CK3 likes setting previously set variables.
It is possible that the datacontext for the title is already set in gui. I will ignore the possible optimization due to not understanding how the gui language works
possibly have to refresh gui after button is clicked to swap it with the other button.


Gui Tooltip:
TITLE_MAKE_PRIMARY_TOOLTIP:1 "#T Make [TitleViewWindow.GetTitle.GetName] your [primary_title|E]#!" - line in localization
produces: Make _TitleName_ your BLUE[Primary Title]
_TitleName_ - has tooltip of title.
BLUE[Primary Title] - has reference to game concept/encyclopedia entry (I am not sure which one it is).

FIND_VASSAL_HEADER:2 "Grant to..."
FIND_VASSAL_RELEVANCE:0 "Relevance"
FIND_VASSAL_BUTTON_TOOLTIP:2 "#T $FIND_VASSAL_HEADER$#!\nChoose someone to grant this [title|E] to.\n\n$FIND_VASSAL_BUTTON_TOOLTIP_SEGMENT$"
FIND_VASSAL_BUTTON_TOOLTIP_SEGMENT:0 "You can grant [titles|E] to one of your [vassals|E], or one of your [guests|E] or any [courtiers|E] in your [realm|E]"
^ entries in localization file
produces:
BIGGERTEXT{Grant To...}
Choose someone to grant this BLUE{Title} to.

You can grant BLUE{Titles} to one of your BLUE{Vassals}, or one of your |end of box
BLUE{Guests} or any BLUE{Coutriers} in your BLUE{Realm}

Interpretation:
[word|E] <- tooltip for encyclopedia entry (most likely from game concepts, has a small icon in top left which would explain image references)
#T # <- make it into title format
\n <- standard new line
$REFERENCE$ <- way to reference another localization entry


No idea how to change localization based on game rule. Will table for now.
Possibly change over core limit warning text to say your instead of your character's name
What if the dejure parent is core, but not for you?


Looking into decision gui/localization:
start_hunt_decision:0 "Call Hunt"
start_hunt_decision_tooltip:1 "A [hunt|E] will be held"
start_hunt_decision_desc:0 "#F The untamed wilds are calling, and all manner of beasts are awaiting my spear and arrow!#!"
start_hunt_decision_go_on_hunt:1 "You go on a #V Hunt#! in one of the baronies of your realm"
start_hunt_decision_tt_header:0 "A successful Hunt will:"
start_hunt_decision_prestige:1 "You may get opportunities to increase your [prestige|E]"
start_hunt_decision_stress:0 "#P    Reduce your [stress|E]#!"
start_hunt_decision_stress_lazy:0 "#N You will not lose [stress|E] since you are [GetTrait('lazy').GetName( GetPlayer )]#!"
start_hunt_decision_confirm:0 "Sound the horn!"
^in localization file. Decision is start_hung_decision^

decision:
Call Hunt on left with Reversed subset of image on right

tooltip:
Call Hunt
a BLUE{Hunt} will be held

Decision Window:
Call Hunt <-fancy font center of top
image (most likely one defined in decision) <- full width of window
greyish-small-minor-text{The untamed wilds are calling, and all manner of beasts are awaiting my spear and arrow} <- desc
new boxlike thing 1
new boxlike thing 2
Effects - centered title like
several blank lines
<- line break from custom tooltip start
* You go on a WHITE{Hunt} in one of the baronies of your realm <- _go_on_hunt
* You lose {stress loss image} 30 BLUE{Stress} <- stress_impact from custom tooltip?
* You may get opportunities to increase your BLUE{Prestige} <- _prestige
one blank line
{red exclaimation mark in circle}ITALICS{RED{Call Hunt will be unavailable for} WHITE{5 years}}
end of box like thing 2
lots of blank
end of box like thing 1
centered
Cost:{gold icon} 67
Button with text {Sound the horn!}
notify when available box.

Interpretation:
key is reused heavily
desc goies wher it showed up in window
#X text# applies an effect to text
custom tooltip is constructed in decision
custom tooltip = x appears to be appending x to the tooltip with a new line in between
 - Unsure of new line consistency will test later and modify scripted gui based on result
Looks like one can only push with things i.e. things can only be launched from on_actions. There is no way to tell something to look for an on_action

looks like show_as_tooltip = {stuff} executes the stuff and show the tooltip of the stuff being done {some way to print things assumed}
can't find how to add numbers to descriptions. Will just try to put in desc = value.
I think there is a nicer way to filter lists, but I don't know what it is right now.
Might need to make localization for effects, triggers, and modifiers. Don't know yet.

!!!Should let character know when they cores are invalidated and destroyed.
!!change to safe_de_core_title


notifications:
I’ll start with the notifications and toasts as they are the simplest. You simply make a database entry in the common folder for your notification and then wherever you want to run it from you use the send_interface_message or send_interface_toast effect.
Want to send toast when de-coring a title. Only should occur if full refund would be given- i.e. the mod removes them for internal reasons.
Add modifier penalty for being over the core limit. Remove decoring titles which are over the limit.

Titles are only decored on:
    most title transfers
    invalid core title due to some reason (change capital or lose title)
!!! No on_action for changing capital or primary title.

-Removing limitation that one cannot add core titles beyond the core limit.
-Add check that child title or parent title is actually owned by this character

-Toast when a title is de-cored (by mod)
-Alert when over core title limit

send_interface_toast = {
    type = message type <- defined in game/common/messages/
    title = localization thing
    desc = localization thing
    left_icon = Seems to be character scope
    right_icon = also seems to be character scope
    show_as_tooltip = { can be used like before presumably to show modifiers effects and the like
    }
    can also put modifier and effects here and they will presumably show up
    custom_tooltip = localization thing
}


Figure out how to put values into encyclopedia entries dynamically.
!!Might have to change scope.trigger to trigger = scope
Might be able to remove if from every_list and instead use just limit
can probably use root instead of scope:this_title in most places.
It looks like some localizations can use the scope to be localized. things like character_scope.GetUIName etc.

RUNNING GAME:
IT DID NOT CRASH!
Decision has empty text 
-and image.
-core_titles game concept is broken
Title and confirmation things in decision are broken. Most of description seems to be working
Upon decison being used toast for core titles lost was triggered. I am king of france in 1066 with no time passed.
No event poped up for the decision.
-parent game concepts apear to be shown in the top right of the entry for the child.
took titles and vassals from brittany and nothing visible happened.
-core title cost list part of description is all bold italics. Look horrible. Same with core title limit.
core title cost game concept does not have an icon.
For the duchy of brittany both the designate core and de-designate core buttons are present. Clicking them did nothing visual.

Checking afterwards what the game rules were:
Overtime Core Title Cost
Half Domain COre Title Limit
Persist Core persistence
Not allowed AI core designation.
description for ai core designation allowed is broken.


Might break if capital is a barony and the primary title is a different county
does not take into account title succession laws
Need to prevent prestige from refunds from giving fame
Need to deal with the situation where the de jure duchy title does not exist, or is held by a different character (similarly for other tiers)
Possibly remove the ability to core titles of the highest tier.
