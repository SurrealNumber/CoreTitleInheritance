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
    core and de-core gui and scripted gui
    notification for core title loss?
on_action:
    on_action for losing core title
    on_action when inheriting core titles
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
    decision to see core titles
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