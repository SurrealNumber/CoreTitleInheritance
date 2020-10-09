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

Running game 2nd time:
Title and on click for decision missing
both designate and de-designate are showing up again. - also both showing up for other character's titles, and for capital titles.

******Making sure I don't for get pattern:*********
save_temporary_scope_as = this_title
scope:this_title available everywhere.

Run 3:
remove_laws possibly button tooltip example.
both buttons still appear on all titles, including primary title. Are valid in both cases.
object browser.
same problems with decision text

Adding test event and decision.
Run 4:
test decision has the same missing localization.
test decision triggered core titles were removed -both of them did
putting into the console event cti_test_event.0001 fired cti_manage_core_titles_event.001 and triggered core titles were removed. BUT NO EVENT WINDOW.
^ makes me think part of the problem lies with the events.

It looks like localization in events MUST be of the form event_name.numbers.thing
same problem occuring
figured out problem with namespace.

It looks like for decisions it should be ai_check_interval, not ai_check_frequency as it says in the .info
In events next line in localization will not trigger a bullet point, while custom_tooltip= will trigger a bullet point.
It looks like what is failing now is recalculate_cores


Looks like having if and else at the base is not valid for scripted triggers. need to use trigger_if or trigger_else
tried to fix up triggers a bit.
still same problem with event



effects:
every_heir_to_title
ordered_heir_title
set_destroy_on_succession
set_special_title
order_title_heir
every_heir_title
every_held_title
set_always_follows_primary_heir

Data Type Title has function IsPrimary

is trigger has_primary_title.

exists trigger any_in_de_jure_heirarchy
exists trigger any_in_de_facto_heirarchy
exists any_this_title_or_de_jure_above
If can't find flag I want (always_follows_primary_heir), then check if seeing if the list of heirs only has one element would work.
Need to test to see if it is an actual flag.
It loooks like flags are only on characters. I will check this.

any_title_heir has scope of title
when setting variables (and presumably elsewhere) in order to use add and the like one must go 
value = {
    value = 0
    add = x
}


set_variable =
{
name = test1
value = {
value = 0
every_title_heir = {add = 1}
}

}

Looks like many failures return yes, or more accuratly it looks like none evaluates to true

c_brie_francaise (6708)
214/18558 - character
always_follows_primary_heir is a line in the save game, and can be set. It looks like unless it is set titles do not have it

game_rules are in settings, so they should be able to be changed.

Could and probably should change to have a curated core title list on the character.
de_facto_liege and de_jure_liege are titles.
there is a capital_barony line in the save game
CANNOT ACCESS always_follows_primary_heir FROM SCRIPT!!!! - Infuruating
Moving away from using this means special titles like religious heads are not handled.
^ actually only required 3 changes to code. Not as tedious to change as I thought
is_shown (and probably other places as well) has an implied AND = {}
can add custom_descriptions to triggers
Looks like I might need trigger = yes if I want checking a trigger to work.
Sooooooooooooooo many syntax errors. I wish there was a better comprehensive guide. I should probably contribute if one is there, but it is beyond me to create one.

Looks like for scripted modifiers there are 4 things one can do with them/give them.
1. modifier = {} straight value, used in script for things like comparing chances also takes factor
2. opinion_modifier = {} modifier to opinion, I think it is applied to characters and effects their opinion (presumably has way to specify who it is between)
    looks like it can take a trigger for when it applies. It also has a who and target arguements
3. compare_modifier = {} takes factor and multiplier
4. compatability_modifier = {} takes who, compatability_target, min, max, and trigger

modifiers can use first valid. they can also have `desc = localization_thing`
exists has_opinion_modifier
can add to list without initializing
exists add_character_modifier - the same modifier does not appear to stack. - takes name = <> time = int
Esists remove_all_character_modifier_instances takes name <- seems to suggest that modifiers can stack.
remove_character_modifier takes name
scripted_tests_recalculate_character_modifier

