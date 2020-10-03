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

PRIMARY TITLE IS CONSIDERED CORE.
Make sure that primary title is never removed from cores.

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
    trigger for eligible title
    trigger for if title is core
    trigger for eligibility requirements
    trigger for over core title limit
    trigger for higher tiers breaking
scripted_value:
-    scripted_value for core limit
value:
    any constants needed - can think of as parameters
scripted_modifier:
-    scripted_modifier for monthly prestige loss - will add or not depending on game rule
variable:
    character scope variable for number of cores
scripted_effect:
    scripted_effect to apply flat/scaled cost
    scripted_effect to apply core flag to titles
    scripted_effect for refunding flat/scaled cost
    scripted_effect to remove core flag from titles
    scripted_effect to remove invalid core titles
    scripted_effect to check wiping coreness and do it
    scripted_effect to update character with given cores
    scripted_effect to remove/refund all core titles
scripted_list:
    scripted_list to retrieve core titles
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