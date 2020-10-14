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

Having both designate and de-esignate buttons visible can exapnd the interface to the point where it stretches off the background.
It looks like the problem is that the grids are keeping the invisible titles.
It appears that gridboxes determine the number of rows and columns and their places at the beginning. Setting some things to be invisible will not help with that.
dynamicgridbox appears to be slightly better or at least behave differently.

fixedgridbox = {
    name = "special_titles_box"
    datacontext = "[CharacterWindow.GetCharacter]"
    flipdirection = yes
    addrow = 90
    addcolumn = 270
    datamodel_wrap = 2
    
^this was there before.
Removing it and letting titles list one by one. Good enough for now.
It is possible to use expanded traits in character window. It appears to be unused for now.
what happens if a county you do not stand to inherit contains a barony which has always_follows_primary_heir = yes? - It does nothing. Counties are the smallest unit of inheritance.

Currently I believe that everything  I want to put in is there at least to some degree (but might not be working or need updating).
TODO:
x Logic - change from holdings to counties - baronies are meaningless in terms of inheritance.
x Logic - ignore baronies.
x Logic - change dejure to defacto (will mean that creating a duchy title between owned county and kingdom will break kingdom. Think about this. Also check what happens if owned county is in vassal's duchy.)
Logic - update for title succession laws
Logic - update for changing capital or primary title.
x Logic - un-comment commented out sections
x Logic - automatically core created titles inserting into middle of heirarchy.
x Localization and game concepts - change to reflect departure from baronies.
~ Localization - add missing localization.
x Interface - Add decision for decoring all core titles.
Debug

NOTE Ignoring baronies means IGNORING them. They are not to be considered in the logic.
de-facto and de-jure can refer to titles owned by other characters. Need to be careful about this.
Title succession law titles will never be considered cores.
need to think if there are cases where core title variable should be changed to no, but set_always follows_primary_heir should not be set to no.
Is there ever a case where I should not use safe_de_core_title? -- No, merging into de_core_title. - only case this would occur is if the variable is cleared.
continue seems to actually be when should I continue. Some descriptions seem to suggest that it is when to stop.
When I clean up my code I should be sure to change to utilizing assumed ands for triggers and limits.
assume that continue checks for the next title.
will have to find in files for holding and for province and purge.
Declaring head of state special - from what I saw these titles have the flag always_follows_primary_heir.
Look into holy orders and mercenary companies.

found this note in the code: # interaction effects are executed for all clients, make sure to open the window only for the actor
^worth keeping in mind.

Can't find way to expand core titles tab in character view from important_action\alert
Unsure of how to open decision window for specific decision.
looks like lists any_ can have optional count parameter.
Looks like list every can have alternative limits.

the format every/any_in_list = {
    list = list_name
    limit = {limit}
    add = x/trigger = yes
}
^ looks like this format is needed for scripted values.

Exist NOR, NAND which are equivalent to NOT = {OR/AND = {}}
It looks like there is a has_variable trigger.

Keeps on complaining about this wherever it shows up.
[23:02:56][jomini_effect.cpp:557]: Unknown effect = at  file:  file: common/scripted_guis/cti_title_buttons_scripted_guis.txt line: 28; designate_core_title line: 3
[23:02:56][jomini_effect.cpp:557]: Unknown effect INCREASE at  file:  file: common/scripted_guis/cti_title_buttons_scripted_guis.txt line: 28; designate_core_title line: 4
[23:02:56][jomini_effect.cpp:557]: Unknown effect COST at  file:  file: common/scripted_guis/cti_title_buttons_scripted_guis.txt line: 28; designate_core_title line: 5

Seems like something might actually be going wrong in set_title_core. Switching order causes it to complain about else\else_if not following if (when it is)
looks like effects might have to be called with effect_name = something. Looking into.
Looks like you do have to. use = yes unless additional things have to be added.
It looks like CK3 does not like recursion.
Down to the only error being complaining my core_title_item list does not exist.
TODO:
Debug
Logic - update for title succession laws
Logic - update for changing capital or primary title.
Localization - add missing localization.
Clean up code
x Logic - find replacement for removed recursion. (used while loop)
Gui - Implement janky filtering gridbox?

replaced exists = var: with has_variable = 
possibly add way to manually recalculate cores.

Save: TestGame
both desigate and de-designate core are still showing up.
Creating the duchy title triggered removing invalid cores. Should not have happened.
Duchy should also be core due to being de-facto above capital.
Destroying the duchy also triggered it.
Usurping title also triggered. it. Both buttons also still showing up on other character's titles.
Need to be careful that no more than three buttons are in a row on title screen, otherwise it stretches it.
Creating Empire of italy triggered removing cores
Still no capital chain except for county of capital appearing.
Special Core Titles still showing up both in singular and plural.
Looks like there is plenty of error logging for me to look at and debug.
It looks like root is the root of the calling interaction or event NOT the root of the code block. Will have to fix many things to work with this.
Unknown new error: promote returned nullptr. <- If I had to guess, I probably for got to check for a variable existing before using it somewhere.

New duchy over capital county is added as designated core, NOT as being part of tha capital chain. Still triggers the removing cores toast.
Still same effect after checking to make sure not to designate it if it was a core not due to cti.
Suspect at this point that is core is not picking up on non-cti cores correctly. <- guess was correct. Anything that is above county tier is automatically a core.
Shoudl change text from de-facto above capital to capital and de-facto above. Also when singular it is just capital. Should fix that localization.
The de_facto_liege for my capital appears to be either my highest tier or primary title (I am not sure).
For duke vassal, county -> his duchy, and duchy -> my kingdom (NOT empire)
My duchy also points to the empire of italy.
Same occures for county under vassal held duchy.
Want to check de-jure, and check to see if it exists, and check to see if you own it. Check up if dejure does not exist.
If dejure exists, but is not owned by you, check de-jure up until reaching primary title tier.
if reached primary title tier and it is not under you, in my model of de-facto heirarchy it would be a child of your primary title.
Auto create core when:
1. my model of de_facto title above it is core, and a de_jure child is core.
2. as a result of above if primary title tier, do not auto core.
3. Also as a result of above, do not auto core counties or baronies. (though they should never be created)

For the capital chain, it is strictly de-jure.
Senario: Emperor of Italy, King of Italy, King of Romagna, no holdings in Kingdom of Italy or Kingdom of Romagna. No other kingdoms or empires.(implied by this is that the capital is elsewhere.)
Should the character be allowed to designate one of the kingdoms as core? <- I feel like they should be able to designate the "primary" kingdom as core.
If so which one? <- One which is de-jure capital of empire?
                         ^What about if it was the Byzantine Empire with all other things being the same. <- mo de-jure capital kingdom.
Requiring that one has at least *one county* de-jure under a title to declare it as core is not to stringent of a condition.

Auto-coring and eligibility of titles will go back to de-jure. Senarios where it would make sense to have a title which you hold no de-jure territory under as core are covered by that title being your primary title.
when checking for core loss, we are looking for titles which are de jure leige which do not have a child title chain excluding the current title.
If possible would like to do this: go to my version of de facto liege. Check for a my version of de facto child which is core.
tricky situations: de-designating county, ruler is emperor. Duchy above does not exist. Kingdom above is core, and has :
1. a different de-jure duchy is core.
2. a different de-jure county without duchy above existing is core.
3. different de-jure county is core with duchy above that is not core (and there are no other cores de-jure below kingdom)
Desired outcomes:
1. will not break.
2. will not break.
3. will break.

look for dejure as well. Seem to be some with that instead (probably developer typo).
To work with have:
any\every\ordered\random_in_de_jure_hierarchy - iterate from self down all the way (can specify a continue or limit)
any\every\random\ordered_this_title_or_de_jure_above - iterate through this title and all dejure liege titles.
is_de_jure_liege_or_above_target - self explainatory <- need to make sure it does not trigger on itself.
target_is_de_jure_liege_or_above - self explainatory <- need to make sure it does not trigger on itself.
any\every\ordered\random_realm_de_jure_xxxx - all de-jure ot title in the character's realm
ordered\random\every_de_jure_county_holder - iterates through characters
ordered\random\every_de_jure_top_liege - iterates through characters
any/every\ordered\random_dejure_vassal_title_holder - iterates through characters - all vassal holders of title <- don't know what this description means
set_de_jure_liege_title - not that relevant here

could do if holder= chain to get to my definition of de-facto liege (I think)
From there excluded child search


I wish I could define a function which returns a scope.

auto coring occurs when child chain is core and my definition of de-facto liege is also core <- these are the conditions for when decoring a title will break a chain.

Creating titles dows not always cause toast now.
Need to change localization for de facto above capital to de jure above capital.
designate core title is not working. Will look into.
!= cannot be used when comparing two objects. same with <

Every title (including those you don't own) still has both buttons. Looking into this next.
nullptr is returned after opening the title window.

IT APPEARS TO BE WORKING (but missing _a_lot_ of localization)


TODO:
Debug
Logic - update for title succession laws
Logic - update for changing capital or primary title.
Localization:
    trigger and effect localization
    broken localization
    capital chain single and plural versions
    fix special cores always showing up.
    update de_facto game concept
Clean up code
x Logic - find replacement for removed recursion. (used while loop)
Gui - Implement janky filtering gridbox?
All - implement way to remove system upon single heir succession.
Misc - Make AI

has_title_law - have given title-specific law
has_title_law_flag - have title-specific law with given flag
.HasLaws in gui language
unclear what the difference between the two in scripting language is. Also looks like they both need to be fed a specific law.
Looks like difference is between specific laws and law groups.
Flags seen: elective_succession_law, advanced_succession_law
^ unclear if the scripting things also consider the realm law when checking.

Possibly find a way to iterate through all possible title laws. That or find a way to use the gui system in script.
I can do that through a scripted gui, but not in the way I need to.
according to wiki, only duchy and above titles can have title laws.
It appears that currently the only title laws are for elective succession, and the only realm laws are for partition or single heir.
^  makes sense, but why can't single heir have ellections?
head of faith specialness seems to come from two places. when defined or made, and the head of faith succession laws.

What is the difference between player_heir and primary_heir
There is a gui MyRealmWindow.GetSuccessionExceptions
MyRealmWindow.GetTitlesCanVote

Found what looks like a bug in the base game. Kingdom of Italia is primary title with elective succession. Kingdom of italy has no title laws. Kingdom of italy does not show up as going to brother who is primary heir in my_realm tab even though it says he will get it in title window.
^ Kingdom of italy does not show up at all.
^ kingdom of italy had title succession law, but same as realm. Most likely this caused it to not show up anywhere.
^ not going to look into further here.

following is stated in _laws.info
Elective is currently only title succession.
Partition is currently only realm.
Single heir can be both.

If single heir and same gender laws as primary title, it will be put into general succession. Otherwise it is treated seperately.
To check for title laws: 
If they have the flag elective_succession_law then they are title law.
If they don't have flag advanced_succession_law they are realm?
If advanced check if same gender law as realm (somehow).

If primary title has other successor of your dynasty, your primary heir will be them. Flag always_follows_primary_heir still works. Capital and de-jure above also pass to them.

there does nor appear to be a way to get the realm or title laws.
exists has_realm_law.

This is disapointing - I cannot deal with for instance mod made title or realm laws. I can have to manually check them.

Outline of check for title laws:
is flag elective -> yes
is flag advanced -> x
else -> no
x - for each gender option, realm has and title has (no -> yes)
for each single heir option, realm has and title has (no ->yes)
no
If there was a list of title laws this would work out.

Need to check if titles when asked laws will give realm laws if they have none.

Before I proceed, what would the effect of title succession laws be?
    cannot core. <- seems reasonable.
    ^ means if primary or in capital chain must exclude. or special?

Don't want to deal with this now. Will create issue on github, and leave be for now.


just found a note in a file saying that |U can only capitalize the first word in a string.
exists set_realm_capital effect
exists set_capital_county - meant for titles
exists set_primary_title_to

setting realm and capital counties through UI might not go through these. I also do not know how I would overwrite them.
Can't find on actions on them.
Will also raise issue on github and leave be for now.

TODO:
Debug
Localization:
    trigger and effect localization
    broken localization
    capital chain single and plural versions
    update de_facto game concept
Clean up code
Gui - Implement janky filtering gridbox? - put into issue on github
All - implement way to remove system upon single heir succession. - put into issue on github
Misc - Make AI - put inot issue on github.

Removing things moved to github issues:
Debug
Clean up code
Localization:
    trigger and effect localization
    broken localization
    capital chain single and plural versions
    update de_facto game concept
    other miscelaneous stuff I know I forgot or did not realize existed.


If one looses a duchy title due to title succession law, de-jure teritory beneath goes as well including capital.
Cannot core duchy even though counties beneath it are core. To debug this effectively I should document the triggers and effects.
ran into error wher is_title_core_due_to_capital caused an error to be raised because the character had no capital. Will have to look into.

documenting triggers:

trigger can have custom_descriptions.
custom_description = {
    text = localization or trigger_localization
    trigger- used in trigger as a whole and when false shows the text.
}

In trigger_localization key is trigger name, and the options are the person of the trigger.
Have $COMPARATOR$ and $NUM$ are available in localization text
CHARACTER, TITLE, PROVINCE seem to be available in localization text in gui language.
For comparator specializations it is unclear what specifies which one is used.
Triggers have implied AND at base level. Will change code to utilize this.
SHOULD BE ABLE TO CORE PRIMARY/CAPITAL CHAIN TITLES - BENEFIT MIGHT BE WANTED.
^would need ui clarification for when they are/are not cores from the mod.
^benefit is already applied. Only difference is that the flat cost is not.
^going to declare good enough as is.

Localization for triggers game says I am missing:
NOT_has_variable_trigger (BUG: has_variable missing perspective)
primary_title_equal
capital_county_equal
work on effects for modifier stuff (BUG remove_all_character_modifier_instances missing perspective)
is_de_jure_liege_or_above_target
NOT_TITLE_TIER_TRIGGER_THIRD
core_title_item
landed_title_this_equal
any_this_title_or_de_jure_above_all
NOT_TITLE_TIER_TRIGGER
is_head_of_faith
^ these are just for buttons
add space between icon and numbers in core title tab. Also make icon bigger. Fix core titles - it should be integer/integer. It is currently 0.00/
break up blob of text in core title encyclopedia entry
fix too much being bold problem in list encyclopedia entries.
fix icon for core title persistence
change icon for core county limit to core title icon. -same with core county
ck3 possibly does not like setting a variable to a scripted value.
will definetely want to make use of custom_descriptions for triggers.
will start with only global trigger localization. Can add more later.
Suspect that character does not have capital spam is being done at game start. Probably also occuring when titles are transfered, as characters losing titles might not have a capital. Also would be a problem if it was called with a baron or other ruler without a county.
now I need:
core_title_item
NOT_IS_DEJURE_LIEGE_OR_ABOVE_TARGET_TRIGGER
NOT_ANY_THIS_TITLE_OR_DE_JURE_ABOVE_ALL_TRIGGER
NOT_CAPITAL_COUNTY_EQUAL_TRIGGER
NOT_IS_DE_JURE_LIEGE_OR_ABOVE_TRIGGER
NOT_PRIMARY_TITLE_EQUAL_TRIGGER
NOT_IS_HEAD_OF_FAITH_TRIGGER
primary_title_equal and has_variable are missing perspective?

wrong context supplied before TITLE_DE_DESIGNATE_CORE_TOOLTIP_PRIMARY_TITLE error. Says unknown formatting tag 'NAK!'

now says I need:
NOT_HAS_VARIABLE_TRIGGER
title.GetName instead of actual name
NOT_IS_DE_JURE_LIEGE_OR_ABOVE_TARGET_TRIGGER
missing thing before or De Jure above:
landed_title_var_equal

removed #T sjfsjdlfjlejlkjdfs# from tooltips for (de)designate cores


Unknown formatting tag '"(v' on character window.
PRIMARY_TITLE_EQUAL_TRIGGER <- prolem here. Says wrong context was provided?
TARGET_TITLE does not seem to be working
is de-jure liege or above seems to be negated in child chain even though it is not.
checking any in a list seems to make the trigger extremely long. will have to look into how to fix it.
need NOT_LANDED_TITLE_VAR_EQUAL_TRIGGER
for some reason the localization at least is saying that a county title is not greater or equal to county
missing title name for is not realm capital and is not primary title.
Seems when looking for childrent it is looking for the title to be de-jure liege or above the title. <- Actually that looks to be part of will break core chain.<- don't know why that one is being triggered.
could designate duchy core after I had the empire. Something funky is going on. It looks like either it is looking for a higher tier title, or is checking going too high?
problem has been reversed - things which I should not be able to core nan now be cored.
County of pavia is showing up incharacter limit as a core title, but it does not have the modifier.<- fixed this bug (I think)
prestige modifier on province does not effect the character.
The check for if I can core the duchy of lombardy seems to be checked based on my _primary_title_!
beyond problems designating, the duchy in question should have been auto cored.

Looks like de-designating does too much with modifiers
Need any_this_title_or_de_jure_above
Need IS_CORE_TITLE_TRIGGER
It looks like core_title_item is only returning the first core title. <- checked and doing every_core_title_item is returning the right amount of things.
need localization for always?


save_temporary_scope_as = _title
holder = {# Changes scope to holder
    any_held_title = { # goes through core titles of owner.
        NOT = {this = scope:_title} # excludes this title from consideration (used for checking breaking chains or adding cores)
        target_is_de_jure_liege_or_above = scope:_title # de_jure_child of this title. Assume that this disqualifies the title I am calling this on.
        
        NOT = { # need to make sure condition is true for every, so looking for one thing which does not meet it.
            any_this_title_or_de_jure_above = {
                trigger_if = {
                    limit = {exists = holder}
                    # nothing in chain is owned by holder (which implies it exists) but is not core.
                    # will break if there is a title which is held by holder, but is not core between two titles.
                    holder = scope:_title.holder # held by owner. This shold also disqualify titles that don't exist.
                    tier < scope:_title.tier #in chain between two titles <- because title itself is presumably in heirarchy
                    #is_title_core = no # not core
                }
                trigger_else = {
                    always = no
                }
            }
        }
    }
}

If I ever use realm, I might want to use sub_realm instead.
Looks like what is happening is that the *tooltip* only displays the first entry in the list.


#is_a_child_title_chain_core = {EXCLUDED = this}
save_temporary_scope_as = _title
save_temporary_scope_as = _excluded
holder = {
	any_core_title_item = {
		trigger_if = {
			limit = {
				NOT = {this = scope:_excluded}
				target_is_de_jure_liege_or_above = scope:_title
			}
			NOT = {
				any_this_title_or_de_jure_above = {
					trigger_if = {
						limit = {exists = holder}
						holder = scope:_title.holder
						tier < scope:_title.tier
						is_title_core = no
					}
					trigger_else = {
						always = no
					}
				}
			}
		}
		trigger_else ={
			always = no
		}
	}
}

check if EXCLUDED is messing things up because the logic is working.

test_trigger = {
    any_held_title = {
        NOT = {this = $EXCLUDED$}
    }
}

test_trigger = {EXCLUDED = this}

Passing in `this` results in the `this` being used, NOT `this_object`
I am using the same names for temporary scopes everywhere. I have to be careful that I am not overriding it by accident.
core titles were removed as opposed to a new core being automatically created.
Prestige modifier and over core limit modifiers do not seem to be working, but otherwise it seems to be mostly functional.
Possible overriding/colliding scope _title names in will_core_loss_break_chain. Changing scope name.
auto creating core working properly
was not calculating correctly for will core loss break chain. K, d, c core -> can decore d. Triggering recalculating cores correctly removes k. Need to figure out why.
will core loss break chain currently allows for core breakage at the level of the primary title.
I might run into problems if I try to do things to a title with no de-jure parents/children like a head of faith title or a titular title.
Problem is in logic of is_a_child_title_chain_core. Need to exclude the title in two places.

extremely non-rigorus testing of ability to core/decore and which titles are eligible is done for now. Next I will try to get all (or as many as I can) of the modifiers to work.
Based off how I am doing things, the core limit will not dynamically update when the character window is open.
Number of cores and core limit numbers are all showing up as 0.0
Managed to get rid of the errors, but everything is still 0.0
Will look in non-debug mode for finishing touches on tooltips.
Designated Core Title is showing singular even though it is supposed to be plural.
^Same with Core Title text in realm tab.
Will be taking over traits expanded.
************I don't see a way into the expanded traits tab, and it does not appear to display any new information. I will commandeer it. I need to make sure to mention it.
Possibly is shown when there are too many modifiers?
The problem is that there is actually a problem with calculating the core limit and core count etc.
It looks like values are referenced by scope.value_name. <- I guess it is like this because they are scopes.


Testing in object explorer seems to indicate for values I have to use every_list_name = {add = 1} as opposed to every_in_list.
What I am looking into right now does not explain core limit. Will have to look into that next.<- it actually does.
Still wondering/want to know if something is wrong with core list. Possibly load order, will try adding numbers before files - think that effects load order.
hypothesis for problem is that the *files* refer to each other, even though the individual *functions* don't. Splitting the files and effecting the load order would fix this.
in testing load order for decisions - 99_ overruled 00_, 01_, and cti_
will try splitting trigger file.

every_list_name causes an error saying it is an unexpected token.
It looks like it is just funkiness with scripted lists. It looks like they are not fully working.
It looks like it is possible to add and remove from a list. This might be preferable to recalculating it every time. Look towards possibility in the future. <- raise on github
^ this looks like it is needed to get around scripted lists not working.
clear and initialize lists when recalculating, add and remove in applying cost function (outside of if it has a cost). Possibly also rename function to reflect that.
One problem with this approach is that the core list in the scripting language can be out of sync with the one on the character window due to not being recalculated after the primary title changed or capital moved.
^Will leave the problem be for now. Seems relatively minor and hard to approach without the on_actions for them.

How to deal with the lists.
add to\create it: - assume create it.
add_to_variable_list = { name = variable_name target = scope }
remove from it:
remove_list_variable = { name = variable_name target = scope }
clear_variable_list = variable_name

what do these do?
variable_list_size, local_variable_list_size and global_variable_list_size

found these in jomini manuscript:
add_to_variable_list = { name = variable_name target = scope }
remove_list_variable = { name = variable_name target = scope }
clear_variable_list = variable_name
any/every/random/ordered_in_list = {
    variable = variable_name
}
is_target_in_variable_list = { name = variable_name target = scope }
variable_list_size = { name = variable_name target = value } <- how does this work?
has_variable_list = variable_name

at some point I have to question the utility of the variable core count.

note that held titles also gives the county capital barony title associated with the owned counties.
appears to work as expected, however I cannot figure out what variable_list_size does.


tasks:
add/remove core to list
add/subtract from core count
apply cost

in gui core_count is working, but core_limit and the count of core_titles is not.
It looks like "this" behaves attrociously. It seems to be maintained as a pointer to this scope, meaning that a value from it is not actually a value, and anything derived from it cannot be passed or moved between scopes.
^ I have determined that I should avoid "this." like the plague
need to add localization for over core limit alert. Possibly also change icon.
core limit is working. Display of number of core titles and display of core limit still is not. Will work on.

.Custom, .Custom2, .Custom2_Title <- might want to investigate what these calls in the gui language do.
Most likely being a bit overly zealous about calculating the variable.

exists trigger has_order_of_succession
has_revokable_lease <- might have to look in to leases.
Should switch to using has_title, has_primary_title, highest_held_title_tier,
apparently special titles exist in the game. Looks like they are related to factions.
exists holds_landed_title
CK3 is throwing a lot of "Property cannot be a data property for animation." but it is still working as I intended. I will ignore the errors for now. They might even survive to the end because it is working.