buildings can have character_modifiers
It looks like it is impossible to stack modifiers. The only way around this I can see is to create a dummy building.
POD mod (about as close to an official mod as one can get) has tiers of modifiers, but no stacking modifiers.
possibly just do it without a modifier and be carefully?

over core limit modifier has only a small finite set of values.
Cost overtime for cores has a potentially infinite set of values (though really limited to under 1000 due to the number of counties.) 

It makes sense that modifiers should not be dynamic/dynamically generated. It is very expensive performance-wise. But to not allow stacking modifiers makes less sense. It would require being a bit more careful, but should be doable relatively easily without a large performance overhead.
Look into possibility of province/county/title modifiers.
exist dynasty modifiers
exist house modifiers
exist scheme modifiers
EXIST COUNTY MODIFIERS:
    add_county_modifier
        add_county_modifier = name
        add_county_modifier = {modifier = name days/weeks/months/years = int}
        supported scopes is landed title <- will need to check if this is in fact only counties, or if it also supports other titles like baronies. Could use it to add text to titles as well with possibly dummy modifiers.
    remove_all_county_modifier_instances - Remove all instances of a modifier from a county
        remove_all_county_modifier_instances = name
        Supported Scopes: landed title
    remove_county_modifier - Remove a modifier from a county
        remove_county_modifier = name
        Supported Scopes: landed title
EXIST PROVINCE MODIFIERS: - looks to be associated with a barony
    add_province_modifier - Add a modifier to a province
        add_province_modifier = name
        add_province_modifier = { modifier = name days/weeks/months/years = int }
        Supported Scopes: province
    remove_all_province_modifier_instances - Remove all instances of a modifier from a province
        remove_all_province_modifier_instances = name
        Supported Scopes: province
    remove_province_modifier - Remove a modifier from a province
        remove_province_modifier = name
        Supported Scopes: province

Need to find out if county titles have an associated province. Also it looks like county capitals cannot be moved.
Idea now:
county -> county and province
barony -> province

PROVINCE MODIFIER SEEMS LIKE THE WAY TO GO.
TIERED MODIFIER FOR THE OVER CORE LIMIT PENALTY.

One interpretation is that the titles which count to core limit are exactly core provinces/baronies. <- it is more succinct to say that the same things which count to domain limit count to core limit
THERE IS A GAME CONCEPT FOR IT -> HOLDING
any_directly_owned_province <- might give holding number, or restrict to holdings
^not as useful, given it would then have to be restricted to core titles. 
set_variable =
{
name = test1
value = {
value = 0
any_directly_owned_province = {add = 1}
}
}

My mod currently has no grace period. I should probably look into adding it.

comments in triggers.log seem to suggest that holding and province are equivalent. This is useful to know. (Though they aren't quite equivalent, as I think one is a building or title, and the other is a map tile)
recalculating core titles from the decision _should_ never cause inconsistent core titles to be lost.
figure out how to do bullet points. especially applicanle in game concepts.
capitals and the like will have a monthly prestige cost, but probably should not.
I SHOULD GIVE PROVINCES WHICH ARE CORE TITLES A SMALL CONTROL GROWTH BUFF.
Really have to figure out how to do dynamic encyclopedia entries.
It is possible to be over the core limit without including any designated core holdings. Will have to address this.
NOTE ABOUT CODE: core_count means core_holding_count. Same with core_limit and core_holding_limit
Have to put in something to trigger over core limit alert.
Possibly consider having the modifiers hidden so they only show up for the character.
it looks like most province modifiers are actually meant for different scopes than provinces.

When to apply province modifier?
    1. When applying the cost of coring.
    2. When recalculating the core titles.
    4. At start of game.

Should add check for if the ai can designate core titles. If they can't then I should disable the functions from applying on them.

I don't know of an on action for changing capitals or primary titles. These are both events which should trigger core titles to recalculate, but I don't know of the on actions for them.
I realize that recalculate is called alot, and the initializers it calls replace what is there. It should be good enough.
Due to how the modifiers work I have to apply them at the start of the game and initialize them.

At start of game things to possibly use:
every_barony
every_county
every_independent_ruler
any_kingdom
any_living_character
any_player
any_province
any_ruler <- count tier and above

IT IS else_if NOT else if


Trying to comment out things until it works.

Removing while clause - assuming it does not work.
looks like passing yes or no won't work. Have to pass a trigger -i.e. always or trigger = {always = no} <- need to verify this
look into possibility of using hidden_effect for options in events.
LOCALIZATIONS CAN USE SCOPES i.e. scope:xxxx exists in the localization one can have [xxxx.SomethingOnDataTypeToReturnString]

Things which exist:
remove_character_flag
remove_localized_text
remove_from_list
remove_global_variable
remove_list_global_variable
remove_list_local_variable
remove_list_variable
remove_local_variable
remove_variable


Can probably get things that are refered to with things like GetPlayer or GetTitle and the like, but will not for now.
For activities there is a WriteListOfAcceptedGuests.
Looks like there is a CharacterSelectionList data type
InteractionOtherEffect has some mention of lists.
There is a SaveGameAnalyzer data type

To get always_follows_primary_heir aspect of titles, Maybe hijack the autosave and read the save file?
the Scope datatype has a GetList function
Esists the Title data type
Esists VariableList and VariableListEntry (and some more) data types
interesting
EventTargetSetupContext = {
		AccessVariableLists -> VariableListStore
		AccessVariables -> VariableStore
		AccessSelf -> [unregistered]
		Self -> [unregistered]
	}

Looks like those things won't work. Looking into creating a custom widget to list titles.


Title view window looking into for listing/showing titles.
in the shared gui folder, lists.gui contains types Scrollbar and as a subset of that type scrollbox. The title window is overiding the scrollbox contents to use the overall scrollbox format.
dejure_tab from title window:
some rules, centered text heading then a scrollbox.
In the scrollbox the content is a vbox.
vbox uses datamodel DeJureVassalGroupItem.GetTitleItems
it has some layout options.
item bloc -> presumably for the vassal group
It prints the [owner of the title]?
defines an item block.
The item contains a button_standard_list. The data context for this is TitleItem.GetTitle
For the title it sets up the onclicks for right and left clicks as defaults for coat of arms. It then defines the tooltip widget as the standard one for titles.
The button_standard_list contains a hbox which has contains a small coat of arms for the title and the name of the title.

It looks like how lists work is that you set the datamodel to be something with items in it, define an item block, and then it will display that item block for every item in the list.
There are global promotes? and global function available in the gui language.

It looks like the gui has a variable system, and one way to acces it is GetVariableSystem(.Exists, .Clear, .Toggle, .Set, .HasValue, )
There is also an ObjectsEqual

Can I re-implement the core title checks in the gui system?
For titles I have IsPrimary, IsBarony, HasHolder, HasLaws, IsHeldBy, GetTierAsName+several similar tier things, GetHolder, MakeScope
Can get some list of titles, MakeScope, and pass to a scripted gui to determine if it is core or not.
global function primary_titles, global function titles, GHWTargetWindow has GetTitles. CharacterInteractionConfirmationWindow has GetTitles, Character Window has get Titles,
MyRealm window has GetTitleSuccession
SuccessionEventWIndow has GetInheritedTitles and GetLostTitles

Places with GetTitles:
GHWTargetWindow
CharacterInteractionConfirmationWindow
CharacterWindow
CreateClaimantFactionAgainstWindow
FindTitleView
LeaseOutBaroniesWindow
SuccessionEventWindowLostTitlesItem
SuccessionLawChangeWindow
TitleSuccessionItem

Might be better off modifying the character view to display core titles.
Sadly succession event is not an actual event.

It looks like I will have to modify the character view.
Can define windows and call them from elsewhere.
Will focus on modifying the titles expanded section of the character window.
As long as I am there, I will try to put core limit below domain limit.
Looks like a little head of faith icon can appear below the domain limit.
I think I want to add an entirely seperate line bellow claims. It will not show the core titles unless clicked, but when clicked it will show them in the manner that clicking on a title does, but split up by sections.
While I am at it I might want to add something to the domain window. and the county/barony view window. <- features to be added later.
Alternate option would be to add it to the domain tab of the my_realm window thing. Or create a new tab in that window.
Question of should other players/characters be able to see which titles are core?
Answer: Yes. It is important for multiplayer or if the AI eventually uses the feature. Otherwise there will be missing information. It should go in the character window for this reason.
expand seems to be a block that will expand to fill up space if there is some.
Tight interface, have to squeeze it in there. Barring further redesign I think I can only add the small thing at the bottom and push the rest up. Space will come from ...
portrait and background at top?
Traits being slightly smaller?
There is always room next to where it says titles and where it says claims.
There might also be room next to where it says claims - a bit more because have the space above and below.
Could put next to claims at low priority? - Not all characters have a claim. Could also put in a scroll bar, but want to avoid that.
Replaces claims if they are not present, if they are eventually collapses to just the right part of the claims window.
Examples: Always assume there are titles.
______________________________________
|n Titles                +k|         |
|T T T T T T T             |         |
|j Cores                 +w|Diplomacy|
|T T T T T                 |         |
======================================

______________________________________
|n Titles                +k|         |
|T T T T T T T             |         |
|j Claims   | m Cores    +w|Diplomacy|
|T T T T T  | T T T T T T  |         |
======================================


______________________________________
|n Titles                +k|         |
|T T T T T T T             |         |
|j Claims   +q|  m Cores +w|Diplomacy|<- can't tell that the titles there are cores.
|T T T T T T T T T T T T T |         |
======================================


______________________________________
|n Titles                +k|         |
|T T T T T T T             |         |
|j Claims   +q|  m Cores +w|Diplomacy|I like this
|T T T T T T T| T T T T T T|         |
======================================


__________________________________________
 Ban|n Titles                +k|         |
 ner|T T T T T T T             |         |
  D |j Claims   +q|  m Cores +w|Diplomacy| How to fit in core limit if it shold be fit in
  h |T T T T T T T| T T T T T T|         |
==========================================

Core limit is not crucial information.
I should add a core titles tab to my_realm to give that information and give some management options.
Essentially the event is now irrelevent and being replaced by proper gui.

Scopes have a GetList function. Might be able to use that and items. Looking into the character window change first.
No idea what the down part of a vbox does.
max titles depends on other factors
all are instances of vbox_titles_claims_box defined at bottom of file.
different title boxes to display.
The different versions only differ in the cutoff for the number of titles and claims and related factors.
Conditions for displaying:
Overall box (vbox)
if no diplomacy or faction -> 1st one 13
if diplomacy then in hbox with diplomacy.
    no faction and diplomacy <=4 -> 2nd one 10
    no faction and 4<diplomacy<=6 -> 3rd one 9
    no faction and 6<diplomacy<=8 -> 4th one 8
    no faction and 8<diplomacy -> 5th one 7

Will have to put in some stuff to make limits play nicely when cores or claims are not present.
What happens when claims but no titles? - only claims display (still one row)
What happens with factions? - I don't see a difference.
Plan now: as above, but in case where titles is missing below claims instead. Start by always having it split with claims. (and restricting claims to half the size)
might have to use variable system to deal with some of the on click things in the gui.
Possible alternative: add icon to cores - like title laws icon. <- subtle. Less intrusive. Don't have to deal with character window. I like it.
Looks like the title sub icon is added at stages other than the title coat of arms stage. This means to change it I would have to change those windows.
Goint with the small icon I would want to probably ideally want to use a flat metal crown texture or interface/icons/event_types/type_magesty or type_generic.
Or icons/flat_icons/army_raise_assigned or go_to_my_capital or heir or mapmode_duchy or mapmode_empire or war_outcome_victory or
Or icons/war_status/ war_won_icon
Or icons/map_icons commander_is_leader or 
Or icons/message_feed/county_corruption or
or icons/portraits/hier or heir_small or me or me_small
Or icons/religion/hellenic
Or icons/ commander_is_leader*** or

Based on the implementtion the small icon would actually require touching more files and display the information less clearly.
Will do the character window idea.
When there are no titles, there will be no core titles. Things will work out then (just need to get limits right).
When there are titles but no claims it will also work out correctly.
What bothers me is that the titles will be repeated.

New Plan:

______________________________________
|n Titles    +k   m Cores  |         |<- same fade for cores as for title.
|T T T T T T T             |         |
|j Cores                 +w|Diplomacy|
|T T T T T                 |         |
======================================

Might want to change to long fade like in the text below for children, siblings etc.
I think I want:
f|n Titles   +k f|Cores (number) <- again, like the ones below. Also want font color like diplomacy, less obtrusive.
Copy most of text stuff from diplomacy.
The main or onl difference in the text for diplomacy is that Title links to the encyclopeia, and diplomacy does not.
Copied from above GetVariableSystem(.Exists, .Clear, .Toggle, .Set, .HasValue, )
there exists SelectLocalization( bool, loc1 (true), loc2 (false))
It looks like for the get variable system toggles, it can be toggled on or off, and exists checks for the thing being toggled on.
Most likely have to put in the variable system conditions elsewhere. Need to hide others when selected, and hide this when others selected.
Whole relations/family etc hidden when one of the other tabs is open.
can put in multiple on-clicks.
Will hijack the is titles expanded to only have to deal with two places.
down = when butotn is suppressed.
two versions of core text - One for title window open, the other for the title window not.
title text - add on click to clear toggle.
core text, title on, core on - toggle, toggle <- core displayed
core text, title on, core off - none, toggle <- title displayed
core text, title off, core off - toggle, toggle. <- other/none displayed
core text, title off, core on - toggle, none <- other displayed, core not toggled off
Will need core text for equal, and both non-equals.

What will the breakdown be by?
Want core by cti and other core reasons. - assume the player can figure out what is a holding.
cti, capital chain, primary title, special.
special will most likely never be visible.
capital chain and primary title should be mutually exclusive unless the capital is the primary title.
^ Note that is NOT equivalent to being a count or baron.
primary title and capital chain always visible. Except will hide both when equal and show different category.
cti core titles visible only if there is a cti reason core title.
might have to remove gridbox from primary title/capital and primary title.
Just noticed there is expand none. It would not have been all to useful. It would not reduce the number of cases.
IN GENERAL I THINK I SHOULD USE DE_FACTO INSTEAD OF DEJURE.
For titles there is GetDeFactoLiege, GetDeFactoTopLiege, and GetDeJureLiege. None of these are recursive.
Will do the core limit very much like the character window does domain limit.

**********************I am considering having icons/commander_is_leader.dds as **the** symbol for core titles.
**********************^ Decided to do this. I like the icon, and think it works well enough for the purpose.

Because I am throwing in the core titles expanded window only shows for alive, landed chracters, it might unexpextedly show up at some points.

Scripted gui things needed:
    tooltip for core limit box <- should probably include penalty.
    is over core limit for core limit background
    text A/B for core limit.
    is capital chain for displaying cc titles.
    are any cores special for displaying special section
    is a core special for displaying special titles
    are there any cti cores
    is the title a cti core
    are there any core titles<- probably actually not necessary because at a minimum the primary title and capital are cores. But possible title succession laws funkiness so will want. have to change conditions for showing core title tab.
    are there more than 1 core titles?
    number of core titles
    
-Scripted guis to make:
    core limit box
        tooltip - character
        bool is over core limit - character
    capital chain character
        bool multiple capital chain - character
    capital chain title <- not necessary to split, but seems better to do it for consistency.
        bool is capital chain - title
    special cores character
        bool are any special cores - character
        bool multiple special - character
    special cores title
        bool is this special - title
    cti cores character
        bool are any cti cores - character
        bool multiple cti cores - character
    cti cores title
        bool is this cti core - title
    core title text line
        bool any core titles - character<- might also be used elsewhere.
        bool multiple core titles - character
Custom localization or like to make:
    core limit
        text A/B - character
    number of core titles
        self explainatory

What do I have to work with in a scripted gui?
    scope
    saved_scopes
    is_shown - returns bool
    is_valid - returns bool
    effect - can have tooltip or call effects
    could possibly use custom or scripted localization to do the text.
These are the only functions on a scripted gui:
ScriptedGui = {
    AccessSelf -> [unregistered]
    BuildTooltip -> CString
    Execute -> void
    ExecuteTooltip -> CString
    IsShown -> bool
    IsShownTooltip -> CString
    IsValid -> bool
    IsValidTooltip -> CString
    Self -> [unregistered]
}

Localization needed: # possibly also need singular versions as well and code to support them.
    CV_CORE_CAPITAL_PRIMARY
    CV_CORE_PRIMARY
    CV_CORES_CAPITAL_CHAIN
    CV_CORES_SPECIAL
    CV_CORES_MOD
    CV_CORES_CAPITAL_CHAIN_SINGULAR
    CV_CORES_SPECIAL_SINGULAR
    CV_CORES_MOD_SINGULAR
    CV_CORE_TITLES_HIDE_CTT
    CV_CORE_TITLES_SHOW_CTT
    CV_CORE_TITLES
    CV_CORE_TITLES_SINGULAR
    CV_CORE_TITLE_TAB_HEADER


******************COUNTY != Barony, capital of a county can be moved and is a barony in it.*************************
******************all titles in capital county are passed together**************************************************
This means a character can have up to 6 core holdings just from their capital county. Have to find a way to not penalize this.
I think the way to do it is to say that the capital county only counts as one core holding.
***********Could also change it so that core holdings is actually core counties. - This would make sense actually as a county is currently the smallest inheritable unit.

Lots of inefficiencies with going through the list of core titles. This can be dealt with later.
I assume that datacontext is essentially saying that you are setting the global/datatype object of that type to what is specified.
Possibly add an opinion penalty to being over the core limit.

Scripted gui:
in is_valid:
custom_description = {
text = "stuff(possibly trigger_loc)"
subject = scope
trigger
}
^ This gives a custom reason for why the decision is not valid in tooltip

customizable location seems to be mainly to differentiate between the elements of the same class of thing or to randomize between equivalent words.
I am using [xxx|V] asuming it mean value, but apparently that is not true. I have no idea what it means.
After a regex I have found in between the |]:
E - encyclopedia
U - seems to be mostly for ui names or the like
0
V - no clue used for all sorts of stuff
1
2
P
L - seems to be mainly for traits
D
N
no |x at all

trigger localization seems to be about getting first, third, etc person correct for triggers.
Will have to to the things I thought could be custom localization in the localization system.

visible = "[AND(ScriptedGui.IsShown(GuiScope.SetRoot(CharacterWindow.GetCharacter)).End, And( VariableSystem.Exists( 'cv_expanded_core_titles_tab'), CharacterWindow.AreTitlesExpanded )) ]"
worst case senario booleans being equal is equivalent to (A and B) or ((not A) and (not B))
possibly can use script things in localization language. Will not use unless see confirmation.

AND(ScriptedGui.IsShown( GuiScope.SetRoot( CharacterWindow.GetCharacter ).End ), Or(And( VariableSystem.Exists('cv_expanded_core_titles_tab'), CharacterWindow.AreTitlesExpanded ), And( Not(VariableSystem.Exists('cv_expanded_core_titles_tab')), Not(CharacterWindow.AreTitlesExpanded) )) )


In script replacing the value core_limit with val_core_limit
.Var() is actually used frequently. I was just looking for GetVariable, and not finding that.


#[And( Character.IsLocalPlayer,
    #.AddScope('viewing_character', Character.MakeScope)
    #saved_scopes = {
    #    viewing_character
    #}
    
Possibly change increase = trigger = {always = no} to increase = {always = no}

    if = {
        limit = {$INCREASE$ = yes}
        set_variable = {
        name = dummy
        value = $COST$
        }
    }
    
Clicking on titles in the character view will not act as intended if the expanded core titles view is open.
Leaving character view or going to a new character un-expands all of the views.